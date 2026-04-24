# Agregação — exemplos avançados

[← Anterior: Sandbox](07-sandbox-operadores-avancados.md) · [Índice](../README.md) · [Próximo: Índices extras →](09-indices-extras-e-utilitarios.md)

---

Assumindo `use guia_consulta` e a collection `pedidos_v2` já populada (veja [Sandbox](07-sandbox-operadores-avancados.md)).

## `$unwind` — um documento por item

```
db.pedidos_v2.aggregate([
  { $unwind: "$itens" },
  { $project: {
      cliente: 1,
      sku: "$itens.sku",
      subtotal: { $multiply: ["$itens.preco", "$itens.qtd"] }
  }}
])
```

## `$addFields` e `$cond`

```
db.pedidos_v2.aggregate([
  { $addFields: {
      liquido: { $subtract: ["$valorTotal", "$desconto"] },
      faixaScore: {
        $cond: [{ $gte: ["$meta.score", 8] }, "alto", "normal"]
      }
  }},
  { $match: { liquido: { $gt: 0 } } }
])
```

## `$sortByCount` — contar tags

```
db.pedidos_v2.aggregate([
  { $unwind: "$tags" },
  { $sortByCount: "$tags" }
])
```

## `$facet` — vários resumos numa só ida

```
db.pedidos_v2.aggregate([
  { $facet: {
      porOrigem: [
        { $group: { _id: "$meta.origem", total: { $sum: "$valorTotal" } } }
      ],
      topClientes: [
        { $sort: { valorTotal: -1 } },
        { $limit: 2 },
        { $project: { _id: 0, cliente: 1, valorTotal: 1 } }
      ]
  }}
])
```

## Datas: `$dateToString`, agrupar por dia

```
db.eventos.insertMany([
  { tipo: "login", quando: ISODate("2025-06-01T08:00:00Z"), userId: "u1" },
  { tipo: "compra", quando: ISODate("2025-06-01T22:15:00Z"), userId: "u1" },
  { tipo: "login", quando: ISODate("2025-06-02T09:00:00Z"), userId: "u2" }
])

db.eventos.aggregate([
  { $group: {
      _id: { $dateToString: { format: "%Y-%m-%d", date: "$quando", timezone: "America/Sao_Paulo" } },
      qtd: { $sum: 1 }
  }},
  { $sort: { _id: 1 } }
])
```

## `$lookup` com pipeline (join condicional)

```
db.regras_desconto.insertMany([
  { tag: "atacado", percentual: 10 },
  { tag: "varejo", percentual: 5 }
])

db.pedidos_v2.aggregate([
  { $unwind: "$tags" },
  { $lookup: {
      from: "regras_desconto",
      let: { t: "$tags" },
      pipeline: [
        { $match: { $expr: { $eq: ["$tag", "$$t"] } } },
        { $project: { _id: 0, percentual: 1 } }
      ],
      as: "regra"
  }},
  { $unwind: { path: "$regra", preserveNullAndEmptyArrays: true } },
  { $project: { cliente: 1, tag: "$tags", pct: "$regra.percentual" } }
])
```
