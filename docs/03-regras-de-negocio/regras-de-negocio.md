# Regras de Negócio — QuimiPort

Esta é a lista consolidada de regras de negócio do sistema, indicando onde
cada uma deve ficar concentrada na arquitetura da aplicação.

| # | Regra | Onde fica concentrada |
|---|---|---|
| RN01 | Um produto químico não pode ser cadastrado sem nome | Domínio — invariante da entidade **Produto Químico** |
| RN02 | Um produto químico não pode ser cadastrado sem Classificação de Risco | Domínio — invariante da entidade **Produto Químico** |
| RN03 | Um produto químico inativo não pode ser usado em novas cargas | Domínio — validada no agregado **Carga Química** ao vincular o produto (`linkProduct`) |
| RN04 | Uma carga química não pode ser registrada sem produto químico associado | Domínio — invariante do agregado **Carga Química** |
| RN05 | Uma carga química não pode ser registrada com produto químico inativo | Domínio — mesma validação da RN03, aplicada no momento do registro |
| RN06 | Uma carga química não pode ser registrada sem Classificação de Risco | Domínio — invariante do agregado **Carga Química** |
| RN07 | A quantidade da carga deve ser maior que zero | Domínio — invariante do Objeto de Valor **Quantidade** (validada na criação) |
| RN08 | Toda carga deve possuir um Responsável Técnico informado | Domínio — invariante do agregado **Carga Química** |
| RN09 | Uma carga química não pode ser liberada sem documentação obrigatória | Domínio — validada no método de liberação (`changeStatus`) do agregado **Carga Química** |
| RN10 | Uma carga bloqueada não pode entrar em movimentação | Domínio — invariante de transição de status do agregado **Carga Química** |
| RN11 | Uma carga cancelada não pode ser liberada | Domínio — invariante de transição de status do agregado **Carga Química** |
| RN12 | Uma carga em inspeção não pode ser finalizada sem antes ser liberada | Domínio — invariante de transição de status do agregado **Carga Química**, coordenada com a entidade **Inspeção** |
| RN13 | Uma carga química não pode ser registrada duas vezes para o mesmo evento de movimentação | Aplicação — caso de uso **Registrar Carga Química** |
| RN14 | Uma carga química só pode ser cancelada se estiver no status "Registrada" ou "Em Inspeção" (antes da liberação) | Domínio — invariante de transição de status do agregado **Carga Química** |

## Por que a maioria das regras fica no domínio

Quase todas as regras acima protegem o **estado interno de um agregado**
(Produto Químico ou Carga Química) — são invariantes que não podem ser
quebradas em nenhuma circunstância, independentemente de quem está
chamando. Por isso ficam concentradas dentro das próprias entidades e
agregados (camada de Domínio), e não espalhadas pelos casos de uso.

## Por que a RN13 é diferente

A RN13 não protege um invariante interno de um único agregado — ela depende
de **verificar dados externos ao agregado** (se já existe outra carga
registrada para o mesmo evento de movimentação, o que exige consultar um
repositório/coleção de cargas). Esse tipo de verificação, que envolve
orquestração e consulta a dados além do próprio agregado, é responsabilidade
da camada de **Aplicação** — no caso, do caso de uso **Registrar Carga
Química**.

## Resumo da divisão de responsabilidades

- **Camada de Domínio (Entidades/Agregados):** concentra as invariantes que
  garantem que um agregado nunca fique em estado inconsistente sozinho
  (RN01 a RN12, RN14).
- **Camada de Aplicação (Casos de Uso):** orquestra o fluxo, busca dados
  necessários, e aplica regras que dependem de contexto externo ao
  agregado, como duplicidade ou permissões (RN13).
