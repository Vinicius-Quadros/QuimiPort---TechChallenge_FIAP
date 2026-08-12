# Agregados — QuimiPort

## Agregado principal: Carga Química

A **Carga Química** é o agregado raiz do sistema. Ela concentra e protege as
regras de negócio mais críticas do domínio, garantindo que nenhuma parte do
sistema consiga colocar uma carga em um estado inconsistente sem passar
pelas invariantes definidas dentro do próprio agregado.

### O que o agregado concentra

- **Produto químico associado** — referência ao Produto Químico (agregado
  separado); não há posse, apenas vínculo.
- **Quantidade** — objeto de valor, deve ser sempre maior que zero.
- **Documentação** — um ou mais Documentos da Carga, necessários para
  liberação.
- **Responsável Técnico** — referência obrigatória.
- **Status** — estado atual da carga (Registrada, Em Inspeção, Liberada,
  Bloqueada, Cancelada).
- **Inspeção** — verificação técnica vinculada à carga.
- **Liberação/Bloqueio** — transições de status controladas pelas regras do
  agregado.
- **Histórico** — registro das mudanças de status ao longo do tempo.

### Por que este agregado foi escolhido

Todas as regras de negócio mais sensíveis do domínio giram em torno do
**estado de uma única Carga Química**:

- não pode ser registrada sem produto ativo, sem Classificação de Risco ou
  sem Responsável Técnico;
- não pode ser liberada sem documentação obrigatória completa;
- não pode ser movimentada se estiver bloqueada;
- não pode ser liberada se estiver cancelada;
- não pode ser finalizada em inspeção sem antes ser liberada.

Ao concentrar essas regras dentro do agregado Carga Química, garantimos que
qualquer alteração de estado (liberar, bloquear, cancelar, atualizar status)
passe obrigatoriamente pelas invariantes do agregado — nenhuma outra parte
do sistema pode alterar o status da carga diretamente sem respeitar essas
regras.

### Agregado secundário: Produto Químico

O **Produto Químico** foi mantido como um agregado separado e independente,
pois seu ciclo de vida (cadastro, ativação, inativação) não depende do
ciclo de vida de nenhuma carga específica. Uma Carga Química apenas
referencia um Produto Químico existente; ela não é responsável por
gerenciar o estado do produto.

### Entidades que ficam fora dos dois agregados

- **Área de Armazenamento** pode evoluir como agregado próprio em fases
  futuras, caso passe a ter regras de capacidade/ocupação. Nesta fase, é
  tratada como uma referência simples associada à carga.
