# Casos de Uso — QuimiPort

Cada caso de uso descreve objetivo, ator, entrada esperada, saída esperada,
regras de negócio aplicadas (ver `regras-de-negocio.md`) e possíveis erros
ou exceções.

## UC01 — Cadastrar Produto Químico

- **Objetivo:** registrar um novo produto químico no sistema.
- **Ator:** Administrador do Sistema.
- **Entrada:** nome, Classificação de Risco.
- **Saída:** produto químico cadastrado com status ativo.
- **Regras aplicadas:** RN01, RN02.
- **Exceções:** nome ausente → erro; Classificação de Risco ausente → erro.

## UC02 — Inativar Produto Químico

- **Objetivo:** marcar um produto químico como inativo, impedindo seu uso em
  novas cargas.
- **Ator:** Administrador do Sistema.
- **Entrada:** identificador do produto químico.
- **Saída:** produto com status inativo.
- **Regras aplicadas:** RN03 (efeito futuro — impede uso em novas cargas).
- **Exceções:** produto não encontrado → erro.

## UC03 — Registrar Carga Química

- **Objetivo:** criar uma nova carga química vinculada a um produto.
- **Ator:** Operador Portuário.
- **Entrada:** produto químico, quantidade, Classificação de Risco,
  Responsável Técnico.
- **Saída:** carga registrada com status inicial ("Registrada").
- **Regras aplicadas:** RN04, RN05, RN06, RN07, RN08, RN13.
- **Exceções:** produto inativo → erro; quantidade ≤ 0 → erro; Responsável
  Técnico ausente → erro; carga duplicada para o mesmo evento de
  movimentação → erro.

## UC04 — Validar Documentação da Carga

- **Objetivo:** anexar e validar os documentos obrigatórios de uma carga.
- **Ator:** Analista de Documentação.
- **Entrada:** identificador da carga, documento(s).
- **Saída:** documentação registrada/validada na carga.
- **Regras aplicadas:** relacionada à RN09 (pré-condição para liberação
  futura).
- **Exceções:** carga não encontrada → erro; documento inválido/tipo não
  reconhecido → erro.

## UC05 — Solicitar Inspeção

- **Objetivo:** registrar uma inspeção técnica sobre a carga.
- **Ator:** Analista de Qualidade.
- **Entrada:** identificador da carga, dados da inspeção.
- **Saída:** carga com status "Em Inspeção" e inspeção registrada.
- **Regras aplicadas:** RN12 (carga em inspeção não finaliza sem liberação).
- **Exceções:** carga bloqueada ou cancelada → erro.

## UC06 — Liberar Carga Química

- **Objetivo:** autorizar a carga para movimentação portuária.
- **Ator:** Analista de Qualidade / Gestor Operacional.
- **Entrada:** identificador da carga.
- **Saída:** carga com status "Liberada".
- **Regras aplicadas:** RN09, RN11.
- **Exceções:** documentação incompleta → erro; carga cancelada → erro.

## UC07 — Bloquear Carga Química

- **Objetivo:** impedir que a carga siga para movimentação.
- **Ator:** Analista de Qualidade / Gestor Operacional.
- **Entrada:** identificador da carga, motivo do bloqueio.
- **Saída:** carga com status "Bloqueada".
- **Regras aplicadas:** RN10 (efeito futuro — impede movimentação).
- **Exceções:** carga já cancelada → erro.

## UC08 — Atualizar Status da Carga

- **Objetivo:** refletir mudanças de status ao longo do fluxo operacional.
- **Ator:** Operador Portuário / Sistema (automatizações futuras).
- **Entrada:** identificador da carga, novo status.
- **Saída:** carga com status atualizado + histórico.
- **Regras aplicadas:** RN10, RN11, RN12, RN14 (invariantes de transição).
- **Exceções:** transição inválida (ex.: de "Cancelada" para "Liberada") →
  erro.

## UC09 — Cancelar Carga Química

- **Objetivo:** encerrar definitivamente uma carga.
- **Ator:** Gestor Operacional.
- **Entrada:** identificador da carga, motivo do cancelamento.
- **Saída:** carga com status "Cancelada".
- **Regras aplicadas:** RN14 — só é permitido cancelar cargas nos status
  "Registrada" ou "Em Inspeção" (antes da liberação).
- **Exceções:** carga já liberada, bloqueada ou já cancelada → erro (não
  permite cancelamento).

## UC10 — Consultar Cargas por Status

- **Objetivo:** listar cargas filtradas por status.
- **Ator:** Gestor Operacional / Operador Portuário.
- **Entrada:** status desejado (filtro opcional).
- **Saída:** lista de cargas.
- **Regras aplicadas:** nenhuma regra de negócio, apenas leitura.
- **Exceções:** nenhuma.

## UC11 — Consultar Histórico da Carga

- **Objetivo:** visualizar o histórico de mudanças de status de uma carga.
- **Ator:** Gestor Operacional / Analista de Qualidade.
- **Entrada:** identificador da carga.
- **Saída:** lista cronológica de mudanças de status.
- **Regras aplicadas:** nenhuma regra de negócio, apenas leitura.
- **Exceções:** carga não encontrada → erro.
