# Arquitetura Proposta — QuimiPort

## Abordagem escolhida

**Arquitetura em Camadas orientada a Domínio**, inspirada em Clean
Architecture: as regras de negócio ficam isoladas no centro do sistema,
sem depender de frameworks, banco de dados ou interface. As dependências
sempre apontam para dentro, em direção ao domínio.

```
Infrastructure → Application → Domain
```

## Camadas

### 1. Domain (Domínio)

- **Contém:** entidades (Produto Químico, Carga Química, Responsável
  Técnico, Documento da Carga, Inspeção, Área de Armazenamento), objetos de
  valor (Classificação de Risco, Quantidade), enums (Status da Carga) e as
  invariantes/regras de negócio que protegem o estado dos agregados (RN01 a
  RN12, RN14).
- **Não depende de nenhuma outra camada.** É o núcleo do sistema, isolado
  de frameworks, banco de dados ou qualquer detalhe de interface.
- **Responsabilidade:** garantir que um agregado nunca fique em estado
  inconsistente, independentemente de quem o está utilizando.

### 2. Application (Aplicação)

- **Contém:** os casos de uso (UC01 a UC11), cada um orquestrando chamadas
  ao domínio.
- **Depende do Domain**, mas não sabe como os dados são persistidos ou
  expostos — não conhece banco de dados nem API.
- **Responsabilidade:** aplicar regras que dependem de contexto externo a
  um único agregado (ex.: RN13 — impedir carga duplicada para o mesmo
  evento, o que exige consultar outras cargas já registradas).
- **Define interfaces (contratos)** de repositórios que a Infrastructure
  vai implementar nas próximas fases (ex.: `CargaQuimicaRepository`,
  `ProdutoQuimicoRepository`), sem conhecer a implementação concreta.

### 3. Infrastructure (Infraestrutura)

- **Nesta fase:** apenas um placeholder — não há banco de dados nem API
  implementados, conforme escopo do desafio.
- **Nas próximas fases, vai conter:** implementação dos repositórios
  definidos pela Application (ex.: com um banco de dados real),
  controllers/rotas de API, e integrações externas.
- **Depende do Application e do Domain**, nunca o contrário.

## Regra de dependência

As setas de dependência sempre apontam para dentro — a camada mais externa
(Infrastructure) depende das mais internas (Application e Domain), e nunca
o inverso. Isso é o que permite trocar banco de dados, framework web, ou
até o tipo de interface (CLI, API REST, GraphQL) no futuro sem alterar as
regras de negócio.

## Como o projeto pode evoluir

- **Backend:** a camada Infrastructure recebe um framework (ex.:
  Express, Fastify ou NestJS), implementa os repositórios com um banco de
  dados real (ex.: PostgreSQL) e expõe os casos de uso via API REST.
- **Frontend:** consome a API REST exposta pela Infrastructure; não tem
  acesso direto ao Domain.
- **Mobile:** segue a mesma lógica do frontend, consumindo a mesma API.
- **Microsserviços:** se necessário no futuro, o domínio já está isolado o
  suficiente para ser extraído em serviços menores (ex.: um serviço de
  Produtos Químicos separado de um serviço de Cargas Químicas), já que os
  agregados têm fronteiras bem definidas desde esta fase.

## Por que essa separação evita acoplamento excessivo

- O Domain não conhece a Application nem a Infrastructure — pode ser
  testado isoladamente, sem mocks de banco de dados ou API.
- A Application não conhece detalhes de implementação de persistência —
  depende apenas de interfaces (contratos), o que permite trocar a
  tecnologia de banco de dados sem alterar casos de uso.
- A Infrastructure pode mudar livremente (trocar de framework, de banco,
  de provedor de nuvem) sem impactar as regras de negócio.
