# Documentos — CRUD

[← Anterior: Banco e collections](01-banco-e-collections.md) · [Índice](../README.md) · [Próximo: Pesquisas →](03-pesquisas-find.md)

---

Use um banco só para testes (ex.: `loja_estudos`).

```
use loja_estudos
```

## CREATE — inserir documentos

**Um documento:**

```
db.clientes.insertOne({
  nome: "Ana Silva",
  email: "ana@email.com",
  idade: 28,
  tags: ["vip", "newsletter"],
  endereco: { cidade: "São Paulo", uf: "SP" },
  criadoEm: new Date()
})
```

**Vários de uma vez:**

```
db.clientes.insertMany([
  { nome: "Bruno", email: "bruno@email.com", idade: 35, tags: ["newsletter"] },
  { nome: "Carla", email: "carla@email.com", idade: 22, tags: ["vip"] },
  { nome: "Daniel", email: "daniel@email.com", idade: 41 }
])
```

O campo `_id` é gerado automaticamente se você não enviar (tipo **ObjectId**).

## READ — ler (buscar) documentos

**Todos os documentos da collection:**

```
db.clientes.find()
```

**Formato mais legível (uma linha por campo):**

```
db.clientes.find().pretty()
```

**Só o primeiro que bater no filtro:**

```
db.clientes.findOne({ nome: "Ana Silva" })
```

**Projeção:** trazer só alguns campos (`1` = incluir, `_id` vem por padrão):

```
db.clientes.find({}, { nome: 1, email: 1, _id: 0 })
```

**Contar quantos documentos:**

```
db.clientes.countDocuments({})
db.clientes.countDocuments({ idade: { $gte: 30 } })
```

## UPDATE — alterar documentos

**Atualizar um documento** (operador `$set` adiciona ou altera campos):

```
db.clientes.updateOne(
  { email: "ana@email.com" },
  { $set: { idade: 29, ultimaCompra: new Date() } }
)
```

**Incrementar número** (`$inc`):

```
db.clientes.updateOne(
  { nome: "Bruno" },
  { $inc: { pontosFidelidade: 10 } }
)
// Se o campo não existir, ele é criado com o incremento.
```

**Remover um campo** (`$unset`):

```
db.clientes.updateOne(
  { nome: "Carla" },
  { $unset: { tags: "" } }
)
```

**Atualizar vários que casam o filtro:**

```
db.clientes.updateMany(
  { idade: { $lt: 30 } },
  { $set: { faixa: "jovem" } }
)
```

**Substituir o documento inteiro** (cuidado: remove campos não enviados):

```
db.clientes.replaceOne(
  { email: "daniel@email.com" },
  { nome: "Daniel Santos", email: "daniel@email.com", idade: 41 }
)
```

## DELETE — excluir documentos

**Apagar um documento:**

```
db.clientes.deleteOne({ email: "bruno@email.com" })
```

**Apagar todos que casam o filtro:**

```
db.clientes.deleteMany({ faixa: "jovem" })
```

**Apagar todos da collection (mantém a collection vazia):**

```
db.clientes.deleteMany({})
```
