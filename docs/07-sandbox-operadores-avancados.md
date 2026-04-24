# Sandbox `guia_consulta` — operadores avançados

[← Anterior: Referência rápida](06-guia-referencia-rapida.md) · [Índice](../README.md) · [Próximo: Agregação avançada →](08-agregacao-avancada.md)

---

Ambiente separado para não misturar com `clientes` (índice único em `email`) nem duplicar `pedidos`. Pode resetar quando quiser:

```
use guia_consulta
db.dropDatabase()   // opcional: zera o banco inteiro
use guia_consulta
```

## Dados ricos para consultas avançadas

```
db.pedidos_v2.insertMany([
  {
    cliente: "Ana",
    valorTotal: 300,
    desconto: 50,
    itens: [
      { sku: "A1", preco: 100, qtd: 2 },
      { sku: "B2", preco: 50, qtd: 2 }
    ],
    tags: ["atacado", "urgente"],
    meta: { origem: "web", score: 8 }
  },
  {
    cliente: "Bruno",
    valorTotal: 120,
    desconto: 0,
    itens: [
      { sku: "C3", preco: 120, qtd: 1 }
    ],
    tags: ["varejo"],
    meta: { origem: "app", score: 5 }
  },
  {
    cliente: "Carla",
    valorTotal: 200,
    desconto: 200,
    itens: [
      { sku: "D4", preco: 200, qtd: 1 }
    ],
    tags: ["atacado", "promo"],
    meta: { origem: "web", score: 9 }
  }
])
```

## `$elemMatch` (array de documentos)

Pedidos em que **algum item** tem `preco` menor que 110 **e** `qtd` >= 2:

```
db.pedidos_v2.find({
  itens: {
    $elemMatch: { preco: { $lt: 110 }, qtd: { $gte: 2 } }
  }
})
```

## `$nor` e `$not`

```
// Nenhuma das condições pode ser verdadeira
db.pedidos_v2.find({
  $nor: [{ "meta.origem": "app" }, { valorTotal: { $gt: 250 } }]
})

// negação de uma condição
db.pedidos_v2.find({ "meta.score": { $not: { $gte: 8 } } })
```

## `$expr` — comparar campos do mesmo documento

`valorTotal` igual a `desconto`:

```
db.pedidos_v2.find({ $expr: { $eq: ["$valorTotal", "$desconto"] } })
```

Líquido maior que zero: `valorTotal - desconto > 0`:

```
db.pedidos_v2.find({
  $expr: { $gt: [{ $subtract: ["$valorTotal", "$desconto"] }, 0] }
})
```

## Atualizar arrays: `$push`, `$addToSet`, `$pull`, `$pop`

```
db.pedidos_v2.updateOne(
  { cliente: "Ana" },
  { $push: { tags: "frete_gratis" } }
)

db.pedidos_v2.updateOne(
  { cliente: "Ana" },
  { $addToSet: { tags: "atacado" } }
)

db.pedidos_v2.updateOne(
  { cliente: "Ana" },
  { $pull: { tags: "urgente" } }
)

db.pedidos_v2.updateOne(
  { cliente: "Bruno" },
  { $pop: { itens: 1 } }
)
```

## Posicional `$` — atualizar primeiro item que casa o filtro

```
db.pedidos_v2.updateOne(
  { cliente: "Ana", "itens.sku": "A1" },
  { $set: { "itens.$.preco": 95 } }
)
```

## `arrayFilters` — vários elementos do array

```
db.pedidos_v2.updateOne(
  { cliente: "Ana" },
  { $set: { "itens.$[elem].qtd": 3 } },
  { arrayFilters: [{ "elem.sku": "A1" }] }
)
```

## Upsert — atualizar ou criar se não existir

```
db.config_loja.updateOne(
  { chave: "taxa_servico" },
  { $set: { valor: 0.05 }, $setOnInsert: { criadoEm: new Date() } },
  { upsert: true }
)

db.config_loja.find({ chave: "taxa_servico" })
```

## `findOneAndUpdate` — atualizar e ver documento antes/depois

```
db.contador.insertOne({ _id: "pedidos", seq: 10 })

db.contador.findOneAndUpdate(
  { _id: "pedidos" },
  { $inc: { seq: 1 } },
  { returnDocument: "after" }
)
```

No `findOneAndUpdate`, use nas opções `returnDocument: "before"` ou `"after"` para devolver o documento antes ou depois da alteração.
