# Entidades — QuimiPort

## Produto Químico

- **Responsabilidade:** representar uma substância química cadastrável no
  sistema, que pode ser associada a cargas.
- **Principais atributos:** nome, Classificação de Risco, status
  (ativo/inativo).
- **Regras relacionadas:**
  - Não pode ser cadastrado sem nome.
  - Não pode ser cadastrado sem Classificação de Risco.
  - Se estiver inativo, não pode ser usado em novas cargas.
- **Relacionamento com outras entidades:** um Produto Químico pode estar
  associado a N Cargas Químicas.

## Carga Química (agregado raiz)

- **Responsabilidade:** concentrar e proteger todas as regras relacionadas à
  operação de uma carga específica, do registro até a liberação/bloqueio.
- **Principais atributos:** produto associado, quantidade, Classificação de
  Risco, status, Responsável Técnico, documentos, histórico de status.
- **Regras relacionadas:**
  - Não pode ser registrada sem produto químico associado.
  - Não pode ser registrada com produto químico inativo.
  - Não pode ser registrada sem Classificação de Risco.
  - Não pode ser liberada sem documentação obrigatória.
  - Se bloqueada, não pode entrar em movimentação.
  - Se cancelada, não pode ser liberada.
  - Se em inspeção, não pode ser finalizada sem antes ser liberada.
  - A quantidade deve ser maior que zero.
  - Deve possuir um Responsável Técnico informado.
- **Relacionamento com outras entidades:** referencia um Produto Químico;
  contém um ou mais Documentos da Carga; pode conter uma ou mais Inspeções;
  referencia um Responsável Técnico; pode estar associada a uma Área de
  Armazenamento.

## Responsável Técnico

- **Responsabilidade:** representar a pessoa habilitada tecnicamente para
  validar a operação de uma carga.
- **Principais atributos:** nome, registro profissional.
- **Regras relacionadas:** toda Carga Química deve possuir um Responsável
  Técnico informado.
- **Relacionamento com outras entidades:** um Responsável Técnico pode estar
  vinculado a N Cargas Químicas.

## Documento da Carga

- **Responsabilidade:** representar um item de documentação obrigatória
  anexado a uma carga.
- **Principais atributos:** tipo do documento, status (pendente/validado),
  data de anexação.
- **Regras relacionadas:** a Carga Química não pode ser liberada sem que a
  documentação obrigatória esteja completa e validada.
- **Relacionamento com outras entidades:** pertence a uma única Carga
  Química.

## Inspeção

- **Responsabilidade:** registrar a verificação técnica realizada sobre uma
  carga antes da liberação.
- **Principais atributos:** data, resultado, responsável pela inspeção.
- **Regras relacionadas:** uma carga em inspeção não pode ser finalizada sem
  antes ser liberada.
- **Relacionamento com outras entidades:** pertence a uma única Carga
  Química.

## Área de Armazenamento

- **Responsabilidade:** representar o local físico onde a carga fica
  armazenada antes ou durante a movimentação portuária.
- **Principais atributos:** identificação, capacidade.
- **Regras relacionadas:** nenhuma regra explícita nesta fase; pode receber
  regras de capacidade/ocupação em fases futuras.
- **Relacionamento com outras entidades:** pode estar associada a N Cargas
  Químicas.
