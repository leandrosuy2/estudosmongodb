# Pesquisas com `find`

[← Anterior: CRUD](02-crud-documentos.md) · [Índice](../README.md) · [Próximo: Agregação →](04-agregacao-e-lookup.md)

---

Insira dados de exemplo para brincar com filtros:

```
use loja_estudos

db.pedidos.insertMany([
  { cliente: "Ana",   valor: 120.5,  status: "pago",    itens: ["mouse", "teclado"], data: ISODate("2025-01-10T10:00:00Z") },
  { cliente: "Bruno", valor: 45,     status: "pendente", itens: ["cabo"],           data: ISODate("2025-02-05T14:30:00Z") },
  { cliente: "Ana",   valor: 200,    status: "pago",    itens: ["monitor"],        data: ISODate("2025-02-15T09:00:00Z") },
  { cliente: "Carla", valor: 89.9,   status: "cancelado", itens: ["headset"],     data: ISODate("2025-03-01T16:00:00Z") }
])
```

## Igualdade simples

```
db.pedidos.find({ status: "pago" })
db.pedidos.find({ cliente: "Ana" })
```

## Comparação numérica e datas

```
db.pedidos.find({ valor: { $gte: 100 } })           // maior ou igual
db.pedidos.find({ valor: { $gt: 50, $lte: 150 } }) // entre (exclusive >50 e inclusive <=150)
db.pedidos.find({ data: { $gte: ISODate("2025-02-01") } })
```

## Operadores úteis

| Operador | Significado |
|----------|-------------|
| `$gt`, `$gte`, `$lt`, `$lte` | comparações |
| `$ne` | diferente |
| `$in`, `$nin` | valor em lista / fora da lista |

```
db.pedidos.find({ status: { $in: ["pago", "pendente"] } })
db.pedidos.find({ status: { $nin: ["cancelado"] } })
```

## Lógica: AND, OR

**AND** é o padrão quando vários campos estão no mesmo objeto:

```
db.pedidos.find({ cliente: "Ana", status: "pago" })
```

**OR** explícito:

```
db.pedidos.find({
  $or: [
    { valor: { $lt: 50 } },
    { status: "cancelado" }
  ]
})
```

## Arrays

**Contém o elemento** (qualquer posição):

```
db.pedidos.find({ itens: "mouse" })
```

**Contém todos** (ordem não importa):

```
db.pedidos.find({ itens: { $all: ["mouse", "teclado"] } })
```

**Tamanho do array:**

```
db.pedidos.find({ itens: { $size: 1 } })
```

## Existência e tipo de campo

```
db.pedidos.find({ observacao: { $exists: true } })
db.pedidos.find({ valor: { $type: "double" } })
```

## Busca textual com regex (use com moderação em bases grandes)

```
db.pedidos.find({ cliente: /^Ana$/i })
db.pedidos.find({ itens: /mouse/i })
```

## Ordenar, limitar e pular (paginação)

```
db.pedidos.find().sort({ valor: -1 })        // maior valor primeiro
db.pedidos.find().sort({ data: 1 }).limit(2)
db.pedidos.find().sort({ data: -1 }).skip(1).limit(2)
```

## Distintos (valores únicos de um campo)

```
db.pedidos.distinct("status")
db.pedidos.distinct("cliente", { valor: { $gte: 100 } })
```
