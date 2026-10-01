# 4. Arquitetura Distribuída e Modelo de Dados — SentinelTrade

## 4.1. Visão Geral da Arquitetura de Componentes
O **SentinelTrade** foi desenhado sob uma arquitetura de **Microsserviços Distribuídos** orientada a eventos. Essa abordagem garante o isolamento de falhas, escalabilidade horizontal e alta disponibilidade (99,99%), assegurando que picos de acesso no módulo de cotações não comprometam o processamento transacional das ordens.

* **Camada de Apresentação:** Interface reativa em Vue.js para o investidor acompanhar gráficos, carteira e submeter ordens.
* **Camada de Borda & Segurança (API Gateway):** Ponto único de entrada responsável por terminação TLS 1.3, validação de tokens JWT, controle de acesso (RBAC) e proteção contra ataques de força bruta.
* **Microsserviços Core:** 
  * *Auth-Service:* Gerencia identidades, hash seguro de senhas e verificação MFA.
  * *Wallet & Risk Service:* Controla saldos financeiros, custódia e executa o portão de risco pré-execução.
  * *Order Service:* Gerencia o ciclo de vida completo da ordem.
  * *Market Simulator Service:* Simula a conexão com a bolsa externa e cotações.
* **Mensageria & Cache (Resiliência):** Apache Kafka (barramento de eventos assíncronos) e Redis (cache de cotações e chaves de idempotência).
* **Persistência & Auditoria:** PostgreSQL (banco transacional principal) e Storage WORM / S3 (logs de auditoria imutáveis).

---

## 4.2. Modelo de Dados Relacional (Entidades Principais)

### A. Investidor (`Investidor`)
* `id` (UUID, PK) — Identificador único
* `nome_completo` (VARCHAR) — Nome do usuário
* `email` (VARCHAR, UNIQUE) — E-mail de login
* `senha_hash` (VARCHAR) — Hash seguro da senha
* `mfa_secret` (VARCHAR) — Chave secreta do MFA
* `perfil` (ENUM: `INVESTIDOR`, `AUDITOR`, `ADMIN`)

### B. Conta Corrente (`Conta`)
* `id` (UUID, PK)
* `investidor_id` (UUID, FK ➔ Investidor)
* `saldo_disponivel` (DECIMAL) — Saldo livre para novas ordens
* `saldo_bloqueado` (DECIMAL) — Saldo reservado em ordens pendentes
* `status` (ENUM: `ATIVA`, `BLOQUEADA`, `ENCERRADA`)

### C. Ativo Financeiro (`Ativo`)
* `id` (UUID, PK)
* `ticker` (VARCHAR) — Ex: `PETR4`, `VALE3`
* `nome` (VARCHAR) — Nome da empresa ou fundo
* `tipo` (ENUM: `ACAO`, `ETF`, `FII`)
* `preco_atual` (DECIMAL) — Último preço fornecido pelo provedor

### D. Posição em Carteira (`CarteiraPosicao`)
* `id` (UUID, PK)
* `investidor_id` (UUID, FK ➔ Investidor)
* `ativo_id` (UUID, FK ➔ Ativo)
* `quantidade` (INT) — Quantidade de cotas/ações custodiadas
* `preco_medio` (DECIMAL) — Preço médio de aquisição

### E. Ordem de Negociação (`Ordem`)
* `id` (UUID, PK)
* `idempotency_key` (VARCHAR, UNIQUE) — Chave anti-duplicidade
* `investidor_id` (UUID, FK ➔ Investidor)
* `ativo_id` (UUID, FK ➔ Ativo)
* `tipo` (ENUM: `COMPRA`, `VENDA`)
* `quantidade` (INT)
* `preco_limite` (DECIMAL)
* `status` (ENUM: `PENDENTE`, `VALIDADA`, `ENVIADA_BOLSA`, `EXECUTADA`, `REJEITADA`, `CANCELADA`)
* `criado_em` (TIMESTAMP)