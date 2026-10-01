# DOCUMENTO DE ENGENHARIA DE REQUISITOS — SENTINELTRADE

## 1. Visão e Objetivo do Sistema

O SentinelTrade é uma plataforma distribuída de Trade Financeiro de Alta Criticidade desenvolvida para a corretora fictícia Orion Capital. Atualmente, as operações da corretora sofrem com sistemas legados fragmentados, dificultando a rastreabilidade de ordens, o controle de risco e auditorias.

- Objetivo Principal: Unificar a gestão de contas, carteiras, cotações em tempo real e roteamento de ordens com máxima segurança, tolerância a falhas e trilha de auditoria imutável, mitigando impactos financeiros, regulatórios e reputacionais.

---

## 2. Identificação dos Atores

- **Investidor Autorizado**: Usuário final autenticado por múltiplos fatores que gerencia carteiras, consulta cotações e envia ordens de compra/venda.
- **Provedor Externo de Cotações**: Serviço externo responsável por alimentar a plataforma com cotações de mercado em tempo quase real.
- **Bolsa / Corretora Simulada**: Sistema parceiro responsável por simular o ambiente de execução e liquidação de ordens.
- **Auditor / Regulador**: Papel focado na fiscalização, conformidade e consulta a logs imutáveis de transações.

--- 

## 3. Requisitos Funcionais (RF)

- **RF**01 - **Autenticação com MFA**: O sistema deve autenticar os investidores exigindo credenciais de acesso e verificação por Autenticação Multifator (MFA).

- **RF**02 - **Gestão de Investidores e Carteiras**: O sistema deve manter cadastros de investidores, contas, posições consolidadas em carteira e limites financeiros.

- **RF**03 - **Consulta de Cotações**: O sistema deve receber e disponibilizar cotações de ativos (ações, ETFs, FIIs) em tempo quase real provenientes do provedor externo.

- **RF**04 - **Gestão do Ciclo de Ordens**: O sistema deve permitir que o investidor submeta, consulte e cancele ordens de compra e venda de ativos.

- **RF**05 - **Validação Prévia de Risco**: O sistema deve validar obrigatoriamente o saldo financeiro, a posição de ativos, o limite de risco e o status do mercado antes de transmitir qualquer ordem.

- **RF**06 - **Integração com Bolsa Simulada**: O sistema deve encaminhar ordens validadas para a bolsa simulada e acompanhar o seu ciclo de vida (executada, rejeitada, cancelada).

- **RF**07 - **Notificações Operacionais**: O sistema deve notificar o investidor em tempo real sobre atualizações no status de suas ordens e execuções.

- **RF**08 - **Auditoria Imutável**: O sistema deve registrar todas as transações, acessos e alterações críticas em logs de auditoria à prova de adulteração.

--- 

## 4. Requisitos Não Funcionais (RNF)

- **RNF**01 - **Alta Disponibilidade**: A plataforma deve operar sob arquitetura distribuída tolerante a falhas, mirando uma disponibilidade de 99,99%.

- **RNF**02 - **Consistência e Idempotência**: O sistema deve prevenir duplicidade de ordens através de chaves de idempotência sob falhas de rede.

- **RNF**03 - **Segurança de Dados**: Criptografia ponta a ponta (TLS 1.3 em trânsito e AES-256 em repouso) para dados sensíveis, financeiros e credenciais.

- **RNF**04 - **Desempenho e Baixa Latência**: O processamento e validação de ordens pré-execução devem ocorrer em milissegundos para evitar perdas financeiras por atraso.

---

## 5. Regras de Negócios (RN)

- **RN**01 - **Bloqueio por Insuficiência**: Nenhuma ordem pode ser encaminhada à bolsa simulada caso o saldo financeiro (para compras) ou a quantidade de ativos (para vendas) seja insuficiente.

- **RN**02 - **Trilha WORM (Write Once, Read Many)**: Os registros de auditoria financeira não podem ser apagados ou modificados por nenhum usuário, garantindo conformidade regulatória.

- **RN**03 - **Janela de Mercado**: Ordens só podem ser enviadas se o ativo estiver com o mercado aberto e operando segundo o provedor de cotações.

---

## 6. Restrições (RT)

- **RT**01 - **Arquitetura de Microsserviços**: O sistema deve ser desacoplado em serviços independentes utilizando mensageria assíncrona (ex: Apache Kafka).

- **RT**02 - **Ausência de Segredos**: É estritamente proibida a inclusão de chaves de API, senhas ou tokens no código-fonte ou no repositório Git.

---

## 7. Critérios de Aceitação (CA)

- **CA**01 **(RF01)** ou **Autenticação com MFA**: Tentativas de login sem o código MFA válido devem ser rejeitadas imediatamente.

- **CA**02 **(RF05)** ou **Validação Prévia de Risco**: Uma ordem de compra cujo valor exceda o saldo disponível do investidor deve ser bloqueada antes de chegar à bolsa simulada, gerando um registro de rejeição.

- **CA**03 **(RNF02)** ou **Consistência e Idempotência**: Requisições repetidas com a mesma chave de idempotência (UUID) devido a timeout de rede devem retornar o status da ordem original sem processar a operação duas vezes.

---

## 8. Matriz de Rastreabilidade

| Requisito | Descrição Resumida | Modelagem / Diagrama | Implementação Técnica | Critério de Aceitação / Teste |
|---|---|---|---|---|
| RF01 | Autenticação MFA | Diagrama de Sequência (Login) | Módulo `AuthService` (Spring Security / JWT) | Validação de token e desafio MFA obrigatório no login. |
| RF05 | Validação Prévia de Risco | Diagrama de Atividades (Trade) | Microsserviço `Risk & Wallet Service` | Rejeição automática se saldo ou posição forem insuficientes. |
| RNF02 | Prevenção de Duplicidade | Diagrama de Componentes | Filtro de Idempotência (Redis Cache) | Rejeição de requisições duplicadas pelo mesmo UUID. |
| RN02 | Trilha de Auditoria | Diagrama de Implantação | Armazenamento WORM (S3 / Storage Imutável) | Garantia de imutabilidade e consulta restrita a auditores. |