# partition

### Divide el Observable fuente en dos, uno con los valores que cumplen una condición, y otro con los valores que no la cumplan

### Firma

`partition<T>(source: any, predicate: (value: T, index: number) => boolean, thisArg?: any): [Observable<T>, Observable<T>]`

### Parámetros

<table>
<tr><td>source</td><td>Tipo: <code>any</code>.</td></tr>
<tr><td>predicate</td><td> Una función que evalúa cada valor emitido por el Observable fuente. Si devuelve <code>true</code>, el valor se emite en el primer Observable del array retornado. Si devuelve <code>false</code>, el valor se emite en el segundo Observable del array. El parámetro <code>index</code> es el número <code>i</code> para la <code>i</code>-ésima emisión de la fuente que haya ocurrido desde la suscripción, comenzando con el número 0.</td></tr>
<tr><td>thisArg</td><td>Opcional. El valor por defecto es <code>undefined</code>.
Un argumento opcional para determinar el valor del <code>this</code> en la función <code>predicate</code>.</td></tr>
</table>

### Retorna

`[Observable<T>, Observable<T>]`: Un array con dos Observables: uno con valores que cumplen la función `predicate`, y otro con valores que no la cumplen.

### Descripción

Es como `filter`, pero retorna dos Observables: uno como el Observable resultante de `filter`, y otro con los valores que no han superado la condición.

<img src="assets/images/marble-diagrams/transformation/partition.png" alt="Diagrama de canicas del operador partition">

`partition` retorna un array con dos Observables que dividen los valores del Observable fuente según cumplan o no la condición especificada por la función `predicate`. El primer Observable del array emite los valores que sí cumplen la condición (la función devuelva `true`.) El segundo Observable emite los valores que no cumplan la condición (la función devuelva `false`.) El primer Observable se comporta como el operador `filter` y el segundo como el operador `filter` con la condición negada.

## Ejemplos

Dividir una serie de Pokémon en dos Observables, uno con los de tipo Agua y otro con los restantes

[StackBlitz](https://stackblitz.com/edit/rxjs-partition-1?file=index.ts)

```javascript
import { partition, from } from "rxjs";

const pokemon$ = from([
  { name: "Charmander", type: "Fire" },
  { name: "Squirtle", type: "Water" },
  { name: "Bulbasaur", type: "Grass" },
  { name: "Cyndaquil", type: "Fire" },
  { name: "Totodile", type: "Water" },
  { name: "Chikorita", type: "Grass" },
]);

const [waterPokemon$, miscellaneousPokemon$] = partition(
  pokemon$,
  ({ type }) => type === "Water"
);

// Emite los Pokémon de tipo agua
waterPokemon$.subscribe(console.log);
// Salida: { name: "Squirtle", type: "Water" }, { name: "Totodile", type: "Water" }

// Emite el resto de Pokémon
miscellaneousPokemon$.subscribe(console.log);
// Salida: { name: "Charmander", type: "Fire" }, { name: "Bulbasaur", type: "Grass" }, { name: "Cyndaquil", type: "Fire" }, { name: "Chikorita", type: "Grass" }
```

Dividir las peticiones realizadas con éxito y las peticiones fallidas en dos Observables distintos

[StackBlitz](https://stackblitz.com/edit/rxjs-partition-2?file=index.ts)

```javascript
import { catchError, concatMap, map } from "rxjs/operators";
import { ajax } from "rxjs/ajax";
import { of, partition } from "rxjs";

const filmId$ = of(
  "58611129-2dbc-4a81-a72f-77ddfc1b1b49",
  "voy-a-provocar-un-404",
  "2baf70d1-42bb-4437-b551-e5fed5a87abe"
).pipe(
  concatMap((id) => getGhibliFilms(id)),
  catchError((err) => of(err))
);

function getGhibliFilms(id: string) {
  return ajax(`https://ghibliapi.herokuapp.com/films/${id}`);
}

const [successfulRequest$, failedRequest$] = partition(
  filmId$,
  ({ status }) => status === 200
);

successfulRequest$.pipe(map(({ response }) => response)).subscribe(console.log);
// Salida: { title: "Castle in the Sky"... }, { title: "My Neighbor Totoro"... }

failedRequest$.subscribe(console.log);
// Salida: Error { message: "ajax error 404"... }
```

### Ejemplo de la documentación oficial

Divide una secuencia de números en dos Observables de números pares e impares

```javascript
import { of, partition } from "rxjs";

const observableValues = of(1, 2, 3, 4, 5, 6);
const [evens$, odds$] = partition(
  observableValues,
  (value, index) => value % 2 === 0
);

odds$.subscribe((x) => console.log("impares", x));
evens$.subscribe((x) => console.log("pares", x));

// Salida:
// impares 1
// impares 3
// impares 5
// pares 2
// pares 4
// pares 6
```

- [Documentación oficial en inglés](https://rxjs-dev.firebaseapp.com/api/index/function/partition)
- [Código fuente](https://github.com/ReactiveX/rxjs/blob/master/src/internal/operators/partition.ts)