Readings: https://github.com/ardanlabs/gotraining/tree/master/reading
Learnings: https://github.com/ardanlabs/gotraining/tree/master/topics/go

# Golang & Good practices

## Syntax
### Variables

Inicializo variables vacías.

```go
var a int
var b string
var c float64
var d bool
```

Declaración e inicializamos al mismo tiempo.

```go
aa := 10
bb := "Hello"
cc := 3.14159
dd := true
```

Go no tiene casting, sino que tiene conversión (tenemos un costo de memoria ya que convertimos de un tipo al otro). No hace casting por una cuestión de integridad, para no alterar memoria.

```go
aaa := int32(10)

fmt.Printf("aaa := int32(10) %T [%v]\n", aaa, aaa)
```

### Structs types

Un tipo con diferentes campos:

```go
type example struct {
  flag bool
  count int16
  pi float32
}

func main() {
  var e1 example
}
```

Tenemos que tener cuidado de como armamos nuestros structs ya que podemos estar desperdiciando mucha memoria. Podemos generar mucho "padding" (rellenos de memoria o bytes que se omiten por querer completar bloques de memoria). Para optimizar la memoria lo que conviene hacer es acomodar los campos del mayor al menor, según el más grande las direcciones de memoria cambian, los multiplos cambian, es decir, si metemos un `float64` al principio de todo, las direcciones de memoria van a hacer multiples de 8, si es un float32 el primero serían múltiplos de 4, como los siguientes campos son menores, van a ir acomodándose mejor en memoria.

Lo correcto.

```go
type example struct {
  pi float32
  count int16
  flag bool
}
```

En el primer ejemplo donde definimos `e1` lo hicimos con valores vacíos. Para evitar esto tenemos que hacer una "literal construction":

```go
e2 := example{
  flag: true,
  counter: 10,
  pi: 3.141592,
}

fmt.Println("Flag", e2.flag)
```

En este lenguaje hay cosas que son nombradas como no, ejemplo:

```go
var e1 struct {
  pi float32
  count int16
  flag bool
}
```

Puedo declarar un struct no nombrado y no dejarlo vacío en una "literal construction"

```go
e2 := struct {
  pi float32
  count int16
  flag bool
}{
  pi: 3.14,
  count: 10,
  flag: true
}
```

Esto puede realizarse mucho cuando tenemos que declarar respuestas, por ejemplo json. No es necesario nombrarla porque solo se va a usar ahí y enviar.

```go
type bill struct {
  pi float32
  count int16
  flag bool
}

type alice struct {
  pi float32
  count int16
  flag bool
}

var b bill
var a alice

b = bill(a) // Cuando son nombrados debo asignarlo así. Implicit convertion
b = e2 // El anterior que no está nombrado. Es más flexible. Es un "literal value".

```

### Pointers
Comencemos diciendo que "lo que ves, es lo que obtenés". En un principio, todo en go, se pasa por valor.

de esta forma se pasa por referencia. Compartiendo la dirección de memoria con el `&` y luego apuntamos a ella con el `*`. De esta forma utilizamos la variable
del frame, no tenemos copias en memoria (en distintos frames). Compartimos la data entre frames (cruzamos "program boundaries")

```go

func main() {
  count := 10

  increment(&count)
}

func increment(inc *int){ // Necesitamos el tipo para saber escribir/leer la memoria
  *inc++
}

```

La memoria debajo del frame activo no tiene integridad, si main llama otra función va a pisar el frame que había construido para increment en el stack.

### Constants

```go
const ui = 12345

const ti int = 12345

const (
  A1 = iota // 0: starts at 0
  b1 = iota // 1: Increment by 1
  C1 = iota // 2: Increment by 1
)
```


### Arrays
Uso de memoria un bloque al lado del otro.

```go
var fruits [5]string

fruits[0] = "Hola"
...
...

for i, fruit := range fruits { // fruit es una copia, pasado como valor
  fmt.Println(i, fruit)
}

for i := range fruits { // No es como valor
  fmt.Println(i, fruits[i])
}
```

Se hace una copia del array, se hace una iteración sobre la copia del array original.

### Slices (la estructura más importante)

Usamos make cuando sabemos el tamaño

```go
fruits := make([]string, 5)

fruits[5] = "Runtine error" // No podemos acceder fuera de la longitud del slice

fruits2 := make([]string, 5, 8) // Slice de 5 elementos con capacidad para 8

var data []string // nil slice

data := []string{} // empty slice, este sí tiene un pointer
```

Podemos ir sumando a un slice haciendo `apend(data, value)` si data es un nil slice va a estar creando copias de arrays a medida que le agregamos elementos para poder ir cambiando su capacidad. Si nosotros definimos la capacidad de antemano y vamos agregando elementos no va a ir creando copias del slice ya que conoce su capacidad.

Crear un nuevo slice desde otro (se comparte el array, apunta al mismo array original):

```go

slice1 := make([]string, 5, 8){"apple", "orange", "banana", "grape", "plum"}

slice2 := slice1[2:4] //No incluye el 4. ["banana", "grape"]

slice2[0] = "changed" // slice1 -> ["apple", "orange", "changed", "grape", "plum"]

slice2 = append(slice2, "changed") // También altera el slice1. slice1 -> ["apple", "orange", "changed", "grape", "changed"]

```

Puedo por ejemplo, cambiar la capacidad cuando hago el slice2 y al append ya no altera el original.
Esto no comparte el array original, sino que crea uno nuevo en memoria. Las direcciones de memoria ya son diferentes.

```go
slice2 := slice1[2:4:4] // capacidad de 2
slice2 = append(slice2, "changed") // el slice1 queda intacto y el slice2 con uno más

```

Lo que podemos hacer sino es manualmente copiar un array, no se comparte el original

```go
slice3 := make([]string, len(slice1)) // mismo tamaño que el slice1
copy(slice3, slice1)
```

Hay que tener cuidado ccon situaciones como esta:
Tenemos un slice con elementos y cierta capacidad. Compartimos uno de los elementos y alteramos data del mismo. Luego hacemos un append, pero como no tenía más 
capacidad lo que hace es crear un nuevo slice con la capacidad necesaria. El elemento compartido anteriormente va a seguir apuntando al array anterior, si seguimos modificando data de ese elemento no la va a modificar en el nuevo array. Entonces, cuando hagamos append tenemos que tener mucho cuidado, fijarnos que podemos tener 
side effects y memory leaks.

### Maps

Se declara el tipo de elemento y el tipo de la key. Se puede hacer "literal construction". Usar keys con las que se pueda hashear, tipos que puedan estar en
un `if` statement, que pueda compararse.

```go
users := make(map[string]user)

users["Roy"] = user{"Rob", "Roy"} // value. es una copia

for key, value := range users { // cuando iteramos un map es random
  fmt.Println(key, value)
}

delete(users, "Roy")

u, found := users["Roy"] // la segunda 'found' sirve para saber si estaba o no la key.

fmt.Println("Roy", found, u) // -> Roy false {}
```

### Declare & receiver
Go tiene funciones y podemos escribir métodos, un método es una función que tiene un "receiver". Estos métodos, funciones con receivers, permiten a la data tener comportamiento. La pregunta también es: Cuando un pedazo de data debería tener comportamiento?

```go

type user struct {
  name string
  email string
}

func (u user) notify(){ // value receiver
  fmt.Printf("Sending User Email to %s<%s>\n", u.name, u.email)
}

func (u *user) changeEmail(email string) { // pointer receiver
  u.email = email
}


bill := user{"Bill", "bill@email.com"}
bill.changeEmail("bill@hotmail.com")
bill.notify()
```

Go se ajusta para hacer el llamado en ambas y lo que pasa en realidad es:

`(&bill).changeEmail..`
`(&bill).notify..`

Mientras no mezclemos "semánticas" no tendremos dolores de cabeza. Semántica de valor y semántica de puntero.

### Value & Pointer Semantics
Cuando elegimos una semántica tenemos que ser consistentes. Tenemos que elegir la que es razonable y práctica para el tipo que tenemos.

Tenemos 3 clases de tipos:
- Built-in: numerics, string y bool
- Reference: slices, maps, channels, interface values y funciones
- User defined: structs

Si usamos los built-in deberíamos usar value semantics. Incluso los campos dentro de un struct. La excepción es que se esté haciendo marshalling y un-marshalling.

Si usamos reference types lo mismo. Deberíamos usar value semantics. Solo una excepción, deberíamos obtener la dirección de memoria de un slice o array si estamos compartiendo dicha data en la call-stack para abajo y a una función que es llamada "decode" o "un-marshall".

para la tercer clase, tenemos que estar seguros de qué necesitamos. Si no lo sabemos, usamos pointer semantics, pero si estamos seguros, usemos value semantics que debería ser nuestra primer opción.

De esta forma las mutaciones podemos hacerlas de forma aislada retornando lo que nosotros queramos luego de dicha mutación. No tenemos side-effects

Ahora cuando tenemos un struct y tenemos que decidir que semántica usar.. podemos preguntarnos:
- Podemos hacer copias sin problemas?
- Tenemos que hacer copias de esto o necesitamos compartirlo?
- Es algo único? Tiene lógica que estemos copiandolo?

Podemos tener "Factory functions" las cuales dictan la semántica a utilizarse. Si la función retorna un valor, quiere decir que va a estar usando valoras y haciendo copias.

```go
type Time struct {
  sec int64
  nsec int32
  loc *Location
}

func Now() Time {
  sec, nsec := now()
  return Time{sec + unixToInternal, nsec, Local}
}

func div(t Time, d Duration) (qmod2 int, r Duration) { // Seguimos usando valores
  // code here
}
```

entonces para este caso la excepción para usar pointer semantics es cuando usamos unmarshall y relacionados. Neccesitan naturalmente punteros ya que van a ser mutados.

```go
func (t *Time) UnmarshalBinary(data []byte) error {}
func (t *Time) GobDecode(data []byte) error {}
func (t *Time) UnmarshalJSON(data []byte) error {}
func (t *Time) UnmarshalText(data []byte) error {}
```

Como vimos, los factory functions definen la semántica:

```go
func Open(name string) (file *File, err error) {
  return OpenFile(name, O_RDONLY, 0)
}
```

En este caso retorna un puntero a un tipo File. Esto quiere decir que se debería estar usando luego pointer semantics y compartir tipos File.

### Fuctions & Methods variables
Los métodos son escritos por nosotros, no son nativos. Es syntactic sugar para ver que la data tiene comportamiento. Creamos funciones que tienen
receivers

También puedo guardarme las funciones como valor, me guardo la firma:

```go
f1 := d.getName
f1()
```

Sólo hay que tener en cuenta que este `getName` puede tomar el receiver como valor o puntero. De ambas formas tenemos una indirección, 2 lugares que apuntan a lo mismo.

Si hacemos decoupling de data en Go lo que obtendremos va a hacer indirecciones y allocation (uso de memoria por las copias), tiene que estar justificado entonces el decoupling.

Darle comportamiento a la data no debería ser la regla sino al excepción.

### Interfaces

