# catchError

### Captura errores en el Observable que se manejan devolviendo un Observable nuevo o lanzando un error

### Firma

`catchError<T, O extends ObservableInput<any>>(selector: (err: any, caught: Observable<T>) => O): OperatorFunction<T, T | ObservedValueOf<O>>`

### Parámetros

<table>
<tr><td>selector</td>Una función que recibe como argumentos <code>err</code>, que es el error, y <code>caught</code>, que es el Observable fuente, por si se quiere "reiniciar" el Observable devolviéndolo otra vez. El Observable que se retorne por el selector es el que se utilizará para continuar la cadena Observable.<td></td></tr>
</table>

### Retorna

`OperatorFunction<T, T | ObservedValueOf<O>>`: Un Observable que se puede originar en el Observable fuente o en el Observable retornado por la función `selector`.

## Descripción

`catchError` captura errores en el Observable fuente, manejándolos de dos maneras posibles: bien devolviendo un Observable nuevo o bien lanzando un nuevo error.

<img src="assets/images/marble-diagrams/error-handling/catchError.png" alt="Diagrama de canicas del operador catch">

## Ejemplos

Capturar un error, retornando un Observable

[StackBlitz](https://stackblitz.com/edit/docu-rxjs-catcherror?file=index.ts)

```javascript
import { throwError, of } from "rxjs";
import { catchError } from "rxjs/operators";

const error$ = throwError("¡Oh no!");

error$
  .pipe(catchError((error) => of(`Error capturado grácilmente: ${error}`)))
  .subscribe(console.log);
// Salida: Error capturado grácilmente: ¡Oh no!
```

Capturar un error y lanzar otro error

[StackBlitz](https://stackblitz.com/edit/docu-rxjs-catcherror-2?file=index.ts)

```javascript
import { throwError, of } from "rxjs";
import { catchError } from "rxjs/operators";

const error$ = throwError("Oh no!");

error$
  .pipe(
    catchError((error) => {
      throw `Lanzando un nuevo error: ${error}`;
    })
  )
  .subscribe(console.log, console.error);
// Salida: (Error) Lanzando un nuevo error: Oh no!
```

Al capturar los errores que ocurren en un Observable interno (un Observable emitido por un Observable de orden superior), se debe tener cuidado a la hora de utilizar el operador `catchError` ya que, si se coloca en el sitio equivocado, el flujo del Observable fuente no seguirá ejecutándose tras capturar el error.

A continuación, se puede ver cómo el uso incorrecto de `catchError` hará que, después de capturar el error que devuelve la primera petición, el flujo se completará y no se harán las otras dos peticiones restantes:

[StackBlitz](https://stackblitz.com/edit/docu-rxjs-catcherror-inner?file=index.ts)

```javascript
import { map, concatMap, catchError } from "rxjs/operators";
import { ajax } from "rxjs/ajax";
import { of } from "rxjs";

const pokemonId$ = of(-3, 5, 6);

function getPokemonName(id: number) {
  return ajax
    .getJSON(`https://pokeapi.co/api/v2/pokemon/${id}`)
    .pipe(map(({ name }) => name));
}

pokemonId$
  .pipe(
    concatMap((id) => getPokemonName(id)),
    catchError((error) => of(`¡Oh no, ha ocurrido un error! ${error}`))
  )
  .subscribe(console.log, console.error, () => console.log("Completado"));
// Salida: ¡Oh no, ha ocurrido un error! AjaxError: ajax error 404, Completado
```

Sin embargo, si se utiliza `catchError` en el Observable interno, el comportamiento es el que se busca: cuando falle la primera petición, se capturará el error y el flujo seguirá ejecutándose, realizando las dos peticiones restantes:

[StackBlitz](https://stackblitz.com/edit/docu-rxjs-catcherror-inner-2?file=index.ts)

```javascript
import { map, concatMap, catchError } from "rxjs/operators";
import { ajax } from "rxjs/ajax";
import { of } from "rxjs";

const pokemonId$ = of(-3, 5, 6);

function getPokemonName(id: number) {
  return ajax
    .getJSON(`https://pokeapi.co/api/v2/pokemon/${id}`)
    .pipe(map(({ name }) => name));
}

pokemonId$
  .pipe(
    concatMap((id) =>
      getPokemonName(id).pipe(catchError((error) => of(`¡Oh no! ${error}`)))
    )
  )
  .subscribe(console.log, console.error, () => console.log("Completado"));
// Salida: ¡Oh no, ha ocurrido un error!, charmeleon, charizard, Completado
```

### Ejemplos de la documentación oficial

Continuar con un Observable diferente cuando ocurre un error

```javascript
import { of } from "rxjs";
import { map, catchError } from "rxjs/operators";

of(1, 2, 3, 4, 5)
  .pipe(
    map((n) => {
      if (n === 4) {
        throw "four!";
      }
      return n;
    }),
    catchError((err) => of("I", "II", "III", "IV", "V"))
  )
  .subscribe((x) => console.log(x));
// 1, 2, 3, I, II, III, IV, V
```

Reiniciar el Observable fuente en caso de error, parecido al operador retry()

```javascript
import { of } from "rxjs";
import { map, catchError, take } from "rxjs/operators";

of(1, 2, 3, 4, 5)
  .pipe(
    map((n) => {
      if (n === 4) {
        throw "four!";
      }
      return n;
    }),
    catchError((err, caught) => caught),
    take(30)
  )
  .subscribe((x) => console.log(x));
// 1, 2, 3, 1, 2, 3...
```

Lanza un error nuevo cuando el Observable fuente lanza un error

```javascript
import { of } from "rxjs";
import { map, catchError } from "rxjs/operators";

of(1, 2, 3, 4, 5)
  .pipe(
    map((n) => {
      if (n === 4) {
        throw "four!";
      }
      return n;
    }),
    catchError((err) => {
      throw "error en la fuente. Detalles: " + err;
    })
  )
  .subscribe(
    (x) => console.log(x),
    (err) => console.log(err)
  );
// 1, 2, 3, error en la fuente. Detalles: four!
```

- [Documentación oficial en inglés](https://rxjs-dev.firebaseapp.com/api/operators/catchError)
- [Código fuente](https://github.com/ReactiveX/rxjs/blob/master/src/internal/operators/catchError.ts)