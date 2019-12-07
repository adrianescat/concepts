## Cracking the code Interview
Este es un link muy bueno sobre mucha información acerca de interviews: https://yangshun.github.io/tech-interview-handbook

Computer Science Basics: https://medium.com/basecs

Para practicar: https://leetcode.com/
Hay muchas como CodeWars, Harcker Rank etc.. LeetCode supuestamente es la que tiene ejercicios más parecidos a los que toman en entrevistas.

### Big O
Big O asymptomatic analysis
Lenguage que usamos para saber cuanto cuesta, cuanto tarda en correr un código.

Que es un buen código?
- Se puede leer bien
- Escalable (acá es donde entra Big O)

https://www.bigocheatsheet.com/

#### O(n)

```javascript
const array = ["holis"];

function findSaludo(array) {
  for (let i = 0; i < array.length; i++) {
    if (array[i] === 'holis') {
      console.log(array[i]);
    }
  }
}

findSaludo(array);

```

Cuantos más elementos hay en el array mayor es el tiempo de ejecución.
En este caso recorremos el array 1 vez por cada elemento, eso sería O(n) o Linear time.

#### O(1) - constant time
No importa cuantos elementos haya, no importa que vaya creciendo, el tiempo
de ejecución siempre va a ser el mismo.

```javascript

function getValue(array, position) {
  console.log(array[position]);
}

```

Si tenemos esto: 

```javascript

function getValues(array, position1, position2) {
  console.log(array[position1]);
  console.log(array[position2]);
}

```
Esto va a ser O(2), sigue siendo constante. Aunque cuando se calcula la complejidad
no se tiene en cuanta si es O(2), O(3)... etc, se reduce a O(1).

#### Ejercicio 1

Cuál es el Big O de esto?:
_Seguramente quieras ir linea por linea_

```javascript

function funChallenge(input) {
  let a = 10;
  a = 50 + 3;

  for (let i = 0; i < input.length; i++) {
    anotherFunction();
    let stranger = true;
    a++;
  }
  return a;
}

```
Si vamos anotando linea por linea nos quedaría así:

```javascript

function funChallenge(input) {
  let a = 10; //O(1)
  a = 50 + 3; //O(1)

  for (let i = 0; i < input.length; i++) { //O(n)
    anotherFunction(); //O(n)
    let stranger = true; //O(n)
    a++; //O(n)
  }
  return a; //O(1)
}

```

Nuestra notación sería entonces: `O(3 + 4n)`. Pero en realidad
todo se reduce a `O(n)` en un código como este.

En la mayoría de las entrevistas no se hace esto, sino que la respuesta debería ser una de las variantes de Big O.
Podemos ver estas en el link más arriba.

#### Reglas
Por suerte hay ciertas reglas que podemos seguir para esta notación:

1. Worst Case
2. Remove constants
3. Different terms for inputs
4. Drop Non dominants


##### 1.Worst Case
Siempre pensamos que tenemos el peor escenario, tan simple como eso. Un ejemplo, si tengo un array como este:
`[1,7,3,54,15,9]` y yo quiero encontrar solo el numero 54 dentro del mismo, si itero el array sin ordenar previamente lo voy a encontrar en el medio del array. Pero cuando hablamos en términos de Big O, asumimos que el 54 está al final del array.

##### 2.Remove constants
Cuando calculamos removemos todas las contantes ya que lo que nos importa es cuando escala, sumar contantes
es sumar algo que es insignificante, ejemplo:

`3 + n/2 + 1000`

Esto se reduce a `n`, las constantes no nos importan.

Qué pasa si tenemos `O(2n)` ?
Como vimos antes, las constantes no nos importan, entonces queda: `O(n)`.

##### 3.Different terms for inputs

Si tenemos esto:

```javascript

function iterateDifferentArrays(array1, array2) {
  array1.forEach(element => {
    console.log(element)
  });

  array2.forEach(element => {
    console.log(element)
  });
}
```

Esto si nos apuramos diríamos que es `O(n)` pero no es así, resulta que la primera iteración depende del primer array y la segunda del segundo array, son inputs diferentes, entonces hay que contarlos por separado, lo que resulta en un Big O como este: `O(a + b)` siendo `a` para la primera iteración y `b` para la segunda.

Si tenemos algo como esto:

```javascript

const letters = ['a,', 'b', 'c', 'd,'];

function logAllPairsOfArray (letters) {
  for (let i = 0; i < array.length; i++) {
    for (let j = 0; j < array.length; j++) {
      console.log(array[i], array[j])
    }
  }
}

logAllPairsOfArray(letters);

```

Cuando tenemos nested loops, se transforma en una multiplicación, en este caso:

`O(n * n)` -> `O(n^2)` llamamos a esto Quadratic Time. Y esto es horrible para un algoritmo.

Si agarramos el ejemplo anterior a este, si hicieramos un for de array1 y dentro hacemos otro for de array2 el Big O sería: `O(a * b)`

##### 4.Drop non dominants
Esta regla lo que dice es que nos tenemos que deshacer de los términos que no son dominanantes, por ejemplo, si tenemos `O(n + n^2)`, no es quedamos solo con `n^2`. Nos importa solo lo que es la escabilidad, probando con numeros dentro de la función sabemos cual es el término que más crece, con ese debemos quedarnos.


#### O(n!)
Es hacer un loop por cada elemento

#### Pilars of programming
COmo hablamos, un buen programa es uno que se entiende y que es escalable, cuando hablamos de esto último podemos dividirlo en dos:

  - speed (time complexity)
  - memory (space complexity)

Para ambos usamos Big O para medir la performance.
Y en lineas generales, casi siempre uno tiene que sacrificar de uno para conseguir más del otro.

Cuando un programa se ejecuta, tiene 2 formas de recordas las cosas: HEAP y el Stack. El primero generalmente es donde guardamos las variables que asignamos, el segundo es donde guardamos el trackeo de las funciones que se van llamando.

Ejemplo:

```javascript
 
for (let i = 0; i < array.length; i++) {
  console.log('Holaa');
  
}

```

si hablamos sobre time complexity podemos decir que es `O(n)`, pero sobre space complexity? cuanto de memoria usamos con esto? si nos fijamos bien guardamos solo `let i = 0`. Entonces es `O(1)`


### How to solve coding problems
Qué buscan en las entrevistas?

1. Analytic Skills
2. Coding Skills
3. Technical Skills
4. Communication Skills

ver cheatsheet.pdf


### Interview exercise, how to solve then:

Nos dan 2 arrays:

array1 `['a', 'x', 'c', 'r']`;
array2 `['x', '1', 't', 'u']`;

Y nos piden que devolvamos `true` si tienen elementos en común, `false` para el caso contrario. Siguiendo el cheatshet, vamos a ver la línea de pensamiento:

1. Nos dieron arrays desordenados, tienen strings, son cortos (y acá se le puede preguntar al entrevistador si pueden crecer estos arrays). Input: 2 arrays, output: boolean
2. Vamos con la solución fea que se nos ocurra, uno de los peores casos que podría escribir alguien sin experiencia: para este caso, nested loops, es decir vamos comparando uno por uno cada elemento del primer array con el de segundo, en cuanto encuentre algo igual devolvemos `true`, sino devuelve `false`.

sería algo como:

```javascript
function encontrarParecidos(array1, array2) {
  for (let i = 0; i < array1.length; i++) {
    for (let j = 0; j < array2.length; j++) {
      if (array1[i] === array2[j]) {
        return false;
      }
    }
  }
  return false;
}
```

A simple vista parece `O(n^2)` pero no lo es, acordate que si bien es un nested loop, los arrays (inputs) son diferentes, por lo tanto se trata de un `O(a * b)`el cual es bastante lento.
Debemos explicar que esta sería una mala solución, o por lo menos no es la óptima.

3. Vamos con una mejor solución, escribiendo con comentarios qué es lo que vamos a ir haciendo.

```javascript
function encontrarParecidos(array1, array2) {
  // loop primer array y lo convertimos en object donde las keys son iguales a los valores del array
  // loop segundo array y checkeo si existe en el object anterior
}
```

Solo con esto nos damos cuenta que estamos entregando una mejor solución: `O(a + b)`

https://repl.it/@AdrianE1/containsCommonItem

Tener en cuenta que en JavaScript, cuando se crean las keys se transforman en strings. Con esto quiero decir que si tenemos null, [], números o strings vamos a poder compararlos igual.

Tenemos que pensar también en qué posibles errores pueden ocurrir, qué pasa si no me pasan un array? podemos despejarnos dudas preguntando al entrevistador y en base a sus respuestas modelar manejos de error.

Hay que pensar también en el space complexity y que tan legible es.. si usamos métodos nativos JavaScript va a ser mucho más legible.

Repasar:
- https://developer.mozilla.org/es/docs/Web/JavaScript/Referencia/Objetos_globales/Array
- https://developer.mozilla.org/es/docs/Web/JavaScript/Referencia/Objetos_globales/Object
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set
- https://developer.mozilla.org/es/docs/Web/JavaScript/Referencia/Objetos_globales/Map

### Data structures
Collección de datos.

info que ayuda a entender cómo la PC guarda las estructuras de datos:
- https://www.youtube.com/watch?v=fpnE6UAfbtU
- http://statmath.wu.ac.at/courses/data-analysis/itdtHTML/node55.html

#### Array
Estructura base + acciones básicas: https://repl.it/@AdrianE1/DataStructures-Arrays

static: size previamente definido
dynamic: no está definido el size, puede crecer.

Cómo crearías un array con JS classes? https://repl.it/@AdrianE1/DataStructure-Array-Implementation

Ejercicio 1: Reverse strings. Te dan un string y te piden que retornes invertido
https://repl.it/@AdrianE1/ReverseString

Ejercicio 2: Merge sorted arrays. Te dan dos arrays ordenados, quieren que devuelvas un array que tenga todos los elementos de los 2 arrays. Y que siga ordenado.
https://repl.it/@AdrianE1/MergeSortedArrays

Pros:
- Fast lookups
- Fast push/pop
- Ordered

Cons:
- Slow inserts
- Slow deletes
- Fixed size (if using static array)

#### Hash Tables
key - value. La key es usada para encontrar en memoria el valor.
Se usa una Hash function para decifrar la key a una dirección de memoria, esto tiene una complejidad de O(1).

Todas las operaciones con Hash Tables son `O(1)`. Pero una gran desventaja de las Hash Tables es la 'Collision', colisión de datos. Resulta que a medida que vamos inclyendo key:values a nuestro Hash Table, cada key apunta a una dirección de memoria. A veces ocurre que cuando corre la Hash Function (implementación dependiente del lenguaje/sistema) transforma la key nueva en una dirección de memoria, esta dirección es la misma de otra que ya se está usando para el hash. Para mantener ambos valores o los que sean en la misma dirección de memoria, lo que se hace es usar ahí mismo otra estructura de datos que son las Listas linkeadas. Cuando tenemos una colisión, la búsqueda del valor pasa a ser de `O(1)` a `O(n/k)` siendo `k` el tamaño de nuestro Hash Table, pero como vimos antes, sacamos las constantes y nos queda `O(n)`.

Los Hash Tables tienen diferentes implementaciones en distintos lenguajes. Ahora con ES6 en JavaScript tenemos 2 estructuras nuevas:

- Maps
- Sets

Los Maps, a diferencia de los Objects de siempre de JS, nos dejan tener como key cualquier tipo, es decir, function, number, string etc.. Los Objects hacen un strigify, es decir todas las keys son strings. También mantienen un orden para insertar. Podemos iterar los Maps, para iterar los Objects tenemos que sacar primero sus keys.

Ejercicio: dado un array de números donde pueden repetirse, devolver el número que se repite primero, si ninguno se repite devolver undefined.
https://repl.it/@AdrianE1/firstRecurringCharacter

#### Linked lists
Cabeza, cola y punteros.
Los elementos no están indexados como en un Array o Hash Map. Los elementos están dispersos por toda la memoria,
no están al lado del otro como con un array. Con esto ya sabemos que es más lento iterar una lista que un array.
Pero lo que tiene de bueno, son los inserts ya que no tenemos que reindexar todo cuando ingresa un nuevo elemento, sino que se cambian los punteros y listo. Es mejor que un array en ese sentido aunque sigue siendo `O(n)`, ya que para ingresar o hacer un delete tengo que encontrar primero el index recorriendo la lista.

Puntero: es uan referencia a un espacio en memoria.

Implementación de lista: https://repl.it/@AdrianE1/Data-Structures-Linked-Lists-Implementation-4

Array vs Linked List: https://www.youtube.com/watch?v=DyG9S9nAlUM

#### Stacks
//TODO

#### Queues
//TODO

#### Trees
// TODO

#### Graphs
//TODO

#### Recursion
//TODO

### Recursion
//TODO

### Dynamic programming
Sólo es una técnica de optimización. Consiste en _caching_ y el midset "divide and conquer".
por ejemplo: Memoization (guradarse en un hash map resutlados para no volver a calcular un mismo resultado una y otra vez con los mismos parámetros). Lo mejor es usar closures para no tener nuestros objetos de cache globalmente.

### Sorting
Viendo este cheat sheet https://www.bigocheatsheet.com/ tenemos varios algoritmos de ordenamiento.
Los más comunes podrían ser:
- Bubble Sort
- Insertion Sort
- Selection Sort

Pero si analizamos los casos que más nos interesa que son average/worst son de `O(n^2)`.

Vayamos directo a algoritmos `O(n * log n)`:
- Quick Sort (en el peor de los casos es `O(n^2)`)
- Merge Sort
- Heap Sort
- Tim Sort

#### Merge Sort
Va dividiendo el array (input) en partes iguales hasta tener todos los elementos aislados. Luego va comparandolos y mergeando creando nuevos arrays hasta tener 1 solo todo ordenado. Esta sería una implementación:
https://repl.it/@AdrianE1/mergeSort

_Nadie va a pedirte que implementes un Merge Sort en una entrevista, vale conocer su complejidad y de qué va el algoritmo_

Algoritmos estables vs inestables: _A sorting algorithm is said to be stable if two objects with equal keys appear in the same order in sorted output as they appear in the input array to be sorted. Some sorting algorithms are stable by nature like Insertion sort, Merge Sort, Bubble Sort, etc. And some sorting algorithms are not, like Heap Sort, Quick Sort, etc._ https://stackoverflow.com/questions/1517793/what-is-stability-in-sorting-algorithms-and-why-is-it-important

#### Quick Sort
Usa la técnica de pivoteo, elige al azar un elemento como pivote y va comparando desde el inicio del array, si encuentra elementos que tienen que ir a la derecha de él, va desplazandolos a su derecha, desplazando también el número que estaba previamente a su izquierda y poniéndolo de donde se sacó el número que se movio a la derecha del pivote. Así sucesivamente hasta que no tenga más. Cuando llega ese momento, significa que el pivote está en su ubicación correcta. Ahí utiliza el mecanismo (como el Merge Sort) de "divide y ganarás". Forma 2 nuevas colecciones a los lados del pivote, y pivotea nuevamente en esas nuevas colecciones y así sucesivamente.
Implementación del Quick Sort: https://repl.it/@AdrianE1/quickSort

Quick Sort vs Merge Sort: Merge Sort en el peor de los casos, en cuanto a su tome complexity es mejor que el de Quick Sort, pero este último es mejor en cuanto a space complexity en el peor de los casos. Generalmente es uno de los más rápidos el Quick Sort en lineas generales, pero tiene algunos casos donde performa muy mal.

Tenemos otros sorts como Radix y Counting. No realizan comparaciones. Pero no son los más utilizados.

Ejercicio:
Dados estos casos que sorting usarías.

- Sort 10 schools around your house by distance:
Insertion sort. Es muy rápido en arrays de pocos elementos.

- eBay sorts listings by the current Bid amount:
radix or counting sort

- Sport scores on ESPN
Quick sort. Lo más eficiente, con el Merge podría usar mucha memoria.

- Massive database (can't fit all into memory) needs to sort through past year's user data
Merge Sort

- Almost sorted Udemy review data needs to update and add 2 new reviews
Insertion Sort

- Temperature Records for the past 50 years in Canada
radix or counting Sort
Quick sort if decimal places

- Large user name database needs to be sorted. Data is very random.
Quick sort

- You want to teach sorting
Bubble sort

Para JavaScript no hay limitaciones en cuanto al algoritmo que se implementa para el sorting. Eso quiere decir que los browsers o motores tienen distintas implementaciones. Lo último que se sabe (porque esto puede ir cambiando con las versiones de browsers y motores) es que en mozilla por ejemplo se utiliza Merge Sort y en Chrome y V8 se utiliza Quick Sort / Insertion Sort (para arrays chicos).

Esto quiere decir que si usamos el método de Array `.sort()` vamos a estar utilizando Merge Sort, Quick Sort e Insertion Sort para casos felices. Ya lo implementaron por nosotros, no tenemos que realizar nuestras propias implementaciones.

Sort: https://developer.mozilla.org/es/docs/Web/JavaScript/Referencia/Objetos_globales/Array/sort

`arr.sort([compareFunction])`

Si no se provee compareFunction, los elementos son ordenados convirtiéndolos a strings y comparando la posición del valor Unicode de dichos strings

Si se provee compareFunction, los elementos del array son ordenados de acuerdo al valor que retorna dicha función de comparación. Siendo a y b dos elementos comparados, entonces:

- Si `compareFunction(a, b)` es menor que 0, se sitúa a en un indice menor que b. Es decir, a viene primero.
- Si compareFunction(a, b) retorna 0, se deja a y b sin cambios entre ellos, pero ordenados con respecto a todos los elementos diferentes. Nota: el estandar ECMAscript no garantiza este comportamiento, por esto no todos los navegadores (p.ej.  Mozilla en versiones que datan hasta el 2003) respetan esto.
- Si compareFunction(a, b) es mayor que 0, se sitúa b en un indice menor que a.

### Searching
- Linear Search, recorro el array comparando `O(n)`. El `arr.filter([compareFunction])`, `indexOf`, `find`, `includes` entre otros son así. Es el peor caso, recorremos todo el Array.

- Binary Search `O(log n)`. Nos paramos en el medio y preguntamos si lo que estamos buscando es menor o mayor a lo que está en el medio, de esa forma caes en un grupo reducido y buscás ahí. Lo único necesario para esto, es que el array esté ordenado.

### Non Technical Stuff

- get a raise https://www.youtube.com/watch?v=XY5SeCl_8NE&feature=youtu.be
- 