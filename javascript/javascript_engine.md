## Motor JavaScript (V8)

Nuestra computadora no entiende javascript, no sabe lo que es, lo único que sabe en verdad es hacer es interpretar "unos" y "ceros". El motor javascript es lo que se interpone entre un archivo `.js` y la PC. Es una especie de "traductor".
No existe uno solo, de hecho existen muchos motores jaavscript, acá tenemos una lista: https://en.wikipedia.org/wiki/List_of_ECMAScript_engines. Estos motores los llamamos **ECMAScript engine**. https://en.wikipedia.org/wiki/ECMAScript

Estos motores están desarrollados por distintos equipos, personas, empresas etc, ejemplo, V8 está hecho con C++. V8 es el motor javascript escrito por Google. En el 2008 habían motores javascript pero bastante simples, Google por ese entonces necesitaba una mejor solución para su propio explorador (Chrome) y uno de sus productos más complejos: Google Maps. Necesitaban más poder, mejor procesamiento y más rapidez.

--

EL primer motor JS fue creado por Brendan Eich quien trabajaba para Netscape, el primer browser comercial. Él creó lo que es la primer versiópn de un motor al que hoy conocemos como Spider Monkey, motor que utiliza Firefox hasta el día de hoy. Él creó JavaScript.

--

[img 1.engine]
https://astexplorer.net/
JS FILE -> Parser -> AST -> Interpreter -> Profiler -> Compiler -> Optimized code -> "101010101"

--
¿Por qué cada uno no puede crear su propio motor JS y listo? Como poder podemos, pero sería un completo caos! Para evitar eso se creó ECMAScript, es la estandarización del lenguage, se trata de cómo debe ser escrito y cómo debe funcionar JS.

--
Interpreter: traduce y lee los archivos linea por linea -> así funciona JavaScript
Compilado: no traduce y lee en el momento, sino que previamente crea una traducción del código que escribimos a máquina

Javascript en realidad podemos correrlo de ambas maneras, el tema es Porqué correrías JS de una forma o la otra?
Have you heard of Babel or TypeScript? They are heavily used in the Javascript ecosystem and you should now have a good idea of what they are:

Babel es un compilador que toma el JS moderno y retorna JS viejo compatible con todos los browsers.
TypeScript es una especie de JS con vitaminas que compila a Javascript

Estos dos hacen lo que hacen los compiladores, toman un lenguage y lo transforman en otro.

--
JavaScript en un principio usaba interpreters ya que fue creado en un principio para correr en el browser y nada más, debía correr lo antes posible y de forma rápida. No necesitaba ser compilado, sino solo leido y ejecutado. El problema está cuando comenzamos a ejecutar el mismo código una y otra vez, por ejemplo en un loop. Cada vez comienza a hacerse más lento. Un compilador lo que haría es en vez de ejecutar el mismo código una y otra vez es calcular anteriormente la respuesta y siempre devolver la misma, no tiene que calcularla muchas veces. Los interpretes y compiladores tienen sus pros y sus contras.
Qué pasa si combinamos ambos, existe una opción que tengo lo mejor de ambos? Eso pudimos verlo cerca del año 2000 cuando surgió V8. Esta nueva opción se llama JIP compiler o mejor dicho: "Just in Time Compiler". Entonces, JS es interpretado o compilado? la respuesta es: depende de la implementación.

--
WebAssembly o "wasm". Es código binario para correr en el cliente, es relativamente nuevo. Desarrolladores de Mozilla, Google, Apple entre otros están desarrollandolo, en un futuro tal vez no necesitemos compiladores javascript. https://webassembly.org/

--

Memmory HEAP y Call Stack.
Necesitamos el memory heap para leer y escribir información, de esta forma reservar memoria, usarla y liberarla.
Con el call stack (pila de ejecución) vamos registrando en qué lugar del código estamos parados.

const number = 100; //reserva memoria para number
const string = 'hola'; //reserva memoria para un string
const perro = { nombre: 'firulais' }; //reserva memoria para un object y sus valores

Perro va a apuntar a un área de la memoria.

--
StackOverflow: una pila de ejecución que no deja de crecer. Podemos ver un error que dice -> "Maximun call stack exceeded".

Si hay un error JS, lo que va a hacer es imprimir la ejecución del call stack, el "stack trace".
--

Garbage collection: JS utiliza GC, eso quiere decir que cuando se reverva memoria y dejamos de usar la variable, función u objeto libera esa memoria automáticamente, nosotros no tenemos que hacer nada. Esto no quiere decir que podamos estar tranquilos con el manejo de la memoria, es un gran error. Puede ocurrir que tenemos memoria reservada y nada apunta a la misma.
Cómo funciona el GC en JS? utiliza un algoritmo llamado "mark and sweep".

--

Memory leaks: se trata de espacios de memoria utilizados previamente que ya no se utilizan o nada apunta a ellos y que todavía no han sido liberados. Hay 3 casos muy comunes que provocan memory leaks:
1. global variables. Nunca se libera el espacio reservado.
2. Event listeners. Bindear eventos a elementos y nunca los removemos.
3. setInterval. A no ser que hagamos clear y lo paremos va a estar referenciando dentro variables, funciones etc de lo que haya adentro por un tiempo indeterminado.

SoundCloud tuvo un caso de memory leak bastante interesante. Google it.

--

JS es un lenguaje de programación de *un solo hilo*. Es decir tiene un solo call stack, no puede ejecutar funciones en paralelo.

--

Javascript runtime: si JS fuera solo single-thread nuestras apps serían lentísimas, para solventar eso entra en juego JS runtime. Mientra que el JS engine ejecuta las funciones de forma sincrónica, en los browsers tenemos la Web Api también que tiene funcionalidaes como enviar requests HTTP, escuchar eventos, atrasar ejecuciones ej: setTimeOut puede ser usada hasta cachear cosas o guardar data en el browser. En el browser, el objeto global "window" es lo que provee la WEB API, ahí están todos las funciones disponibles. El browser no puede utilizar solo el single-thread de JS, sería todo extremadamente lento. Por debajo, los browsers utilizan disntintos lenguajes de programación de bajo nivel como C++. Estas WEB APIs son las que llamamos asincrónicas, podemos decirles que realicen una acción por atras (in background) y que nos avise cuando termina y tiene una respuesta.

Entonces cómo funciona?
Tenemos funciones en el call stack que vamos ejecutando, a penas aparece algo como por ejemplo un setTimeOut, que no es parte de javascript, es parte de la WEB API, lo que hace la call stack es delegarle esto a la WEB API a penas se encuentra con este tipo de cosas que son asincrónicas, que pueden estar en el hilo principal de ejecución. Lo que va a pasar es que la WEB API va a estar guardando las respuestas de estos eventos asincrónicos en algo que se llama callback queue. Una cola de ejecución. En cuanto la call stack esté libre, un "mecanismo" llamado Event Loop va a intentar devolver la respuesta que quedó en la cola antes mencionada a la call stack.

Un ejemplo muy simple (pregunta que hacen en muchas entrevistas). Si tengo:

console.log(1)
setTimeOut(() => console.log(2), 0) //puede ser 0 o 10000, no importa
console.log(3)

la consola va a imprimir:

1
3
2

El Event Loop es un loop justamente que está constantemente preguntado si la call stack está libre.
Acá tenemos una herramienta que nos ayuda a ver graficamente como funciona eso: http://latentflip.com/loupe

--

Qué es o qué hace Node.js entonces?? Es un Javascript runtime!! Por debajo hecho con C++. El mecanismo es muy parecido como el que se utiliza en los browsers, con algunas diferencias pero muy parecido al fin.
Cuenta con un Event Loop, una event queue, worker threads etc. Se usa algo llamado LIBUV que corre en C++ para orquestar todo esto. Obviamente Node.js puede hacer mucho más que lo que hace JS en el browser, JS en el browser es lo que llamamos sandbox environment, no puede hacer cosas como por ejemplo leer los archivos de la computadora del usuario, sería una gran falla de seguridad, JS en el browser se ejecuta en la tab que tenemos abierta en ese momento y listo.
Node.js usa V8 engine para entender javascript y usa LIBUV para crear el event loop y extender lo que podamos hacer en background.

un detalle: cuando corremos node, por ejemplo en la consola y ejecutamos "window", es undefined, no existe ese objeto en Node, lo que si existe es "global".

--

Execution context: Cuando JS, el engine ve functionName() crea un "execution context". Pero lo que hay que saber es que lo primero que se crea es el global execution context: global(), dentro de este functionName() y dentro de esta última cualquier cosa que hayamos definido.
El global execution context nos provee de dos cosas: globalObject y this y en ese momento, ambos objetos van a ser iguales. En el browser este globalObject pasa a llamarse "window" en node se lo llama "global". Esto que ocurre con el engine es la primera fase, la fase de creación. Existe una segunda fase que viene luego de la que hablé recién que es la fase de ejecución, ahí se ejecuta el código en sí.

--

En JS, lexical scope o "lexical environment" es la data disponible + las variables **DONDE** la función fue definida. Este determina nuestras variables disponibles.
No determina **QUIÉN LLAMÓ** a la función, eso último se llama dynamic scope.
Las arrow function definen lexicamente el this.

Ejemplo usando prototypes:

function Persona(nombre) {
  this.nombre = nombre;
}

Persona.prototype.saludar = () => { //lexical scope
  return `Hola, me llamo ${this.nombre}`;
}

Persona.prototype.saludar = function() { //dynamic scope
  return `Hola, me llamo ${this.nombre}`;
}

El primero no funciona, ya que el this va a apuntar a un objeto dependiendo de DONDE se llamó la función. digamos que apunta al objeto global. En el segundo caso, va a apuntar al objeto que está llamando dicha función. Al hacer:

const per1 = new Persona("carlos");
per1.saludar(); 

El this dentro de este saludar va a ser per1, el cual es un objeto con las caraterísticas de Persona, ya que lo creamos mediante el new, es decir usamos el constructor function. Este crea un environment context nuevo, con el this apuntando a per1. 

--

Hoisting: este mecanismo se encuentra dentro de la fase de creación. Se trata de mover las declaraciones de variables y/o funciones a arriba de todo de cada uno de los ambientes/scopes durante la compilación

console.log("--1");
console.log(teddy);
console.log(sayHello());

var teddy = "chau";
function sayHello() {
  console.log("Holis");
}

Esto lo que va a imprimir es:
> --1
> undefined
> Holis

Porque lo que hace el hoisting es lo siguiente:

function sayHello() { //HOISTED
  console.log("Holis");
}

var teddy = undefined; //HOISTED

console.log("--1");
console.log(teddy);
console.log(sayHello());

var teddy = "chau";

Todas estas variables y funciones son guardadas en la Memory Heap para tenerlas disponibles una vez comience a ejecutarse el código.

--

Function invocation:

//function expression
const canada = () => {
  console.log('cold');
}

//function declaration
function india() {
  console.log('warm');
}

La primera se define en tiempo de ejecución. La segunda se defina en tiempo de compilación. Cuando invocamos una función se crea el execution context, tenemos nuevamente ambas fases, la de creación y la de ejecución.
En la fase de creación obtenemos 2 cosas: this y arguments, en este caso no obtenemos el objeto global. Este objeto arguments es solo para las funciones.
--

arguments keyword: es peligroso de usar, tiene todos los parámetros que le pasamos a la función. No es un array. A veces sucede que queremos iterar los parametros que le pasamos a una función, para eso lo que podemos hacer es usar:

Array.from(arguments) -> Array

Otra forma es desestructurar, utilizar "Rest parameters", que es haciendo `function hola(...args) {`

---

Variable environment: Algo que también tenemos en las funciones una vez creadas, es el ambiente de las variables.

---

Scope chain:
es el link que tienen las funciones al padre, al ambiente externo. De esta forma pueden acceder a lo que se haya definido afuera de la función.

---

function scope vs block scope:
un nuevo scope se crea cuando definimos una función, todo queda definido dentro de la función en JS. Lo que no teníamos en JS es block scope, es decir cada bloque de código, si definimos var's ahí podemos leerlas fuera del block scope, por ejemplo el bloque de un IF. Luego JS con ES6 introdujo `const` y `let`, estas se definen también solo dentro de los block scopes.

---

IIFE
inmedietly invoked function expression

---

This: this es el objeto del cual la función (en la cual dentro de la misma se utiliza this) es propiedad de ese mismo objeto. Ejemplo:

const objetoSalvaje = {
  name: 'Adri',
  sayHi: function () {
    return 'Hi ' + this.name; <-- this es objetoSalvaje, sayHi es propiedad de objetoSalvaje
  },
}

dicho de otra manera: this es lo que está a la izquierda del `.`. es decir, si hago objetoSalvaje.sayHi() si uso `this` dentro de sayHi, objetoSalvaje va a ser `this`, objetoSalvaje está a la izquierda del `.`;

---
apply() and call(): pedir prestado funciones. Call es lo que siempre se ejecuta cuando llamamos a ejecución una función.

---

bind() and currying:
Con bind seteamos el valor the this además de poder attachear otras variables cuando ejecutemos luego la función.
Lo que podemos hacer con bind es **currying** (que en inglés sería algo como elogiar a alguien para obtener un beneficio, o estar en el servicio de, o dar para recibir algo a cambio pero no de una forma muy sincera). Esto es bindear parámetros a la función por ejemplo, crear funciones a partir de otra función bindeando sus parámetros con lo que nosotros queramos:

function sumar(a, b) {
  return a + b;
}

const sumarConDos = sumar.bind(this, 2);

Le estamos pasando como primer parametro siempre el 2, es decir a=2 mientras usemos la función sumarConDos. Podemos usarla:

sumarConDos(3) //-> 5

---

Tipos en javascript:
- number, boolean, string, undefined, null, Symbol (es6), {}.

pero si hacemos typeOf de alguno de estos vamos a ver cosas que tal vez no esperábamos, ejemplo:

typeof null --> object. Esto no debería ser así, hay una propuesta para cambiarlo pero hay tanto código legacy que cambiarlo no solo sería una ardua tarea sino que hasta impactaría en muchos lugares rompiendo el código de otros.

En JS los arrays y las funciones son objetos.

Tenemos los tipos primitivos y los no-primitivos.
Los primitivos son aquellos que son data que representan un solo valor, 5 en memoria va a ser 5. La variable de un tipo primitivo contiene directamente el valor de ese tipo. Podemos considerarlos como átomos que no pueden descomponerse en partes más pequeñas. (number, boolean, string, undefined, null y symbol).

Los no-primitivos no contienen el valor directamente. Lo que contienen en realidad es una referencia, similar a un puntero a un espacio de memoria donde el objeto está guardado.

Ahora, como alguna vez seguramente haz escuchado, todo en javascript es un objeto. Incluso los primitivos tienen contenedores/wrappers que son un objeto:

Number, String, Boolean.

ejemplo:

true.toString() --> 'true'. Esto funciona, pero porqué se puede hacer un .toString() si es de un tipo primitivo? En realidad es acá donde JS es bastante vivo en hacer cosas sin que nos demos cuenta como esto:

Boolean(true).toString()

---

Pasar por valor vs Pasar por referencia:
los tipos primitivos se pasan por valor.
Cuando hacemos

var a = 5;
var b = a;
b++;
console.log(a) --> 5
console.log(b) --> 6

Lo que hace el motor JS es hacer una copia de a, guardarla en b y luego sumamos sobre b. Eso es pasar por valor.

Los objetos, como son no-primitivos y se guarda uan referencia a ellos, no se copian.

Cuando tenemos

var obj1 = { name: 'Pablo', age: 42}
var obj2 = obj2;

obj2.name = 'Lucas';

console.log(obj1) --> { name: 'Lucas', age: 42}
console.log(obj2) --> { name: 'Lucas', age: 42}

Esto es porque se pasa por referencia, modificamos el objeto mediante referencia. Esto sirve para optimizar el uso de la memoria, imaginate si usamos un objeto o array demasiado grande, cada vez que lo pasemos lo va a estar copiando en memoria una y otra vez.
Entonces, si queremos copiarnos un objeto en una variable, pasarlo a una función o lo que sea y no queremos modificar el original podemos hacer muchas cosas.

En el caso de arrays por ejemplo podemos hacer:

var arrayCopia = [].concat(arrayOriginal);

Para los objetos por ejemplo podemos clonarlos:

let cloneOfObject = Object.assign({}, objectToClone).
También tenemos la funcionalidad de spread operator:

let cloneOfObject = { ...objectToClone };

De lo que hay que tener cuidado es cuando clonamos a un nivel "shallow clone", si el objeto tiene más objetos dentro de si mismo esos van a pasarse por referencia también, por más que estén clonados.
Una forma de copiar entero un objeto sería:

let superClone = JSON.parse(JSON.stringify(objectToClone));

El problema de esto último es que no podemos usarlo cuando hay funciones dentro, además que no es del todo performante.

Como comparar objetos: https://stackoverflow.com/questions/1068834/object-comparison-in-javascript

---

Type coercion:
es cuando el motor de JS convierte un tipo a otro para poder operar o comparar valores, ejemplo:

1 == '1' lo que va a hacer el motor es -> 1 == 1
Ahora no es lo mismo si hacemos 1 === '1', esto no solo compara el valor sino el tipo, es decir, compara explicitamente

herramienta para ver comparaciones: https://dorey.github.io/JavaScript-Equality-Table/

---

HOF, high order functions:
una función que toma como argumento una función y/o que retorna una función.

---
Closures:
Una combinación de funciones y del lexical scope.
lexical environment === [[ scope ]]
Esto nos permite tener muchos super poderes, como manejo de memoria eficiente, encapsulado entre otras.

---

Prototypes:
prototypal inheritance es la propiedad en JS que permite que un objeto tenga las propiedades de otro objeto (functiones, métodos, variables).
Hay una forma de ver esta "cadena de herencias" o Prototype chain.

si tenemos:

const array = [];
// hacemos

array.__proto__ --> devuelve un array con todos los métodos disponibles de un Array propiamente dicho.

// ahora si hacemos

array.__proto__.__proto__ --> devuelve el "base object", el objeto padre de todos con todos sus métodos.

Si nos fijamos en este último podemos ver que tenemos el método .toString(), y si lo pensamos un poco entenderemos que todo descendiente del base object va a contar con está función a través del prototype chain. Podemos hacer:

array.toString().

Esto practicamente es único de este lenguaje, otros lenguajes de programación usan lo que se llama classical inheritance. (Java, C#).

No debemos confundirnos con las clases de javascript, esto que escribimos como:

class Perro {
  constructor(super);
  ...
}

Esto se llama "syntactic sugar" , en realidad no hay clases en JS. Aquí una definición sobre syntactic sugar:
_In computer science, syntactic sugar is syntax within a programming language that is designed to make things easier to read or to express. It makes the language "sweeter" for human use: things can be expressed more clearly, more concisely, or in an alternative style that some may prefer._

Cómo podemos hacer herencia en objetos, hay varias formas según lo que queramos lograr, pero con la que ya vimos hasta ahora, podemos hacer

const objeto1 = {
  metodo1() {
    return "hola"
  }
  metodo2(){
    return "1"
  }
}

const objeto2 = {
  metodo2(){
    return "2"
  }
}

objeto2.metodo1() // -> ERROR! typeError, not aa function

objeto.__proto__ = objeto1;
objeto2.metodo1() // -> "hola"
objeto2.metodo2() // -> "2"

Lo que hace internamente es: cuando hacemos objeto2.metodo1(), trata de fijarse dentro de objeto2 si existe esta función, como no existe dentro de ese scope se fija en el protoype chain si existe y así sucesivamente.

No debemos usar __proto__ en realidad, es malo en cuanto a performance, hay mejores maneras de hacer un de prototype inheritance.

Cual es el motivo principal de querer usar herencia? de tener objetos que compartan las mismas funciones y características?. Además de no tener que repetir código una y otra vez, el principal motivo es la memoria, podemos declarar un objeto, siempre hacer referencia a él en memoria cada vez que un objeto nuevo se crea con sus mismas características.

Los objetos no solo tiene __proto__ sino que tienen un objeto llamado prototype, el __proto__ de un objeto apunta al prototype de su padre en la prototype chain.

Antes hablé de las mejores formas de heredar, una es la siguiente:

let human = {
  mortal: true
}

const socrates = Object.create(human);

Solo las funciones tienen "prototype".

---

OOP:
new keyword crea un nuevo execution context con this apuntando al objeto creado en vez de al objeto global.

algo a tener en cuanta, como es que si hago esto:

const num = 5;

puedo hacer:

num.toString(); --> "5"

??

Resulta que aunque sea un tipo primitivo, lo que hace JS es esto en realidad:

const num = new Number(5);

Utiliza el Objeto global Number o mejor dicho el Number object builder. De esta forma, este num termina teniendo acceso a __proto__ y con eso logra acceder a los métodos del objeto base.

---

Job queue:
En JS, dentro del JRE javascript runtine environment tenemos otro elemento más y es el job queue. Resulta que para manejar código asincrónico, como setTimeOuts, como vimos antes se hace uso de web APIs las cuales cargan una callback queue y luego una task queue. Event loop luego va preguntando si estas taras terminaron y lo agrega al callstack.

El tema es que para hacer uso de las promises que se sumaron a javascript, se tuvo que inventar una nueva queue: JOB QUEUE o como algunos le dicen: Microtask queue. 

Lo que tenemos que saber de esto es que la JOB QUEUE tiene más prioridad que la Callback queue, entonces el Event Loop lo que hace es fijarse primero en la job queue si terminó algo y luego en la callback queue.

Dicho esto, si tenemos esto:

setTimeOut(() => console.log(1), 0);

Promise.resolve(2).then((data) => console.log(data));

console.log(3);

La impresión va a ser:

// 3

// 2

// 1