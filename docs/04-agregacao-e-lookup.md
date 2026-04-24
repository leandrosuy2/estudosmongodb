# Agregação (pipeline) e `$lookup`

[← Anterior: Pesquisas](03-pesquisas-find.md) · [Índice](../README.md) · [Próximo: Índices →](05-indices.md)

---

Exemplo: total de **valor** por **cliente**, só pedidos **pagos**, ordenado do maior total para o menor.

```
use loja_estudos

db.pedidos.aggregate([
  { $match: { status: "pago" } },
  { $group: { _id: "$cliente", total: { $sum: "$valor" }, qtd: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $project: { _id: 0, cliente: "$_id", total: 1, qtdPedidos: "$qtd" } }
])
```

## Join com `$lookup`

Primeiro grave o `_id` de um pedido e use esse valor em `itens_pedido`.

```
// 1) Pegar um _id existente (copie o valor que aparecer no seu ambiente)
var p = db.pedidos.findOne({ cliente: "Ana", status: "pago" })
// 2) Linhas desse pedido em outra collection
db.itens_pedido.insertMany([
  { pedidoRef: p._id, sku: "mouse", qtd: 1 },
  { pedidoRef: p._id, sku: "teclado", qtd: 1 }
])
// 3) Juntar pedidos com suas linhas
db.pedidos.aggregate([
  { $lookup: {
      from: "itens_pedido",
      localField: "_id",
      foreignField: "pedidoRef",
      as: "linhas"
  }},
  { $match: { _id: p._id } }
])
```

Se `itens_pedido` já existir de um teste anterior, use `db.itens_pedido.drop()` antes do `insertMany` ou escolha outro nome de collection.

Para mais estágios (`$unwind`, `$facet`, datas), veja [Agregação avançada](08-agregacao-avancada.md).
