# 1. Visão, Objetivo e Atores do SentinelTrade

## 1.1. Visão e Objetivo do Projeto
O **SentinelTrade** é uma plataforma distribuída de *Trade Financeiro de Alta Criticidade* desenvolvida sob encomenda para a corretora fictícia **Orion Capital**. 

Atualmente, a infraestrutura operacional da Orion Capital depende de sistemas legados fragmentados, o que gera graves deficiências na rastreabilidade de ordens, dificuldades no controle de risco em tempo real e processos de auditoria lentos e falíveis. 

O objetivo principal do SentinelTrade é unificar e blindar a operação de trading da corretora por meio de:
* **Rastreabilidade Ponta a Ponta:** Acompanhamento rigoroso de todo o ciclo de vida das ordens.
* **Segurança e Conformidade:** Autenticação robusta por múltiplos fatores (MFA) e logs de auditoria imutáveis.
* **Tolerância a Falhas e Resiliência:** Arquitetura distribuída capaz de operar com alta disponibilidade (99,99%) e prevenção de duplicidade sob falhas de rede.

No contexto da Orion Capital, falhas operacionais geram impactos severos divididos em três frentes críticas: **financeira** (perdas por execuções incorretas ou atrasadas), **regulatória** (penalidades por descumprimento de normas do mercado financeiro) e **reputacional** (perda de confiança dos investidores). O SentinelTrade mitiga ativamente esses riscos.

---

## 1.2. Identificação dos Atores
* **Investidor Autorizado:** 
  * *Descrição:* Usuário final (pessoa física ou jurídica) cadastrado e autorizado a operar na plataforma.
  * *Responsabilidades:* Autenticar-se via MFA, consultar cotações de mercado, gerenciar sua carteira de ativos (ações, ETFs, FIIs), submeter ordens de compra e venda, e acompanhar o extrato e histórico de operações.
* **Provedor Externo de Cotações:** 
  * *Descrição:* Serviço ou API externa contratada que alimenta o ecossistema com dados de mercado.
  * *Responsabilidades:* Enviar atualizações de preços, volumes e abertura/fechamento de ativos em tempo quase real para o barramento do SentinelTrade.
* **Bolsa / Corretora Simulada:** 
  * *Descrição:* Sistema parceiro externo simulado que representa o ambiente real de negociação.
  * *Responsabilidades:* Receber as ordens validadas pelo SentinelTrade, processar a liquidação no livro de ofertas simulado e retornar o status final de execução (`EXECUTADA`, `REJEITADA` ou `CANCELADA`).
* **Auditor / Regulador:** 
  * *Descrição:* Perfil institucional interno ou externo com privilégios de fiscalização.
  * *Responsabilidades:* Consultar trilhas de auditoria imutáveis para verificar a conformidade das operações, investigar anomalias e assegurar o cumprimento das regras regulatórias.