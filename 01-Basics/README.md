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

