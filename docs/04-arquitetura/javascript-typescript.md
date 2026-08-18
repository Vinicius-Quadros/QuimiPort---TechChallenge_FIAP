# Uso de JavaScript Avançado e TypeScript — QuimiPort

Este documento detalha, com pequenos exemplos conceituais, como os
conceitos de JavaScript Avançado e TypeScript serão aplicados na
construção do QuimiPort. Os exemplos representam a modelagem planejada,
não uma implementação completa (ver ADR 002 para a decisão e
justificativa).

## Enums para status e classificações

Usados para representar conjuntos fechados de valores, evitando strings
soltas espalhadas pelo código.

```typescript
// src/domain/enums/StatusCarga.ts
export enum StatusCarga {
  REGISTRADA = "REGISTRADA",
  EM_INSPECAO = "EM_INSPECAO",
  LIBERADA = "LIBERADA",
  BLOQUEADA = "BLOQUEADA",
  CANCELADA = "CANCELADA",
}

// src/domain/enums/ClasseRisco.ts
export enum ClasseRisco {
  INFLAMAVEL = "INFLAMAVEL",
  CORROSIVO = "CORROSIVO",
  TOXICO = "TOXICO",
  EXPLOSIVO = "EXPLOSIVO",
}
```

## Objetos de valor imutáveis (tipagem forte + classes)

Um Objeto de Valor nunca é alterado depois de criado — qualquer "mudança"
gera uma nova instância.

```typescript
// src/domain/value-objects/Quantidade.ts
export class Quantidade {
  private readonly valorNumerico: number;
  private readonly unidadeMedida: string;

  constructor(valorNumerico: number, unidadeMedida: string) {
    if (valorNumerico <= 0) {
      throw new Error("Quantidade deve ser maior que zero"); // RN07
    }
    this.valorNumerico = valorNumerico;
    this.unidadeMedida = unidadeMedida;
  }

  obterValorFormatado(): string {
    return `${this.valorNumerico} ${this.unidadeMedida}`;
  }

  converterUnidade(novaUnidade: string): Quantidade {
    // retorna uma NOVA instância, nunca altera a atual
    return new Quantidade(this.valorNumerico, novaUnidade);
  }
}
```

## Entidades e agregados (classes + encapsulamento)

A Carga Química, como agregado raiz, expõe apenas métodos que respeitam
suas invariantes — nunca permite alterar o status diretamente por fora.

```typescript
// src/domain/entities/CargaQuimica.ts
import { StatusCarga } from "../enums/StatusCarga";
import { Quantidade } from "../value-objects/Quantidade";
import { DomainError } from "../errors/DomainError";

export class CargaQuimica {
  private status: StatusCarga;
  private readonly quantidade: Quantidade;
  private readonly responsavelTecnicoId: string;

  constructor(quantidade: Quantidade, responsavelTecnicoId: string) {
    if (!responsavelTecnicoId) {
      throw new DomainError("Carga química exige responsável técnico"); // RN08
    }
    this.quantidade = quantidade;
    this.responsavelTecnicoId = responsavelTecnicoId;
    this.status = StatusCarga.REGISTRADA;
  }

  liberar(documentacaoCompleta: boolean): void {
    if (!documentacaoCompleta) {
      throw new DomainError("Não é possível liberar sem documentação obrigatória"); // RN09
    }
    if (this.status === StatusCarga.CANCELADA) {
      throw new DomainError("Carga cancelada não pode ser liberada"); // RN11
    }
    this.status = StatusCarga.LIBERADA;
  }

  cancelar(): void {
    const podeCancelar =
      this.status === StatusCarga.REGISTRADA ||
      this.status === StatusCarga.EM_INSPECAO;

    if (!podeCancelar) {
      throw new DomainError(
        "Só é possível cancelar cargas em 'Registrada' ou 'Em Inspeção'" // RN14
      );
    }
    this.status = StatusCarga.CANCELADA;
  }
}
```

## Interfaces para contratos entre camadas

A Application define o contrato; a Infrastructure implementa nas próximas
fases. Isso mantém o Domain e a Application livres de detalhes técnicos.

```typescript
// src/application/repositories/CargaQuimicaRepository.ts
import { CargaQuimica } from "../../domain/entities/CargaQuimica";

export interface CargaQuimicaRepository {
  salvar(carga: CargaQuimica): Promise<void>;
  buscarPorId(id: string): Promise<CargaQuimica | null>;
  listarPorStatus(status: string): Promise<CargaQuimica[]>;
}
```

## Generics para reaproveitar estruturas

Um exemplo de resultado genérico, reaproveitável por qualquer caso de uso,
para representar sucesso ou falha sem lançar exceção em todo lugar.

```typescript
// src/application/shared/Resultado.ts
export type Resultado<T> =
  | { sucesso: true; dado: T }
  | { sucesso: false; erro: string };

export function sucesso<T>(dado: T): Resultado<T> {
  return { sucesso: true, dado };
}

export function falha<T>(erro: string): Resultado<T> {
  return { sucesso: false, erro };
}
```

## Funções puras para validações

Validações que não dependem de estado externo ficam como funções puras,
fáceis de testar isoladamente.

```typescript
// src/domain/validations/validarQuantidade.ts
export function validarQuantidade(valor: number): boolean {
  return valor > 0; // RN07
}
```

## Async/await para integrações futuras

Ainda sem implementação real nesta fase, mas os casos de uso já são
desenhados prevendo chamadas assíncronas a repositórios.

```typescript
// src/application/use-cases/LiberarCargaQuimica.ts
import { CargaQuimicaRepository } from "../repositories/CargaQuimicaRepository";
import { Resultado, sucesso, falha } from "../shared/Resultado";

export class LiberarCargaQuimica {
  constructor(private readonly repositorio: CargaQuimicaRepository) {}

  async executar(
    cargaId: string,
    documentacaoCompleta: boolean
  ): Promise<Resultado<void>> {
    const carga = await this.repositorio.buscarPorId(cargaId);

    if (!carga) {
      return falha("Carga química não encontrada");
    }

    try {
      carga.liberar(documentacaoCompleta);
      await this.repositorio.salvar(carga);
      return sucesso(undefined);
    } catch (erro) {
      return falha((erro as Error).message);
    }
  }
}
```

## Tratamento de erros com classe de domínio própria

Erros de regra de negócio são diferenciados de erros técnicos genéricos.

```typescript
// src/domain/errors/DomainError.ts
export class DomainError extends Error {
  constructor(mensagem: string) {
    super(mensagem);
    this.name = "DomainError";
  }
}
```

## Módulos ES6+

Todo o código é organizado com `import`/`export` nomeados, um arquivo por
responsabilidade, conforme já refletido na estrutura de pastas em
`docs/04-arquitetura/arquitetura-proposta.md`.
