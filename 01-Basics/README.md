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
