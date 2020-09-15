# isEmpty

### Emite _false_ si el Observable emite cualquier valor, o emite _true_ si el Observable se completa sin emitir ningún valor

### Firma

`isEmpty<T>(): OperatorFunction<T, boolean>`

### Parámetros

No recibe ningún parámetro.

### Retorna

`OperatorFunction<T, boolean>`: Un Observable de valor booleano indicando si el Observable estaba vacío o no.

## Descripción

Indica si un Observable emite algún valor o no.

<img src="" alt="Diagrama de canicas del operador isEmpty">

`isEmpty` transforma un Observable que emite valores en un Observable que emite un solo valor booleano representando si el Observable fuente emite o no valores. En cuanto el Observable fuente emita un valor, `isEmpty` emitirá _false_ y se completará. Si el Observable fuente se completa sin haber emitido ningún valor, `isEmpty` emitirá _true_ y se completará.

Se podría lograr un efecto similar con el operador `count`, pero `isEmpty` puede emitir el valor _false_ antes.

## Ejemplos

[StackBlitz](https://stackblitz.com/edit/rxjs-isempty?file=index.ts)

Emite _false_ para un Observable que no está vacío

```javascript
import { of } from "rxjs";
import { isEmpty } from "rxjs/operators";

const word$ = of("No", "está", "vacío");

word$.pipe(isEmpty()).subscribe(console.log);
// Salida: false
```

Emite _true_ para Observables vacíos

[StackBlitz](https://stackblitz.com/edit/rxjs-isempty-2?file=index.ts)

```javascript
import { EMPTY, of } from "rxjs";
import { isEmpty } from "rxjs/operators";

const empty$ = EMPTY;
const anotherEmpty$ = of();

empty$.pipe(isEmpty()).subscribe(console.log);
// Salida: true

anotherEmpty$.pipe(isEmpty()).subscribe(console.log);
// Salida: true
```

### Ejemplo de la documentación oficial

Emite _false_ para un Sujeto que no está vacío

[StackBlitz](https://stackblitz.com/run?devtoolsheight=50)

```javascript
    import { Subject } from 'rxjs';
    import { isEmpty } from 'rxjs/operators';

    const source = new Subject<string>();
    const result = source.pipe(isEmpty());
    source.subscribe(x => console.log(x));
    result.subscribe(x => console.log(x));
    source.next('a');
    source.next('b');
    source.next('c');
    source.complete();

    // Salida: a, false, b, c
```

- [Documentación oficial en inglés](https://rxjs-dev.firebaseapp.com/api/operators/isEmpty)
- [Código fuente](https://github.com/ReactiveX/rxjs/blob/master/src/internal/operators/isEmpty.ts)