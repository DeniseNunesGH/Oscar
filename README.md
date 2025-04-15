# Nomeados ao Oscar

Contém a base de indicados ao Oscar em formato SQL para treinar comandos CRUD. 

Abaixo, algumas atividades para trabalharmos.

--- 

* Atualize os registros da tabela com os dados do Oscar 2025

---

* Qual o **total** de registros na tabela indicados?

R: 11004

Q:
```js
db.colecao.countDocuments()
11004

```

* Qual o número de indicações por categoria agrupadas por categoria?

R: 2

Q:
```js
db.indicados_ao_oscar.aggregate([
  {
    $group: {
      _id: "$categoria",  
      total_indicacoes: { $sum: 1 }  
    }
  },
  {
    $sort: { total_indicacoes: -1 } 
  }
]);
```

---

* Quantas vezes Natalie Portman foi indicada ao Oscar?

R: 3 vezes

Q:
```js
 db.oscar.find({"Nome": "Natalie Portman"}).countDocuments()
```

---

* Quantos Oscars Natalie Portman ganhou?

```js

db.colecao.countDocuments({nome_do_indicado: "Natalie Portman", vencedor: "true"})
1

```

* Quantas vezes Viola Davis foi indicada ao Oscar?

```js
db.colecao.countDocuments({nome_do_indicado: "Viola Davis"})
4
```

* Quantos Oscars Viola Davis ganhou?

```js
db.colecao.countDocuments({nome_do_indicado: "Viola Davis", vencedor: "true"})
1
```

* Amy Adams já ganhou algum Oscar?

Não

* Quais os atores/atrizes que foram indicados mais de uma vez?

```js
db.colecao.aggregate([
  {$group: {_id: "$nome_do_indicado", total_indicacoes: {$sum: 1}}},
  {$match: {total_indicacoes: {$gt: 1}}}

])

db.colecao.aggregate([
  {$group: {_id: "$nome_do_indicado", total_indicacoes: {$sum: 1}}},
  {$match: {total_indicacoes: {$gt: 1}}},
  {$count: "quantidade_atores_mais_uma_indicacao"}
])
1140

```
---

* A série de filmes Toy Story ganhou Oscars em quais anos?

```js
db.colecao.aggregate([
  {
    $match: {
      vencedor: "true", // cuidado: aqui é string, não boolean
      nome_do_filme: { $regex: /toy story/i } // busca insensível a maiúsculas/minúsculas
    }
  },
  {
    $group: {
      _id: "$ano_cerimonia", // agrupando pelo ano da cerimônia
      filmes: { $addToSet: "$nome_do_filme" },
      categorias: { $addToSet: "$categoria" }
    }
  },
  {
    $sort: { _id: 1 }
  }
])
{
  _id: 2011,
  filmes: [
    'Toy Story 3'
  ],
  categorias: [
    'ANIMATED FEATURE FILM',
    'MUSIC (Original Song)'
  ]
}

2011
2020

```

* A partir de que ano que a categoria "Actress" deixa de existir? 

```js
db.colecao.aggregate([
  {
    $match: {
      categoria: { $regex: /actress/i }
    }
  },
  {
    $group: {
      _id: "$ano_cerimonia"
    }
  },
  {
    $sort: { _id: 1 }
  }
])

1948
```
* Quem ganhou o primeiro Oscar para Melhor Atriz? Em que ano?

```js

db.colecao.find({
  categoria: { $regex: /actress.*leading/i },
  vencedor: "true"
})
.sort({ ano_cerimonia: 1 })
.limit(1)

Faye Dunaway
1977

```

* Na campo "Vencedor", altere todos os valores com "true" para 1 e todos os valores "false" para 0.

```js
// Atualiza "true" para 1
db.colecao.updateMany(
  { vencedor: "true" },
  { $set: { vencedor: 1 } }
)

// Atualiza "false" para 0
db.colecao.updateMany(
  { vencedor: "false" },
  { $set: { vencedor: 0 } }
)

Q: acknowledged: true,
  insertedId: null,
  matchedCount: 8442,
  modifiedCount: 8442,
  upsertedCount: 0

```

---

* Em qual edição do Oscar "Crash" concorreu ao Oscar?

```js
db.colecao.find({nome_do_filme: "Crash"})

2006

```

---

* O filme Central do Brasil aparece no Oscar?

```js
db.colecao.find({nome_do_filme: "Central do Brasil"})

Não
```

* Inclua no banco 3 filmes que nunca foram nem nomeados ao Oscar, mas que merecem ser. 

```js
db.colecao.insertMany([
  {
    id_registro: 9991,
    ano_filmagem: 2000,
    ano_cerimonia: null,
    cerimonia: null,
    categoria: null,
    nome_do_indicado: null,
    nome_do_filme: "O Alto da Compadecida",
    vencedor: 0
  },
  {
    id_registro: 9992,
    ano_filmagem: 2006,
    ano_cerimonia: null,
    cerimonia: null,
    categoria: null,
    nome_do_indicado: null,
    nome_do_filme: "Se eu fosse você",
    vencedor: 0
  },
  {
    id_registro: 9993,
    ano_filmagem: 2006,
    ano_cerimonia: null,
    cerimonia: null,
    categoria: null,
    nome_do_indicado: null,
    nome_do_filme: "Deu a louca na chapeuzinho vermelho",
    vencedor: 0
  }
])



```

* Denzel Washington já ganhou algum Oscar?

```js
db.colecao.find({nome_do_indicado: "Denzel Washington"})

Sim, dois. Um em 1989 e um em 2002

```

* Quais os filmes que ganharam o Oscar de Melhor Filme?

---

* Sidney Poitier foi o primeiro ator negro a ser indicado ao Oscar. Em que ano ele foi indicado? Por qual filme?

---

* Quais os filmes que ganharam o Oscar de Melhor Filme e Melhor Diretor na mesma cerimonia?

---

* Denzel Washington e Jamie Foxx já concorreram ao Oscar no mesmo ano?
