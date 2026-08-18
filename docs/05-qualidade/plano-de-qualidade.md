# Plano de Qualidade de Software — QuimiPort

## Objetivo

Definir como o QuimiPort será testado nas próximas fases, priorizando as
regras de negócio e os casos de uso mais críticos identificados nesta
fase de modelagem.

## Regras de negócio que precisam ser testadas

Todas as regras documentadas em `docs/03-regras-de-negocio/regras-de-negocio.md`
devem ter cobertura de teste, com prioridade especial para as que protegem
transições de status (mais fáceis de quebrar por engano):

| Regra | Prioridade |
|---|---|
| RN01 — Produto sem nome não pode ser cadastrado | Alta |
| RN02 — Produto sem Classificação de Risco não pode ser cadastrado | Alta |
| RN03 — Produto inativo não pode ser usado em novas cargas | Alta |
| RN04–RN06 — Carga não pode ser registrada sem produto, produto ativo ou Classificação de Risco | Alta |
| RN07 — Quantidade deve ser maior que zero | Alta |
| RN08 — Carga deve ter Responsável Técnico informado | Alta |
| RN09 — Carga não pode ser liberada sem documentação obrigatória | Crítica |
| RN10 — Carga bloqueada não pode entrar em movimentação | Crítica |
| RN11 — Carga cancelada não pode ser liberada | Crítica |
| RN12 — Carga em inspeção não finaliza sem liberação | Alta |
| RN13 — Carga não pode ser registrada duas vezes para o mesmo evento | Média |
| RN14 — Cancelamento só permitido em "Registrada" ou "Em Inspeção" | Crítica |

## Casos de uso mais críticos

Priorizados pelo impacto de segurança/operacional caso falhem:

1. **UC06 — Liberar Carga Química** — crítico, pois libera a carga para
   movimentação física real; falha aqui tem impacto de segurança direto.
2. **UC07 — Bloquear Carga Química** — crítico pela mesma razão inversa:
   impedir movimentação quando necessário.
3. **UC03 — Registrar Carga Química** — crítico por ser o ponto de entrada
   de todas as validações iniciais (produto, quantidade, responsável).
4. **UC09 — Cancelar Carga Química** — importante pela regra de restrição
   de status (RN14).
5. **UC08 — Atualizar Status da Carga** — importante por concentrar todas
   as transições possíveis.

Casos de uso de consulta (UC10, UC11) têm prioridade menor, por serem
apenas leitura, sem regras de negócio associadas.

## Tipos de teste

- **Testes unitários:** cobrem entidades, objetos de valor e regras de
  negócio isoladamente, sem depender de banco de dados ou API. É o tipo de
  teste com maior prioridade nesta fase, já que o Domain está pronto e o
  restante ainda não foi implementado.
- **Testes de integração:** planejados para as próximas fases, quando a
  Infrastructure existir (banco de dados, API). Vão validar se os casos de
  uso funcionam corretamente de ponta a ponta, incluindo a persistência
  real.
- **Testes end-to-end (E2E):** previstos para fases futuras, após o
  frontend existir, cobrindo os fluxos principais do ponto de vista do
  usuário.

## Estratégia de testes unitários

- Cada entidade e objeto de valor terá um arquivo de teste correspondente
  (ex.: `CargaQuimica.spec.ts`, `Quantidade.spec.ts`).
- Os testes vão validar tanto o "caminho feliz" (transições e criações
  válidas) quanto os casos de erro (violação de cada regra de negócio,
  esperando que a exceção `DomainError` seja lançada).
- Ferramenta planejada: **Jest** ou **Vitest** (a decidir na fase de
  implementação, ambos compatíveis com TypeScript).

## Estratégia de testes de integração (futuro)

- Vão validar os casos de uso completos, incluindo a interação com os
  repositórios (`CargaQuimicaRepository`, `ProdutoQuimicoRepository`).
- Nas próximas fases, será usado um banco de dados de teste isolado (ou
  um banco em memória) para não afetar dados reais.
- Cada caso de uso crítico (UC03, UC06, UC07, UC09) terá pelo menos um
  teste de integração cobrindo o fluxo completo, do input até a
  persistência.

## Como validar os fluxos principais

- O fluxo de transição de status (`docs/06-diagramas/fluxo-status-carga.md`)
  será usado como roteiro de teste: cada transição permitida e cada
  transição proibida do diagrama deve virar um caso de teste.
- Exemplo de cobertura mínima esperada a partir do diagrama:
  - Registrada → Liberada (com documentação completa) — deve funcionar.
  - Registrada → Liberada (sem documentação) — deve falhar (RN09).
  - Liberada → Cancelada — deve falhar (RN14).
  - Cancelada → qualquer estado — deve falhar (RN11).

## Organização de mocks e dados simulados

- Repositórios serão mockados nos testes unitários de caso de uso,
  implementando a interface definida na Application (ex.: um
  `CargaQuimicaRepositoryFake` em memória, usado só em testes).
- Dados de teste (produtos, cargas, responsáveis técnicos de exemplo)
  ficarão centralizados em fábricas de teste (ex.:
  `tests/factories/cargaQuimicaFactory.ts`), evitando duplicar a criação
  de objetos válidos em cada arquivo de teste.
- Nenhum teste unitário deve depender de um banco de dados real ou de
  chamadas de rede.

## Cenários de teste planejados (exemplos)

- Não permitir cadastro de produto químico sem Classificação de Risco
  (RN02).
- Não permitir registro de carga com produto químico inativo (RN03, RN05).
- Não permitir liberação de carga sem documentação obrigatória (RN09).
- Não permitir movimentação de carga bloqueada (RN10).
- Permitir liberação de carga com documentação válida (caminho feliz da
  RN09).
- Validar quantidade maior que zero, rejeitando zero e negativos (RN07).
- Validar todas as transições de status do diagrama de fluxo, permitidas e
  proibidas (RN09 a RN12, RN14).
