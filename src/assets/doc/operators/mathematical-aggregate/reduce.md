<div class="page-heading">

# reduce

<a target="_blank" href="https://github.com/ReactiveX/rxjs/blob/master/src/internal/operators/reduce.ts">
<svg>
  <use xlink:href="/assets/icons/github.svg#github"></use>
</svg>
</a>
</div>

<h2 class="subtitle"> Aplica una función acumuladora al Observable fuente y retorna el resultado acumulado una vez se completa la fuente</h2>

💡 Si se necesita el valor acumulado en cada emisión, se puede utilizar [scan](/operators/transformation/scan)

### Firma

`reduce<T, R>(accumulator: (acc: T | R, value: T, index?: number) => T | R, seed?: T | R): OperatorFunction<T, T | R>`

### Parámetros

<table>
<tr><td>accumulator</td><td>La función acumuladora que se llama por cada valor de la fuente.</td></tr>
<tr><td>seed</td><td>Opcional. El valor por defecto es <code>undefined</code>.
El valor de acumulación inicial.</td></tr>
</table>

### Retorna

`OperatorFunction<T, T | R>`: Un Observable que emite un solo valor, resutante de haber acumulado los valores emitidos por el Observable fuente.

## Descripción

Combina todos los valores emitidos por la fuente mediante una función acumuladora, que indica cómo unir cada nuevo valor al valor acumulado.

<img src="assets/images/marble-diagrams/mathematical-aggregate/" alt="Diagrama de canicas del operador reduce">

Al igual que `Array.prototype.reduce()`, el operador `reduce` aplica una función acumuladora a cada valor emitido por el Observable fuente para reducirlos a un único valor, que se emite en el Observable resultante. Se debe tener en cuenta que `reduce` solo emite un valor, cuando el Observable fuente se completa. Es equivalente a utilizar el operador `scan` junto al operador `last`.

Retorna un Observable que aplica la función acumuladora especificada a cada elemento emitido por el Observable fuente. Si se especifica un valor `seed`, se utilizará dicho valor como valor inicial para la acumulación inicial. Si no se especificara un valor inicial, se utilizará el primer elemento de la fuente como tal.

## Ejemplos

Count the number of click events that happened in 5 seconds

```javascript
import { fromEvent, interval } from "rxjs";
import { reduce, takeUntil, mapTo } from "rxjs/operators";

const clicksInFiveSeconds = fromEvent(document, "click").pipe(
  takeUntil(interval(5000))
);
const ones = clicksInFiveSeconds.pipe(mapTo(1));
const seed = 0;
const count = ones.pipe(reduce((acc, one) => acc + one, seed));
count.subscribe((x) => console.log(x));
```

## Sobrecargas

`reduce(accumulator: (acc: R, value: T, index: number) => R, seed: R): OperatorFunction<T, R>`

### Parámetros

<table>
<tr><td>accumulator</td><td>Tipo: <code>(acc: R, value: T, index: number) => R</code>.</td></tr>
<tr><td>seed</td><td>Tipo: <code>R</code>.</td></tr>
</table>

### Retorna

`OperatorFunction<T, R>`

`reduce(accumulator: (acc: T, value: T, index: number) => T, seed?: T): MonoTypeOperatorFunction<T>`

### Parámetros

<table>
<tr><td>accumulator</td><td>Tipo: <code>(acc: T, value: T, index: number) => T</code>.</td></tr>
<tr><td>seed</td><td>Opcional. El valor por defecto es <code>undefined</code>.
Tipo: <code>T</code>.</td></tr>
</table>

### Retorna

`MonoTypeOperatorFunction<T>`

`reduce(accumulator: (acc: R, value: T, index: number) => R): OperatorFunction<T, R>`

### Parámetros

<table>
<tr><td>accumulator</td><td>Tipo: <code>(acc: R, value: T, index: number) => R</code>.</td></tr>
</table>

### Retorna

`OperatorFunction<T, R>`

## Recursos adicionales

- [Documentación oficial en inglés](https://rxjs-dev.firebaseapp.com/api/operators/reduce)
