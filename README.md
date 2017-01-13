# clean-code-javascript

## Tabla de Contenidos
  1. [Introducción](#introduccion)
  2. [Variables](#variables)
  3. [Funciones](#funciones)
  4. [Objetos y Estructura de Datos](#objetos-y-estructura-de-datos)
  5. [Clases](#clases)
  6. [Testing](#testing)
  7. [Concurrency](#concurrency)
  8. [Error Handling](#error-handling)
  9. [Formatting](#formatting)
  10. [Comments](#comments)

## Introducción
![Imagen humorística de la estimación de la calidad del software como un recuento de cuantos WTFs al leer el código](http://www.osnews.com/images/comics/wtfm.jpg)

Principios de ingeniería, del libro de Robert C. Martin [*Clean Code*](https://www.amazon.com.mx/C%C3%B3digo-limpio-Clean-code-Craftsmanship/dp/8441532109),
adaptado para JavaScript . Esto no es una guía de estilo. Es una guía para producir software legible, reutilizable y facil de refactorizar en JavaScript.

No todos los principios deben seguirse estrictamente, y aún menos tomarlos como principios universalmente acordados. Estas son directrices y nada más, pero han sido
Codificados durante muchos años de experiencia colectiva por los autores de * Código Limpio *.

Nuestro oficio de ingeniería de software tien un poco más de 50 años de edad, y todavía estamos aprendiendo mucho. Cuando la arquitectura de software sea tan antigua como la propia arquitectura, tal vez entonces tendremos reglas más difíciles de seguir. Por ahora, deje que estas directrices sirvan como piedra angular para evaluar la calidad del código JavaScript que usted y su equipo producen.

Una cosa más: saber esto no lo hará de inmediato un mejor desarrollador de software, y trabajar con ellos durante muchos años no significa que no cometa errores. Cada pedazo de código comienza como un primer bosquejo, como la arcilla mojada formandose en su forma final. Finalmente, tallamos las imperfecciones cuando las revisamos con nuestros compañeros. No te hagas daño por los primeros borradores que necesitan mejoras. ¡Dale una paliza al código en su lugar!

## **Variables**
### Utilizar nombres de variables significativos y pronunciables

**Mal:**
```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**Bien**:
```javascript
const yearMonthDay = moment().format('YYYY/MM/DD');
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Usa el mismo vocabulario para el mismo tipo de variable

**Mal:**
```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Bien**:
```javascript
getUser();
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Utiliza nombres se puedan buscar
Leeremos más código de lo que escribiremos. Es importante que el código que escribimos sea legible y pueda ser consultado. Al no nombrar variables que terminen siendo significativas para entender nuestro programa, lastimamos a nuestros lectores.
Haga que sus nombres se puedan buscar. Herramientas como [buddy.js](https://github.com/danielstjules/buddy.js) y [ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)
Pueden ayudar a identificar constantes sin nombre.

**Mal:**
```javascript
// Para que diablos es 86400000 ?
setTimeout(() => {
  this.blastOff();
}, 86400000);

```

**Bien**:
```javascript
// Declare them as capitalized `const` globals.
const MILLISECONDS_IN_A_DAY = 86400000;

setTimeout(() => {
  this.blastOff();
}, MILLISECONDS_IN_A_DAY);

```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Utilice variables explicativas
**Mal:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityStateRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityState(address.match(cityStateRegex)[1], address.match(cityStateRegex)[2]);
```

**Bien**:
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityStateRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [, city, state] = address.match(cityStateRegex);
saveCityState(city, state);
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Evitar Mapeo Mental
Explícito es mejor que implícito.

**Mal:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((l) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Wait, what is `l` for again?
  dispatch(l);
});
```

**Bien**:
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((location) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### No agregue contexto innecesario
Si su nombre de clase / objeto le dice algo, no lo repita en su nombre de variable.

**Mal:**
```javascript
const Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
};

function paintCar(car) {
  car.carColor = 'Red';
}
```

**Bien**:
```javascript
const Car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
};

function paintCar(car) {
  car.color = 'Red';
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Usar argumentos predeterminados en lugar de enredaderes o condicionales

**Mal:**
```javascript
function createMicrobrewery(name) {
  const breweryName = name || 'Hipster Brew Co.';
  // ...
}

```

**Bien**:
```javascript
function createMicrobrewery(breweryName = 'Hipster Brew Co.') {
  // ...
}

```
**[⬆ volver arriba](#tabla-de-contenidos)**

## **Funciones**
### Argumentos de la función (2 o menos idealmente)
Limitar la cantidad de parámetros de la función es increíblemente importante porque facilita la comprobación de su función. Tener más de tres argumentos conduce a una explosión combinatoria donde hay que probar toneladas de casos diferentes con cada argumento separado.

Cero Argumentos es el caso ideal. uno o dos argumentos esta bien, Y tres deben ser evitados. Cualquier cosa más que eso debería ser consolidado. Por lo general, si tiene más de dos argumentos entonces tu función está tratando de hacer demasiado. En los casos en que no lo es, la mayoría de las veces un objeto de nivel superior será suficiente como argumento.

Dado que JavaScript nos permite hacer objetos sobre la marcha, sin muchas clases
boilerplate, puedes usar un objeto si te encuentras necesitando muchos argumentos.

**Mal:**
```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
```

**Bien**:
```javascript
const menuConfig = {
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
};

function createMenu(config) {
  // ...
}

```
**[⬆ volver arriba](#tabla-de-contenidos)**


### Las funciones deben hacer solo una cosa
Esta es con mucho la regla más importante en la ingeniería de software. Cuando las funciones hacen mas de una cosa, son dificiles de hacer, testear, y entender.
Cuando se puede aislar una función con una sola acción, Pueden ser refactorizados fácilmente y su código leerá mucho más limpio. Si no toma nada más de esta guía mas que esto, usted estará delante de muchos desarrolladores.

**Mal:**
```javascript
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Bien**:
```javascript
function emailClients(clients) {
  clients
    .filter(isClientActive)
    .forEach(email);
}

function isClientActive(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Los nombres de las funciones deben decir lo que hacen

**Mal:**
```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// It's hard to to tell from the function name what is added
addToDate(date, 1);
```

**Bien**:
```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Las funciones deben ser sólo un nivel de abstracción
Cuando tienes mas de un nivel de abstración tu funcion esta haciendo demasido
Cuando tienes más de un nivel de abstracción, tu función normalmente está haciendo demasiado. La división de funciones conduce a la reutilización y la prueba más fácil.

**Mal:**
```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex...
  });

  ast.forEach((node) => {
    // parse...
  });
}
```

**Bien**:
```javascript
function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push( /* ... */ );
    });
  });

  return tokens;
}

function lexer(tokens) {
  const ast = [];
  tokens.forEach((token) => {
    ast.push( /* ... */ );
  });

  return ast;
}

function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const ast = lexer(tokens);
  ast.forEach((node) => {
    // parse...
  });
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Elimina código duplicado
Nunca, nunca, bajo ninguna circunstancia, tenga código duplicado. No hay razón para ello y es muy posiblemente el peor pecado que puede cometer como un desarrollador profesional. El código duplicado significa que hay más de un lugar para alterar algo si necesita cambiar alguna lógica. JavaScript no fuertemente tipado, por lo que hace tener funciones genéricas bastante fácil. ¡Aprovéchate de eso! Herramientas como [jsinspect](https://github.com/danielstjules/jsinspect) Puede ayudarle a encontrar código duplicado elegible para la refactorización.

**Mal:**
```javascript
function showDeveloperList(developers) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Bien**:
```javascript
function showList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    let portfolio = employee.getGithubLink();

    if (employee.type === 'manager') {
      portfolio = employee.getMBAProjects();
    }

    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Establecer objetos predeterminados con Object.assign

**Mal:**
```javascript
const menuConfig = {
  title: null,
  body: 'Bar',
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || 'Foo';
  config.body = config.body || 'Bar';
  config.buttonText = config.buttonText || 'Baz';
  config.cancellable = config.cancellable === undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Bien**:
```javascript
const menuConfig = {
  title: 'Order',
  // User did not include 'body' key
  buttonText: 'Send',
  cancellable: true
};

function createMenu(config) {
  config = Object.assign({
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
  }, config);

  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```
**[⬆ volver arriba](#tabla-de-contenidos)**


### No utilice banderas como parámetros de función
Las banderas indican a su usuario que esta función hace más de una cosa.Las funciones deben hacer una cosa. Divida sus funciones si están siguiendo diferentes rutas de código basadas en un booleano.

**Mal:**
```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Bien**:
```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Evite los efectos secundarios
A function produces a side effect if it does anything other than take a value in
and return another value or values. A side effect could be writing to a file,
modifying some global variable, or accidentally wiring all your money to a
stranger.

Now, you do need to have side effects in a program on occasion. Like the previous
example, you might need to write to a file. What you want to do is to
centralize where you are doing this. Don't have several functions and classes
that write to a particular file. Have one service that does it. One and only one.

The main point is to avoid common pitfalls like sharing state between objects
without any structure, using mutable data types that can be written to by anything,
and not centralizing where your side effects occur. If you can do this, you will
be happier than the vast majority of other programmers.

**Mal:**
```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Bien**:
```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(' ');
}

const name = 'Ryan McDermott';
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### No escribir en funciones globales
Contaminacion global es una mala practica en JavaScript porque podría chocar con otra biblioteca y el usuario de tu API no se enteraría hasta obtener un excepción en produción. Pensemos en un ejemplo: y si deseas extender los metodos de los Arreglos de JavaScript para tener un metodo `diff` Que podría mostrar la diferencia entre dos arreglos? Podrías escribir tu nueva función en el `Array.prototype`, pero podría chocar con otra libreria que trata de hacer lo mismo. Qué pasa si esa otra biblioteca sólo estaba usando `diff` para encontrar la diferencia entre el primer y el último elemento de un array? Esta es la razón por la cual sería mucho mejor usar las clases de ES2015/ES6 y simplemente ampliar el 'Array` global.

**Mal:**
```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const values = [];
  const hash = {};

  for (const i of comparisonArray) {
    hash[i] = true;
  }

  for (const i of this) {
    if (!hash[i]) {
      values.push(i);
    }
  }

  return values;
};
```

**Bien:**
```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    return this.filter(elem => !comparisonArray.includes(elem));
  }
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Favorecer la programación funcional sobre la programación imperativa
JavaScript no es un lenguaje funcional en la forma en que Haskell lo es, pero tiene un sabor funcional similar. Los lenguajes funcionales son más limpios y fáciles de probar.
Favorece este estilo de programación cuando puedas.

**Mal:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Bien**:
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput
  .map((programmer) => programmer.linesOfCode)
  .reduce((acc, linesOfCode) => acc + linesOfCode, 0);
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Encapsulate conditionals

**Mal:**
```javascript
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
```

**Bien**:
```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Avoid negative conditionals

**Mal:**
```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Bien**:
```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Evitar condicionales
Esto parece una tarea imposible. Al escuchar esto, la mayoría de la gente dice,
"Cómo se supone que debo hacer algo sin declarar un `if`?" La respuesta es que se puede utilizar el polimorfismo para lograr la misma tarea en muchos casos. La segunda pregunta usualmente es, "Bueno, eso es genial, pero ¿por qué querría hacer eso?" La respuesta es un concepto de código limpio que aprendimos anterirmente : una función sólo debe hacer una cosa. Cuando tienes clases y funciones que tienen declaraciones `if`, le está diciendo a su usuario que su función hace más de una cosa. Recuerda, Solo haz una cosa.

**Mal:**
```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Bien**:
```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Evitar la comprobación de tipos (parte 1)
JavaScript no es tipado, lo que significa que sus funciones pueden tomar cualquier tipo de argumento.
A veces eres mordido por esta libertad y se vuelve tentador hacer comprobaciones de tipo en las funciones. Hay muchas maneras de evitar tener que hacer esto.
Lo primero a considerar son las APIs consistentes.

**Mal:**
```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.peddle(this.currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'));
  }
}
```

**Bien**:
```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'));
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Evitar la comprobación de tipos (parte 2)
Si está trabajando con valores primitivos básicos como cadenas, enteros, y arreglos,
y no puedes usar polimorfismo pero todavía siente la necesidad de comprobar el tipo,
debe considerar el uso de TypeScript. Es una excelente alternativa al JavaScript normal, ya que proporciona la escritura estática por encima de la sintaxis JavaScript estándar. El problema con la comprobación de tipo manual de JavaScript normal es que hacerlo bien requiere tanta verborrea extra que el falso "tipado-seguro" que se obtiene no compensa la legibilidad perdida. Mantenga su JavaScript limpio, escribe buenos tests, Y ten buenas revisiones de código. De lo contrario, hacer todo eso, pero con TypeScript (Que, como he dicho, es una gran alternativa!).

**Mal:**
```javascript
function combine(val1, val2) {
  if (typeof val1 === 'number' && typeof val2 === 'number' ||
      typeof val1 === 'string' && typeof val2 === 'string') {
    return val1 + val2;
  }

  throw new Error('Must be of type String or Number');
}
```

**Bien**:
```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### No sobre-optimizar
Los navegadores modernos hacen un montón de optimización en tiempo de ejecución. Muchas veces, si usted está optimizando entonces usted está perdiendo su tiempo. [Hay buenos recursos](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
para ver donde falta optimización. Dirigete a esto mientras tanto, hasta que sean reparados si es que pueden ser reparados.

**Mal:**
```javascript

// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Bien**:
```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Eliminar código muerto
El código muerto es tan malo como el código duplicado. No hay razón para mantenerlo en su código base. Si no se está llamando, desaste de eso! Seguirá estando seguro en tu historial de versiones si todavía lo necesitas.

**Mal:**
```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');

```

**Bien**:
```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```
**[⬆ volver arriba](#tabla-de-contenidos)**

## **Objetos y Estructura de Datos**
### Utiliza getters y setters
JavaScript no tiene interfaces o tipos por lo que es muy difícil hacer cumplir este patrón, porque no tenemos palabras claves como `public` y` private`. El uso de getters y setters para acceder a datos sobre objetos es mucho mejor que simplemente buscar una propiedad en un objeto. "Por que?" podrías preguntar. Bueno, he aquí una lista desorganizada de razones del por que:

* Cuando desea hacer más cosas que obtener una propiedad de objeto, no tiene que buscar y cambiar todos los complementos en tu código base.
* Hace la adición de validación simple cuando se hace un `set`.
* Encapsula la representación interna.
* Fácil de agregar registro y manejo de errores al obtener y configurar.
* Al heredar esta clase, puede anular la funcionalidad predeterminada.
* Puedes cargar lentamente las propiedades de tu objeto, digamos conseguirlo desde un servidor.


**Mal:**
```javascript
class BankAccount {
  constructor() {
    this.balance = 1000;
  }
}

const bankAccount = new BankAccount();

// Buy shoes...
bankAccount.balance -= 100;
```

**Bien**:
```javascript
class BankAccount {
  constructor(balance = 1000) {
    this._balance = balance;
  }

  // It doesn't have to be prefixed with `get` or `set` to be a getter/setter
  set balance(amount) {
    if (verifyIfAmountCanBeSetted(amount)) {
      this._balance = amount;
    }
  }

  get balance() {
    return this._balance;
  }

  verifyIfAmountCanBeSetted(val) {
    // ...
  }
}

const bankAccount = new BankAccount();

// Buy shoes...
bankAccount.balance -= shoesPrice;

// Get balance
let balance = bankAccount.balance;

```
**[⬆ volver arriba](#tabla-de-contenidos)**


### Make objects have private members
This can be accomplished through closures (for ES5 and below).

**Mal:**
```javascript

const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Bien**:
```javascript
const Employee = function (name) {
  this.getName = function getName() {
    return name;
  };
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```
**[⬆ volver arriba](#tabla-de-contenidos)**


## **Clases**
### Principio de Responsabilidad Única (SRP)
Como se indica en Codigo Limpio, "Nunca debe haber más de una razón para que una clase cambie". Es tentador empaquetar una clase con mucha funcionalidad, Como cuando sólo se puede llevar una maleta en su vuelo. El problema con esto es que su clase no será conceptualmente cohesiva y le dará muchas razones para cambiarla. Minimizar la cantidad de veces que necesita cambiar una clase es importante.
Es importante porque si hay demasiada funcionalidad en una clase y se modifica una parte de ella, Puede ser difícil de entender cómo afectará a otros módulos dependientes en tu codigo base.

**Mal:**
```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Bien**:
```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}


class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Principio abierto/cerrado (OCP)
Según Bertrand Meyer, "las entidades de software (classes, modules, functions,
etc.) deben estar abiertos para su extensión, pero cerrados para su modificación." ¿Qué significa eso? Este principio básicamente establece que debe permitir a los usuarios ampliar la funcionalidad de su módulo sin tener que abrir el archivo `.js`  de código fuente y manipularlo manualmente.

**Mal:**
```javascript
class AjaxRequester {
  constructor() {
    // What if we wanted another HTTP Method, like DELETE? We would have to
    // open this file up and modify this and put it in manually.
    this.HTTP_METHODS = ['POST', 'PUT', 'GET'];
  }

  get(url) {
    // ...
  }

}
```

**Bien**:
```javascript
class AjaxRequester {
  constructor() {
    this.HTTP_METHODS = ['POST', 'PUT', 'GET'];
  }

  get(url) {
    // ...
  }

  addHTTPMethod(method) {
    this.HTTP_METHODS.push(method);
  }
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**


### Principio de sustitución de Liskov (LSP)
Este es un término de miedo para un concepto muy simple. Se define formalmente como "Si S es un subtipo de T, entonces los objetos de tipo T pueden ser reemplazados por objetos de tipo S (i.e., Objetos de tipo S pueden sustituir objetos de tipo T) sin alterar ninguna de las propiedades deseables de ese programa (Corrección, tarea realizada, etc.)." Esa es una definición aún más aterradora.

La mejor explicación para esto es si tienes una clase padre y una clase hijo, entonces la clase base y la clase hijo se pueden usar indistintamente sin obtener resultados incorrectos. Esto todavía puede ser confuso, así que echemos un vistazo al ejemplo clásico de Square-Rectangle. Matemáticamente, un cuadrado es un rectángulo, pero si lo modelas utilizando la relación "es-un" a través de la herencia, rápidamente te metes en problemas.

**Mal:**
```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach((rectangle) => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // Mal: Will return 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Bien**:
```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor() {
    super();
    this.width = 0;
    this.height = 0;
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor() {
    super();
    this.length = 0;
  }

  setLength(length) {
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach((shape) => {
    switch (shape.constructor.name) {
      case 'Square':
        shape.setLength(5);
        break;
      case 'Rectangle':
        shape.setWidth(4);
        shape.setHeight(5);
    }

    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(), new Rectangle(), new Square()];
renderLargeShapes(shapes);
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Principio de Segregación de Interfaz (ISP)
JavaScript no tiene interfaces por lo que este principio no se aplica tan estrictamente como otros. Sin embargo, es importante y relevante, incluso con JavaScript que no es tipado.

ISP establece que "los clientes no deben verse obligados a depender de interfaces que no usan." Interfaces son contratos implícitos en JavaScript debido a la tipificación falsa.

Un buen ejemplo que demuestra este principio en JavaScript son las clases que requieren grandes objetos de configuración. No es necesario que los clientes configuren enormes cantidades de opciones, porque la mayoría de las veces no necesitarán todos los ajustes. Hacerlos opcionales ayuda a evitar tener una "interfaz pesada".

**Mal:**
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});

```

**Bien**:
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  options: {
    animationModule() {}
  }
});
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Principio de inversión de dependencias (DIP)
Este principio establece dos cosas esenciales:
1. Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones.
2. Las abstracciones no deben depender de detalles. Los detalles deben depender de las abstracciones.

Esto puede ser difícil de entender al principio, pero si has trabajado con Angular.js,
usted ha visto una puesta en práctica de este principio en la forma de inyección de dependencia (DI). Aunque no son conceptos idénticos, DIP mantiene los módulos de alto nivel de conocer los detalles de sus módulos de bajo nivel y configurarlos.
Puede lograrse a través de DI. Una gran ventaja de esto es que reduce el acoplamiento entre módulos. El acoplamiento es un patrón de desarrollo muy malo porque hace que su código sea difícil de refactorizar.

Como se ha indicado anteriormente, JavaScript no tiene interfaces por lo que las abstracciones dependientes son contratos implícitos.Es decir, Los métodos y propiedades que un objeto/clase expone a otro objeto/clase.En el ejemplo siguiente, El contrato implícito es que cualquier módulo Request para un `InventoryTracker` tendrá un método` requestItems`.

**Mal:**
```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // Mal: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(['apples', 'bananas']);
inventoryTracker.requestItems();
```

**Bien**:
```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ['WS'];
  }

  requestItem(item) {
    // ...
  }
}

// Construyendo externamente nuestras dependencias e inyectándolas, podemos fácilmente
// Sustituir nuestro módulo de Request por uno nuevo que utilice WebSockets.
const inventoryTracker = new InventoryTracker(['apples', 'bananas'], new InventoryRequesterV2());
inventoryTracker.requestItems();
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Prefeir clases ES2015/ES6 sobre funciones planas de ES5
Es muy difícil obtener herencia de clases legible, construcción y definicion de metodos para las clasicas clases de ES5. Si necesita herencia (Y tenga en cuenta que tal vez no), entonces prefiera las clases. Sin embargo, prefiera las funciones pequeñas sobre las clases hasta que se encuentre necesitando objetos más grandes y más complejos.

**Mal:**
```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error('Instantiate Animal with `new`');
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error('Instantiate Mammal with `new`');
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error('Instantiate Human with `new`');
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Bien:**
```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() { /* ... */ }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() { /* ... */ }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() { /* ... */ }
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**


### Use method chaining
Against the advice of Clean Code, this is one place where we will have to differ.
It has been argued that method chaining is unclean and violates the [Law of Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter).
Maybe it's true, but this pattern is very useful in JavaScript and you see it in
many libraries such as jQuery and Lodash. It allows your code to be expressive,
and less verbose. For that reason, I say, use method chaining and take a look at
how clean your code will be. In your class functions, simply return `this` at
the end of every function, and you can chain further class methods onto it.

**Mal:**
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car();
car.setColor('pink');
car.setMake('Ford');
car.setModel('F-150');
car.save();
```

**Bien**:
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car()
  .setColor('pink')
  .setMake('Ford')
  .setModel('F-150')
  .save();
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Prefer composition over inheritance
As stated famously in [*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
Bien reasons to use inheritance and lots of Bien reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
relationship (Animal->Human vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
(Change the caloric expenditure of all animals when they move).

**Mal:**
```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Mal because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Bien**:
```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

## **Testing**
Testing is more important than shipping. If you have no tests or an
inadequate amount, then every time you ship code you won't be sure that you
didn't break anything. Deciding on what constitutes an adequate amount is up
to your team, but having 100% coverage (all statements and branches) is how
you achieve very high confidence and developer peace of mind. This means that
in addition to having a great testing framework, you also need to use a
[Bien coverage tool](http://gotwarlost.github.io/istanbul/).

There's no excuse to not write tests. There's [plenty of Bien JS test frameworks]
(http://jstherightway.org/#testing-tools), so find one that your team prefers.
When you find one that works for your team, then aim to always write tests
for every new feature/module you introduce. If your preferred method is
Test Driven Development (TDD), that is great, but the main point is to just
make sure you are reaching your coverage goals before launching any feature,
or refactoring an existing one.

### Single concept per test

**Mal:**
```javascript
const assert = require('assert');

describe('MakeMomentJSGreatAgain', () => {
  it('handles date boundaries', () => {
    let date;

    date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    date.shouldEqual('1/31/2015');

    date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);

    date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```

**Bien**:
```javascript
const assert = require('assert');

describe('MakeMomentJSGreatAgain', () => {
  it('handles 30-day months', () => {
    const date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    date.shouldEqual('1/31/2015');
  });

  it('handles leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);
  });

  it('handles non-leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```
**[⬆ volver arriba](#tabla-de-contenidos)**

## **Concurrency**
### Use Promises, not callbacks
Callbacks aren't clean, and they cause excessive amounts of nesting. With ES2015/ES6,
Promises are a built-in global type. Use them!

**Mal:**
```javascript
require('request').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', (requestErr, response) => {
  if (requestErr) {
    console.error(requestErr);
  } else {
    require('fs').writeFile('article.html', response.body, (writeErr) => {
      if (writeErr) {
        console.error(writeErr);
      } else {
        console.log('File written');
      }
    });
  }
});

```

**Bien**:
```javascript
require('request-promise').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return require('fs-promise').writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });

```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Async/Await are even cleaner than Promises
Promises are a very clean alternative to callbacks, but ES2017/ES8 brings async and await
which offer an even cleaner solution. All you need is a function that is prefixed
in an `async` keyword, and then you can write your logic imperatively without
a `then` chain of functions. Use this if you can take advantage of ES2017/ES8 features
today!

**Mal:**
```javascript
require('request-promise').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return require('fs-promise').writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });

```

**Bien**:
```javascript
async function getCleanCodeArticle() {
  try {
    const request = await require('request-promise');
    const response = await request.get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin');
    const fileHandle = await require('fs-promise');

    await fileHandle.writeFile('article.html', response);
    console.log('File written');
  } catch(err) {
    console.error(err);
  }
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**


## **Error Handling**
Thrown errors are a Bien thing! They mean the runtime has successfully
identified when something in your program has gone wrong and it's letting
you know by stopping function execution on the current stack, killing the
process (in Node), and notifying you in the console with a stack trace.

### Don't ignore caught errors
Doing nothing with a caught error doesn't give you the ability to ever fix
or react to said error. Logging the error to the console (`console.log`)
isn't much better as often times it can get lost in a sea of things printed
to the console. If you wrap any bit of code in a `try/catch` it means you
think an error may occur there and therefore you should have a plan,
or create a code path, for when it occurs.

**Mal:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Bien:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### Don't ignore rejected promises
For the same reason you shouldn't ignore caught errors
from `try/catch`.

**Mal:**
```javascript
getdata()
.then((data) => {
  functionThatMightThrow(data);
})
.catch((error) => {
  console.log(error);
});
```

**Bien:**
```javascript
getdata()
.then((data) => {
  functionThatMightThrow(data);
})
.catch((error) => {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
});
```

**[⬆ volver arriba](#tabla-de-contenidos)**


## **Formatting**
Formatting is subjective. Like many rules herein, there is no hard and fast
rule that you must follow. The main point is DO NOT ARGUE over formatting.
There are [tons of tools](http://standardjs.com/rules.html) to automate this.
Use one! It's a waste of time and money for engineers to argue over formatting.

For things that don't fall under the purview of automatic formatting
(indentation, tabs vs. spaces, double vs. single quotes, etc.) look here
for some guidance.

### Use consistent capitalization
JavaScript is untyped, so capitalization tells you a lot about your variables,
functions, etc. These rules are subjective, so your team can choose whatever
they want. The point is, no matter what you all choose, just be consistent.

**Mal:**
```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const Artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Bien**:
```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```
**[⬆ volver arriba](#tabla-de-contenidos)**


### Function callers and callees should be close
If a function calls another, keep those functions vertically close in the source
file. Ideally, keep the caller right above the callee. We tend to read code from
top-to-bottom, like a newspaper. Because of this, make your code read that way.

**Mal:**
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  lookupMananger() {
    return db.lookup(this.employee, 'manager');
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(user);
review.perfReview();
```

**Bien**:
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupMananger() {
    return db.lookup(this.employee, 'manager');
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ volver arriba](#tabla-de-contenidos)**

## **Comments**
### Only comment things that have business logic complexity.
Comments are an apology, not a requirement. Bien code *mostly* documents itself.

**Mal:**
```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = ((hash << 5) - hash) + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Bien**:
```javascript

function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = ((hash << 5) - hash) + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}

```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Don't leave commented out code in your codebase
Version control exists for a reason. Leave old code in your history.

**Mal:**
```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Bien**:
```javascript
doStuff();
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Don't have journal comments
Remember, use version control! There's no need for dead code, commented code,
and especially journal comments. Use `git log` to get history!

**Mal:**
```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Bien**:
```javascript
function combine(a, b) {
  return a + b;
}
```
**[⬆ volver arriba](#tabla-de-contenidos)**

### Avoid positional markers
They usually just add noise. Let the functions and variable names along with the
proper indentation and formatting give the visual structure to your code.

**Mal:**
```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Bien**:
```javascript
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

const actions = function() {
  // ...
};
```
**[⬆ volver arriba](#tabla-de-contenidos)**
