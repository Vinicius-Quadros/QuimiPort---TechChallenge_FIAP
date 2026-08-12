# Objetos de Valor — QuimiPort

Objetos de Valor (Value Objects) não possuem identidade própria: são
definidos e comparados exclusivamente pelo seu conteúdo/valor, e são
imutáveis.

## Classificação de Risco

- **Por que é um Objeto de Valor:** não tem ciclo de vida próprio nem
  identidade — é um atributo descritivo. Duas instâncias com a mesma
  categoria (ex.: "Inflamável") são consideradas iguais em qualquer contexto,
  seja no Produto Químico ou na Carga Química.
- **Composição:** categoria de risco (ex.: Inflamável, Corrosivo, Tóxico,
  Explosivo — representada como enum).
- **Usado por:** Produto Químico e Carga Química.

## Quantidade

- **Por que é um Objeto de Valor:** representa apenas um par
  valor/unidade, sem identidade própria. Duas quantidades com o mesmo valor
  e unidade são intercambiáveis.
- **Composição:** valor numérico, unidade de medida (ex.: kg, litros,
  toneladas).
- **Regra relacionada:** o valor numérico deve ser maior que zero.
- **Usado por:** Carga Química.
