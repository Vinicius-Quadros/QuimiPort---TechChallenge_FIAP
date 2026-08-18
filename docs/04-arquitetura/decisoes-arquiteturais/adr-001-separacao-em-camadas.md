# ADR 001 — Separação em Camadas (Domain, Application, Infrastructure)

**Status:** Aceito

## Contexto

O QuimiPort precisa concentrar regras de negócio críticas (ex.: uma carga
não pode ser liberada sem documentação, uma carga cancelada não pode ser
reaberta) de forma que essas regras não fiquem espalhadas ou duplicadas
pelo sistema. Além disso, o projeto vai evoluir ao longo de várias fases
do curso, ganhando banco de dados, API, frontend e possivelmente mobile —
sem que isso deva forçar reescrever as regras de negócio já validadas.

## Decisão

Adotar uma arquitetura em camadas orientada a domínio, com três camadas —
Domain, Application e Infrastructure — onde as dependências sempre apontam
para dentro (Infrastructure → Application → Domain). O Domain concentra as
entidades, objetos de valor e invariantes; a Application orquestra casos
de uso; a Infrastructure lida com detalhes técnicos (banco de dados, API),
implementados apenas nas próximas fases.

Ver detalhamento completo em `docs/04-arquitetura/arquitetura-proposta.md`.

## Consequências

**Positivas:**
- As regras de negócio ficam concentradas e protegidas no Domain,
  reduzindo o risco de duplicação ou inconsistência.
- É possível testar o Domain isoladamente, sem depender de banco de dados
  ou frameworks.
- A troca de tecnologia de infraestrutura (banco de dados, framework web)
  no futuro não deve impactar as regras de negócio já implementadas.

**Negativas / trade-offs:**
- Mais arquivos e mais indireção do que uma abordagem monolítica simples
  (ex.: tudo em um único arquivo de rotas com lógica embutida).
- Exige disciplina do grupo para não "vazar" lógica de negócio para a
  Application ou Infrastructure ao longo das próximas fases.

## Alternativas consideradas

- **Arquitetura monolítica simples (MVC tradicional, sem separação de
  domínio):** mais rápida de começar, mas tende a espalhar regras de
  negócio entre controllers e models, dificultando manutenção e testes à
  medida que o sistema cresce nas próximas fases. Rejeitada por não se
  alinhar ao objetivo do desafio de aplicar DDD.
