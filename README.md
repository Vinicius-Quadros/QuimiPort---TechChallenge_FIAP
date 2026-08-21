# QuimiPort

Sistema para gestão de cargas químicas em contexto portuário, inspirado em
operações logísticas do Porto de Santos.

## Sobre o projeto

O QuimiPort nasceu do Tech Challenge da Fase 1 da Pós Tech em Full Stack
Development (FIAP). Nesta primeira fase, o objetivo não é entregar uma
aplicação funcional, e sim a **proposta técnica e arquitetural** completa:
entendimento do domínio, modelagem com Domain-Driven Design, casos de uso,
regras de negócio, arquitetura em camadas, decisões arquiteturais, plano de
qualidade de software e diagramas — base que será evoluída para uma
aplicação full stack nas próximas fases do curso.

## Problema que resolve

Hoje o registro de cargas químicas costuma ser manual ou descentralizado,
dificultando a consulta de informações, o acompanhamento do status e a
validação de regras de segurança antes da liberação da carga para
movimentação portuária. O QuimiPort centraliza esse controle, permitindo
cadastrar produtos químicos, registrar cargas, classificar riscos, controlar
documentação obrigatória, acompanhar status e validar regras de segurança
antes da liberação.

## Usuários do sistema

- Operador Portuário
- Responsável Técnico
- Analista de Documentação
- Analista de Qualidade
- Gestor Operacional
- Administrador do Sistema

## Status do projeto

✅ Fase 1 concluída — proposta técnica, modelagem de domínio, arquitetura,
plano de qualidade e diagramas documentados.

## Arquitetura em resumo

Arquitetura em camadas orientada a domínio, inspirada em Clean
Architecture, com as dependências sempre apontando para dentro:

```
Infrastructure → Application → Domain
```

- **Domain** — entidades, objetos de valor e regras de negócio (RN01–RN14).
  Não depende de nenhuma outra camada.
- **Application** — os 11 casos de uso (UC01–UC11), que orquestram o
  domínio e definem os contratos (interfaces) de repositório.
- **Infrastructure** — nesta fase, apenas um placeholder; receberá banco de
  dados, API e integrações nas próximas fases.

Detalhes completos em [`docs/04-arquitetura/`](./docs/04-arquitetura).

## Documentação

A documentação completa está organizada em [`docs/`](./docs):

| Pasta | Conteúdo |
|---|---|
| [`docs/01-dominio/`](./docs/01-dominio) | Entendimento do domínio, linguagem ubíqua, entidades, objetos de valor e agregados |
| [`docs/02-casos-de-uso/`](./docs/02-casos-de-uso) | Os 11 casos de uso planejados (UC01–UC11), com ator, entrada, saída, regras e exceções |
| [`docs/03-regras-de-negocio/`](./docs/03-regras-de-negocio) | As 14 regras de negócio (RN01–RN14), com indicação de onde cada uma é concentrada na arquitetura |
| [`docs/04-arquitetura/`](./docs/04-arquitetura) | Arquitetura proposta em camadas, uso de JavaScript Avançado/TypeScript (com exemplos) e ADRs (decisões arquiteturais) |
| [`docs/05-qualidade/`](./docs/05-qualidade) | Plano de qualidade de software: regras e casos de uso críticos, tipos de teste, mocks e cenários planejados |
| [`docs/06-diagramas/`](./docs/06-diagramas) | Diagrama de domínio, fluxo de status da carga, diagrama de casos de uso e diagrama de arquitetura em camadas |

### Navegação sugerida

Para quem quer entender o projeto do zero, a leitura recomendada segue esta
ordem:

1. [`entendimento-do-dominio.md`](./docs/01-dominio/entendimento-do-dominio.md) — o problema e o contexto
2. [`linguagem-ubiqua.md`](./docs/01-dominio/linguagem-ubiqua.md) — o glossário de termos do domínio
3. [`entidades.md`](./docs/01-dominio/entidades.md), [`objetos-de-valor.md`](./docs/01-dominio/objetos-de-valor.md) e [`agregados.md`](./docs/01-dominio/agregados.md) — a modelagem DDD
4. [`diagrama-dominio.md`](./docs/06-diagramas/diagrama-dominio.md) — a visualização do modelo
5. [`casos-de-uso.md`](./docs/02-casos-de-uso/casos-de-uso.md) e [`diagrama-casos-de-uso.md`](./docs/06-diagramas/diagrama-casos-de-uso.md) — o que o sistema faz
6. [`regras-de-negocio.md`](./docs/03-regras-de-negocio/regras-de-negocio.md) e [`fluxo-status-carga.md`](./docs/06-diagramas/fluxo-status-carga.md) — as regras e o ciclo de vida da carga
7. [`arquitetura-proposta.md`](./docs/04-arquitetura/arquitetura-proposta.md) e [`diagrama-arquitetura-camadas.md`](./docs/06-diagramas/diagrama-arquitetura-camadas.md) — como o sistema será organizado
8. [`decisoes-arquiteturais/`](./docs/04-arquitetura/decisoes-arquiteturais) — os ADRs que justificam as escolhas
9. [`javascript-typescript.md`](./docs/04-arquitetura/javascript-typescript.md) — como JS Avançado e TypeScript serão aplicados
10. [`plano-de-qualidade.md`](./docs/05-qualidade/plano-de-qualidade.md) — como o projeto será testado

## Próximas fases

- Implementação da camada Infrastructure (banco de dados e API REST)
- Implementação dos casos de uso e testes unitários do Domain
- Desenvolvimento do frontend, consumindo a API
- Possível evolução para aplicativo mobile e/ou arquitetura de microsserviços

Ver estratégia detalhada em
[`adr-003-estrategia-de-evolucao.md`](./docs/04-arquitetura/decisoes-arquiteturais/adr-003-estrategia-de-evolucao.md).
