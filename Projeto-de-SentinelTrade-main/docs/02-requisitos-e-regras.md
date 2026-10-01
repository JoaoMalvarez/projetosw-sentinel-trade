# 2. Requisitos, Regras de Negócio e Restrições Técnicas

## 2.1. Requisitos Funcionais (RF)
* **RF01 - Autenticação Multifator (MFA):** O sistema deve autenticar os investidores exigindo credenciais de acesso padrão (e-mail e senha com hash) combinadas obrigatoriamente a um código gerado por MFA (ex: TOTP).
* **RF02 - Gestão de Investidores, Contas e Carteiras:** O sistema deve manter cadastros completos de investidores, contas correntes associadas, saldos financeiros (disponíveis e bloqueados) e posições consolidadas em carteira de ativos.
* **RF03 - Consulta de Cotações em Tempo Quase Real:** O sistema deve receber e disponibilizar para os investidores as cotações atualizadas de ações, ETFs e fundos imobiliários provenientes do provedor externo.
* **RF04 - Gestão do Ciclo de Ordens:** O sistema deve permitir que o investidor submeta ordens de compra e venda (informando ativo, quantidade e preço limite), além de possibilitar o cancelamento e a consulta ao histórico operacional.
* **RF05 - Validação Prévia de Risco:** O sistema deve executar obrigatoriamente um portão de validação pré-execução, checando saldo financeiro, posição em carteira, limites de risco parametrizados e status de abertura do mercado antes de transmitir a ordem.
* **RF06 - Integração com a Bolsa Simulada:** O sistema deve encaminhar ordens aprovadas no portão de risco para a bolsa simulada e monitorar as respostas de retorno.
* **RF07 - Notificações Operacionais:** O sistema deve notificar o investidor em tempo real sobre mudanças críticas no status de suas ordens (ex: rejeitada por risco, executada pela bolsa, cancelada).
* **RF08 - Trilha de Auditoria Imutável:** O sistema deve registrar transações financeiras, alterações de carteira e eventos de login em logs de auditoria protegidos contra modificações.

## 2.2. Requisitos Não Funcionais (RNF)
* **RNF01 - Alta Disponibilidade:** A plataforma deve operar sob arquitetura distribuída e tolerante a falhas, mirando uma disponibilidade operacional de 99,99% ao ano.
* **RNF02 - Consistência e Idempotência:** O sistema deve garantir a prevenção de duplicidade de ordens através de chaves de idempotência, assegurando resiliência sob quedas repentinas de conexão ou reenvios de pacotes.
* **RNF03 - Segurança e Criptografia:** Todos os dados sensíveis, credenciais e comunicações devem ser protegidos por criptografia robusta (TLS 1.3 em trânsito e AES-256 em repouso).
* **RNF04 - Desempenho e Baixa Latência:** As etapas de validação pré-execução e roteamento interno devem ocorrer em milissegundos para evitar perdas financeiras por defasagem temporal de preços.

## 2.3. Regras de Negócio (RN)
* **RN01 - Bloqueio por Insuficiência:** Nenhuma ordem de compra pode ser enviada à bolsa caso o saldo disponível seja inferior ao valor total da operação; da mesma forma, ordens de venda são bloqueadas se a quantidade de ativos na carteira for menor que a solicitada.
* **RN02 - Imutabilidade de Auditoria (WORM):** Os registros gerados na trilha de auditoria devem seguir o modelo *Write Once, Read Many* (WORM), impedindo exclusão ou edição por qualquer usuário do sistema, incluindo administradores.
* **RN03 - Janela de Operação de Mercado:** O envio de ordens só é permitido para ativos cujos mercados estejam abertos e ativos no provedor de cotações no momento da submissão.

## 2.4. Restrições Técnicas (RT)
* **RT01 - Arquitetura de Microsserviços e Mensageria:** O sistema deve ser estruturado em microsserviços desacoplados, utilizando barramento de mensageria assíncrona para entrega garantida de eventos.
* **RT02 - Política de Zero Segredos no Repositório:** É estritamente proibido o armazenamento de chaves de API, senhas, tokens JWT ou credenciais de banco de dados no código-fonte ou no histórico do repositório Git.