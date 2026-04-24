<p align="center">
  <img src="github/logo.png" alt="Logo do repositório mongoestudos" width="280" />
</p>

# MongoDB — material de estudos (índice)

**Âmbito:** só **banco de dados MongoDB** — servidor, dados, consultas, índices e ferramentas oficiais (**mongosh**, **Compass**, **Atlas**, `mongoimport` / `mongoexport`). Não há conteúdo sobre linguagens de programação, drivers nem ODM.

Roteiro do básico ao avançado com exemplos no **mongosh**. O conteúdo está **dividido em arquivos** na pasta [`docs/`](docs/): abra na ordem ou pule para o que precisar.

**Pré-requisito:** MongoDB (local ou [Atlas](https://www.mongodb.com/atlas)) e **mongosh** instalados.

## Conectar

```bash
mongosh "mongodb://localhost:27017"
# ou Atlas:
mongosh "mongodb+srv://USUARIO:SENHA@cluster0.xxxxx.mongodb.net/"
```

---

## Documentos do curso (ordem sugerida)

| # | Arquivo | Conteúdo |
|---|---------|----------|
| 1 | [docs/01-banco-e-collections.md](docs/01-banco-e-collections.md) | Criar/listar/trocar/apagar banco; collections |
| 2 | [docs/02-crud-documentos.md](docs/02-crud-documentos.md) | `insert`, `find`, `update`, `delete` |
| 3 | [docs/03-pesquisas-find.md](docs/03-pesquisas-find.md) | Filtros, operadores, `sort`, `distinct` |
| 4 | [docs/04-agregacao-e-lookup.md](docs/04-agregacao-e-lookup.md) | Pipeline, `$group`, `$lookup` |
| 5 | [docs/05-indices.md](docs/05-indices.md) | `createIndex`, `explain` |
| 6 | [docs/06-guia-referencia-rapida.md](docs/06-guia-referencia-rapida.md) | Tabelas de consulta (cola do dia a dia) |
| 7 | [docs/07-sandbox-operadores-avancados.md](docs/07-sandbox-operadores-avancados.md) | Banco `guia_consulta`, `$elemMatch`, `$expr`, arrays |
| 8 | [docs/08-agregacao-avancada.md](docs/08-agregacao-avancada.md) | `$unwind`, `$facet`, datas, `$lookup` com pipeline |
| 9 | [docs/09-indices-extras-e-utilitarios.md](docs/09-indices-extras-e-utilitarios.md) | TTL, collation, texto, `bulkWrite`, export |
| 10 | [docs/10-roteiro-teorico-e-glossario.md](docs/10-roteiro-teorico-e-glossario.md) | Fases teóricas, glossário, como estudar |

---

## Exercícios e diagnóstico

| Arquivo | Uso |
|---------|-----|
| [docs/12-exercicios-praticos.md](docs/12-exercicios-praticos.md) | **Exercícios do zero** com **fluxogramas Mermaid** (biblioteca, clínica, vendas, performance) |
| [docs/11-erros-comuns.md](docs/11-erros-comuns.md) | Erros típicos (índice único, projeção, `$lookup`, TTL, etc.) |

> **Visualizar fluxogramas:** GitHub, GitLab ou VS Code com preview de Markdown renderizam Mermaid. Se vir só código, instale uma extensão “Mermaid” no editor.

---

## Recursos oficiais

| Recurso | URL |
|---------|-----|
| Manual | https://www.mongodb.com/docs/manual/ |
| Cursos gratuitos | https://learn.mongodb.com/ |
| Atlas | https://www.mongodb.com/atlas |

---

## Checklist de prática

- [ ] Percorrer os arquivos **01 → 05** na ordem, executando os blocos no `mongosh`
- [ ] Consultas com `$in`, `$or`, `sort` + `limit` ([03](docs/03-pesquisas-find.md))
- [ ] Um `aggregate` com `$match` + `$group` + `$sort` ([04](docs/04-agregacao-e-lookup.md))
- [ ] Índice + `explain` ([05](docs/05-indices.md))
- [ ] Sandbox [07](docs/07-sandbox-operadores-avancados.md): `$elemMatch`, `$expr`, `$push` / `$pull`
- [ ] Fazer pelo menos **dois** exercícios em [12](docs/12-exercicios-praticos.md) sem copiar os comandos
- [ ] Ler [11-erros-comuns](docs/11-erros-comuns.md) quando algo falhar

---

*Cada arquivo em `docs/` tem links “Anterior / Índice / Próximo” para navegar em sequência.*
