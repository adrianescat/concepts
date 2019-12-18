# FullStack cencepts

## SSH
Secure shell
Es un protocolo, como http, https, ftp entre otros. Estos son protocolos para comunicar máquinas entre si.

Por ejemplo, http te permite transmitir archivos como HTML, CSS, JS, Imágenes. Es entre browsers y pcs.
FTP sirve para transmitir archivos también desde una computadora a un servidor. HTTPS es muy parecido a HTTP pero este está encriptado, entonces terceros no pueden interceptar las conexiones. SSH es un protocolo más, que nos permite comunicar 2 computadoras a través de internet. Sirve para transferir archivos y hasta controlar otras computadoras de forma remota. La data va encriptada por lo tanto otros no pueden interceptar esa información. La diferencia con HTTPS es que es a nivel de Shell, es lo que se comunica con el sistema operativo.

Cómo se usa?

`ssh {user}@host`

Se usan 3 técnicas para este protocolo:
1. Encriptación simétrica.
2. Encriptación asimétrica.
3. Hashing.

1. Symmetrical encryption: Usa una llave secreta tanto para el cliente como también para el server. Se transmiten datos y desencriptan el mensaje usando la llave. Lo malo de esto es que cualquiera que de alguna forma obtenga esta key puede leer los mensajes. Por eso, para comunicarse y usar la key se utiliza lo que se llama "key exchange algorithm", una forma de compartir las keys sin que terceron intercepten el mensaje. Es seguro porque no se comparte la key secreta en si, sino que se comparte data pública y con eso se calcula la clave secreta. Mientras terceros no tengan este algoritmo no pueden calcular claves secretas por más que hayan conseguido las públicas.

2. Asymmetrical Encryption: Solo usado para compartir una clave secreta. Consume más tiempo que el anterior.

3. Hashing: Que pasa si un tercero quiere modificar el mensaje quee se envía de una pc a otra de una forma malintencionada? para eso existe el hashing. Es un tipo de criptografía. Lo que se hace es HMX, "Hash bassed Message Authentication Codes". Utilizando varias variables como el numero de secuencia, la llave y el dato que se quiere enviar, todo esto combinado en una función de hash. Ese resultado se le envía a la otra pc. Lo que hace la otra PC es sabiendo el numero de secuencia y teniendo la llave del otro. Ejecuta la misma función de hash para desencriptar el dato.


## Performance

Claves para mejorar performance:

- Mejoras en el frontend
- Mejores es como transmitimos los datos
- Mejoras en el backend

Primero recordemos lo básico, para ver una página lo que hacemos es hacer un request a un servidor con nuestro browser. Este servidor devuelve el o los arhcivos necesarios, por ejemplo un HTML y luego va pidiendo otros archivos a medida que parsea el HTML para mostrarlo en pantalla. Cuanto más pesado son estos archivos, el usuario más tiempo tarda en descargarlos. Siempre se debe tener en cuenta, sobretodo en esta era mobile, que las conexiones mobile no son tan buenas como el WIFI.

### Mejoras en la transmisión de datos

En este punto podemos hacer 2 cosas:

1. Reducir el peso de nuestros archivos: Se trata de "minificar" (reducir tamaño mediante distintas técnicas) los archivos. Para los JS podemos usar uglifyJS o similares por ejemplo. Para el HTML y css hay herramientas similares. Esto se realiza mediante herramientas de buildeo a la hora de crear una versión productiva, como webpack. El minificado de imágenes es algo más complicado
Primero que nada tenemos que tener en cuenta para qué se usa cada formate de imagen. JPG se usa para imágenes complejas con muchos colores por ejemplo, lo que no tienen es transparencia. Eso mismo lo provee PNG, reduce la cantidad de colores. Luego tenemos los SVG (vector graphics), son imágenes vectorizadas, pueden ser redimencionadas sin perder la calidad. Hay otros formatos nuevos, mejorados y optimizados pero todavía no están implementados en la mayoría de los browsers, estos son por ejemplo webp, JPG-X entre otros. "ImageOptim" es una muy buena herramienta para reducir el peso de las imágenes.
Luego lo que conviene es utilizar media queries y srcset para mostrar distintas imágenes según el tamaño del componente o lo que quiero mostrar. No conviene tener una imagen de 2000x1250 en una caja de 200x150 por ejemplo.. la iamgen debería tener esas dimensiones a lo sumo. Hay que usar CDNs (Content delivery neetwork) como "imgIX" y CLoudinary entre otros. Otra cosa que podemos hacer es eliminar la meta data de la imagen, el "exif", no es necesario tener eso en nuestras imágenes para un website.

2. Delivery optimizations: Primero que nada, enviar lo necesario. Preguntarse si necesitamos enviar libraries gigantes que solo hacen un querySelector por detrás por ejemplo, es decir, tenemos opciones nativas. Hay cierto límite además del peso que se puede enviar de un archivo y de cuantos archivos a la vez se pueden enviar. Combiene a veces ccombinar archivos como tener un "bundle JS", es decir todos nuestros archivos JS concatenados y combinados en un solo archivo. Lo mismo para los CSS. A veces conviene enviar los CSS que se van a mostrar primero y luego enviar los que el usuario va a ver en otras interacciones.

### Critical Rendering Path (CRP)
Con lo anterior ya optimizamos los archivos que se envían al cliente. Ahora, que sucede cuando estos llegan al mismo?
Cuando llega el HTML, el browser comienza a crear el DOM (document object model). Lo que hace es leer el HTML, tokenizar, parsear los tags y crear el arbol de elementos, esto último es el DOM. Al momento de parsear el DOM, si se encuentra con un tag de tipo <link> y tiene por ejemplo un archivo `css` lo que hace el browser es pedirlo. Una vez que llega sigue parseando el DOM. Al recibir todos los css el broser comienza a crear otro árbol, este es el CSSOM, es el CSS object model. Arma este arbol en base a los tags del DOM. Cuando el parser del broser se encuentra con un archivo JS lo que hace es pedirlo y una vez que llega lo lee, es decir, lo ejecuta. Una vez que termina de armar estos árboles, el browser arma un tercero llamado **Render tree**, este tiene la combinación de ambos árboles. De esta forma sabe renderizar todo.
Para renderizar en el browser, lo que hace primero es calcular la geografía de los elementos, es decir, el tamaño, ubñicación y cómo se relacionan geográficamente los elementos. Luego de eso pinta los pixeles en la pantalla. Con las imágenes pasa lo mismo, en cuanto encuentra la url comienza a pedirlas pero no es parte del proceso de renderizado, lo hace en background, una vez que son cargadas se muestran en la página.
En todo este proceso hay eventos o se les puso un nombre a ciertas instancias de este proceso. Por ejemplo:
*DOMContentLoaded* es cuando terminó de cargar todos los assets (css y JS) y comienza a crear el render tree.
el *Load* se da cuando termina todo este proceso pintando los pixeles en la pantalla.

Entonces, cómo optimizamos estas intancias?

1. LLega el HTML y comienza a leerlo: En esta instancia debemos poner los css que se descarguen al principio del HTML, CSS necesarios para el primer render y el above the fold. Podemos pedir lo restante luego. Los JS ponerlos al final del HTML con algunas excepciones. 
2. Encuentra CSS y los pide: CSS es render blocking, es decir bloquea la construcción del render tree. Entonces lo que hay que tener en cuenta es que hay que cargar solo lo necesario, above the fold en principio, media attributes y la menor especificidad posible. En el `window.onload` podemos traernos el resto de los CSS que faltan. Se puede hacer uso también de tags <style> para cargar cuanto antes el critical css.
3. Encuentra JS, los pide y ejecuta: JS es parser blocking, se bloquea mientras lo descarga y ejecuta. Para mejorar esta instancia podemos hacer unas cuantas cosas. En un principio podemos cargar JS de forma asincrónica. Para hacer esto último podemos poner el attribute `async` en un script tag y lo que hace es decirle a un worker que lo descargue en background, mientras sigue parseando el HTML y una vez que se descarga ejecuta el JS, luego de ejecutarlo sigue parseando. Como `async` funciona así, la recomendación es utilizar este cuando sabemos que el script no afecta al DOM o al CSSOM, porque podemos tener fallas al bloquear el parseo del HTML, no sabemos si todavía no tenemos los elementos que van a ser modificados en el JS (estamos hablando de scripts que no tienen ninguna relación con nuestro código, scripts como analytics, tracking scripts etc). La otra opción es usar `defer`, lo que hace esto es ir descargando en paralelo los JS que fue encontrando pero no los ejecuta al terminar de cconseguirlos, sino que comienza a ejecutarlos una vez el HTML terminó de ser parseado, y los ejecuta en orden de llegada. Esta opción es muy buena para scripts que interactuan con el render tree o DOM. Solo deberíamos cargar scripts sin async o defer cuando son super críticos, es core de nuestra aplicación. 
4. Render tree: Esta es la etapa siguiente de haber creado el DOM, el CSSOM y de haber encontrado css y JS para descargar y ejecutar. Lo que hay que tener en cuenta es que si luego tenemos un JS que modifica el DOM, lo que causa es un redibujado del render tree y provoca pasar por un layout y paint. Lo que tenemos que hacer es no tratar de manipular el DOM de forma erronea o hacer modificaciones innecesarias. Hay técnicas para evitar relayouts o repaints con css o javascript. React por ejemplo, con el uso del virtual dom, modificando solo las partes necesarias debido a interacciones del usuario o de la aplicación misma, logra cambiar el DOM solo lo necesario.
5. Hay otras técnicas como prefetch, preconnect, preloading, entre otras. Ver https://css-tricks.com/prefetching-preloading-prebrowsing/

Material importante:
- Performace Tool: https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/reference
- https://developers.google.com/web/fundamentals/performance/why-performance-matters

### HTTP/2
El objetivo de esta nueva versión del protocolo es mejor la latencia de red, es decir, qué tan rápido transmitimos los archivos.
Recursos: https://developers.google.com/web/fundamentals/performance/http2/?hl=es

### Optimizing code
Podemos implementar con la ayuda de webpack code splitting, podemos ir importando bloques de código a medida que los necesitamos. Chunks o Chunking. React loadable. React.lazy

### React Performance optimizations
- En un principio podemos utilizar el query string `?react_perf` y grabar con performance tool el comportamiento de nuestros componentes, con esto podemos evaluar como performa nuestra app y ver como renderizan los componentes, a veces tenemos uan cascada de componentes renderizandose y eso hace que todo sea más lento. Redux nos ayuda a renderizar directamente los componentes necesarios ya que cada uno puede subscribirse al store, no se van pasando props de padre a hijo.
- Podemos hacer uso de `shouldComponentUpdate` y de PureComponents

### Progressive Web Apps (PWAs)
Se trata de darle desde una web una experiencia al usuario lo más parecida a una app nativa. Para una web app supongamos que solo necesitamos JS, CSS y HTML. Para crear una app nativa necesitamos un lenguaje nativo como Java, Swift, Android etc. En una web app los assets se consumen a demanda, en una app nativa todos los archivos neccesarios están descargados previamente en el dispositivo. Las apps nativas pueden enviarte push notifications y hasta funcionar sin internet. Por este motivo las apps nativas funcionan mucho mejor que las apps webs en su gran mayoría.
Acá es donde entran las PWAs.
listado de PWAs: https://appsco.pe/
Podemos enfocarnos en 3 cosas que hacen a una PWA (algo más extenso acá https://developers.google.com/web/progressive-web-apps/checklist):
- HTTPS: Por seguridad y requerido para varias características propias de una PWA. Letsencript es un buen servicio para obtener un certificado.
- App Manifest: Es un JSON con la configuración necesaria para mostrar la aplicación correctamente, controlar íconos, colores, nombre de la app y muchas más cosas.
- Service Workers. _ver abajo el concepto_

Service Workers: Es un script que corre el browser en el background aparte de la aplicación. Es usado generalmente para features que no necesitan una página o la interacción del usuario. Esta herramienta es lo que nos ayuda a que nuestra aplicación funcione offline. La implementación del service worker depende de cada browser. No todos lo soportan. Podemos definir qué archivos queremos que estén en la cache del browser, a medida que va pidiendo un asset nuevo lo guarda en la cache, si vuelve a pedir el mismo el service worker se encarga de interceptar el pedido y entrar el asset antes de realizar un request.

## Tests