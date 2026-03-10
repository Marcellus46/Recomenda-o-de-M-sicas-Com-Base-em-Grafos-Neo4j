# Recomendação de Músicas Com Base em Grafos Neo4j
Projeto de Sistema de Recomendações de Músicas em Grafos Neo4j


## 🎵 Sistema de Recomendação de Músicas com Neo4j (Graph Database)

Projeto de estudo utilizando Neo4j para construir um algoritmo simples de recomendação de músicas baseado em grafos.

A ideia central é representar usuários, músicas, artistas e gêneros como nós conectados, permitindo descobrir padrões e recomendações com facilidade.

Este projeto é apenas um protótipo educacional, mas demonstra como sistemas de streaming podem usar grafos para gerar recomendações inteligentes. Podendo ser adaptado para qualquer cenário. 

📌 1. Problema

Plataformas de música precisam sugerir novas músicas para os usuários.

Modelos tradicionais baseados em tabelas podem ter dificuldade para analisar muitas conexões entre usuários, músicas e preferências.

Por exemplo:

usuários que gostam de artistas parecidos

usuários com gostos musicais similares

músicas populares entre amigos

artistas relacionados ao mesmo gênero

Grafos são ideais para esse tipo de problema porque relacionamentos são tratados como elementos principais do banco de dados. Diferentes de Bancos de Dados Tradicionais em Tabelas.

🧠 2. Por que usar Grafos?

Bancos relacionais funcionam bem para dados estruturados, mas recomendações dependem muito de conexões.

Com grafos podemos facilmente descobrir:

músicas ouvidas por amigos

artistas relacionados a um gênero

músicas populares entre usuários com gostos semelhantes

Neo4j permite navegar nesses relacionamentos de forma muito rápida e natural.

🧩 3. Modelo do Grafo

Após análise do esboço inicial, a modelagem recomendada ficou assim:

Nós (Nodes)
(:Usuario)
(:Musica)
(:Artista)
(:Genero)
Propriedades

Usuario

id
nome

Musica

id
titulo

Artista

id
nome

Genero

nome
Relacionamentos
(:Usuario)-[:OUVIU {nota, data}]->(:Musica)

(:Usuario)-[:SEGUE]->(:Artista)

(:Usuario)-[:AMIGO_DE]->(:Usuario)

(:Usuario)-[:GOSTA_DE]->(:Genero)

(:Artista)-[:CRIOU]->(:Musica)

(:Artista)-[:PERTENCE_A]->(:Genero)

(:Usuario)-[:RECOMENDA]->(:Musica)
Visualizando o Schema

Dentro do Neo4j execute:

CALL db.schema.visualization()

Isso gera uma visualização automática do modelo do grafo.

📂 4. Dataset de Exemplo

Arquivo exemplo:

dataset/musicas.csv

Exemplo:

musica_id,titulo,artista
1,Shape of You,Ed Sheeran
2,Blinding Lights,The Weeknd
3,Levitating,Dua Lipa

Arquivo:

dataset/usuarios.csv
usuario_id,nome
1,Roberto
2,Janaina
3,Lika
4,Ticio
5,Mervio
⚙️ 5. Scripts de Carga (Cypher)
Criando Usuários
LOAD CSV WITH HEADERS FROM 'file:///usuarios.csv' AS row
CREATE (:Usuario {
    id: row.usuario_id,
    nome: row.nome
});
Criando Músicas
LOAD CSV WITH HEADERS FROM 'file:///musicas.csv' AS row
CREATE (:Musica {
    id: row.musica_id,
    titulo: row.titulo
});
Criando Artistas
MERGE (:Artista {nome:"Ed Sheeran"})
MERGE (:Artista {nome:"The Weeknd"})
MERGE (:Artista {nome:"Dua Lipa"})
Conectando Artista e Música
MATCH (a:Artista {nome:"Ed Sheeran"})
MATCH (m:Musica {titulo:"Shape of You"})
MERGE (a)-[:CRIOU]->(m)
Usuário ouvindo música
MATCH (u:Usuario {nome:"Roberto"})
MATCH (m:Musica {titulo:"Shape of You"})
CREATE (u)-[:OUVIU {nota:5}]->(m)

📊 6. Queries de Negócio

Aqui estão alguns exemplos de perguntas que o grafo consegue responder.

🎧 Músicas que meus amigos ouviram

MATCH (u:Usuario {nome:"Roberto"})-[:AMIGO_DE]->(amigo)

MATCH (amigo)-[:OUVIU]->(m:Musica)

RETURN amigo.nome, m.titulo

⭐ Músicas mais bem avaliadas

MATCH (u:Usuario)-[r:OUVIU]->(m:Musica)

RETURN m.titulo, avg(r.nota) as nota_media

ORDER BY nota_media DESC

🎤 Artistas mais populares

MATCH (u:Usuario)-[:SEGUE]->(a:Artista)

RETURN a.nome, count(*) as seguidores

ORDER BY seguidores DESC

🎼 Recomendar músicas com base em gênero favorito

MATCH (u:Usuario {nome:"Roberto"})-[:GOSTA_DE]->(g:Genero)

MATCH (a:Artista)-[:PERTENCE_A]->(g)

MATCH (a)-[:CRIOU]->(m:Musica)

RETURN m.titulo

🖼️ 7. Evidências Visuais

As consultas foram visualizadas usando:

Neo4j Browser

Neo4j Bloom

Exemplos de visualizações:

prints/
grafo_recomendacao.png
usuarios_musicas.png
recomendacao_por_genero.png

Essas imagens demonstram as conexões entre usuários, músicas e artistas.

🧪 8. Dificuldades Encontradas (Troubleshooting)

Durante o desenvolvimento alguns problemas surgiram.

Problema 1 — CSV não carregava

Erro:

Couldn't load the external resource

Solução:

Colocar os arquivos dentro da pasta:

neo4j/import
Problema 2 — Relacionamentos duplicados

Algumas relações estavam sendo criadas várias vezes.

Solução:

Usar MERGE em vez de CREATE.

Problema 3 — Modelagem inicial confusa

No primeiro modelo:

MUSICA -> SEGUE -> ARTISTA

Isso não fazia sentido conceitualmente.

A modelagem foi corrigida para:

ARTISTA -> CRIOU -> MUSICA
🚀 9. Possíveis Evoluções do Projeto

Este projeto pode evoluir para:

algoritmo de recomendação colaborativa

uso da Graph Data Science Library

detecção de usuários com gosto musical semelhante

recomendação automática baseada em similaridade

📚 10. Conclusão

Este projeto demonstra como bancos de dados em grafo são extremamente úteis para sistemas de recomendação.

Mesmo sendo um protótipo simples, ele mostra:

modelagem de dados conectados

consultas de recomendação

análise de comportamento de usuários

Esse tipo de arquitetura é utilizado em diversas plataformas modernas de recomendação.

✅ Projeto educacional desenvolvido para estudo de Graph Databases com Neo4j.

## OBSERVAÇÕES E AJUSTES:

🔎 1. Estrutura Geral do Grafo

Eu utilizei corretamente 4 tipos principais de nós:

Node	Função

USUÁRIO	pessoas que usam a plataforma

MÚSICA	músicas disponíveis

ARTISTA	criadores das músicas

GÊNERO	categoria musical

Essa estrutura é exatamente o que plataformas de streaming utilizam como base conceitual.

✔ Modelagem correta

🔗 2. Relacionamentos que eu modelei.

Observei os seguintes relacionamentos no meu grafo:

Relação	Avaliação:

USUÁRIO → OUVIU → MÚSICA	✔ correto

USUÁRIO → SEGUE → ARTISTA	✔ correto

ARTISTA → CRIOU → MÚSICA	✔ perfeito

ARTISTA → PERTENCE → GÊNERO	✔ correto

USUÁRIO → CURTIU → GÊNERO	✔ muito bom

USUÁRIO → AMIGO → USUÁRIO	✔ excelente para recomendação

USUÁRIO → RECOMENDA → MÚSICA	✔ útil para social recommendation

Isso cria vários caminhos de recomendação, por exemplo:

Usuário -> Amigo -> Ouviu -> Música

ou

Usuário -> Curtiu -> Gênero -> Artista -> Música

Esses caminhos são exatamente o que algoritmos de grafos exploram.

⭐ 3. Pontos fortes do meu modelo

Meu grafo agora permite 3 tipos de recomendação.

1️⃣ Recomendação social
Usuário → Amigo → Ouviu → Música

"Usuários ouvem músicas que seus amigos gostam."

2️⃣ Recomendação por gosto musical
Usuário → Curtiu → Gênero → Artista → Música

"Usuários recebem músicas do gênero que gostam."

3️⃣ Recomendação por artista
Usuário → Segue → Artista → Criou → Música

"Usuário recebe novas músicas do artista que segue."

⚠️ 4. Pequenas melhorias que eu fiz (opcional)

Meu modelo já funciona, mas alguns ajustes podem deixá-lo ainda mais profissional.

1️⃣ Padronizar nomes dos relacionamentos

Neo4j recomenda usar maiúsculas com underscore.

Exemplo:

OUVIU
SEGUE
CURTIU
AMIGO_DE
CRIOU
PERTENCE_A
RECOMENDA

2️⃣ Adicionei propriedades importantes

Exemplo:

OUVIU

nota
data
vezes

RECOMENDA

data
3️⃣ Evitar duplicação de relação AMIGO

Em grafos sociais normalmente é usado:

(:Usuario)-[:AMIGO_DE]-(:Usuario)

Sem direção.

🧠 5. Estrutura final ideal do seu grafo

Visualmente ficaria assim:

(:Usuario)-[:OUVIU]->(:Musica)

(:Usuario)-[:SEGUE]->(:Artista)

(:Usuario)-[:CURTIU]->(:Genero)

(:Usuario)-[:AMIGO_DE]-(:Usuario)

(:Usuario)-[:RECOMENDA]->(:Musica)

(:Artista)-[:CRIOU]->(:Musica)

(:Artista)-[:PERTENCE_A]->(:Genero)

📊 6. Avaliação do seu grafo (como avaliador técnico)

Critério	Nota

Modelagem conceitual	⭐⭐⭐⭐⭐

Relacionamentos	⭐⭐⭐⭐⭐

Clareza visual	⭐⭐⭐⭐

Uso para recomendação	⭐⭐⭐⭐⭐

🎯 Nota final: 9 / 10

Para um iniciante em grafos, esse modelo já demonstra:

pensamento em grafos

modelagem correta

visão de recomendação

Isso fica muito bom em um portfólio no GitHub.
