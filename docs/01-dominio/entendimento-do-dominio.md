# Entendimento do Domínio — QuimiPort

## 1. Qual problema o sistema pretende resolver?

Atualmente o registro de cargas químicas em contexto portuário é feito de forma
manual ou descentralizada, o que dificulta:

- a consulta de informações sobre produtos e cargas;
- o acompanhamento do status de cada carga;
- a validação de regras de segurança antes da liberação da carga para
  movimentação portuária.

O QuimiPort centraliza esse controle, permitindo cadastrar produtos químicos,
registrar cargas, classificar riscos, controlar documentação obrigatória,
acompanhar status e validar regras de segurança de forma consistente.

## 2. Quem são os usuários envolvidos?

- **Operador Portuário** — registra e movimenta as cargas.
- **Responsável Técnico** — validado tecnicamente em cada carga.
- **Analista de Documentação** — valida/anexa a documentação obrigatória.
- **Analista de Qualidade** — acompanha regras de segurança e qualidade.
- **Gestor Operacional** — acompanha o status geral das cargas.
- **Administrador do Sistema** — gerencia cadastros e configurações gerais.

## 3. Quais informações precisam ser controladas?

- Dados do Produto Químico (nome, Classificação de Risco, status ativo/inativo).
- Dados da Carga Química (produto associado, quantidade, Classificação de
  Risco, status).
- Documentação obrigatória da carga.
- Responsável Técnico vinculado à carga.
- Histórico de status e inspeções da carga.

## 4. Quais processos fazem parte da operação?

1. Cadastro de produto químico.
2. Registro de carga química.
3. Associação da carga a um produto químico.
4. Definição da Classificação de Risco.
5. Anexação da documentação obrigatória.
6. Definição do Responsável Técnico.
7. Solicitação e realização de inspeção.
8. Liberação ou bloqueio da carga.
9. Acompanhamento e atualização do status da carga.

## 5. Quais decisões precisam ser tomadas pelo sistema?

A principal decisão é **se uma carga pode ou não ser liberada** para
movimentação portuária. Essa decisão depende de validar, no mínimo:

- se o produto químico associado está ativo;
- se a Classificação de Risco está definida;
- se a documentação obrigatória está completa;
- se há um Responsável Técnico informado;
- se a quantidade é maior que zero;
- se a carga não está bloqueada ou cancelada.

## 6. Quais riscos ou restrições precisam ser considerados?

- Falta de documentação obrigatória antes da liberação.
- Uso de produto químico inativo em uma nova carga.
- Carga registrada sem Classificação de Risco.
- Quantidade inválida (zero ou negativa).
- Tentativa de movimentar carga bloqueada ou cancelada.
- Finalização de inspeção sem a devida liberação.

## 7. Quais partes do sistema poderão evoluir nas próximas fases?

- Persistência real dos dados (banco de dados).
- Implementação de backend (API) e frontend.
- Autenticação e controle de acesso por perfil de usuário.
- Notificações e integrações com outros sistemas do porto.
- Evolução para arquitetura de microsserviços, se necessário.
