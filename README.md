# Desafio_1_Curso_Analise_dados_NEO4J
Este é o primeiro desafio do curso de Análise de Dados em Neo4j da DIO

## Desafio 1
- Desenvolver a modelagem de grafos para um serviço de streamming
- Aplicar os conhecimentos de CYPHER para modelagem e população do banco de dados

## Modelagem 

 (User) -[WATCHED {rating}]-> (Movie)
 (User) -[WATCHED {rating}]-> (Series)

 (Movie) -[IN_GENRE]-> (Genre)
 (Series) -[IN_GENRE]-> (Genre)

 (Actor) -[ACTED_IN]-> (Movie)
 (Actor) -[ACTED_IN]-> (Series)

 (Director) -[DIRECTED]-> (Movie)
 (Director) -[DIRECTED]-> (Series)


### Tipos de Nós
- User: {id, name}
- Movie: {id, title, year}
- Series: {id, title, seasons}
- Genre: {name}
- Actor: {id, name}
- Director: {id, name}

### Relacionamentos
- WATCHED (User → Movie/Series) {rating}
- ACTED_IN (Actor → Movie/Series)
- DIRECTED (Director → Movie/Series)
- IN_GENRE (Movie/Series → Genre)

 
 ## CYPHER
 
// =============================================
// 1. CONSTRAINTS
// =============================================
CREATE CONSTRAINT user_id IF NOT EXISTS FOR (u:User) REQUIRE u.id IS UNIQUE;
CREATE CONSTRAINT movie_id IF NOT EXISTS FOR (m:Movie) REQUIRE m.id IS UNIQUE;
CREATE CONSTRAINT series_id IF NOT EXISTS FOR (s:Series) REQUIRE s.id IS UNIQUE;
CREATE CONSTRAINT actor_id IF NOT EXISTS FOR (a:Actor) REQUIRE a.id IS UNIQUE;
CREATE CONSTRAINT director_id IF NOT EXISTS FOR (d:Director) REQUIRE d.id IS UNIQUE;
CREATE CONSTRAINT genre_name IF NOT EXISTS FOR (g:Genre) REQUIRE g.name IS UNIQUE;

// =============================================
// 2. INSERT GENRES
// =============================================
UNWIND ["Action", "Drama", "Comedy", "Sci-Fi", "Thriller"] AS genre
CREATE (:Genre {name: genre});

// =============================================
// 3. INSERT USERS (10 usuários)
// =============================================
UNWIND [
{ id: 1, name:"Alice" },
{ id: 2, name:"Bruno" },
{ id: 3, name:"Carla" },
{ id: 4, name:"Daniel" },
{ id: 5, name:"Eva" },
{ id: 6, name:"Fernando" },
{ id: 7, name:"Giovana" },
{ id: 8, name:"Hugo" },
{ id: 9, name:"Isabela" },
{ id:10, name:"João" }
] AS data
CREATE (:User {id:data.id, name:data.name});

// =============================================
// 4. INSERT MOVIES (5 filmes)
// =============================================
UNWIND [
{ id:101, title:"Matrix", year:1999, genre:"Sci-Fi" },
{ id:102, title:"Inception", year:2010, genre:"Sci-Fi" },
{ id:103, title:"Titanic", year:1997, genre:"Drama" },
{ id:104, title:"Avengers", year:2012, genre:"Action" },
{ id:105, title:"The Mask", year:1994, genre:"Comedy" }
] AS data
CREATE (m:Movie {id:data.id, title:data.title, year:data.year})
WITH m, data
MATCH (g:Genre {name:data.genre})
CREATE (m)-[:IN_GENRE]->(g);

// =============================================
// 5. INSERT SERIES (5 séries)
// =============================================
UNWIND [
{ id:201, title:"Breaking Bad", seasons:5, genre:"Drama" },
{ id:202, title:"The Office", seasons:9, genre:"Comedy" },
{ id:203, title:"Stranger Things", seasons:4, genre:"Sci-Fi" },
{ id:204, title:"Sherlock", seasons:4, genre:"Thriller" },
{ id:205, title:"The Boys", seasons:4, genre:"Action" }
] AS data
CREATE (s:Series {id:data.id, title:data.title, seasons:data.seasons})
WITH s, data
MATCH (g:Genre {name:data.genre})
CREATE (s)-[:IN_GENRE]->(g);

// =============================================
// 6. INSERT ACTORS (5 atores)
// =============================================
UNWIND [
{ id:301, name:"Keanu Reeves" },
{ id:302, name:"Leonardo DiCaprio" },
{ id:303, name:"Bryan Cranston" },
{ id:304, name:"Jim Carrey" },
{ id:305, name:"Robert Downey Jr." }
] AS data
CREATE (:Actor {id:data.id, name:data.name});

// =============================================
// 7. INSERT DIRECTORS (5 diretores)
// =============================================
UNWIND [
{ id:401, name:"Wachowski Sisters" },
{ id:402, name:"Christopher Nolan" },
{ id:403, name:"James Cameron" },
{ id:404, name:"Joss Whedon" },
{ id:405, name:"Greg Daniels" }
] AS data
CREATE (:Director {id:data.id, name:data.name});

// =============================================
// 8. RELATIONSHIPS: ACTED_IN & DIRECTED
// =============================================

// ---------------- Actors in Movies ----------------
MATCH (a:Actor {name:"Keanu Reeves"}),(m:Movie {title:"Matrix"}) CREATE (a)-[:ACTED_IN]->(m);
MATCH (a:Actor {name:"Leonardo DiCaprio"}),(m:Movie {title:"Inception"}) CREATE (a)-[:ACTED_IN]->(m);
MATCH (a:Actor {name:"Leonardo DiCaprio"}),(m:Movie {title:"Titanic"}) CREATE (a)-[:ACTED_IN]->(m);
MATCH (a:Actor {name:"Jim Carrey"}),(m:Movie {title:"The Mask"}) CREATE (a)-[:ACTED_IN]->(m);
MATCH (a:Actor {name:"Robert Downey Jr."}),(m:Movie {title:"Avengers"}) CREATE (a)-[:ACTED_IN]->(m);

// ---------------- Actors in Series ----------------
MATCH (a:Actor {name:"Bryan Cranston"}),(s:Series {title:"Breaking Bad"}) CREATE (a)-[:ACTED_IN]->(s);

// ---------------- Directors ----------------
MATCH (d:Director {name:"Wachowski Sisters"}),(m:Movie {title:"Matrix"}) CREATE (d)-[:DIRECTED]->(m);
MATCH (d:Director {name:"Christopher Nolan"}),(m:Movie {title:"Inception"}) CREATE (d)-[:DIRECTED]->(m);
MATCH (d:Director {name:"James Cameron"}),(m:Movie {title:"Titanic"}) CREATE (d)-[:DIRECTED]->(m);
MATCH (d:Director {name:"Joss Whedon"}),(m:Movie {title:"Avengers"}) CREATE (d)-[:DIRECTED]->(m);
MATCH (d:Director {name:"Greg Daniels"}),(s:Series {title:"The Office"}) CREATE (d)-[:DIRECTED]->(s);

// =============================================
// 9. RELATIONSHIPS: USERS WATCHED MOVIES/SERIES
// =============================================
UNWIND [
{user:1, item:101, rating:5},
{user:2, item:102, rating:4},
{user:3, item:103, rating:5},
{user:4, item:104, rating:4},
{user:5, item:105, rating:3},
{user:6, item:201, rating:5},
{user:7, item:202, rating:4},
{user:8, item:203, rating:5},
{user:9, item:204, rating:4},
{user:10,item:205, rating:5}
] AS data
MATCH (u:User {id:data.user})
MATCH (item) WHERE item.id = data.item
CREATE (u)-[:WATCHED {rating:data.rating}]->(item);

