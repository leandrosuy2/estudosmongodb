# Roteiro teórico e glossário

[← Anterior: Índices extras](09-indices-extras-e-utilitarios.md) · [Índice](../README.md) · [Próximo: Erros comuns →](11-erros-comuns.md)

---

Use depois de praticar os arquivos [01](01-banco-e-collections.md) a [09](09-indices-extras-e-utilitarios.md).

## Fase 0 — Contexto

- NoSQL vs SQL; documento BSON; limite **16 MB** por documento; **ObjectId**.

## Fase 1 — Ambiente

- Instalação local ou **Atlas**; **mongosh** e **Compass**.

## Fase 2 — Modelagem

- Embedding vs referência; padrões (bucket, subset); [Data Modeling](https://www.mongodb.com/docs/manual/core/data-modeling-introduction/).

## Fase 3 — Avançado em consultas

- `$elemMatch`, `$regex` e performance; `$expr` comparando campos; **$jsonSchema** na collection.

## Fase 4 — Aggregation avançada

- `$facet`, `$bucket`, `$graphLookup`, `$merge`, `$out`.

## Fase 5 — Produção

- Replica set, read preferences; **transações**; **sharding** e escolha de shard key.

## Fase 6 — Segurança e performance

- RBAC, TLS; **profiler**; auditoria; **bulkWrite** no **mongosh** ou via ferramentas de dados; backups e restauração; monitorização no **Atlas**.

---

## Glossário rápido

| Termo | Significado |
|-------|-------------|
| `mongosh` | Cliente de linha de comando oficial do MongoDB (conecta ao servidor e executa comandos) |
| Database | “Banco” — namespace que agrupa collections |
| Collection | Conjunto de documentos (análogo conceitual a uma tabela) |
| Documento | Um registro em BSON/JSON |
| Consultas / updates / pipeline | Sintaxe do servidor: `find`, operadores `$…`, `update*`, `aggregate` (ver [referência](06-guia-referencia-rapida.md)) |

---

## Como estudar com este repositório

1. Siga a ordem da tabela no [README](../README.md): banco → collections → CRUD → pesquisas.
2. **Apague** com `dropDatabase` ou `drop` só em ambiente de teste.
3. Altere filtros e veja o resultado de `find` e de `explain`.
4. Faça os [exercícios práticos](12-exercicios-praticos.md) e consulte [erros comuns](11-erros-comuns.md) quando travar.
5. Aprofunde no [Manual](https://www.mongodb.com/docs/manual/) e [MongoDB University](https://learn.mongodb.com/).
