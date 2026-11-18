# Documentação de domínio

Como as skills de engenharia devem consumir a documentação de domínio deste repositório ao explorar o código.

## Antes de explorar, leia

- **`GLOSSARY.md`** na raiz do repositório, ou
- **`GLOSSARY-MAP.md`** na raiz, se existir: ele aponta para um `GLOSSARY.md` por contexto. Leia cada um relevante para o assunto.
- **`docs/adr/`**: leia as ADRs que tocam a área em que você vai trabalhar. Em repositórios multi-contexto, verifique também `src/<context>/docs/adr/` para decisões específicas de contexto.

Se algum desses arquivos não existir, **siga em silêncio**. Não sinalize a ausência nem sugira criá-los de antemão. A skill `/domain-modeling` (alcançada por `/grill-with-docs` e `/improve-codebase-architecture`) os cria sob demanda quando termos ou decisões realmente forem resolvidos.

## File structure

Repositório single-context (a maioria dos repositórios):

```
/
├── GLOSSARY.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

Repositório multi-contexto (presença de `GLOSSARY-MAP.md` na raiz):

```
/
├── GLOSSARY-MAP.md
├── docs/adr/                          ← decisões do sistema inteiro
└── src/
    ├── ordering/
    │   ├── GLOSSARY.md
    │   └── docs/adr/                  ← decisões específicas do contexto
    └── billing/
        ├── GLOSSARY.md
        └── docs/adr/
```

## Use o vocabulário do glossário

Quando sua saída nomear um conceito de domínio (no título de uma Issue, numa proposta de refatoração, numa hipótese, no nome de um teste), use o termo como definido em `GLOSSARY.md`. Não migre para sinônimos que o glossário evita explicitamente.

Se o conceito de que você precisa ainda não está no glossário, isso é um sinal: ou você está inventando linguagem que o projeto não usa (reconsidere) ou existe uma lacuna real (anote para `/domain-modeling`).

## Sinalize conflitos com ADRs

Se sua saída contradiz uma ADR existente, mostre isso explicitamente em vez de sobrepor em silêncio:

> _Contradiz a ADR-0007 (event-sourced orders), mas vale reabrir porque…_
