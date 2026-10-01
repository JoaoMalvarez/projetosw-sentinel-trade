# 3. Critérios de Aceitação e Matriz de Rastreabilidade

## 3.1. Critérios de Aceitação
* **CA01 (Referente ao RF01):** O sistema deve recusar o acesso de qualquer investidor que informe a senha correta mas omita ou erre o código de Autenticação Multifator (MFA), gerando um log de alerta de segurança.
* **CA02 (Referente ao RF05):** Caso um investidor tente submeter uma ordem de compra no valor de R$ 10.000,00 possuindo apenas R$ 5.000,00 de saldo disponível, o portão de risco deve interceptar, bloquear e rejeitar a ordem antes que ela chegue à bolsa simulada, informando o motivo exato.
* **CA03 (Referente ao RNF02):** Se ocorrer uma falha de rede (*timeout*) exatamente no momento do envio da ordem e o cliente reenviar a requisição utilizando a mesma chave de idempotência (`UUID`), o sistema deve retornar o status da ordem original sem processar uma nova compra ou débito em duplicidade.

---

## 3.2. Matriz de Rastreabilidade
A tabela a seguir correlaciona os requisitos funcionais e não funcionais críticos com os mecanismos lógicos, diagramas conceituais e critérios de aceitação do projeto:

| Requisito | Descrição Resumida | Modelagem Conceitual / Fluxo | Mecanismo Lógico / Arquitetura | Critério de Aceitação / Teste |
| :--- | :--- | :--- | :--- | :--- |
| **RF01** | Autenticação MFA | Diagrama de Caso de Uso (Módulo de Acesso) | Módulo de Autenticação e Gestão de Sessão | Validação de credenciais e desafio MFA obrigatório no login. |
| **RF03** | Consulta de Cotações | Diagrama de Caso de Uso / Sequência | Consumidor de API do Provedor Externo | Exibição em tempo quase real das variações de ativos na interface. |
| **RF05** | Validação Prévia de Risco | Diagrama de Atividades (Ciclo de Vida da Ordem) | Portão de Risco & Serviço de Carteira/Saldo | Rejeição automática imediata se saldo, limites ou posição forem insuficientes. |
| **RNF02** | Prevenção de Duplicidade | Diagrama de Componentes / Comunicação | Filtro de Idempotência e Cache Distribuído | Rejeição de requisições repetidas pelo mesmo UUID sob falha de rede. |
| **RN02** | Trilha de Auditoria | Diagrama de Implantação / Segurança | Armazenamento WORM (Write Once, Read Many) | Garantia de imutabilidade e consulta restrita a auditores regulatórios. |