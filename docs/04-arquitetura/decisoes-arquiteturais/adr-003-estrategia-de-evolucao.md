# ADR 003 — Estratégia de Evolução do Projeto

**Status:** Aceito

## Contexto

Esta é a Fase 1 de um projeto que será desenvolvido ao longo de várias
fases do curso. Nesta etapa, apenas o domínio, as regras de negócio e a
arquitetura são propostos — sem banco de dados, API, frontend ou mobile
implementados. É importante que as decisões desta fase não travem nem
compliquem as fases seguintes.

## Decisão

Adotar a seguinte estratégia de evolução, apoiada na separação em camadas
definida no ADR 001:

- **Backend:** a camada Infrastructure receberá um framework Node (ex.:
  Express, Fastify ou NestJS) e implementará os repositórios definidos
  como interfaces na Application, conectando a um banco de dados real
  (ex.: PostgreSQL). Os casos de uso já modelados nesta fase serão
  expostos como endpoints de uma API REST.
- **Frontend:** consumirá exclusivamente a API REST exposta pela
  Infrastructure, sem qualquer acesso direto às camadas de Domain ou
  Application.
- **Mobile:** seguirá a mesma lógica do frontend, consumindo a mesma API
  REST, possivelmente com endpoints adicionais otimizados para uso móvel
  se necessário.
- **Microsserviços:** caso o sistema cresça a ponto de justificar essa
  divisão, os agregados já modelados (Carga Química e Produto Químico)
  têm fronteiras bem definidas o suficiente para serem extraídos como
  serviços independentes, cada um com seu próprio Domain, Application e
  Infrastructure.
- **Evitar acoplamento excessivo:** todas as comunicações entre camadas
  acontecem por meio de interfaces (contratos) definidas na Application,
  nunca por acesso direto a detalhes de implementação de outra camada.
  Nenhuma regra de negócio deve ser implementada fora do Domain, mesmo
  que pareça mais rápido fazer isso diretamente em um controller ou
  componente de frontend futuramente.

## Consequências

**Positivas:**
- As próximas fases podem adicionar tecnologia (banco de dados,
  framework web, frontend) sem precisar redesenhar o domínio já validado
  nesta fase.
- A possibilidade de extrair microsserviços fica em aberto sem exigir
  retrabalho, já que os agregados já respeitam fronteiras de consistência
  bem definidas.

**Negativas / trade-offs:**
- Exige disciplina contínua do grupo nas próximas fases para não violar a
  regra de dependência (ex.: a tentação de fazer uma consulta direta ao
  banco de dados dentro de um caso de uso, pulando a interface de
  repositório).

## Alternativas consideradas

- **Definir logo de início um único caminho fechado (ex.: já decidir
  "vai ser NestJS + PostgreSQL + React")**: foi descartado nesta fase
  porque o desafio pede apenas a proposta arquitetural, não a escolha
  definitiva de stack. Manter a decisão de tecnologia específica em aberto
  para as próximas fases reduz o risco de comprometer o grupo com uma
  escolha prematura.
