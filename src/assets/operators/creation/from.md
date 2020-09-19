# from

### Crea un Observable a partir de un Array, un objeto similar a un Array, una Promesa, un objeto iterable o un objeto similar a un Observable

<div class="fading-line"></div>

### Firma

`from<T>(input: any, scheduler?: SchedulerLike): Observable<T>`

### Parámetros

<table>
<tr><td>input</td><td>Tipo: <code>any</code></td></tr>
<tr><td>scheduler</td><td>Opcional. El valor por defecto es <code>undefined</code>Tipo:<code>SchedulerLike</code></td></tr>
</table>

### Retorna

`Observable<T>`: Un Observable que emite los argumentos descritos anteriormente y se completa.

<div class="fading-line"></div>

## Descripción

Convierte prácticamente cualquier elemento en un Observable.

<img class="marble-diagram" src="assets/images/marble-diagrams/creation/from.png" alt="Diagrama de canicas de from">

`from` convierte varios tipos de datos u objetos en Observables. También puede convertir una Promesa, un objeto similar a un Array o un objeto iterable en un Observable que emite los elementos de dicha Promesa, Array o iterable. Un String, en este contexto, se interpreta como un array de caracteres. Los objetos similares a Observables (contienen una función nombrada con el Símbolo ES2015 que corresponde a Observable) también se puede transformar mediante este operador.

## Ejemplos

Crear un Observable a partir de una cadena

[Stackblitz](https://stackblitz.com/edit/docu-rxjs-from?file=index.ts)

```javascript
import { from } from "rxjs";

const letter$ = from("RxJS mola");

letter$.subscribe(console.log);
// Salida: 'R', 'x', 'J', 'S', ' ', 'm', 'o', 'l', 'a'
```

Crear un Observable a partir de un Array de cadenas

[Stackblitz](https://stackblitz.com/edit/docu-rxjs-from-2?file=index.ts)

```javascript
import { from } from "rxjs";

const fruit$ = from(["Fresa", "Cereza", "Mora"]);

fruit$.subscribe((fruit) => console.log(fruit));
// Salida: Fresa, Cereza, Mora
```

Crear un Observable a partir de un `Map`

[Stackblitz](https://stackblitz.com/edit/docu-rxjs-from-3?file=index.ts)

```javascript
import { from } from "rxjs";

const pokemon$ = from(
  new Map([
    ["Squirtle", "Water"],
    ["Charmander", "Fire"],
    ["Bulbasur", "Grass"],
  ])
);

pokemon$.subscribe(console.log);
// Salida: ["Squirtle", "Water"], ["Charmander", "Fire"], ["Bulbasur", "Grass"]
```

Crear un Observable a partir de una promesa

[Stackblitz](https://stackblitz.com/edit/docu-rxjs-from-4?file=index.ts)

```javascript
import { from } from "rxjs";

const promise$ = from(Promise.resolve("Prometo empezar a aprender RxJS"));

promise$.subscribe(console.log);
// Salida: 'Prometo empezar a aprender RxJS'
```

Crear un Observable a partir de un `NodeList`

[Stackblitz](https://stackblitz.com/edit/docu-rxjs-from-5?file=index.html)

```javascript
import { from } from "rxjs";

const node$ = from(document.querySelectorAll("p"));

node$.subscribe((node) => console.log(node));
// Salida: HTMLParagraphElement {tagName: "p", attributes: {...}}
```

### Ejemplos de la documentación oficial

Convertir un array a un Observable

```javascript
import { from } from "rxjs";

const array = [10, 20, 30];
const result = from(array);

result.subscribe((x) => console.log(x));

// Salida:
// 10
// 20
// 30
```

Convertir un iterable infinito (a partir de un generador) a un Observable

```javascript
import { from } from "rxjs";
import { take } from "rxjs/operators";

function* generateDoubles(seed) {
  let i = seed;
  while (true) {
    yield i;
    i = 2 * i; // dóblalo
  }
}

const iterator = generateDoubles(3);
const result = from(iterator).pipe(take(10));

result.subscribe((x) => console.log(x));

// Salida:
// 3
// 6
// 12
// 24
// 48
// 96
// 192
// 384
// 768
// 1536
```

Con el planificador `asyncScheduler`

```javascript
import { from, asyncScheduler } from "rxjs";

console.log("Comienzo");

const array = [10, 20, 30];
const result = from(array, asyncScheduler);

result.subscribe((x) => console.log(x));

console.log("Fin");

// Salida:
// Comienzo
// Fin
// 10
// 20
// 30
```

## Recetas

## Recursos Adicionales

- [from](https://rxjs.dev/api/index/function/from) - Documentación oficial en inglés
- [Código fuente](https://github.com/ReactiveX/rxjs/blob/master/src/internal/observable/from.ts)