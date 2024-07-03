# Apuntes

## Three.js
Three.js es una biblioteca de JavaScript que permite crear y mostrar gráficos 3D en un navegador web. Utiliza WebGL para renderizar escenas 3D de manera eficiente. Con Three.js, los desarrolladores pueden crear experiencias visuales interactivas y complejas en la web, incluyendo juegos, visualizaciones de datos, y simulaciones. La biblioteca proporciona una amplia gama de funcionalidades, como cámaras, luces, materiales, sombras, y soporte para modelos 3D, facilitando el desarrollo de aplicaciones 3D sin necesidad de profundizar en los detalles de bajo nivel de WebGL.

## WebGL
WebGL (Web Graphics Library) es una API de JavaScript para renderizar gráficos 3D y 2D dentro de cualquier navegador web compatible, sin necesidad de plugins adicionales. Se basa en OpenGL ES, una versión de OpenGL optimizada para dispositivos móviles y embebidos. WebGL permite a los desarrolladores crear aplicaciones gráficas complejas y de alto rendimiento que se ejecutan directamente en el navegador, aprovechando la aceleración por hardware de la GPU del dispositivo. Esto lo hace ideal para juegos, visualizaciones de datos, arte digital, y cualquier otra aplicación que requiera gráficos interactivos en la web.

### pasos para instalar vite y Three.js
1. `npm install vite`
2. `npm install three`

### Utilizar Three.js
En un archivo JavaScript se debe importar Three.js

```javascript
import * as THREE from 'three'
```
Esto nos dará acceso a la mayoría de clases y métodos de Three.js. Para utilizar una clase de Three.js necesitamos instanciarla. 


Se necestia 4 elementos para un escena básica:
1. `Scene/Escena`: Es un contenedor donde se colocan los objetos, modelos, partículas, luces, etc.
Ejemplo:
```javascript
const scene = nre THREE.Scene()
```

2. `Objects/Objetos`: Pueden ser cosas como geometrías primitivas, modelos importados, partículas, luces, etc. Necesitamos un objeto visible que se denomina `Mesh`, que es una combinación de una geometría(la forma) y un material(como se verá).
```javascript
const geometry = new THREE.BoxGeometry(1, 1, 1)
const material = new THREE.MeshBasicMaterial({color: 0xff0000})
const mesh = new THREE.Mesh(geometry, material)
scene.add(mesh)
```

3. `Camera/Cámara`: Es un punto de vista teórico usado cuando se renderiza. Podemos tener multiples cámaras pero usualmente solo utilizamos una. Toma mínimo dos parámetros el campo de vista(expresado en grados) y el relación de aspecto/aspect ratio(es el ancho del canvas dividido por el alto).
```javascript 
const sizes = {
    width: 800,
    height: 600
}
const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height)
scene.add(camera)
```

4. `Renderer/Renderizador`: Renderiza la escena vista desde el punto de vista de la cámara. El resultado será dibujado dentro de un canvas. Podemos crear el canvas o dejar que el renderizador lo genere y después lo agregue a la página web.
```javascript
const canvas = document.querySelector('canvas.webgl')

const renderer = new THREE.WebGLRenderer({
    canvas: canvas
})
renderer.setSize(sizes.width, sizes.height)
renderer.render(scene, camera)
```

En este punto estaría todo listo, pero no veremos nada porque estaríamos "dentro" del objeto y no se puede ver el objeto desde dentro, así que tenemos que mover la cámara para atrás. Podemos mover los objetos usando su posición, rotación y escala.
```javascript
camera.position.z = 3
```

> Un canvas es un elemento HTML que se utiliza como un lienzo en el cual se pueden dibujar gráficos mediante programación, usando JavaScript. Es parte de HTML5 y permite la renderización de gráficos 2D y, con la ayuda de bibliotecas adicionales como WebGL, también se pueden renderizar gráficos 3D.

## Tranformar un objeto 
Para mover un objeto podemos utilizar su posición, escale, rotación y el cuaternio. Se puede modificar estos valores ya sea de la cámara, del mesh o de cualquier objeto que herede del `Object3D` por ejemplo: PerspectiveCamera.
```javascript
mesh.position.x = 0.7
mesh.position.y = - 0.6
mesh.position.z = 1
```
### position
Se puede cambiar la posición también utilizando el objeto `Vector3` u otros que contienen métodos útiles. La propiedad `position` también hereda de Vector3.

Se puede actualizar las tres posiciones al mismo tiempo utilizando el método `set`.
```javascrip
mesh.position.set(0.7, -0.6, 1)
```

Posicionar objetos en el espacio puede ser difícil, pero se puede utilizar `AxesHelper` para agregar líneas en los ejes, el Eje X es de color rojo, el eje Y es de color verde y el eje Z es de color azul.
```javascript 
const axesHelper = new THREE.AxesHelper(3)
scene.add(axesHelper)
```

### escale
En Three.js, la propiedad scale se refiere a la escala de un objeto en el espacio 3D. Esta propiedad permite modificar el tamaño de un objeto en cualquiera de los tres ejes (x, y, z) de manera independiente. Al igual que position y rotation, scale es una instancia de Vector3, lo que significa que tiene tres componentes: x, y, y z, que representan la escala del objeto en cada uno de esos

Modificar la escala de un objeto puede hacer que parezca más grande o más pequeño sin cambiar sus proporciones originales, a menos que se escale de manera no uniforme (diferente valor en cada eje). Por ejemplo, si se establece la escala de un objeto a (2, 2, 2), el objeto será dos veces más grande en cada dimensión que su tamaño original.

```javascript
mesh.scale.x = 2
mesh.scale.y = 0.5
mesh.scale.z = 0.5
```

También se puede usar los métodos heredados de Vector3.
```javascript
mesh.scale.set(2, 0.5, 0.5)
```

### rotation
En Three.js, rotate se refiere a la operación de rotar un objeto en el espacio 3D. Cada objeto que hereda de Object3D, incluyendo cámaras, luces y mallas (meshes), tiene propiedades y métodos que permiten controlar su rotación. La rotación puede ser especificada en términos de ángulos de Euler (usando las propiedades rotation.x, rotation.y, rotation.z para los ejes X, Y y Z, respectivamente) o mediante cuaterniones para una representación más compleja y libre de problemas como el bloqueo de cardán (gimbal lock).

Para modificar la rotación de un objeto, puedes ajustar directamente las propiedades de rotation o utilizar métodos como rotateX(), rotateY(), y rotateZ(), que rotan el objeto alrededor de los ejes X, Y y Z respectivamente, por un ángulo especificado en radianes.

Se puede utilizar la propiedad `rotation` o también la propiedad `quaternion`. Actualizar cualquiera de las propiedades lo hará también a la otra.
```javascript 
mesh.rotation.x = Math.PI * 0.25
mesh.rotation.y = Math.PI * 0.25
```

Instancias de Object#d tienen el método `lookAt(...)` que es utilizado para orientar un objeto hacia otro punto en el espacio 3D. Este método ajusta la orientación del objeto de tal manera que su eje local -Z apunta hacia el objetivo especificado. Es comúnmente usado para cámaras, permitiendo que la cámara "mire" hacia un objeto o una posición específica, pero también puede ser usado con cualquier tipo de objeto que herede de Object3D.


### Quaternion/Cuaternión
Un cuaternión, en el contexto de gráficos 3D y Three.js, es una forma de representar rotaciones que evita algunos de los problemas asociados con los ángulos de Euler, como el bloqueo de cardán (gimbal lock). Un cuaternión se compone de cuatro componentes: uno real y tres imaginarios, y se utiliza para representar una rotación alrededor de un eje en el espacio tridimensional.

Los cuaterniones son útiles en animaciones y rotaciones complejas porque proporcionan una interpolación suave entre orientaciones (conocida como interpolación esférica o slerp) y no sufren el problema del bloqueo de cardán.

En Three.js, puedes usar cuaterniones para controlar la rotación de objetos de la siguiente manera:

1. Directamente a través de la propiedad quaternion de un objeto: Cada objeto en Three.js que puede ser rotado tiene una propiedad quaternion que representa su orientación actual en el espacio. Puedes ajustar esta propiedad directamente para cambiar la orientación del objeto.

2. Usando métodos para establecer la rotación: Three.js ofrece métodos como setRotationFromQuaternion(quaternion) que te permiten definir la rotación de un objeto a partir de un cuaternión.

3. Interpolación entre cuaterniones: Puedes usar cuaterniones para crear animaciones suaves rotando un objeto desde una orientación inicial a una final. Three.js proporciona métodos como Quaternion.slerp(quaternionStart, quaternionEnd, t) para realizar esta interpolación.

> Se puede poner diferentes objetos en un grupo y así escalarlos, rotarlos y posicionarlos a todos los objetos a la vez, un grupo hereda de la clase Object3D.
```javascript
const group = new THREE.Group()
scene.add(group)

const cube1 = new THREE.Mesh(
    new THREE.BoxGeometry(1, 1, 1),
    new THREE.MeshBasicMaterial({ color: 0xff0000})
)
group.add(cube1)

const cube2 = new THREE.Mesh(
    new THREE.BoxGeometry(1, 1, 1),
    new THREE.MeshBasicMaterial({ color: 0x00ff00 })
)
group.add(cube2)
```

## Animaciones/Animation
Las animaciones en Three.js son técnicas que permiten crear movimiento o transformaciones en los objetos 3D a lo largo del tiempo. Esto puede incluir cambios en la posición, rotación, escala, o cualquier otra propiedad que afecte la apariencia o comportamiento de un objeto en la escena. Las animaciones son fundamentales para dar vida a las escenas 3D, creando efectos visuales dinámicos o simulando comportamientos físicos.

En Three.js, las animaciones pueden ser implementadas de varias maneras:

1. `Animaciones de fotogramas clave (Keyframe Animations)`: Utilizan una serie de valores predefinidos para las propiedades de los objetos en momentos específicos. Three.js interpola automáticamente entre estos valores para crear transiciones suaves.

2. `Animaciones basadas en bucles de animación`: Se ejecutan dentro del ciclo de renderizado de la aplicación, donde las propiedades de los objetos se actualizan en cada cuadro basándose en el tiempo o en una lógica específica. Esto es útil para animaciones continuas o dependientes del tiempo real.

3. `Sistemas de partículas`: Permiten la animación de grandes cantidades de objetos pequeños, como chispas, humo, o lluvia, creando efectos complejos a partir de la interacción de elementos individuales.

4. `Animaciones con cuaterniones`: Para rotaciones complejas sin sufrir el problema del bloqueo de cardán, utilizando interpolación esférica (slerp) entre orientaciones.

5. `Uso de la biblioteca de animación de Three.js (AnimationMixer y AnimationClip)`: Three.js proporciona un sistema de animación que permite reproducir, pausar, detener y mezclar animaciones importadas o definidas en el código.

### Frames
En el contexto de Three.js y la programación de gráficos 3D en general, un "frame" se refiere a un único cuadro o imagen en una secuencia de animación o visualización. Cada frame representa el estado visual de la escena en un momento específico. Cuando se renderizan múltiples frames en rápida sucesión, se crea la ilusión de movimiento o animación.

Three.js utiliza un bucle de animación, comúnmente implementado con la función `requestAnimationFrame`, para actualizar y renderizar la escena repetidamente a una alta tasa de frames por segundo (FPS). Esto permite animaciones fluidas y la interactividad en tiempo real. En cada iteración del bucle de animación (es decir, en cada frame), se pueden realizar cambios en la escena, como mover objetos, cambiar colores, aplicar transformaciones, y luego renderizar la escena actualizada

> La mayoria de las pantallas corren a 60 frames por segundo(FPS). Pero hay computadoras que pueden correr a una mayor tasa de frames.

> Las animaciones deben verse igual en distintos dispositivos independientemente de la tasa de frames. 

> Si hay una tasa baja de frames puede significar que tenemos problemas de rendimiento en el código.

### Animar utilizando requestAnimationFrame()
requestAnimationFrame es una función de JavaScript que le dice al navegador que quieres realizar una animación y solicita que el navegador programe el repintado de la ventana para el próximo ciclo de animación. La función toma como argumento otra función a la que llamar antes de realizar el repintado. requestAnimationFrame es parte de la Web API y es utilizada en Three.js y otras bibliotecas de gráficos para crear animaciones suaves y eficientes.

En el contexto de Three.js, requestAnimationFrame se utiliza para crear un bucle de animación que actualiza y renderiza la escena a una tasa óptima para el navegador. Esto es crucial para animaciones 3D, ya que asegura que la escena se actualice de manera eficiente y en sincronía con la tasa de refresco del dispositivo, proporcionando una experiencia de usuario fluida.
```javascript
const tick = () => {
  // Update Objects
  mesh.rotation.x += 0.01

  // Render
  renderer.render(scene, camera)

  window.requestAnimationFrame(tick)
}

tick()
```

Para poder lograr que las animaciones se vean igual en varios dispositivos sin que importe la tasa de refresco que estos tengan, debemos saber cuanto tiempo a pasado desde el último `tick`, para esto podemos utilizar `Date.now` para obtener la `marca de tiempo(timestamp)` actual. La marca de tiempo es cuantos milisegundo han pasado desde el 1 de enero de 1970 por lo que es una medida de tiempo única.

```javascript
// Time
let time = Date.now()

// Animations
const tick = () => {
  // Time
  const CurrentTime = Date.now()
  const deltaTime = CurrentTime - time
  time = CurrentTime
  

  // Update Objects
  mesh.rotation.x += 0.002 * deltaTime

  // Render
  renderer.render(scene, camera)

  window.requestAnimationFrame(tick)
}

tick()
```

### Animar utilizando Clock
En Three.js, Clock es una clase que facilita el seguimiento del tiempo transcurrido. Se utiliza comúnmente para gestionar y sincronizar animaciones o cualquier otra operación que dependa del tiempo. Un objeto Clock puede medir el tiempo transcurrido desde su creación o desde la última vez que se llamó a su método .getDelta(), que devuelve el tiempo en segundos (no milisegundos, como Date.now()) desde la última vez que se invocó dicho método.

```javascript
// Clock
const clock = new THREE.Clock()

// Animations
const tick = () => {
  // Clock
  const elapsedTime = clock.getElapsedTime()  

  // Update Objects
  mesh.position.y = Math.sin(elapsedTime)
  mesh.position.x = Math.cos(elapsedTime)

  // Render
  renderer.render(scene, camera)

  window.requestAnimationFrame(tick)
}

tick()
```

### Animar utilizando una librería
Para utilizar librerías externas para animar en Three.js, una opción popular es GSAP (GreenSock Animation Platform). GSAP es una potente biblioteca de animación que puede animar cualquier propiedad numérica de JavaScript, lo que la hace ideal para su uso con Three.js para animaciones complejas y de alto rendimiento.

`npm install gsap`
```javascript
import gsap from 'gsap'

gsap.to(mesh.position, {
  duration: 1,
  delay: 1,
  x: 2
})
gsap.to(mesh.position, {
  duration: 1,
  delay: 2,
  x: 0
})
```

## Cámara/Camera
En Three.js, una camera (cámara) es un objeto que determina qué parte de la escena se renderiza en el lienzo (canvas). Es esencialmente el punto de vista desde el cual el usuario ve la escena. Three.js ofrece varios tipos de cámaras, pero los más comunes son PerspectiveCamera y OrthographicCamera.

Es una clase abstracta por lo que no la utilizamos directamente ya que PerspectiveCamera y las demás heredan desde esta clase.

### ArrayCamera
La ArrayCamera en Three.js es una cámara especial que permite renderizar múltiples vistas de una escena en un solo pase de renderizado. Se compone de un arreglo de cámaras (PerspectiveCamera o OrthographicCamera), cada una configurada para renderizar una parte específica de la escena. Esto es particularmente útil para aplicaciones como la realidad virtual (VR), donde se necesita renderizar una vista para cada ojo, o para crear efectos de visualización complejos como paneles de visualización múltiple dentro de una misma escena.

La ArrayCamera funciona al asignar a cada cámara del arreglo una porción del lienzo de renderizado. Por ejemplo, en una configuración de realidad virtual, podrías tener dos PerspectiveCamera dentro de tu ArrayCamera, cada una configurada para renderizar a la mitad del lienzo, una para el ojo izquierdo y otra para el ojo derecho, creando así una experiencia inmersiva.

### StereoCamera
La StereoCamera en Three.js es una cámara especializada que se utiliza para crear efectos de visión estereoscópica, simulando la forma en que los ojos humanos perciben el mundo en tres dimensiones. Esto es especialmente útil en aplicaciones de realidad virtual (VR) o en cualquier experiencia que busque aumentar la sensación de profundidad y espacio en 3D.

A diferencia de la ArrayCamera, que puede contener múltiples cámaras para diferentes propósitos, la StereoCamera está específicamente diseñada para trabajar en pares, generando dos perspectivas ligeramente desplazadas de la misma escena, una para cada ojo. Este desplazamiento imita la separación entre los ojos humanos y crea un efecto de paralaje que el cerebro interpreta como profundidad, resultando en una experiencia 3D convincente.

La StereoCamera en Three.js automáticamente calcula y ajusta las vistas para el ojo izquierdo y derecho basándose en una cámara principal. Esto simplifica el proceso de renderizado estereoscópico, ya que el desarrollador solo necesita preocuparse por la configuración de la cámara principal, y StereoCamera se encarga de generar las dos vistas necesarias para la experiencia estereoscópica.

### CubeCamera
La CubeCamera es un tipo de cámara en Three.js que se utiliza para crear mapas de entorno (environment maps) en forma de cubo alrededor de un punto en el espacio. Estos mapas de entorno se pueden utilizar para efectos de reflexión o refracción en materiales, permitiendo que los objetos en la escena reflejen su entorno de manera realista.

a CubeCamera funciona generando seis renderizados internos para cada una de las seis direcciones (arriba, abajo, izquierda, derecha, adelante, atrás) desde un punto en el espacio, creando así un mapa de entorno cúbico. Este mapa se puede aplicar luego a materiales que soporten reflexiones o refracciones, como MeshStandardMaterial o MeshPhysicalMaterial, para simular superficies reflectantes o transparentes como vidrio o agua.

### OrthographicCamera
La OrthographicCamera en Three.js es un tipo de cámara que utiliza una proyección ortográfica para renderizar la escena. A diferencia de la PerspectiveCamera, que simula la forma en que el ojo humano percibe el mundo con objetos más distantes que aparecen más pequeños, la OrthographicCamera renderiza todos los objetos con el mismo tamaño, sin importar su distancia a la cámara. Esto significa que no hay perspectiva en la imagen resultante, lo que puede ser útil para ciertos tipos de juegos (como juegos de estrategia en tiempo real o plataformas 2D), visualizaciones arquitectónicas, o cualquier aplicación donde se desee este efecto específico.

Para crear una OrthographicCamera en Three.js, necesitas especificar los límites del frustum de la cámara en el espacio de la escena: izquierda, derecha, arriba, abajo, cerca y lejos. Estos límites definen una caja dentro de la cual todo será renderizado con el mismo tamaño.

La proyección ortográfica es especialmente útil para:
1. Representaciones técnicas o arquitectónicas donde se requiere precisión en las dimensiones y proporciones.
2. Juegos o escenas donde se desea mantener el mismo tamaño de los objetos, independientemente de su distancia a la cámara.
3. UIs o HUDs donde se necesitan elementos gráficos que no se distorsionen con la perspectiva.

```javascript
const camera = new THREE.OrthographicCamera(-1, 1, 1, -1, 0.1, 1000)
```

### PerspectiveCamera
La PerspectiveCamera en Three.js es un tipo de cámara que simula la perspectiva tal como la ve el ojo humano, donde los objetos más cercanos aparecen más grandes y los más lejanos más pequeños. Esta cámara utiliza una proyección en perspectiva, que es la forma más común de proyección utilizada en la mayoría de los juegos y aplicaciones 3D para crear una sensación de profundidad y realismo.

La PerspectiveCamera se define principalmente por dos parámetros: el campo de visión (fov, que es el ángulo de visión en grados en la dirección vertical) y la relación de aspecto (aspect, que es la relación entre el ancho y el alto del lienzo de renderizado). Además, se especifican los planos near y far, que determinan los límites del espacio que la cámara puede ver. Solo los objetos que se encuentran dentro de este espacio serán renderizados.

```javascript
const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height, 1, 1000)
```
1. El primer parámetro que acepta es `campo de visión/field of view` (75), está escrito en grados y es el ángulo de visión vertical, también se le conoce como `fov`.
2. El segundo parámetro es `relación de aspecto/aspect ratio` que es el ancho del render dividido por el alto (sizes.width / sizes.height).
3. El tercer parámetro que toma es `near` (1). Este parámetro define el plano cercano (near clipping plane) del frustum de la cámara. En otras palabras, es la distancia mínima a la que la cámara puede ver objetos. Todo objeto que esté más cerca de la cámara que este valor no será renderizado o visible en la escena. El propósito de este parámetro es ayudar a definir el volumen de visión de la cámara, lo cual es crucial para el rendimiento y la calidad visual de la aplicación 3D. 
4. El cuarto parámetro es `far` (1000). Este parámetro define el plano lejano (far clipping plane) del frustum de la cámara. Es la distancia máxima a la que la cámara puede ver objetos. Todo objeto que esté más lejos de la cámara que este valor no será renderizado o visible en la escena. El propósito de este parámetro es limitar la profundidad de la escena que se debe renderizar, lo cual es importante tanto para el rendimiento como para la calidad visual. Al no renderizar objetos que están demasiado lejos para ser vistos claramente, se puede ahorrar en recursos de computación y evitar el uso innecesario de memoria y tiempo de procesamiento. 

> Al ajustar adecuadamente los planos cercano y lejano (near y far), se puede optimizar el uso del buffer de profundidad, minimizando problemas de precisión y maximizando También ayuda reduciendo artefactos visuales como el z-fighting (cuando dos o más objetos compiten por el mismo píxel en el buffer de profundidad).

### Mover la cámara con el mouse
Primero debemos obtener las coordenadas del mouse.

```javascript
const cursor = {
    x: 0,
    y: 0
} 
window.addEventListener('mousemove', (event) => {
    cursor.x = event.clientX / sizes.width - 0.5
    cursor.y = event.clientY / sizes.height -0.5
})
```

Dentro de la función `thick` actualizamos la posición de la camára con las coordenadas del cursor.
```javascript 
// Update camera
camera.position.x = Math.sin(cursor.x * Math.PI * 2) * 2
camera.position.y = cursor.y * 5
camera.position.z = Math.cos(cursor.x * Math.PI * 2) * 2
camera.lookAt(mesh.position)
```

### built-in controls
Three.js tiene integradas multiples clases llamadas controles que nos ayudan a mover la cámara.

#### FlyControls
FlyControls es una de las varias clases de controles integradas en Three.js que permite al usuario mover la cámara libremente por la escena, como si estuviera volando. Esto se logra mediante el uso del teclado y/o el mouse para controlar la dirección y velocidad de la cámara. FlyControls ofrece una forma interactiva y versátil de explorar escenas 3D desde diferentes ángulos y posiciones, lo que puede ser especialmente útil en aplicaciones como simuladores, juegos, o visualizaciones arquitectónicas.

Para utilizar FlyControls, primero se debe incluirlos en el proyecto, ya que pueden no estar incluidos en el build principal de Three.js. Luego, se puede instanciar FlyControls pasando la cámara que se desea controlar y el elemento DOM (usualmente el canvas) sobre el cual se renderiza la escena.

#### FirstPersonControls
FirstPersonControls es otra clase de controles integrada en Three.js diseñada para permitir al usuario navegar por una escena 3D desde una perspectiva en primera persona. Al igual que FlyControls, FirstPersonControls permite al usuario mover la cámara, pero de una manera que simula el movimiento de caminar o correr por el entorno virtual, manteniendo la cámara a una altura constante y permitiendo girar la vista en horizontal y vertical para mirar alrededor.

Este tipo de controles es ideal para aplicaciones como juegos en primera persona, simulaciones de caminata, o cualquier entorno virtual donde se desee dar al usuario la sensación de estar "dentro" de la escena, moviéndose como si estuvieran caminando o corriendo.

Para usar FirstPersonControls en Three.js, primero se debe asegurar de que la clase esté incluida en el proyecto. Luego, se puede instanciar FirstPersonControls pasando la cámara que se desea controlar y, opcionalmente, el elemento DOM (usualmente el canvas) sobre el cual se renderiza la escena. Después de instanciar, se puede configurar varias propiedades para personalizar el comportamiento de los controles, como la velocidad de movimiento y la sensibilidad de la rotación.

#### PointerLockControls
PointerLockControls es una clase de controles integrada en Three.js diseñada para facilitar la creación de experiencias de navegación en primera persona en una escena 3D, de manera similar a FirstPersonControls. La principal diferencia y ventaja de PointerLockControls es que captura y bloquea el cursor del mouse dentro de la ventana del navegador, permitiendo una experiencia de usuario más inmersiva y controlada. Esto es especialmente útil en juegos en primera persona o simulaciones donde se desea que el usuario tenga un control total sobre la cámara sin la distracción o limitación del movimiento del cursor fuera de la ventana del juego o aplicación.

Cuando se activa el bloqueo del puntero, los movimientos del mouse mueven directamente la cámara en la escena, sin mostrar el cursor del mouse, lo que permite un control más fluido y natural de la vista en primera persona. El usuario puede mirar alrededor en todas las direcciones girando el mouse, y esta funcionalidad se combina comúnmente con el teclado para moverse dentro de la escena.

Para utilizar PointerLockControls en Three.js, primero se debe asegurar de que la clase esté incluida en el proyecto. Luego, se instancian los controles pasando la cámara que se desea controlar. Es importante manejar también la lógica para solicitar al usuario que permita el bloqueo del puntero al hacer clic en la ventana o presionar una tecla específica, ya que esta acción requiere una interacción del usuario por razones de seguridad y usabilidad.

#### TrackballControls
TrackballControls es una clase de controles en Three.js que permite al usuario interactuar con la escena 3D de una manera similar a como lo haría con un trackball físico. Esto significa que el usuario puede rotar la escena alrededor de un punto de interés, acercar (zoom) y alejar la vista, y panear (mover la vista lateralmente) utilizando el mouse o gestos táctiles. La principal diferencia entre TrackballControls y otros controles como OrbitControls es la forma en que se maneja la rotación; TrackballControls ofrece una experiencia más libre, permitiendo rotaciones en cualquier dirección, como si estuvieras girando un objeto en tus manos.

Para utilizar TrackballControls en Three.js, primero se debe asegurar de que la clase esté incluida en el proyecto, ya que puede no estar incluida en el build principal de Three.js. Luego, se instancian los controles pasando la cámara que se desea controlar y el elemento DOM (usualmente el canvas) sobre el cual se renderiza la escena. Después de instanciar, se pueden configurar varias propiedades para personalizar el comportamiento de los controles, como la sensibilidad del zoom, la velocidad de rotación, y si el pan debe ser permitido.

#### TransformControls
TransformControls es una clase de controles en Three.js que permite al usuario interactuar con objetos dentro de la escena 3D de manera que pueda trasladarlos (moverlos), rotarlos y escalarlos de forma intuitiva. Esta clase es especialmente útil en aplicaciones de edición o diseño 3D, donde se requiere que el usuario tenga la capacidad de manipular objetos directamente dentro de la escena.

Para utilizar TransformControls en Three.js, se deben seguir estos pasos básicos:
1. Asegurarse de que la clase TransformControls esté incluida en el proyecto.
2. Instanciar TransformControls pasando la cámara que se está utilizando y el elemento DOM (usualmente el canvas) sobre el cual se renderiza la escena.
3. Agregar TransformControls a la escena.
4. Asociar TransformControls con el objeto que se desea manipular. Esto se hace mediante el método attach(object), donde object es el objeto 3D que se quiere manipular.
5. Escuchar eventos de control para actualizar la escena cuando el usuario interactúe con el objeto. Esto es necesario para que los cambios se reflejen en tiempo real.

#### DragControls
DragControls es una clase de controles en Three.js que permite al usuario interactuar con objetos dentro de la escena 3D arrastrándolos con el mouse. Esta funcionalidad es útil en aplicaciones donde se requiere que el usuario tenga la capacidad de reposicionar objetos en la escena de manera intuitiva, como en aplicaciones de diseño, juegos de estrategia, o interfaces de usuario donde la manipulación directa de objetos es necesaria.

Para utilizar DragControls en Three.js, se deben seguir estos pasos básicos:
1. Incluir la clase DragControls en el proyecto: Asegurarse de que DragControls esté disponible, ya que puede no estar incluido en el build principal de Three.js.
2. Instanciar DragControls: Crear una nueva instancia de DragControls, pasando un arreglo de objetos que se desean hacer arrastrables, la cámara que se está utilizando, y el elemento DOM (usualmente el canvas) sobre el cual se renderiza la escena.
3. Configurar eventos: DragControls emite varios eventos como dragstart, drag, y dragend, que se pueden utilizar para ejecutar lógica específica durante el inicio del arrastre, mientras se arrastra, y cuando se termina de arrastrar un objeto, respectivamente.

#### OrbitControls
OrbitControls es una clase de controles en Three.js que permite al usuario rotar, acercar (zoom) y alejar la vista alrededor de un punto de interés, generalmente el centro de la escena, mediante el uso del mouse o gestos táctiles. Esta clase es ampliamente utilizada en aplicaciones de visualización 3D, como visualizadores de modelos, aplicaciones educativas, y cualquier entorno donde se desee inspeccionar objetos desde diferentes ángulos sin cambiar la posición de la cámara en el espacio.

Para utilizar OrbitControls, primero se debe incluir en el proyecto, ya que puede no estar incluido en el build principal de Three.js. Luego, se instancian los controles pasando la cámara que se desea controlar y el elemento DOM (usualmente el canvas) sobre el cual se renderiza la escena.

* Importar OrbitControls
```javascript
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
```

* Instanciar OrbitControls
```javascript
const controls = new OrbitControls(camera, canvas)
```


## Geometries/Geometrías
En Three.js, una geometría es una estructura de datos que contiene toda la información necesaria para describir la forma de un objeto 3D. Esto incluye, principalmente, los vértices (puntos en el espacio) y las caras (polígonos que conectan estos puntos) que definen la superficie del objeto. Además, la geometría puede incluir información adicional como las normales (vectores perpendiculares a las caras que ayudan a determinar cómo interactúa la superficie con la luz), colores de vértices, y coordenadas de mapeo de texturas (que permiten aplicar imágenes a las superficies de los objetos para darles detalle y realismo).

Three.js ofrece varias clases de geometrías predefinidas, como BoxGeometry (para crear cubos o paralelepípedos), SphereGeometry (para esferas), CylinderGeometry (para cilindros), entre otras, facilitando la creación de formas comunes. También es posible definir geometrías personalizadas especificando manualmente los vértices y las caras.

### BufferGeometry
BufferGeometry en Three.js es una clase que representa una geometría de manera más eficiente en comparación con la clase Geometry tradicional. Mientras que Geometry almacena los datos de los vértices, las caras, y otros atributos de forma estructurada y fácil de entender y manipular en JavaScript, BufferGeometry organiza estos datos en buffers o arreglos planos. Esto permite una transferencia más directa y rápida de los datos a la GPU para su procesamiento, lo que resulta en un rendimiento significativamente mejorado, especialmente en escenas complejas o con un gran número de objetos.

La clase BufferGeometry es especialmente útil para aplicaciones que requieren una alta eficiencia en la renderización de gráficos 3D, como juegos, visualizaciones de datos complejas, y simulaciones. Permite un control detallado sobre los atributos de los vértices, incluyendo posición, color, normales, coordenadas de textura, y más, todo mientras mantiene la memoria y el procesamiento al mínimo.

```javascript
// Crear una instancia de BufferGeometry
const geometry = new THREE.BufferGeometry();

// Crear un arreglo de puntos (vértices) para la geometría
const vertices = new Float32Array([
  -1.0, -1.0,  1.0,
   1.0, -1.0,  1.0,
   1.0,  1.0,  1.0,
]);

// Crear un buffer de atributo para los vértices y añadirlo a la geometría
geometry.setAttribute('position', new THREE.BufferAttribute(vertices, 3));

// Crear un material
const material = new THREE.MeshBasicMaterial({color: 0xff0000});

// Crear un mesh usando la geometría y el material
const mesh = new THREE.Mesh(geometry, material);

// Añadir el mesh a la escena
scene.add(mesh);
```

### Geometrías incorporadas/built-in
Las geometrías incorporadas en la API de Three.js son clases predefinidas que facilitan la creación de formas comunes en 3D. A continuación, se enumeran algunas de las más utilizadas:
* BoxGeometry: Para crear cubos o paralelepípedos.
* CircleGeometry: Para crear círculos o discos.
* ConeGeometry: Para crear conos.
* CylinderGeometry: Para crear cilindros.
* DodecahedronGeometry: Para crear dodecaedros (poliedros de 12 caras).
* EdgesGeometry: Para crear un conjunto de líneas que representan los bordes de una geometría.
* ExtrudeGeometry: Para extruir una forma a lo largo de un camino, creando una geometría 3D.
* IcosahedronGeometry: Para crear icosaedros (poliedros de 20 caras).
* LatheGeometry: Para generar geometrías rotando un conjunto de puntos alrededor de un eje.
* OctahedronGeometry: Para crear octaedros (poliedros de 8 caras).
* PlaneGeometry: Para crear planos o superficies rectangulares.
* RingGeometry: Para crear anillos o discos con un agujero en el centro.
* SphereGeometry: Para crear esferas o bolas.
* TetrahedronGeometry: Para crear tetraedros (poliedros de 4 caras).
* TorusGeometry: Para crear toroides (forma de dona).
* TorusKnotGeometry: Para crear nudos toroidales, que son toroides con un patrón de trenzado complejo.
* TubeGeometry: Para crear tubos o cilindros con una trayectoria curva.
* WireframeGeometry: Para crear una geometría de alambre a partir de otra geometría, mostrando solo los bordes.
* TextGeometry permite crear geometrías 3D a partir de texto, lo que es útil para añadir etiquetas, títulos, o cualquier otro texto en 3D a tus escenas.