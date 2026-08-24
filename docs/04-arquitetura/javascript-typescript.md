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
// src/domain/enums/CargoStatus.ts
export enum CargoStatus {
  REGISTERED = "REGISTRADA",
  UNDER_INSPECTION = "EM_INSPECAO",
  RELEASED = "LIBERADA",
  BLOCKED = "BLOQUEADA",
  CANCELLED = "CANCELADA",
}

// src/domain/enums/RiskClass.ts
export enum RiskClass {
  FLAMMABLE = "INFLAMAVEL",
  CORROSIVE = "CORROSIVO",
  TOXIC = "TOXICO",
  EXPLOSIVE = "EXPLOSIVO",
}
```

## Objetos de valor imutáveis (tipagem forte + classes)

Um Objeto de Valor nunca é alterado depois de criado — qualquer "mudança"
gera uma nova instância.

```typescript
// src/domain/value-objects/Quantity.ts
export class Quantity {
  private readonly numericValue: number;
  private readonly unit: string;

  constructor(numericValue: number, unit: string) {
    if (numericValue <= 0) {
      throw new Error("Quantidade deve ser maior que zero"); // RN07
    }
    this.numericValue = numericValue;
    this.unit = unit;
  }

  getFormattedValue(): string {
    return `${this.numericValue} ${this.unit}`;
  }

  convertUnit(newUnit: string): Quantity {
    // retorna uma NOVA instância, nunca altera a atual
    return new Quantity(this.numericValue, newUnit);
  }
}
```

## Entidades e agregados (classes + encapsulamento)

A Carga Química, como agregado raiz, expõe apenas métodos que respeitam
suas invariantes — nunca permite alterar o status diretamente por fora.

```typescript
// src/domain/entities/ChemicalCargo.ts
import { CargoStatus } from "../enums/CargoStatus";
import { Quantity } from "../value-objects/Quantity";
import { DomainError } from "../errors/DomainError";

export class ChemicalCargo {
  private status: CargoStatus;
  private readonly quantity: Quantity;
  private readonly technicalResponsibleId: string;

  constructor(quantity: Quantity, technicalResponsibleId: string) {
    if (!technicalResponsibleId) {
      throw new DomainError("Carga química exige responsável técnico"); // RN08
    }
    this.quantity = quantity;
    this.technicalResponsibleId = technicalResponsibleId;
    this.status = CargoStatus.REGISTERED;
  }

  release(documentationComplete: boolean): void {
    if (!documentationComplete) {
      throw new DomainError("Não é possível liberar sem documentação obrigatória"); // RN09
    }
    if (this.status === CargoStatus.CANCELLED) {
      throw new DomainError("Carga cancelada não pode ser liberada"); // RN11
    }
    this.status = CargoStatus.RELEASED;
  }

  cancel(): void {
    const canCancel =
      this.status === CargoStatus.REGISTERED ||
      this.status === CargoStatus.UNDER_INSPECTION;

    if (!canCancel) {
      throw new DomainError(
        "Só é possível cancelar cargas em 'Registrada' ou 'Em Inspeção'" // RN14
      );
    }
    this.status = CargoStatus.CANCELLED;
  }
}
```

## Interfaces para contratos entre camadas

A Application define o contrato; a Infrastructure implementa nas próximas
fases. Isso mantém o Domain e a Application livres de detalhes técnicos.

```typescript
// src/application/repositories/ChemicalCargoRepository.ts
import { ChemicalCargo } from "../../domain/entities/ChemicalCargo";

export interface ChemicalCargoRepository {
  save(cargo: ChemicalCargo): Promise<void>;
  findById(id: string): Promise<ChemicalCargo | null>;
  listByStatus(status: string): Promise<ChemicalCargo[]>;
}
```

## Generics para reaproveitar estruturas

Um exemplo de resultado genérico, reaproveitável por qualquer caso de uso,
para representar sucesso ou falha sem lançar exceção em todo lugar.

```typescript
// src/application/shared/Result.ts
export type Result<T> =
  | { success: true; data: T }
  | { success: false; error: string };

export function success<T>(data: T): Result<T> {
  return { success: true, data };
}

export function failure<T>(error: string): Result<T> {
  return { success: false, error };
}
```

## Funções puras para validações

Validações que não dependem de estado externo ficam como funções puras,
fáceis de testar isoladamente.

```typescript
// src/domain/validations/validateQuantity.ts
export function validateQuantity(value: number): boolean {
  return value > 0; // RN07
}
```

## Async/await para integrações futuras

Ainda sem implementação real nesta fase, mas os casos de uso já são
desenhados prevendo chamadas assíncronas a repositórios.

```typescript
// src/application/use-cases/ReleaseChemicalCargo.ts
import { ChemicalCargoRepository } from "../repositories/ChemicalCargoRepository";
import { Result, success, failure } from "../shared/Result";

export class ReleaseChemicalCargo {
  constructor(private readonly repository: ChemicalCargoRepository) {}

  async execute(
    cargoId: string,
    documentationComplete: boolean
  ): Promise<Result<void>> {
    const cargo = await this.repository.findById(cargoId);

    if (!cargo) {
      return failure("Carga química não encontrada");
    }

    try {
      cargo.release(documentationComplete);
      await this.repository.save(cargo);
      return success(undefined);
    } catch (error) {
      return failure((error as Error).message);
    }
  }
}
```

## Tratamento de erros com classe de domínio própria

Erros de regra de negócio são diferenciados de erros técnicos genéricos.

```typescript
// src/domain/errors/DomainError.ts
export class DomainError extends Error {
  constructor(message: string) {
    super(message);
    this.name = "DomainError";
  }
}
```

## Módulos ES6+

Todo o código é organizado com `import`/`export` nomeados, um arquivo por
responsabilidade, conforme já refletido na estrutura de pastas em
`docs/04-arquitetura/arquitetura-proposta.md`.
