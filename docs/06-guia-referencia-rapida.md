# Guia de consulta rápida (referência)

[← Anterior: Índices](05-indices.md) · [Índice](../README.md) · [Próximo: Sandbox →](07-sandbox-operadores-avancados.md)

---

Use este arquivo como **índice de sintaxe** quando você já souber *o que* quer fazer e precisar lembrar *como*.

## Comandos úteis no `mongosh`

| Ação | Comando |
|------|---------|
| Ajuda geral | `help` |
| Métodos em `db` | `db.help()` |
| Ajuda de uma collection | `db.nomeCollection.help()` |
| Versão do servidor | `db.version()` |
| Host atual | `db.getMongo()` |
| Listar comandos administrativos | `db.adminCommand({ listCommands: 1 })` |
| Rodar comando admin | `db.adminCommand({ connectionStatus: 1 })` |
| Sair | `exit` ou `quit` |

## Filtros de consulta (`find`, `$match`, `deleteMany`…)

| Operador | Uso típico |
|----------|------------|
| `{ campo: valor }` | igualdade |
| `$eq`, `$ne` | igual / diferente |
| `$gt`, `$gte`, `$lt`, `$lte` | comparação |
| `$in`, `$nin` | valor na lista / fora |
| `$and`, `$or`, `$nor`, `$not` | lógicos |
| `$exists` | campo existe ou não |
| `$type` | [tipo BSON](https://www.mongodb.com/docs/manual/reference/operator/query/type/) |
| `$regex` | padrão textual |
| `$all` | array contém todos os elementos |
| `$elemMatch` | **pelo menos um** elemento do array de documentos satisfaz condições |
| `$size` | tamanho exato do array |
| `$mod` | resto da divisão (`{ campo: { $mod: [4, 0] } }` divisível por 4) |
| `$text` | busca textual (exige índice de texto) |
| `$where` | Expressão avaliada no servidor (operador desaconselhado: custo e risco; prefira filtros com `$expr` / campos indexados) |
| `$expr` | expressão com operadores `$` comparando campos do mesmo documento |

**Notação por ponto** (campo dentro de subdocumento ou array por índice):

```
// endereco.cidade igual a "Curitiba"
db.clientes.find({ "endereco.cidade": "Curitiba" })
// primeiro telefone marcado como principal
db.clientes.find({ "telefones.0.principal": true })
```

## Projeção no segundo argumento do `find`

| Padrão | Significado |
|--------|-------------|
| `{ nome: 1, email: 1 }` | incluir só esses campos (`_id` continua a menos que `_id: 0`) |
| `{ segredo: 0 }` | excluir `segredo`; demais campos vêm (exceto conflito misto 0/1 em subpaths) |
| `{ resultado: { $slice: 2 } }` | limitar tamanho de array projetado aos 2 primeiros |

## Operadores de atualização (segundo argumento de `update*`)

| Operador | Efeito |
|----------|--------|
| `$set` | define / altera campos |
| `$unset` | remove campos |
| `$inc` | soma número (datas em milissegundos também) |
| `$mul` | multiplica |
| `$min` / `$max` | mantém menor / maior valor |
| `$currentDate` | grava `Date` ou timestamp |
| `$rename` | renomeia campo |
| `$setOnInsert` | só aplica quando **upsert** cria o documento |
| `$push` | empilha no array (`$each`, `$position`, `$slice`, `$sort`) |
| `$pull` | remove elementos do array que casam filtro |
| `$pullAll` | remove lista de valores do array |
| `$addToSet` | adiciona ao array se ainda não existir |
| `$pop` | remove primeiro (`-1`) ou último (`1`) do array |
| Posicionais `$`, `$[]`, `$[<identifier>]` | atualizar elemento específico do array (filtro + arrayFilters) |

## Métodos encadeados ao cursor (`find`)

| Método | Função |
|--------|--------|
| `.sort({ campo: 1 })` | 1 crescente, -1 decrescente |
| `.limit(n)` | no máximo `n` documentos |
| `.skip(n)` | pula `n` (custo em offsets grandes) |
| `.project({ ... })` | mesma ideia do 2º arg de `find` |
| `.batchSize(n)` | tamanho do lote de leitura |
| `.hint("nome_1")` | força uso de índice |
| `.maxTimeMS(ms)` | tempo máximo |
| `.collation({ locale: "pt", strength: 1 })` | comparação de strings (acentos / maiúsculas) |
| `.allowDiskUse(true)` | em agregações pesadas, opção no segundo parâmetro de `aggregate()` |

Percorrer cursor no shell:

```
db.pedidos.find({ status: "pago" }).forEach(doc => print(doc.cliente, doc.valor))
let c = db.pedidos.find().limit(2)
while (c.hasNext()) printjson(c.next())
```

## Estágios de aggregation mais usados

| Estágio | Função resumida |
|---------|-----------------|
| `$match` | filtro (cedo no pipeline reduz volume) |
| `$project` | forma de saída, incluir/excluir, expressões |
| `$addFields` / `$set` | adiciona campos calculados |
| `$group` | agrupa por `_id`, acumuladores |
| `$sort` | ordena |
| `$limit` / `$skip` | paginação no pipeline |
| `$lookup` | join com outra collection |
| `$unwind` | explode array em um doc por elemento |
| `$facet` | vários sub-pipelines no mesmo conjunto |
| `$bucket` / `$bucketAuto` | faixas numéricas |
| `$sortByCount` | equivalente a `$group` + `$sort` por contagem |
| `$count` | conta documentos (ou nome de campo) |
| `$replaceRoot` | troca raiz do documento |
| `$unionWith` | une resultados de outra collection |
| `$merge` / `$out` | grava resultado em collection (cuidado em produção) |

Acumuladores comuns em `$group` / `$project`: `$sum`, `$avg`, `$min`, `$max`, `$first`, `$last`, `$push`, `$addToSet`.
