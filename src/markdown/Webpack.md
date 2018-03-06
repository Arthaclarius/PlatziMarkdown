Webpack
===
Module Bundler for modern JavaScript applications

**WP** es un Module Bundler for Modern JS Applications, es decir un empaquetador de módulos para el desarrollo de aplicaciones modernas en JavaScript.

[TOC]

![WebpackCompare](https://i.pinimg.com/564x/c8/93/25/c8932591ffd0f9c908f70589a182e177.jpg "WebpackCompare")

¿Por qué usar Webpack?

* Aunque hay otras alternativas WP es un la forma mas sofisticada para cargar y transformar módulos. WP trae todas las formas de importación de módulos, en resumen trae lo mejor de todos los mundos.

* Entrypoints - múltiples puntos de entrada a tus aplicaciones - archivos iniciales, tienes uno por cada pagina que vayas a usar.

* Puedes tener multiples entrypoints.

* OUTPUT. Si bien le decimos cual es el archivo fuente, debemos decirle que hacer con eso y donde ponerlo, porque no queremos mezclar los archivos finales que lee el navegador con los archivos fuente.

* Loaders. Nos ayudan a cargar todo tipo de formato de archivos.

* Plugins. nos ayudan a extender las caracteristicas de WP, por ejemplo comprimir archivos, dividir nuestros modulos en chunks, etc.

* WP es developer experience.

# Introducción y conceptos básicos de Webpack

## Instalación del entorno

* NodeJs & NPM
* Webpack

### Iniciamos proyecto
`npm init`

`npm i -D webpack webpack-cli`

## Creando el primer bundle

Crear archivo nuevo

_index.js_
```
console.log('Za Warudo')
```
Ejecutar

`npx webpack index.js --mode development`

o bien podemos crear un comando en `package.json`

```
"scripts": {
    "build": "npx webpack index.js --mode development"
  }
```

## Creando un webpack.config

_webpack.config.js_
```
const path = require('path')

module.exports = {
  entry: path.resolve(__dirname, 'index.js'),
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
}
```


_package.json_
```
  "scripts": {
    "build": "webpack --mode development"
  },
```

`npm run build`

## Cargando configuración de rutas relativas

Crearemos configuraciones para webpack con cualquier ruta

_./webpack_

_./webpack.config.js_
```
const path = require('path')

module.exports = {
  entry: path.join(__dirname, '../', 'index.js'),
  output: {
    filename: 'bundle.js',
    path: path.join(__dirname, '../', 'dist')
  }
}
```

_.package.json_
```
"scripts": {
    "build": "webpack --config ./webpack/webpack.config.js --mode development"
  },
```

## Loaders

Instalar los loaders que se requieren

`npm i -D style-loader css-loader`

Configuramos webpack para que soporte archivos css

```
const path = require('path')

module.exports = {
  ...
  module: {
    // Loaders
    rules: [
      {
        // test: Tipo de archivo que se va a reconocer "/\.js$/"
        // use: Cual Loader se va a encargar del archivo "name-loader"
        test: /\.css$/,
        use: [
          "style-loader",
          "css-loader"
        ]
      }
    ]
  }
}
```

Creamos nuestros archivos

_./index.html_
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
</head>
<body>
  <script src="dist/bundle.js"></script>
</body>
</html>
```

_./styles.css_
```
body {
  background: lightgoldenrodyellow;
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  margin: 0px;
  font-size: 40px;
}
```

Importamos el css a nuestro archivo JavaScript
_./index.js_
```
import './styles.css'

document.write("Za Warudo")

console.log('Za Warudo')
```

## Plugins

Con el uso de un plugin podemos extraer el css y asignarlo como un archivo css al html

Instalar Plugin

> Nota: El plugin "extract-text-webpack-plugin" no esta actualizado para la ultima version de webpack (4.*) a fecha de 26/2/2018


`npm i -D extract-text-webpack-plugin`

_webpack.config.js_
```
const path = require('path')

// Plugins
const ExtractTextPlugin = require("extract-text-webpack-plugin")

module.exports = {
  ...
  module: {
    // Loaders
    rules: [
      {
        // test: Tipo de archivo que se va a reconocer "/\.js$/"
        // use: Cual Loader se va a encargar del archivo "name-loader"
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          fallback: 'style-loader',
          use: 'css-loader'
        })
      }
    ]
  },
  plugins: [
    new ExtractTextPlugin("dist/styles.css")
  ]
}
```

Debemos asignar el styles.css al html

## Múltiples entry points

Para manipular multiples archivos debemos modificar el webpack de la siguiente manera

```
entry: {
    index: path.join(__dirname, '../', './src/js', 'index.js'),
    home: path.join(__dirname, '../', './src/js', 'home.js'),
    contact: path.join(__dirname, '../', './src/js', 'contact.js')
  },
  output: {
    filename: '[name].js',
    path: path.join(__dirname, '../', 'dist')
  },
```
De esta manera los multiples archivos que tenga en `./src/js` serán cargados y cada uno generara un bundle especifico respecto a su nombre.

Si deseamos cargarlos debemos hacer diferentes HTML'S que carguen cada uno de los bundles generados según su ruta


## Iniciando un servidor de desarrollo - Webpack DevServer

Cada vez que hacemos un cambios debemos ejecutar webpack para actualizar nuestro bundle y html. 

Utilizando un Watch o un Servidor de Desarrollo nos permitirá actualizar nuestros bundles y nos permita hacer refresh a nuestra pagina del navegador

Lo siguiente nos permitirá actualizar nuestro bundle cada vez que realizamos algún cambio

_package.json_
```
"build:dev": "webpack --config ./webpack/webpack.config.js --mode development --watch"
```

Ahora si deseamos actualizar el navegador directamente podemos utilizar

`npm i -D webpack-dev-server`

y cambiar la configuración de package.json

```
    "build:dev": "webpack-dev-server --config ./webpack/webpack.config.js --mode development"
```

Importante! se debe cambiar a donde apunta el html. Este debera apuntar al bundle generado desde la vista del `publicPath` el cual por default es `./dist/`

```
<script src="./bundle.js"></script>
```

# Personalizando la configuración con loaders y plugins

## Soporte de EcmaScript

Gracias a Babel es un copilador JavaScript. Podemos utilizar un Loader de Babel para webpack

`npm install babel-loader babel-core babel-preset-env webpack`

> Nota `babel-preset-env` puede ser cambiado por `babel-preset-es2016`

_webpack.config.js_
```
rules: [
      ...
      // Loader de babel
      {
        test: /\.js$/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ['es2016']
          }
        }
      }
    ]
```

Ahora podemos usar JS moderno

_index.js_
```
import '../css/styles.css'
import {delayMessage, firstMessage} from './message'



delayMessage()
document.write(firstMessage)
```
_message.js_
```
import renderToDOM from './renderToDom'

const waitTime = new Promise((res, rej) => {
  setTimeout(() => {
    res(true)
  }, 5000)
})


export const firstMessage = 'Za Warudo Exported'
export const delayMessage = async () => {
  const message = await waitTime
  
  const element = document.createElement('p')
  element.textContent = message

  renderToDOM(element)
}

```

Ahora podemos ver como en el documento se hace render del `firstMessage` y luego del `delayMessage` después de su Promise

## Soporte de Imágenes en Webpack

Para poder cargar assets en tus proyectos podemos usar [url-loader](https://github.com/webpack-contrib/url-loader)

`npm install --save-dev url-loader`

_webpack.config.js_
```
...
{
  test: /\.(jpg|png|gif)$/,
  use: {
    loader: "url-loader",
    options: {
      limit: 2 * 1024 * 1000
    }
  }
},
...
```

_index.js_
```
import undeadTimeFreeze from '../assets/undeadTimeFreeze.jpg'

document.body.style.background = `url(${undeadTimeFreeze}) no-repeat center center fixed`
document.body.style.color = "white"
```


## Soporte de fuentes en Webpack

Para utilizar fuentes tenemos la opcion de descargar fuentes desde [fontsquirrel](https://www.fontsquirrel.com/)

Podemos usar `url-loader` agregando la extension de las fuentes `eot|woff|ttf|svg`

_styles.css_
```
@font-face {
    font-family: 'OpenSans';
    src: url('../assets/OpenSans-Regular.eot') format('eot'),
         url('../assets/OpenSans-Regular.woff') format('woff'),
         url('../assets/OpenSans-Regular.svg') format('svg');
         url('../assets/OpenSans-Regular.ttf') format('ttf');
    font-weight: normal;
    font-style: normal;
}
```
## Soporte de vídeos

`npm install --save-dev file-loader`

_webpack.config.js_
```
{
  test: /\(mp4|web)$/,
  use: {
    loader: 'url-loader',
    options: {
      limit: 1 * 1024 * 1000,
      name: 'videos/[name].[hash].[ext]'
    }
  }
}
```

_index.js_
```
import videoFile from "../video/que-es-core.mp4"

const video = document.createElement('video')
imp.setAttribute('src', videoFile)
imp.setAttribute('width', 480)
document.body.append(video)
```

## Soportando archivos JSON

> Nota: JSON Loader no es requerido para webpack >= v4.0.0

[JSON Loader](https://github.com/webpack-contrib/json-loader)

`npm install --save-dev json-loader`

```
{
  test: /\.json$/,
  use: "json-loader"
},
```

_index.js_
```
import dataTeachers from '../data/teachers.json'

console.log(dataTeachers)
```


## Configuración para React.js

Para dar soporte a los archivos `.jsx` podemos usar la babel para react

[Babel React](https://babeljs.io/docs/plugins/preset-react/)

`npm install --save-dev babel-cli babel-preset-react`

Ahora debemos instalar React

`npm i -s react react-dom`

_webpack.config.js_
```
{
  test: /\.jsx?$/,
  use: {
    loader: "babel-loader",
    options: {
      presets: ['es2016', 'react']
    }
}
```

_TeachersComponent.jsx_
```
import React, { Component } from 'react'

export class Teacher extends Component {
  render() {
    return (
      <li className="Teacher"><a href={this.props.url}></a>{this.props.children}</li>
    )
  }
}

export class Teachers extends Component {
  render() {
    return (
      <ul>
        {
          this.props.teachers.map((val, key) => {
            return (
              <Teacher key={key} url={val.twitter}>{val.name}</Teacher>
            )
          })
        }
      </ul>
    )
  }
}
```

_index.js_
```
import {
  render
} from "react-dom"
import React from 'react'
import dataTeachers from '../data/teachers.json'
import {Teachers} from "./TeachersComponent.jsx"

const appReactEl = document.createElement("div")
appReactEl.setAttribute("id", "app-react")
document.body.appendChild(appReactEl)
render(<Teachers teachers={dataTeachers.teachers}></Teachers>, document.getElementById("app-react"))
```


## Soportando Estilos

### Estilos con Sass

[Sass Loader](https://github.com/webpack-contrib/sass-loader)

`npm install sass-loader node-sass webpack --save-dev`

_webpack.config.js_
```
{
  test: /\.scss$/,
  use: [
    "style-loader",
    "css-loader",
    "sass-loader"
  ]
},
```

_teachers.scss_
```
$borderWidth: 2px + 3px;
$padding: $borderWidth * 2;

.Teachers {
  border: $borderWidth dashed orange;
  background: gray;
  
  li {
    padding: $padding;
    color: white;
  }
}
```

_TeachersComponent.jsx_
```
import '../css/teachers.scss'
...
```

### Estilos con Styles

[Stylus Loader](https://github.com/shama/stylus-loader)

`npm install stylus-loader stylus --save-dev`

> También podemos instalar nib y rupture que nos ayudaran a extender las posibilidades de css

`npm i -D nib rupture`

_webpack.config.js_
```
{
  test: /\.styl$/,
  use: [
    "style-loader",
    "css-loader",
    {
      loader: "stylus-loader",
      options: {
        use: [
          require('nib'),
          require('rupture')
        ],
        // Estas importaciones nos permitirán usar configuraciones de css que funcionen en todos los navegadores
        // ~ Es el para acceder a node_modules
        import: [
          '~nib/lib/nib/index.styl',
          '~rupture/rupture/index.styl'
        ]
      }
    }
  ]
},
```

_teachers.styl_
```
.Teacher
  border 1px solid red
  display flex
  background green
  color: white
  +below(768px)
    background: blue

```

### Estilos con Less

[Less Loader](https://github.com/webpack-contrib/less-loader)

`npm install --save-dev less-loader less`

_webpack.config.js_
```
{
  test: /\.less$/,
  use: [
    "style-loader",
    "css-loader",
    {
      loader: "less-loader",
      options: {
        noIeCompat: true,
      }
    }
  ]
},
```

_Teachers.less_
```
@nice-blue: #5B83AD;
@light-blue: @nice-blue + #111;

#header {
  background: @nice-blue;
}
```

### Estilos con PostCSS

PostCSS es un Post-procesador de css y la forma moderna de hacerlo

[PostCSS](http://postcss.org/)

[PostCSS Platzi](https://platzi.com/cursos/postcss/)

[PostCSS Loader](https://github.com/postcss/postcss-loader)

`npm i -D postcss-loader postcss-cssnext`

_webpack.config.js_
```
{
  test: /\.css$/,
  use: [
    "style-loader",
    {
      loader: 'css-loader',
      options: {
        module: true,
        importLoader: 1
      }
    },
    "postcss-loader"
  ]
},
```

_postcss.config.js_
```
module.exports = {
  plugins: {
    'postcss-cssnext': {}
  }
}
```

# Conceptos avanzados de Webpack

## Prevenir código duplicado

Cuando? Cuando se tenga muchos entry points se da que cada bundle va a tener código duplicado, React se va a duplicar, Angular se va a duplicar etc.

> Webpack >= 4.0.0

```
entry: {
  index: path.join(__dirname, '../', './src/js', 'index.js'),
  home: path.join(__dirname, '../', './src/js', 'home.js')
},
output: {
  filename: 'bundle.[name].js',
  path: path.join(__dirname, '../', 'dist')
},
...
optimization: {
  splitChunks: {
    name: "common",
    chunks: "initial"
  }
}
```

> Webpack < 4.0.0
```
const webpack = require('webpack')
...
entry: {
  index: path.join(__dirname, '../', './src/js', 'index.js'),
  home: path.join(__dirname, '../', './src/js', 'home.js')
},
output: {
  filename: 'bundle.[name].js',
  path: path.join(__dirname, '../', 'dist')
},
...
plugins: [
  new webpack.optimize.CommonsChunkPlugin({
    name: 'common'
  })
]
...
```

Al final habrán 3 archivos, los 2 entry points y un common.js donde estará el "código repetido"

## Eligiendo dependencias comunes

_webpack.config.js_
```
entry: {
  vendor: [
    'react',
    'react-dom'
  ],
...
  {
    name: "vendor",
    chunks: "initial",
    minChunks: Infinity,
  }
...
```

## Optimizando el paquete de dependencias comunes

Para evitar que los archivos comunes no se generen cada vez que hacemos build utilizaremos [DLL](https://webpack.js.org/plugins/dll-plugin/)

Creamos un nuevo webpack config

_webpack.config.dll.js_
```
const path = require('path')
const webpack = require('webpack')

module.exports = {
  entry: {
    modules: [
      'react',
      'react-dom'
    ]
  },
  output: {
    filename: '[name].dll.js',
    path: path.join(__dirname, '../', 'dist'),
    library: '[name]'
  },
  plugins: [
    new webpack.DllPlugin({
      name: '[name]',
      path: path.join(__dirname, '[name]-manifest.json')
    })
  ]
}
```
> __Explicación:__ En el entry se pondrá los módulos que no se actualizaran para solo tener que ejecutarlos una vez. Library en el output definirá el nombre del modulo generado. Con el plugin definiremos como se llamara el dll y su archivo manifiesto que sera un `json` con las rutas

> Dato Curioso: Si le ponen “module” al entry point les dará el error `__webpack_require__(...) is not a function`

nuevo comando para package.json

`"build:dll": "webpack --config ./webpack/webpack.config.dll.js --mode development",`


_webpack.config.js_
```
const path = require('path')
const webpack = require('webpack')

module.exports = {
  entry: {
    index: path.join(__dirname, '../', './src/js', 'index.js'),
    home: path.join(__dirname, '../', './src/js', 'home.js')
  },
  output: {
    filename: 'bundle.[name].js',
    path: path.join(__dirname, '../', 'dist')
  },
  devServer: {...},
  module: {...},
  plugins: [
    new webpack.DllReferencePlugin({
      manifest: require('./modules-manifest.json')
    })
  ]
}
```
> __Explicación:__ Ya no necesitaremos los Chunks ya que se tomo los archivos repetidos en un `dll`. Ahora debemos importarlos con `DllReferencePlugin` requiriendo el archivo manifiesto generado por del dll

_index.html_
```
<script src="./dist/modules.dll.js"></script>
<script src="./dist/bundle.index.js"></script>
```

## Cargando módulos asincronamente

Si realizamos lo siguiente

_index.js_
```
const $button = document.getElementById("dynamic-import");
$button.addEventListener("click", async () => {
  const alerta = await import('./alerta')
  alerta()
})
```
> Se da por alto de que el modulo `alerta` exista y sea una función y que existe un elemento con el id "dynamic-import"

Tendremos un error
```
ERROR in ./src/js/index.js
Module build failed: SyntaxError: C:/Users/sgcg5/OneDrive/Documentos/GitHub/Webpack-Platzi/src/js/index.js: Unexpected token (18:23)

  16 | const $button = document.getElementById("dynamic-import");
  17 | $button.addEventListener("click", async () => {
> 18 |   const alerta = await import('./alerta')
     |                        ^
  19 |   alerta()
  20 | })
  21 |
```

Quiere decir que requerimos de un plugin de `babel`. [Syntax Dynamic Import](https://babeljs.io/docs/plugins/syntax-dynamic-import/)

`npm install --save-dev babel-plugin-syntax-dynamic-import`

_webpack.config.js_
```
{
  test: /\.jsx?$/,
  use: {
    loader: "babel-loader",
    options: {
      presets: ['es2016', 'react'],
      plugins: ['syntax-dynamic-import'] //<---
    }
  }
}
```

