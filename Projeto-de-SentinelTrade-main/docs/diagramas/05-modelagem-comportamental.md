# 5. Modelagem Comportamental (Diagramas e Fluxos) — SentinelTrade

## 5.1. Diagrama de Casos de Uso
O Diagrama de Casos de Uso mapeia a interação dos atores externos com as funcionalidades principais do SentinelTrade:
* **Investidor Autorizado:** Realiza login com MFA (`RF01`), consulta cotações em tempo real (`RF03`), consulta saldo e extrato da carteira (`RF02`), submete ordens de compra/venda (`RF04`) e cancela ordens pendentes.
* **Provedor Externo de Cotações:** Alimenta o sistema com as cotações de mercado (`RF03`).
* **Bolsa / Corretora Simulada:** Recebe ordens encaminhadas pelo portão de risco (`RF05`) e retorna o status de execução.
* **Auditor / Regulador:** Acessa trilhas de auditoria imutáveis WORM (`RF08`).

---

## 5.2. Diagrama de Sequência — Fluxo de Envio e Execução de Ordem
Descreve a ordem cronológica de troca de mensagens entre os componentes durante uma operação de compra:
1. O **Investidor** submete uma ordem de compra pelo **Frontend (Vue.js)** com uma chave de idempotência (`UUID`).
2. O **API Gateway** valida o token JWT e encaminha a requisição para o **Order Service**.
3. O **Order Service** verifica no **Redis** se a chave de idempotência já foi processada (prevenindo duplicidade sob falha de rede) (`RNF02`).
4. O serviço aciona o **Wallet & Risk Service** para realizar o portão de validação pré-execução (saldo disponível, posição e status de mercado) (`RF05`).
5. Sendo aprovado, o **Order Service** persiste o status inicial no **PostgreSQL**, publica o evento no barramento **Apache Kafka** e encaminha a ordem para a **Bolsa Simulada**.
6. A **Bolsa Simulada** processa a execução e retorna a confirmação.
7. O sistema atualiza o status para `EXECUTADA`, grava o evento definitivo no **Storage de Auditoria WORM** (`RF08`) e notifica o investidor em tempo real (`RF07`).

---

## 5.3. Diagrama de Atividades — Ciclo de Vida da Ordem
Mapeia o fluxo lógico de tomada de decisão do estado de uma ordem:
* [Início] ➔ **Ordem Criada** ➔ [Portão de Risco: Validação de Saldo e Limites]
  * Se **Reprovado**: Status muda para `REJEITADA` ➔ Registra Log WORM ➔ [Fim].
  * Se **Aprovado**: Status muda para `VALIDADA` ➔ **Enviada à Bolsa Simulada**.
  * No retorno da Bolsa:
    * Se **Executada**: Status muda para `EXECUTADA` ➔ Atualiza Posição em Carteira ➔ [Fim].
    * Se **Cancelada pelo Usuário**: Status muda para `CANCELADA` ➔ [Fim].