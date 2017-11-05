Angular
=======

Angular es un framework basado en JavaScript que nos permite crear Single Page Applications, lo que significa que tu navegador nunca te va a redirigir a otra página, sino que Angular y su ruteador se van a encargar de eso.

___Versiones de Angular___

- __Angular JS:__ versión inicial del Framework.
- __Angular 2:__ Revisión completa del Framework. Se desecho AngularJS y se reescribió de nuevo. Esto porque AngularJS tenía errores. Literalmente era más sencillo crear un nuevo Framework que corregir el que ya se tenía.
- __Angular 4:__  primer update mayor de Angular 2.

# Typescript

Typescript, es un superset de JS, nos agrega características como tipos, clases e interfaces. Los programas que escribimos en Typescript son transitados a código JS.

Es un agregado a JavaScript, no lo reemplaza. Habrá algunas tareas que TypeScript no pueda hacer entonces JavaScript se encargará de ello.

Agrega nuevas características como:

- Tipos.
- Clases.
- Interfaces.

_TypeScript es transpilado (Lo traduce y lo compila) a Javascript._

Esto es porque la mayoría de los navegadores no entiende TypeScript. Pero sí JavaScript. Por lo cual el navegador recibirá JavaScript. El transpilado se hace de forma automática.

Es el lenguaje en el que programaremos para Angular 4.

# Proyecto

## Herramientas

- Navegador web
- Editor de texto
- Node.js
- Angular CLI

## Angular CLI

Es un set de herramientas de consola que usaremos en nuestrso proyectos de Angular4

```
npm install -g @angular/cli

ng new name-app

cd name-app

ng server
```

## Modulos y Componentes

Los modulos son las piezas mas grandes en las que se puede dividir una aplicación de Angular 4, son piezas que podemos reutilizar dentro de nuestro proyecto pero también podemos usarlas en otros proyectos.
Angular nos ofrece módulos nativos como HTTP, Browser, Forms, Reactive Forms.

En la aplicacion tenemos el archivo `app.module.ts` en la carpeta app donde añadiremos los componentes de nuestra aplicacion

_app.module.ts_
```
@NgModule({
  declarations: [
    AppComponent,
    .../*Componentes Custom*/
  ],
  imports: [
    BrowserModule,
    /*Componentes Nativos*/
  ],
  providers: [],
  bootstrap: [AppComponent/*Componentes al iniciar*/]
})
```

Los componentes se definen con un ts y un template html

```
@Component({
  selector: 'app-root',/*En cual etiqueta se inserta*/
  templateUrl: './app.component.html',/*Direccion del Template*/
  styleUrls: ['./app.component.css'] /*Estilos*/
})
export class AppComponent {
  title = 'PlatziSquare';
}
```

## Data Binding

### ¿Qué es Data Binding?

Comunicación entre tú código TypeScript y el HTML.

Lo único que al cliente (La empresa que nos contrató) le importa es lo que ve, así que comunicar nuestra lógica (TypeScript) al template (HTML) es de suma importancia. Nuestro proyecto debe de estar presentable y hacerle sentir al cliente que todo va perfecto usando Data Binding.

### Tipos de Data Binding en Angular 4.

#### String Interpolation
__{{}} _TypeScript => HTML___
Tener información (Variable, Array, por ejemplo) y presentarla a los usuarios en modo de HTML.

#### Property Binding
__[] _TypeScript <= HTML___ 
Información del lado de HTML que puede ser por ejemplo, información que el usuario ingrese o que nosotros pongamos un valor por defecto. Viajando la información de HTML a TypeScript.

#### Event Binding
__[] _TypeScript <= HTML___
Escuchar eventos desde HTML y pasarlo a TypeScript.

#### Two Way Data Binding
__[()] _TypeScript <=> HTML___
Comunicación de dos Vías. De lo que el cliente ve a TypeScript como de TypeScript hacia lo que el cliente ve.

