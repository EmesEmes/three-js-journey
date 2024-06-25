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