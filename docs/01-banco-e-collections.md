# Banco de dados e collections

[← Voltar ao índice](../README.md) · [Próximo: CRUD →](02-crud-documentos.md)

---

Conceito: em MongoDB, **database** agrupa **collections**; cada collection guarda **documentos** (JSON/BSON).

## Banco de dados

### Listar bancos existentes

```
show dbs
```

### “Criar” banco (na prática: escolher e gravar algo)

O MongoDB **cria o banco automaticamente** quando você grava o primeiro dado nele.

```
use loja_estudos
// Ainda pode não aparecer em show dbs até existir dado:
db.getName()   // "loja_estudos"
```

### Trocar de banco

```
use admin
use loja_estudos
```

### Apagar o banco atual (cuidado: irreversível)

Você precisa estar no banco que quer apagar (`use nome`).

```
use loja_estudos
db.dropDatabase()
// Retorno típico: { ok: 1, dropped: "loja_estudos" }
```

### Informações do banco atual

```
db.stats()
db.getCollectionNames()
```

---

## Collections

Entre no banco de estudo antes dos comandos abaixo.

```
use loja_estudos
```

### Criar collection vazia (explícito)

```
db.createCollection("produtos")
db.createCollection("clientes")
```

Também é criada **implicitamente** ao fazer o primeiro `insert` em `db.nomeDaCollection.insertOne(...)`.

### Listar collections do banco atual

```
show collections
// equivalente:
db.getCollectionNames()
```

### Renomear collection

```
db.produtos.renameCollection("catalogo_produtos")
```

### Apagar collection inteira

```
db.catalogo_produtos.drop()  // true se removeu, false se não existia
```
