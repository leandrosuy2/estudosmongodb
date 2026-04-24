# Erros e armadilhas comuns

[← Anterior: Roteiro teórico](10-roteiro-teorico-e-glossario.md) · [Índice](../README.md) · [Próximo: Exercícios →](12-exercicios-praticos.md)

---

Consulte esta página quando algo **falhar** ou o resultado **não bater** com o esperado.

## Índice único e `E11000 duplicate key error`

**Sintoma:** ao rodar `insertOne` ou `insertMany` de novo, erro de chave duplicada em `email` (ou outro campo com `unique: true`).

**Causa:** o mesmo valor já existe na collection.

**O que fazer:**

- Use **outro email** nos inserts de teste, ou
- `db.clientes.deleteMany({})` antes de repetir o tutorial, ou
- `db.clientes.dropIndex("email_1")` se quiser remover a unicidade (não recomendado se o modelo exige unicidade).

---

## Projeção: misturar inclusão (`1`) e exclusão (`0`)

**Sintoma:** erro ou comportamento estranho em `find({}, { a: 1, b: 0 })`.

**Regra:** na mesma projeção, **não misture** `1` e `0` em campos raiz, exceto `_id: 0` com outros `1` (isso é permitido: “traga só estes campos e esconda `_id`”).

**Válido:**

```
db.x.find({}, { nome: 1, email: 1, _id: 0 })
```

**Evite:** `{ nome: 1, outro: 0 }` (mistura ilegal na raiz).

---

## Segundo índice de texto na mesma collection

**Sintoma:** erro ao criar outro `{ $text: ... }` ou segundo índice com `text`.

**Causa:** MongoDB permite **apenas um índice de texto** por collection.

**O que fazer:** inclua todos os campos textuais no **mesmo** `createIndex({ titulo: "text", corpo: "text" })` ou remova o índice antigo com `dropIndex` antes de recriar.

---

## `dropDatabase` no banco errado

**Sintoma:** sumiu o banco que você não queria apagar.

**Prevenção:** sempre confira **antes**:

```
db.getName()
show dbs
```

Só então `db.dropDatabase()`.

---

## `$lookup` não junta nada (`linhas: []`)

**Causas frequentes:**

1. **Tipos diferentes:** `localField` é `ObjectId` mas `foreignField` guarda **string** (ou o contrário).
2. **Nome do campo errado** (`pedidoId` vs `pedidoRef`).
3. Collection `from` está em **outro database** (por padrão `$lookup` usa o mesmo `db`).

**Debug:** `findOne` em ambos os lados e compare tipos com `typeof doc.campo` ou inspecione no Compass.

---

## Regex sem âncora em base grande

**Sintoma:** consulta lenta, `COLLSCAN` no `explain`.

**Causa:** padrões como `/mongo/` forçam varredura ampla.

**Mitigação:** prefixo `^mongo`, índice adequado, ou **Atlas Search** / índice de texto para busca full-text.

---

## `skip(N)` com N muito grande

**Sintoma:** paginação lenta nas últimas “páginas”.

**Causa:** o servidor ainda precisa percorrer documentos para pular.

**Alternativa:** paginação por **range** em campo indexado (ex.: `_id` ou `criadoEm` > último valor visto).

---

## `upsert` criando “duplicatas lógicas`

**Sintoma:** vários documentos quase iguais.

**Causa:** o **filtro** do `updateOne` não é único; cada combinação diferente pode gerar um novo documento com `upsert: true`.

**O que fazer:** garantir filtro nos campos que **identificam** o documento (ex.: `chave` única) e índice `unique` quando fizer sentido.

---

## `updateMany` sem filtro (ou filtro `{}`)

**Sintoma:** todos os documentos foram alterados.

**Prevenção:** rode antes o mesmo filtro com `find` ou `countDocuments` e revise.

---

## `replaceOne` apagou campos

**Causa:** `replaceOne` substitui o documento inteiro pelo novo objeto; campos omitidos **somem**.

**Preferência:** use `$set` / `$unset` para alterações parciais.

---

## TTL não apaga na hora

**Sintoma:** documento “passou do tempo” e continua lá.

**Causa:** o thread TTL roda em intervalos; pode haver atraso de **minutos**.

**Lembrete:** o campo indexado deve ser **Date** (ou array de Date); a semântica é `campo + expireAfterSeconds` comparado ao relógio do servidor.

---

## `countDocuments` vs `estimatedDocumentCount`

- **`estimatedDocumentCount()`** não aceita filtro; é aproximado e rápido.
- **`countDocuments({ ... })`** aceita filtro e é preciso; pode ser mais caro.

Não use `estimatedDocumentCount` esperando filtrar por campo.

---

## Documentação relacionada

- [Guia de referência rápida](06-guia-referencia-rapida.md)
- [Exercícios práticos](12-exercicios-praticos.md)
