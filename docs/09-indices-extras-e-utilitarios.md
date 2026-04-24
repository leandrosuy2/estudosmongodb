# Índices extras, utilitários e exportação

[← Anterior: Agregação avançada](08-agregacao-avancada.md) · [Índice](../README.md) · [Próximo: Roteiro teórico →](10-roteiro-teorico-e-glossario.md)

---

```
use guia_consulta
```

## Índice parcial (só documentos que casam filtro)

```
db.pedidos_v2.createIndex(
  { cliente: 1 },
  { partialFilterExpression: { "meta.score": { $gte: 8 } } }
)
```

## Índice sparse (ignora docs sem o campo)

```
db.pedidos_v2.createIndex({ "campoOpcional": 1 }, { sparse: true })
```

## TTL — expiração automática por tempo

```
db.sessoes.insertOne({ criadoEm: new Date(), token: "abc" })
db.sessoes.createIndex({ criadoEm: 1 }, { expireAfterSeconds: 120 })
// Documentos removidos após ~120s a partir do instante em criadoEm (ajuste para testes).
```

## Collation no índice e na query

```
db.clientes_collation.drop()
db.clientes_collation.insertMany([
  { nome: "José" },
  { nome: "jose" },
  { nome: "JOSE" }
])
db.clientes_collation.createIndex(
  { nome: 1 },
  { collation: { locale: "pt", strength: 2 } }
)

db.clientes_collation.find({ nome: "jose" }).collation({ locale: "pt", strength: 2 })
```

## Texto completo (mínimo viável)

```
db.artigos.insertMany([
  { titulo: "MongoDB em produção", corpo: "Replica set e índices são importantes." },
  { titulo: "Introdução ao MongoDB", corpo: "Aprenda BSON e consultas básicas." }
])
db.artigos.createIndex({ titulo: "text", corpo: "text" })

db.artigos.find({ $text: { $search: "MongoDB replica" } })
```

> **Atenção:** só pode existir **um** índice de texto por collection. Se der erro de índice duplicado, use `db.artigos.dropIndexes()` com cuidado ou outra collection.

---

## `bulkWrite` — mistura de inserts, updates, deletes

```
use guia_consulta
db.fila_ops.drop()
db.fila_ops.bulkWrite([
  { insertOne: { document: { ref: 1, status: "novo" } } },
  { insertOne: { document: { ref: 2, status: "novo" } } },
  { updateOne: {
      filter: { ref: 1 },
      update: { $set: { status: "processado" } }
  }},
  { deleteOne: { filter: { ref: 2 } } }
])
db.fila_ops.find()
```

## `replaceOne` com `upsert`

```
db.cache_externo.replaceOne(
  { chave: "user:42" },
  { chave: "user:42", payload: { nome: "X" }, atualizadoEm: new Date() },
  { upsert: true }
)
```

## `distinct` com filtro

```
db.pedidos_v2.distinct("meta.origem", { valorTotal: { $gte: 150 } })
```

## `estimatedDocumentCount` vs `countDocuments`

```
db.pedidos_v2.estimatedDocumentCount()
db.pedidos_v2.countDocuments({ cliente: "Ana" })
```

## Validação de schema na collection (servidor)

```
db.createCollection("alunos_validados", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["nome", "nota"],
      properties: {
        nome: { bsonType: "string" },
        nota: { bsonType: "int", minimum: 0, maximum: 10 }
      }
    }
  },
  validationLevel: "strict",
  validationAction: "error"
})

db.alunos_validados.insertOne({ nome: "Eva", nota: 9 })
// db.alunos_validados.insertOne({ nome: "Eva" })  // falha: falta nota
```

## `ObjectId` e timestamps embutidos

```
let oid = new ObjectId()
oid.getTimestamp()
ObjectId.createFromTime(Math.floor(Date.now() / 1000))
```

## Exportar / importar (CLI)

Fora do `mongosh` (ajuste URI e caminho):

```bash
mongoexport --uri="mongodb://localhost:27017/guia_consulta" --collection=pedidos_v2 --out=pedidos_v2.json
mongoimport --uri="mongodb://localhost:27017/guia_consulta" --collection=pedidos_v2_backup --file=pedidos_v2.json
```
