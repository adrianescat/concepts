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
Los tests se puden dividir en 3 grandes grupos:
- Unit tests: testeamos de individualmente funciones o clases. Los más fáciles de implementar.
- Integration tests: testeamos como distintas funciones, clases o servicios funcionan con otras partes de nuestra aplicación.
- Automation tests: testeamos el correcto funcionamiento en la aplicación o en el browser, simulando interacciones del usuario.

Hay un montón de libraries para testear y dependiendo lo que neccesitemos tendremos que ir descargándolas. Tenemos Jest, Jasmine o Mocha como library que ayuda de scaffolding para los test.
Luego necesitamos una library para hacer assertions, Jasmine, Jest o Chai pueden funcionar bien.
Luego necesitamos un runner de tests, Jasmine, Jest, Mocha o Karma funcionan.
Por otro lado necesitamos Spys, Mocks y Stubs. Jasmine, Jest y Sinon.js funcionan.
Los spys nos sirven para recaudar información sobre funciones, es decir, cuantas veces se llamó una función, quien la llamó etc
Los stubs reemplazan funciones específicas por otras funciones para comprobar que tienen el funcionamiento esperado. Fingimos comportamiento pudiendo arrojar dintintos resultados.
Los Mocks son pra fingir comportamiento.
Por último necesitaremos una library para ver el code coverage, podemos usar istanbul o jest

Jest parece ser la library más completa.
Jest cheatsheet: https://github.com/sapegin/jest-cheat-sheet

## SPA vs SSR
Renderizando client-side el tiempo de render claramente es más largo. Debemos descargar un bundle JS y además no tenemos tanto HTML en el documento inicial. Una vez se descarga el JS, se ejecuta y según el framework utilizado comienza a pintar los pixeles en la pantalla, en el caso de react, creando los componentes y todo lo referente a nuestra APP. El beneficio es que una vez que está esto cargado no debemos ir al server a pedir por más, generalmente tenemos todo precargado en el cliente y logra una experiencia muy rápida. Hay que recurrir a loadings, skeletons etc para cubrir las pantallas blancas.

Para server-side, en el caso de react se utiliza `renderToString` de `react-dom/server` para renderizar en el servidor HTML puro desde un componente <App /> y en el cliente se utiliza `React.hydrate`. Este último no toca el HTML descargado del servidor sino que cargo los event-handlers, si no corremos `hydrate` no es interactivo. Necesitamos el `window` object del browser para attachear eventos, no podemos hacerlo desde el server.

las contras del CSR:
- tiempo de carga
- poco potencial SEO

las buenas del CSR:
- Interacciones fluidas y rápidas
- Web applications

Las contras del SSR:
- reloads
- el rendering es más lento
- más requests al server

las buenas del SSR:
- Mucho potencial SEO
- Renderizado incial muy rápido

Web components: https://developers.google.com/web/fundamentals/web-components

## Security

### Injections
en JS en vez de usar innerHTML deberíamos usar `document.createTextNode(input)` cuando el `input` proviene de forma externa o por el usuario ya que lo que hace el primer método es sanitizar y convertirlo en texto puro por más que tenga JS.
Qué podemos hacer para evitar injections entonces?
- Sanitizar inputs, parámetros etc ([validator](https://www.npmjs.com/package/validator))
- Parametrizar queries. No queriar directo con el input del usuario
- Usar ORMs o Knex.js

### 3rd party libraries
Hay una gran variedad de herramientas que permiten auditar libraries.
La idea es siempre saber lo que uno incluye en el proyecto, ver github, las starsm los forks etc.

### Logging
Llevar un registro. Hay paquetes muy buenos como winston o morgan

### HTTPS Everywhere
Protocolo encriptado de comunicación, no pueden intrometerse terceros. Certificados gratis en https://letsencrypt.org/

### XSS & CSRF
Ejecutar código en un sitio ajeno. Lo más frecuente es robarse la información del usuario, lo que se encuentra en sus cookies.
El segundo es cross site request forgery, es hacer que un usuario haga algo por nosotros. Para evitarlo podemos setear un header llamado `Content-Security-Policy` y configurar cuales son los endpoints en los que confiamos para hacer requests.

### Code secrets
No mostrar keys o información crítica. Utilizar variables de entorno en le servidor, searializar o tokenizar del lado del cliente.

### Secure headers
Incluir headers que nos proveen seguridad y buenas técnicas para prevenir hackeos. Hay un paquete muy bueno llamado `helmet`.

### Access Control
Usar CORS, dar la menor cantidad de permisos posibles.

### Data Management
Utilizar encriptación tanto en le pasaje de datos críticos en el cliente como en la base de datos, incluso en la data redundante. Hay muchas herramientas como Aragon2, bcrypt pgcrypto etc

## Code Analysis
En un proyecto node lo que debemos hacer es primero mirar el package.json, ver las dependencias y comenzar a ver las herramientas que utiliza la aplicación. Googlear las que no conocemos.
Luego deberíamos continuar por el "entry-point", sería el archivo principal que ejecuta node, ahí veremos seguramente la configuración de express o simil, base de datos, rutas etc. Podríamos seguir revisando los endpoints, las rutas para saber como levantar el proyecto y comenzar a obtener respuestas del servidor.
Mirando las rutas podemos seguir distintos flujos de la aplicación, mirando middlewares, controllers y models.

## Docker
Para poder correr nuestra aplicación en distintos ambientes de forma correcta, con las versiones de las dependencias correctas sin errores o bugs indeseados que puedan darse en cada máquina, nodo, pc de desarrollador etc existe una solución y son los "contenedores". La mayoría de las aplicaciones hoy en día no se construyen sobre un monolito (una aplicación que lo hace todo) sino que se lo hace desarrollando micro servicios, distintas partes que funcionan independientemente comunicándose entre si. Cada una en su propio container.
La idea de separar nuestra aplicación en partes pequeñas fue popularizada gracias a servicios de contenedores como docker. Cada uno de estos servicios puede tener sus propias dependencias, configuraciones, distintas versiones de paquetes, de node etc. Además, si tuviésemos que instalar nuestro micro-servicio en una máquina nueva, cada vez que lo hagamos tendríamos que configurar todo el ambiente de desarrollo específico para dicho servicio. No sería ideal instalar todo esto en un solo paso con un solo comando sin preocuparse de las versiones a usar, servicios, paquetes o lo que sea? Esto soluciona docker.
Qué es un container:
En un principio tenemos el host, es la máquina donde vamos a alojar nuestro container.
Luego tenemos el container que se crea con docker. Dentro de este tenemos una "imagen". El host no sabe qué es lo que tiene el container, solo tiene que poder correr docker. La imagen contiene todo lo que el container debe hacer. Lo que se puede tener también es un file-system dentro del container para poder hacer uso de archivos también.
Tenemos a nustra disposición hub.docker.com donde podemos descargar imágenes pre-configuradas.
Como el host (el servidor de AWS o nuestra pc) no conoce el container y su contenido, lo que hay que hacer es lo que se llama "port fowarding", que consiste en exponer un puerto desde el container para que el host pueda comunicarse.
Docker compose es una herramienta de docker que funciona como orquestador, hace el setup de todos los servicios (database, redis, node app etc) con un solo comando.
Para mapear los archivos locales a los que tiene el container, lo que podemos hacer es crear volúmenes. Si hacemos cambios en los archivos locales se cambian también en el container.

## Redis
NoSQL en memoria BD. **Es un KVS**. Utilizado para data de corta duración. Se guarda en memoria y son pequeños pedazos de información. Se utiliza también cuando queremos acceder rápido a pequeños pedazos de información y que podemos llegar a perder información. Puede llegar a guardar snapshots en el disco por seguridad.

## Sessions and JWT
Tenemos 2 formas de hacerlo. La más vieja de todas es usar cookies, la más nueva es usar tokens.
Cómo funciona con Cookies?
Desde el browser se envía una petición al server como por ejemplo `POST /authenticate?username=Juan&password...`. Lo que hace el server es revisar si este usuario existe en su base de datos y si es así devuelve al cliente una respuesta 200 seteando en el mismo una cookie "session" con un string aleatorio. Este string aleatorio representa el browser en cuestión. Luego, con cada petición que haga el browser controlamos que se esté enviando siempre esta cookie para saber si es correcta la petición.
Esto es statefull. En ambos lados tiene que guardarse la info de quién está logueado.

Cómo funciona con tokens? En este caso JWT
Un usuario se loguea, el browser envía `POST /authenticate?username=Juan&password...` al servidor, este revisa si existe el usuario, si está bien la password y en vez de devolver un 200 con una cookie en caso de encontrarlo, lo que hace es enviar un token (JWT), muy similar a un string ya que se encuentra serializado, y se guarda en el browser en vez de guardarse en una cookie. En la session store, local store entre otras opciones. Luego, con cada petición se envía este JWT y lo único que debe hacer el server es verificarlo, no hace falta que se comunique con BDs.
Esto es stateless. No hace falta guardarse qué usuarios están logueados. Solo se decodifica el token y se asegura que sea válido.

Utilizando tokens tenemos ciertas ventajas como autenticar usuarios de forma muy sencilla en distintas APIs, el token solo tiene que ser válido. Con cookies es posible pero mucho más complejo. Otra ventaja sería que funciona mucho mejor para plataformas mobiles, ya que usar cookies en estas plataformas es mucho más complejo.

Hay mucho debate de cual es la mejor opción:
dzone.com/articles/cookies-vs-tokens-the-definitive-guide
stackoverflow.com/questions/17000835/token-authentication-vs-cookies
scotch.io/bar-talk/why-jwts-suck-as-session-tokens

*Podemos combinar ambas soluciones, guardando el JWT en una redis por ejemplo.*

## AWS
**EC2** basic server.
**S3** Object storage service. Key or ID tiene acceso a un file. Cualquier tipo de archivo, sirve mucho para guardar imágenes.
**Lambda** Le das una función y le decís cuando querés que la corra. Super escalable y rápido. Amazon te cobra cada vez que se ejecuta, no por tiempo. Podemos usar "Serverless" para deployar una lambda function desde command line en vez de usar su dashboard.
**CloudFront** es una especie de CDN
**DynamoDB** NoSQL super rápida. KVS.

## Backend Performance

### CDN
Content Delivery Network. Cachea archivos en servers ubicados alrededor del mundo. El contenido es servido a los clientes desde el servidor más cercano al cliente. Cloudflare es el servicio de CDN de amazon.

### GZIP
Comprimimos los archivos para que pesen significativamente mucho menos. Son menos bytes que se envían por la red. La mayoría de los browsers soportan GZIP. Podemos comprimir casi cualquier tipo de archivo. NO hay razón para no hacerlo. Muchos servicios hoy en día lo hacen por defecto. Hay una mejor solución hoy en día, creada por Google, llamada Brotli, tiene cerca de los 20% de compresión más que GZIP. Todavía no está implementado por todos los browsers. Se puede ver en los headers "content-enconding: br".

### Databases scaling
1. Identificar queries ineficientes.
2. Incrementar memoria.
3. Escalar verticalmente. Delegamos algunas cosas a otras bases de datos como a un KVS por ejemplo.
4. "Sharding". Es tener nuestra app distribuido en distintas partes, según las queries que hagamos vamos a buscarlo a un shard o al otro. Muy complejo de implementar. ElasticSearch (document storage) puede realizar esto.
5. Más BDs. 
6. Tipo de BD. Según las características y las necesidades podemos elegir una o la otra.

### Caching
CPUs, RAM y Discos. Los tres cuentan con formas de guardar información para obtenerla lo más pronto posible.
CPUs tienen "registros", son las más rápidas pero de poca capacidad.
RAM (Random access memory). Puede guardar mucha información en memoria, es muy rápida.
Discos duros o SSD (más rápidos). Aún así son más lentos que las RAM (su información perdura).
Hay muchísimas formas de implementar el caching en el desarrollo web.
Ejemplo: CDNs cachean nuestros estáticos.
En el server podemos cachear guardando en un KVS, en una variable en memoria etc.
También podemos cachear en el cliente. Utilizando por ejemplo service workers.
Del lado del servidor podemos utilizar los headers: max-age, etag entre otros para controlar manualmente como cacheamos los archivos.
Cuando vemos el status 304 de un request, "not modified" es cuando el browser se fija en el ETAG del header, un hash autogenerado por express, con el cual nos daremos cuenta si el archivo cambia o no.

https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching

## Load Balancing
Para servir los archivos podemos usar herramientas como Nginx o Apache, los cuales mediante cierta condifuración van a servir nuestros archivos según los requests que se hagan. Estas herramientas pueden usarse como load balancers. Lo que se hace es ante una petición, esta llega a un servidor Nginx y este decide a que servidor ir, supongamos que tenemos 3, Nginx elige el que esté más ocioso. Lo que puede hacer también es cachear las respuestas para no tener que ir al server otra vez.

## CI/CD
Continuous integration / Continuous Delivery
Herramienta para mantener un orden en nuestra aplicación a medida que crece el equipo y la app misma. Para entregar software de calidad necesitamos de estos procesos. Asegurarnos que no tiene bugs, que está todo testeado o incluso que cumple con ciertos estándares.

Continuous integration: Es la forma de tener organizado un repositorio compartido por varios developers, donde se realizarán PRs, con cada uno de estos puede correrse cosas automáticas como cove coverage, building y tests entre otras, podemos anticipar errores, mantener la calidad del código y ahorrarnos tiempo.

Continuous delivery: es la práctica de mantener nuestro código deployable todo el tiempo.

Continuous deployment: es la práctica de deployar automáticamente luego de que pasaron los tests, code coverage, building etc. No hay procesos manuales de subida.

Herramientas: Jenkins, CircleCI, Bammbo y más.

## SRE Site Reliability Engineering
Segun Google, lo que hace SRE es lo que hacía hace un tiempo un equipo de operaciones. Pero ahora es un conjunto de software Engineers
expertos que están predispuestos y tienen la habilidad de diseñar e implementar software automatizado para reemplazar el trabajo manual.
Se manejan con tickets así se organizan y logran mantener el servicio estable y operable. Este equipo debería quedar con muy poco laburo operacional
y enteramente dedicado al desarrollo. Porque el servicio en sí, corre y se repara automáticamente.
Este equipo es responsable de: availability, latency, performance, efficiency, change management, monitoring, emergency response, and capacity planning of their service(s).
El negocio debe definir el uptime o availability esperada. Si se desea estar 99.60%, el otro 0.40% es la cantidad de errores que tiene el servicio.
- Utilizar Canaries antes de cualquier deploy masivo.
- No siempre es lo mejor apuntar al 100%. Simplemente hay que definir el riesgo que se quiere a la innovación y la fiabilidad del sitio. El SLO nos ayuda a saber qué tantos releases podemos hacer.

### SLO Service Level Objectives.
definir objetivos como:
- 99% (en 1 min) GET RPC calls completas en menos de 100ms