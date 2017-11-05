GraphQL
=

Es un lenguaje de consulta para comunicar clientes y servidores

* Es una alternativa a REST

* Nos permite definir la estructura de los datos

* Se puede implementar en cualquier lenguaje de servidor

_servidor_
```
type Curso {
    titulo: String
    profesor: Profesor
    alumnos: [Alumno]
}
```
_cliente_
```
{
    curso(name: "GraphQL") {
        titulo
    }
}
```
 _resultado_
 ```
 {
     "curso": {
         "titulo": Curso de GraphQL
     }
 }
 ```

<!-- TOC -->

    - [Sus origenes](#sus-origenes)
        - [Motivaciones](#motivaciones)
- [REST vs. GraphQL](#rest-vs-graphql)
- [Schema](#schema)
    - [Types](#types)
        - [Scalars](#scalars)
        - [Objects](#objects)
        - [Interface](#interface)
        - [Union](#union)
        - [Modificadores de tipo](#modificadores-de-tipo)
        - [Root Type: Query](#root-type-query)
        - [Root Type: Mutation](#root-type-mutation)
- [GraphiQL](#graphiql)
    - [Argumentos](#argumentos)
    - [Variables](#variables)
    - [Aliases](#aliases)
    - [Fragments](#fragments)
    - [Inline Fragments](#inline-fragments)
    - [Directives](#directives)
    - [Mutations](#mutations)
- [Proyecto](#proyecto)
    - [Creacion del Esquema](#creacion-del-esquema)
    - [Decrecacion de Campos](#decrecacion-de-campos)
    - [Resolvers](#resolvers)
    - [Mocking](#mocking)
    - [Base de datos](#base-de-datos)
    - [Modularizacion](#modularizacion)
    - [Mutaciones](#mutaciones)
        - [Add](#add)
        - [Edit](#edit)
        - [Delete](#delete)
    - [Busqueda](#busqueda)
    - [Errores](#errores)
    - [GraphQL sin GraphiQL](#graphql-sin-graphiql)
- [Clientes GraphQL](#clientes-graphql)
    - [Apollo vs Relay](#apollo-vs-relay)
        - [Relay](#relay)
        - [Apollo](#apollo)

<!-- /TOC -->


## Sus origenes
Facebook lo crea en el 2009 impulsado por el equipo mobile

Aparece Open Source en le 2015

### Motivaciones

* Mejorar lo que ofrece REST
* Evitar multiples pedidos al servidor

# REST vs. GraphQL

| REST | GraphQL |
|------|---------|
|Es solo una convencion|Es un lenguaje tipado y validable|
|El servidor expone recursos|El cliente define que recibe|
|Suele enviar informacion de mas| Se envia solo lo necesario|
|Multipiles reques por vista o custom endpoint|Un solo request por vista|
|Documenta ajena al desarrollo|Documentado por definicion|

# Schema
Define que puede pedir el cliente

## Types

### Scalars
|Tipos| |
|-----|-|
|Int|Numeros enteros|
|Float|Numeros decimales|
|String|Boolean|
|ID|Identificador unico|

### Objects

```
type Curso {
    id: ID!
    descripcion: String
    profesores: [Profesor]
    rating: Int
}

type Profesor {
    nombre: String
    edad: Int
}
```

El simbolo "!" significa __requerido__

 ### Enum

```
enum Genero {
    MASCULINO
    FEMENINO
}
```

### Interface
```
interface  Perfil {
    nombre: String!
    email: String!
}
```

_Implementacion_
```
type PerfilFB implements Perfil {
    nombre: String!
    email: String!
    amigos: [Usuario]
}

type PerfilTW implements Perfil {
    nombre: String!
    email: String!
    handle: String
    seguidores: [Usuario]
}
```

### Union

```
union Busqueda = Amigo | Lugar | Evento | Pagina
```

### Modificadores de tipo
|Tipos| |
|-----|-|
|String!| NOT NULL|
|[String]|Lista|
|[String]!|Lista NOT NULL|
|[String!]!|Lista NOT NULL con valores NOT NULL|

### Root Type: Query
endpoints en GraphQL
```
type Query {
    cursos: [Curso]
    profesores: [Profesor]
    curso(id: String!): Curso
    profesor(id: String!, limite: Int): Profesor
}
```

### Root Type: Mutation

```
type Mutation {
    agregarCurso(
        descripcion: String,
        profesorId: String
    ): Curso
}
```

# GraphiQL

Si ejecutamos:

```
{
  cursos {
    titulo
    descripcion
    profesor {
      nombre
      nacionalidad
    }
  }
  profesores{
    nombre
  }
}
```

Obtenemos una data de los cursos y profesores en un solo request y con los datos que especificamos


## Argumentos

Enviado parametros obtenemos informacion preciza

```
{
  curso(id: 1) {
    titulo
  }
}

/*-------*/

{
  "data": {
    "curso": {
      "titulo": "corporis et"
    }
  }
}
```

## Variables

Podemos definir variables en la consulta de GraphQL, debemos crear un objeto con las variables y asignarlas al definir el Query

```
{
  "id": 4
}
/*-------*/
query UnCurso($id: Int = 1){
  curso(id: $id){
    titulo
  }
}
```

## Aliases

Podemos asignar Aliases para evitar confictos en las consultas a mismo "objeto"

```
{
  cursoMasVotado: curso(id: 1){
    titulo
  }
  cursoMasVisto: curso(id: 2){
    titulo
  }
}
```

## Fragments

Podemos usar Fragments para evitar duplicidad

```
{
  curso(id: 1){
    ...CamposNecesarios
  }
  cursos {
    ...CamposNecesarios
  }
}

fragment CamposNecesarios on  Curso {
  titulo
  descripcion
}
```


## Inline Fragments
Supongamos que el query que estamos ejecutando y este hace un JOIN donde el tipo puede ser de uno o de otro

```
{
  buscar(query: "GraphQL") {
    ... on Curso{
      titulo
    }
    ... on Profesor{
      nombre
    }
  }
}
```

## Directives

Podemos definir cuando incluir una variable haciendo uso de un include o skip

```
{
  "conDescripcion": false
}
/*-------------------*/

query Cursos($conDescripcion: Boolean!) {
  cursos {
    titulo
    descripcion @include(if: $conDescripcion)
    profesor @skip(if: $conDescripcion) {
      nombre
    }
  }
}
```

## Mutations

Podemos usar mutaciones para modificar los resultados. En este caso de utiliza uno llamado profesorAdd el cual nos insertara un profesor y devolvera la informacion del mismo

```
mutation {
  profesorAdd(profesor: {
    nombre: "Paula"
    genero: FEMENINO
    nacionalidad: "Argentina"
  }) {
    id
  }
}
/*---------------*/

{
  "data": {
    "profesorAdd": {
      "id": "29"
    }
  }
}
```

# Proyecto

Aqui crearemos un proyecto usando GraphQL desde cero usando Nodejs

Usaremos:

* [Node.js](https://nodejs.org)
* [Express](http://expressjs.com/es/)
* [yarn](https://yarnpkg.com/lang/en/)
* [Apollo Tools](http://dev.apollodata.com)
* [sqlite]()

Instalaremos Node.js y Yarn

Crearemos un proyecto y lo inicializamos con `yarn init`. Añadiremos las siguientes dependencias:

`yarn add express body-parser graphql graphql-server-express graphql-tools`

Ahora bien, a trabajar en los arhivos

_./server.js_
```
const express = require('express')
const bodyParse = require('body-parser')
const { graphqlExpress, graphiqlExpress } = require('graphql-server-express')
const schema = require('./schema')

const app = express()

app.use(
    '/graphql',
    bodyParse.json(), 
    graphqlExpress({schema})
)

app.use(
    '/graphiql',
    graphiqlExpress({
        endpointURL: '/graphql'
    })
)

const PORT = 5678
app.listen(PORT, () => {
    console.log('Server ON')
})
```

_./schema_
```
const { makeExecutableSchema } = require('graphql-tools')

const typeDefs = `
    type Curso {
        id: ID!
        titulo: String!
    }

    type Query {
        cursos: [Curso]
    }
`

const schema = makeExecutableSchema({
    typeDefs: typeDefs
})

module.exports = schema
```

## Creacion del Esquema

_./schema_
```
const { makeExecutableSchema } = require('graphql-tools')

const typeDefs = `

    # Curso del Sistema
    type Curso {
        id: ID!
        titulo: String!
        descripcion: String!
        profesor: Profesor
        rating: Float
        comentarios: [Comentario]
    }

    type Profesor {
        id: ID!
        nombre: String!
        nacionalidad: String!
        genero: Genero
        cursos: [Curso]
    }

    enum Genero {
        MASCULINO
        FEMENINO
    }

    type Comentario {
        id: ID!
        nombre: String!
        cuerpo: String!
    }

    type Query {
        cursos: [Curso]
        profesores: [Profesor]
        curso(id: Int): Curso
        profesor(id: Int): Profesor
    }

`

const schema = makeExecutableSchema({
    typeDefs: typeDefs
})

module.exports = schema
```

## Decrecacion de Campos

Podemos deprecar campos que no se utilizaran en el futuro

```
    type Curso {
        id: ID!
        titulo: String!
        # Descripcion del curso
        descripcion: String!
        profesor: Profesor
        rating: Float @deprecated(reason: "No creemos mas en los puntajes")
        comentarios: [Comentario]
    }
```

## Resolvers

Podemos definir resolvers los cuales seran la informacion a consultar desde los querys de GraphQL

_./schema_
```
const resolvers = {
    Query: {
        cursos: () => {
            return [
                {
                    id: 1,
                    titulo: 'Curso de GraphQL',
                    descripcion: 'Aprendiendo GraphQL'
                },
                {
                    id: 2,
                    titulo: 'Curso de PHP',
                    descripcion: 'Aprendiendo PHP'
                }
            ]
        }
    },
    Curso: {
        profesor: () => {
            return {
                nombre: 'Pablo',
                nacionalidad: 'Argentina'
            }
        },
        comentarios: () => {
            return [
                {
                    nombre: 'Juan',
                    cuerpo: 'Buen video!'
                }
            ]
        }
    },
    
}

const schema = makeExecutableSchema({
    typeDefs: typeDefs,
    resolvers: resolvers
})
```

## Mocking

Podemos resolver problemas de data en desarrollo utilizando una libreria llamada casual `yarn add casual`. Con esta podemos realizar un Mock del schema de la siguiente manera

```
const { makeExecutableSchema, addMockFunctionsToSchema } = require('graphql-tools')
const casual = require('casual')

/* Aqui va el typeDefs, el Resolver y el Schema */
# Code

var i = 0
var idGenerator = () => {
    return i++;
}
addMockFunctionsToSchema({
    schema: schema,
    mocks: {
        Curso: () => {
            return {
                id: idGenerator(),
                titulo: casual._title,
                descripcion: casual.text
            }
        },
        Profesor: () => {
            return {
                nombre: casual.name,
                nacionalidad: casual.country
            }
        }
    },
    preserveResolvers: false
})
```

## Base de datos

Podemos implementar cualquier base de datos y que la consulta se realize utilizando un promise, el cual podamos devolver en el schema.

Para este caso utilizaremos __sqlite3__ con las librerias ___knex___ y ___objection___

Resolvemos las solicitudes con GraphQL de la siguiente manera

_schema_
```
const resolvers = {
    Query: {
        cursos: () => Curso.query().eager('[profesor, comentarios]'),
        profesores: () => Profesor.query().eager('cursos'),
        curso: (rootValue, args) => Curso.query().findById(args.id).eager('[profesor, comentarios]'),
        profesor: (rootValue, args) => Profesor.query().findById(args.id).eager('cursos')
    }
}
```

## Modularizacion

Para modularizar el esquema actual debemos dividir nuestro archivo schema.

_schema/index.js_
```
const { makeExecutableSchema, addMockFunctionsToSchema } = require('graphql-tools')
const resolvers = require('../resolvers')
const Profesor = require('./Profesor.gql')
const Curso = require('./Curso.gql')
const rootQuery = require('./rootQuery.gql')

const schema = makeExecutableSchema({
    typeDefs: [rootQuery, Profesor, Curso],
    resolvers: resolvers
})

var i = 0
var idGenerator = () => {
    return i++;
}

module.exports = schema
```

_schema/rootQuery.gql_
```
module.export = `
type Query {
    cursos: [Curso]
    profesores: [Profesor]
    curso(id: Int): Curso
    profesor(id: Int): Profesor
}
`
```

_schema/Profesor.gql_
```
module.exports = `
type Profesor {
    id: ID!
    nombre: String!
    nacionalidad: String!
    genero: Genero
    cursos: [Curso]
}

enum Genero {
    MASCULINO
    FEMENINO
}
`
```

_shema/Curso.gql_
```
module.exports = `
# Curso del Sistema
type Curso {
    id: ID!
    titulo: String!
    # Descripcion del curso
    descripcion: String!
    profesor: Profesor
    rating: Float @deprecated(reason: "No creemos mas en los puntajes")
    comentarios: [Comentario]
}

type Comentario {
    id: ID!
    nombre: String!
    cuerpo: String!
}


`
```

## Mutaciones

Crearemos las mutaciones en rootQuery y agregaremos nuevos inputs segun la mutacion.

### Add

_rootQuery.gql_
```
type Mutation {
    profesorAdd(profesor: NuevoProfesor): Profesor
}
```

_Profesor.gql_
```
input NuevoProfesor {
    nombre: String!
    genero: Genero
    nacionalidad: String!
}
```

_resolvers_
```
const resolvers = {
    Mutation: {
        profesorAdd: (_, {profesor}) => {
            return Profesor.query().insert(profesor)
        }
    }
}
```

_graphiql_
```
mutation profesorAdd {
  profesorAdd(profesor: {
    nombre: "Arthaclarius"
    genero: MASCULINO
    nacionalidad: "Costa Rica"
  }){
    id
    nombre
    nacionalidad
    genero
  }
}

/*-------*/
{
  "data": {
    "profesorAdd": {
      "id": "13",
      "nombre": "Arthaclarius",
      "nacionalidad": "Costa Rica",
      "genero": "MASCULINO"
    }
  }
}
```


### Edit

_rootQuery.gql_
```
type Mutation {
    profesorEdit(id: Int!, profesor: ProfesorEditable): Profesor
}
```

_Profesor.gql_
```
input ProfesorEditable{
    nombre: String
    genero: Genero
    nacionalidad: String
}
```

_resolvers_
```
const resolvers = {
    Mutation: {
        profesorEdit: (_, {id, profesor}) => {
            return Profesor.query().patchAndFetchById(id, profesor)
        }
    }
}
```

_graphiql_
```
mutation EditProfesor {
  profesorEdit(id:1, profesor:{
    nombre: "Juan"
  }){
    id
    nombre
    nacionalidad
    genero
  }
}

/*-------*/
{
  "data": {
    "profesorEdit": {
      "id": "1",
      "nombre": "Juan",
      "nacionalidad": "Sudan",
      "genero": "MASCULINO"
    }
  }
}
```

### Delete

_rootQuery.gql_
```
type Mutation {
    profesorDelete(id: Int!):Profesor
}
```

_resolvers_
```
const resolvers = {
    Mutation: {
        profesorDelete(_,{id}){
            return Profesor.query().findById(id).then((profesor) => {
                return Profesor.query().deleteById(id).then(() => {
                    return profesor
                })
            })
        }
    }
}
```

_graphiql_
```
mutation profesorDelete {
  profesorDelete(id:11){
    id
    nombre
    nacionalidad
    genero
  }
}

/*-------*/
{
  "data": {
    "profesorDelete": {
      "id": "11",
      "nombre": "Stuard Carrillo",
      "nacionalidad": "Costa Rica",
      "genero": "MASCULINO"
    }
  }
}
```


## Busqueda

Podemos implementar una busqueda que nos traiga diferentes resultados utilizando union

_rootQuery.glq_
```
union ResultadoBusqueda = Profesor | Curso

type Query {
    cursos: [Curso]
    profesores: [Profesor]
    curso(id: Int): Curso
    profesor(id: Int): Profesor
    buscar(query: String!): [ResultadoBusqueda]
}
```

_resolvers_
```
const resolvers = {
    Query: {
        cursos: () => Curso.query().eager('[profesor, comentarios]'),
        profesores: () => Profesor.query().eager('cursos'),
        curso: (rootValue, args) => Curso.query().findById(args.id).eager('[profesor, comentarios]'),
        profesor: (rootValue, args) => Profesor.query().findById(args.id).eager('cursos'),

        buscar: (_, {query}) => {
            return [
                Profesor.query().findById(1),
                Curso.query().findById(1)
            ]
        }
    },
    ResultadoBusqueda: {
        __resolveType: (obj) => {
            if (obj.nombre) {
                return 'Profesor'
            }
            return 'Curso'
            console.log(obj)
        }
    }
}
```

_graphiql_
```
{
  buscar(query: "GraphQL"){
    ... on Profesor{
      nombre
    }
    ... on Curso{
      titulo
    }
  }
}

/*-------*/
{
  "data": {
    "buscar": [
      {
        "nombre": "Juan"
      },
      {
        "titulo": "qui ipsam"
      }
    ]
  }
}
```

## Errores

Podemos configurar como se muestra errores en nuestra aplicacion

_server_
```
app.use(
    '/graphql',
    bodyParse.json(), 
    graphqlExpress({
        schema,
        formatError: (error) => {
            return {
                name: error.name,
                message: error.message
            }
        }
    })
)
```

## GraphQL sin GraphiQL

De acuerdo con Apollo para poder hacer consultas a GraphQL utilizaremos la ruta asignada en ___server___ utilizando POST de la siguiente manera

```
{
    "query": "{ cursos { titulo } }"
}
```

# Clientes GraphQL

Es una libreria de JS que se ejecuta en el navegador.

* Queries mas faciles
* Caching
* Normalizacion de datos
* Optimistic UI
* Paginacion

## Apollo vs Relay

### Relay

- Creado por Facebook
- Cliente mas maduro
- Optimizado para alta performance
- Solo para React
- Estructura opininada
- Demasiado complejo para la mayoria de los casos

### Apollo

- Creado por Meteor
- API sencilla
- Funciona con cualquier framework JS
- Estructura flexible
- Adopcion incremental
- iOS y Android