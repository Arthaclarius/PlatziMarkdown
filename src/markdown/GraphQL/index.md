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

* [Node.js]()
* [Express]()
* [graphql-server]()
* [yarn](https://yarnpkg.com/lang/en/)
* [sqlite]()
