# Índices básicos e `explain`

[← Anterior: Agregação](04-agregacao-e-lookup.md) · [Índice](../README.md) · [Próximo: Guia de referência →](06-guia-referencia-rapida.md)

---

Índices aceleram **find** e muitos estágios de **$match** / **$sort** compatíveis com o índice.

## Criar índice simples e composto

```
use loja_estudos

db.clientes.createIndex({ email: 1 }, { unique: true })
db.pedidos.createIndex({ cliente: 1, data: -1 })
```

## Listar índices

```
db.pedidos.getIndexes()
```

## Remover índice

```
db.pedidos.dropIndex("cliente_1_data_-1")
```

## Ver plano de execução (índice foi usado?)

```
db.pedidos.find({ cliente: "Ana" }).explain("executionStats")
```

Procure por `IXSCAN` (bom, uso de índice) vs `COLLSCAN` (varredura em toda a collection).

Índices parciais, TTL, collation e texto: [Índices extras e utilitários](09-indices-extras-e-utilitarios.md).
