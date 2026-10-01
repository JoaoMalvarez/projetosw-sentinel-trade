# Projeto de Projeto de Software - SentinelTrade

## Integrantes:

* Débora Lobato Santos / RA: 10732810
* Helen Santana de Araújo Teixeira / RA: 10742524
* João Pedro Mazzante Alvarez / RA: 10723837
* Vinicius Bisordi Acauã / RA: 10739883
---

## Visão do Projeto

O SentinelTrade nasce da necessidade preta de modernizar e blindar a infraestrutura operacional da corretora fictícia Orion Capital. Atualmente, o ecossistema da corretora sofre com sistemas legados fragmentados, o que dificulta o rastreamento rigoroso de ordens, expõe a instituição a falhas de conformidade e torna o controle de risco e a auditoria processos lentos e suscetíveis a erros.

No mercado financeiro, falhas operacionais não representam apenas perdas técnicas; elas geram impactos diretos em três frentes críticas: financeira (perdas por execuções incorretas ou atrasadas), regulatória (penalidades por descumprimento de normas de conformidade) e reputacional (perda de confiança dos investidores). Para mitigar esses riscos, o SentinelTrade foi concebido como uma plataforma de trade financeiro distribuída, altamente segura, escalável e tolerante a falhas.

## 📂 Estrutura do Repositório (`docs/`)

O projeto está organizado da seguinte forma:

```text
SentinelTrade/
├── .env.example                        # Exemplo de variáveis de ambiente
├── README.md                           # Documentação principal e visão geral
├── docs/                               # Documentação técnica e modelagem
│   ├── diagramas/                      # Arquivos visuais e modelagem comportamental
│   │   ├── 05-modelagem-comportamental.md
│   │   ├── Diagrama de Casos de Uso.png
│   │   ├── Diagrama de Máquina de Estados.png
│   │   └── Diagrama de Sequência.png
│   ├── 01-visão-e-atores.md            # Visão geral, objetivos e atores
│   ├── 02-requisitos-e-regras.md       # Requisitos funcionais, não funcionais e regras
│   ├── 03-criterios-e-rastreabilidade.md # Critérios de aceitação e matriz
│   ├── 04-arquitetura-e-dados.md         # Arquitetura distribuída e DER
│   └── requisitos.md                     # Documento unificado de requisitos
└── src/                        
    └── codigo.java             # Código-fonte do projeto - ainda não tem nada
```

### Pilares Arquiteturais e Funcionais
1. Segurança e Autenticação Rigorosa
A segurança é a camada inicial de defesa da plataforma. O acesso de investidores exige não apenas credenciais tradicionais com hash seguro, mas obrigatoriamente a verificação por Autenticação Multifator (MFA). Além disso, o controle de acesso por perfis garante que cada usuário interaja estritamente com seus próprios dados e limites financeiros, protegendo contra acessos não autorizados.

2. Ciclo de Vida e Validação de Ordens
Diferente de sistemas simplificados, o SentinelTrade implementa um rigoroso portão de risco pré-execução. Nenhuma ordem de compra ou venda gerada pelo investidor é enviada à Bolsa simulada sem que o sistema valide simultaneamente:

- A suficiência de saldo financeiro na conta;

- A posição real de ativos custodiados na carteira;

- Os limites de risco parametrizados para o perfil do investidor;

- A situação atual e a abertura do mercado para o ativo negociado.

Caso qualquer uma dessas validações falhe, a ordem é rejeitada preventivamente, gerando um registro imediato.

3. Resiliência, Mensageria e Prevenção de Duplicidade
Para garantir alta disponibilidade (alvo de 99,99%) e suportar picos de volatilidade sem queda de performance, a arquitetura utiliza processamento assíncrono baseado em filas de mensagens (mensageria).

- Idempotência: Para evitar que falhas de rede gerem o reenvio acidental de ordens duplicadas, o sistema utiliza identificadores únicos (UUID e chaves de idempotência) e cache distribuído, garantindo que a mesma ordem seja processada apenas uma vez, independentemente de quantas vezes o cliente tente reenviá-la em caso de timeout.

- Modo de Indisponibilidade Segura: Se houver instabilidade na comunicação com o provedor externo de cotações ou com a bolsa simulada, a plataforma entra em modo de proteção, enfileirando ou rejeitando operações com segurança para evitar exposições financeiras indesejadas.

4. Auditoria Imutável (Write Once, Read Many)
A conformidade regulatória exige que todas as transações, alterações de carteira e tentativas de login deixem uma trilha incorruptível. O SentinelTrade prevê o armazenamento de logs em estruturas imutáveis baseadas no conceito WORM (Write Once, Read Many). Isso impede que qualquer agente — interno ou externo — altere o histórico de operações, garantindo total transparência para auditorias regulatórias.

### Conclusão
Em suma, o SentinelTrade transforma a operação de trading da Orion Capital em um ambiente robusto, onde a velocidade das cotações em tempo quase real caminha lado a lado com a blindagem contra fraudes, duplicidades e falhas sistêmicas. O projeto entrega à banca avaliadora uma especificação completa de engenharia de software voltada para sistemas críticos onde a tolerância a falhas não é um diferencial, mas um requisito obrigatório.

---

## Requisitos 

### Engenharia de Requisitos 

- [X] Visão e objetivo do SentinelTrade
- [X] Identificação dos atores
- [X] Requisitos funcionais e não funcionais
- [X] Regras de negócio
- [X] Restrições técnicas
- [X] Critérios de aceitação
- [X] Matriz de rastreabilidade entre requisito, diagrama, implementação e teste

### Funcionais

- [ ] Cadastro e gestão de investidores
- [ ] Autenticação com MFA
- [ ] Consulta de carteira
- [ ] Consulta de cotações
- [ ] Envio de ordem de compra e venda
- [ ] Cancelamento de ordem
- [ ] Validação de saldo, posição e limites
- [ ] Acompanhamento do status da ordem
- [ ] Notificações; consulta de histórico
- [ ] Auditoria


### Segurança

- [ ] Senhas protegidas por hash
- [ ] MFA
- [ ] Controle de acesso por perfil
- [ ] Atributos privados
- [ ] Validação de entradas
- [ ] Tratamento seguro de exceções
- [ ] Prevenção de injeção
- [ ] Proteção contra reenvio/duplicidade de ordem
- [ ] Ausência de segredos no repositório

### Resiliência 

- [ ] Time-out de integração
- [ ] Retentativa controlada
- [ ] Idempotência
- [ ] Fila de mensagens para processamento assíncrono
- [ ] Modo de indisponibilidade segura
- [ ] Recuperação de falhas
- [ ] Consistência de dados

### Qualidade

- [ ] Auditabilidade
- [ ] Confidencialidade
- [ ] Integridade
- [ ] Disponibilidade
- [ ] Desempenho
- [ ] Rastreabilidade
- [ ] Manutenibilidade
