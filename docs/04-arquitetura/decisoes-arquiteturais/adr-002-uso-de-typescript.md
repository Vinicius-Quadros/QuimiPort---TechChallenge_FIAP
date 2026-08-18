# ADR 002 — Uso de TypeScript

**Status:** Aceito

## Contexto

O domínio do QuimiPort tem várias regras que dependem de tipos bem
definidos e consistentes — por exemplo, o Status da Carga só pode assumir
um conjunto fixo de valores (Registrada, Em Inspeção, Liberada, Bloqueada,
Cancelada), e a Classificação de Risco é um Objeto de Valor imutável que
não deve ser confundido com uma string qualquer. Além disso, a disciplina
"Arquitetura de Software com TypeScript" desta fase exige que essas
decisões sejam explicitadas.

## Decisão

Utilizar TypeScript em todo o código do projeto (Domain, Application e,
futuramente, Infrastructure), aproveitando:

- **Tipagem forte** para atributos de entidades e objetos de valor,
  evitando estados inválidos (ex.: quantidade como `number` positivo,
  nunca `any`).
- **Interfaces** para contratos entre camadas (ex.: `CargaQuimicaRepository`
  na Application, implementado futuramente pela Infrastructure).
- **Enums** para conjuntos fechados de valores, como `StatusCarga` e
  `ClasseRisco`.
- **Classes** para entidades e agregados, quando fizer sentido encapsular
  estado e comportamento juntos (ex.: `CargaQuimica` com seus métodos de
  transição de status).
- **Funções puras** para validações que não dependem de estado externo
  (ex.: validar se uma quantidade é maior que zero).
- **Generics**, quando aplicável, para tipar repositórios ou estruturas
  reutilizáveis (ex.: uma interface genérica de repositório).
- **Módulos ES6+** (`import`/`export`) para organizar o código por
  responsabilidade.
- **Async/await** já planejado para as integrações futuras com banco de
  dados e APIs externas na Infrastructure.
- **Tratamento de erros** com classes de erro específicas do domínio (ex.:
  `DomainError`), em vez de lançar exceções genéricas, deixando claro
  quando uma regra de negócio foi violada.

## Consequências

**Positivas:**
- Erros de tipo (ex.: passar uma string onde se espera uma Quantidade) são
  detectados em tempo de compilação, antes de chegar em produção.
- Os enums e interfaces documentam o próprio código, facilitando o
  entendimento do domínio por qualquer novo integrante do grupo.
- Facilita a evolução para um backend real, já que a maioria dos
  frameworks Node modernos (NestJS, tRPC, etc.) tem suporte nativo a
  TypeScript.

**Negativas / trade-offs:**
- Curva de aprendizado adicional para quem só tem experiência com
  JavaScript puro.
- Necessidade de configurar `tsconfig.json` e um passo de compilação/build
  antes de rodar o código.

## Alternativas consideradas

- **JavaScript puro (sem tipagem estática):** mais simples de começar, mas
  aumenta o risco de bugs relacionados a tipos incorretos, especialmente
  em um domínio com várias regras de validação como o QuimiPort. Rejeitada
  porque a disciplina de Arquitetura de Software com TypeScript é parte
  explícita do escopo desta fase.
