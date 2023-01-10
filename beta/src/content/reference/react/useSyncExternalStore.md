---
title: useSyncExternalStore
---
<Intro>

`useSyncExternalStore` es un Hook de React que te permite suscribirte a una fuente de almacenamiento de datos externa.

```js
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)
	@@ -16,16 +16,16 @@ const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?
---
## Uso {/*usage*/}
### Subscribiéndose a una fuente de almacenamiento datos externa {/*subscribing-to-an-external-store*/}
Normalmente la mayoría de tus componentes de React solo leerán datos de sus [propiedades,](/learn/passing-props-to-a-component), [estado,](/apis/react/useState) y [contexto.](/apis/react/useContext). Sin embargo, a veces un componente necesita leer algunos datos de alguna fuente de almacenamiento fuera de React que cambia con el tiempo. Esto incluye:
* Bibliotecas de gestión de estado de terceros que mantienen el estado fuera de React.
* APIs del navegador que exponen un valor mutable y eventos para suscribirse a sus cambios.
Llama a `useSyncExternalStore` en el nivel mas alto de tu componente para leer un valor de una fuente de datos externa.
```js [[1, 5, "todosStore.subscribe"], [2, 5, "todosStore.getSnapshot"], [3, 5, "todos", 0]]
import { useSyncExternalStore } from 'react';
	@@ -37,14 +37,14 @@ function TodosApp() {
}
```
Esto devuelve la <CodeStep step={3}>instantánea</CodeStep> del dato en la fuente de almacenamiento de datos. Necesitas pasar dos funciones como argumentos:
1. La <CodeStep step={1}>función `subscribe`</CodeStep> deberá suscribirse a la fuente de almacenamiento de datos y devolver una función que permita des suscribirse.
2. La <CodeStep step={2}>función `getSnapshot`</CodeStep> deberá obtener una instantánea del dato de la fuente de almacenamiento de datos.
React utilizará estas funciones para mantener tu componente suscrito a la fuente de almacenamiento de datos y volver a renderizarlo con los cambios.
Por ejemplo, en el sandbox de a continuación, `todosStore` se implementa como una fuente de almacenamiento de datos externa que almacena datos fuera de React. El componente `TodosApp` se conecta a esta fuente de almacenamiento externa de datos con el Hook `useSyncExternalStore`.
<Sandpack>
	@@ -69,11 +69,11 @@ export default function TodosApp() {
```
```js todoStore.js
// Este es un ejemplo de una fuente de almacenamiento de datos de terceros
// que podría necesitar integrar con React.
// Si su aplicación está completamente construida con React,
// recomendamos usar el control de estado de React en su lugar.
let nextId = 0;
let todos = [{ id: nextId++, text: 'Todo #1' }];
	@@ -106,15 +106,15 @@ function emitChange() {
<Note>
Cuando sea posible, recomendamos usar el control de estado incorporado en React con [`useState`](/apis/react/useState) y [`useReducer`](/apis/react/useReducer) en su lugar. La `useExternalSyncStore` API is principalmente útil si necesita integrarse con código existente que no sea de React.
</Note>
---
### Subscribiéndose a una API de navegador {/*subscribing-to-a-browser-api*/}
Otra razón para usar `useSyncExternalStore` es cuando desea suscribirse a algún valor expuesto por el navegador que cambia con el tiempo. Por ejemplo, suponga que desea que su componente muestre si la conexión de red está activa. El navegador expone esta información a través de una propiedad llamada [`navigator.onLine`.](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/onLine) Este valor puede cambiar con el tiempo sin que React sea notificado, por lo que necesitas leerlo con `useSyncExternalStore`.
```js
import { useSyncExternalStore } from 'react';
	@@ -124,16 +124,15 @@ function ChatIndicator() {
  // ...
}
```
Para implementar la función `getSnapshot`, lee el valor actual de la API del navegador:
```js
function getSnapshot() {
  return navigator.onLine;
}
```
A continuación, debes implementar la función `subscribe`. Por ejemplo, cuando `navigator.onLine` cambia, el navegador activa los eventos [`online`](https://developer.mozilla.org/en-US/docs/Web/API/Window/online_event) y [`offline` ](https://developer.mozilla.org/en-US/docs/Web/API/Window/offline_event) en el objeto `window`. Debe suscribir el argumento `callback` a los eventos correspondientes y luego devolver una función que limpie estas suscripciones:
```js
function subscribe(callback) {
	@@ -146,7 +145,7 @@ function subscribe(callback) {
}
```
Ahora React sabe cómo leer el valor de la API `navigator.onLine` externa y cómo suscribirse a sus cambios. Intente desconectar su dispositivo de la red y observe que el componente se vuelve a renderizar en respuesta:
<Sandpack>
	@@ -176,11 +175,11 @@ function subscribe(callback) {
---
### Extrayendo la lógica en un Hook personalizado {/*extracting-the-logic-to-a-custom-hook*/}
Por lo general, no deberías escribir `useSyncExternalStore` directamente en tus componentes. En su lugar, normalmente lo llamarás desde tu propio Hook personalizado. Esto te permite usar la misma fuente de almacenamiento externa desde diferentes componentes.
Por ejemplo, este Hook personalizado `useOnlineStatus` monitoriza si la red está en línea:
```js {3,6}
import { useSyncExternalStore } from 'react';
	@@ -199,7 +198,7 @@ function subscribe(callback) {
}
```
Ahora diferentes componentes pueden llamar a `useOnlineStatus` sin repetir la implementación subyacente:
<Sandpack>
	@@ -261,14 +260,14 @@ function subscribe(callback) {
---
### Añadiendo soporte para la renderización en el servidor {/*adding-support-for-server-rendering*/}
Si su aplicación React usa [renderización en el servidor,](/apis/react-dom/server), sus componentes React también se ejecutarán fuera del entorno del navegador para generar el HTML inicial. Esto crea algunos desafíos cuando se conecta a una fuente de datos externa:
- Si se está conectando a una API unicamente de navegador, no funcionará porque no existe en el servidor.
- Si se está conectando a una fuente de datos externa de terceros, necesitará que sus datos coincidan entre el servidor y el cliente.
Para resolver estos problemas, pase una función `getServerSnapshot` como tercer argumento a `useSyncExternalStore`:
```js {4,12-14}
import { useSyncExternalStore } from 'react';
	@@ -291,26 +290,26 @@ function subscribe(callback) {
}
```
La función `getServerSnapshot` es similar a `getSnapshot`, pero solo se ejecuta en dos situaciones:
- Se ejecuta en el servidor al generar el HTML.
- Se ejecuta en el cliente durante la [hidratación](/apis/react-dom/client/hydrateRoot), es decir, cuando React toma el HTML del servidor y lo hace interactivo.
Esto le permite proporcionar la instantánea del valor inicial que se utilizará antes de que la aplicación se vuelva interactiva. Si no hay un valor inicial significativo para la representación del servidor, puede [forzar el componente para que se renderize solo en el cliente.](/apis/react/Suspense#providing-a-fallback-for-server-errors-and-server-only-content)
<Note>
Asegúrate de que `getServerSnapshot` devuelva exactamente los mismos datos en el renderizado inicial del cliente que en el que es devuelto en el servidor. Por ejemplo, si `getServerSnapshot` devolvió algún contenido prepopulado de la fuente de almacenamiento externa en el servidor, debes transferir este contenido al cliente. Una forma común de hacer esto es emitir una etiqueta `<script>` que establece una propiedad global como `window.MY_STORE_DATA` durante la renderización del servidor, y que permitirá poder leer esa propiedad global desde el cliente en `getServerSnapshot`. Tu fuente de almacenamiento externa debería proporcionar instrucciones sobre cómo hacer esto.
</Note>
---
## Referencia {/*reference*/}
### `useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)` {/*usesyncexternalstore*/}
Llame a `useSyncExternalStore` en el nivel superior de su componente para leer un valor de una fuente de almacenamiento de datos externa.
```js
import { useSyncExternalStore } from 'react';
	@@ -322,38 +321,38 @@ function TodosApp() {
}
```
Devuelve la instantánea de los datos en la fuente de almacenamiento de datos externa. Necesitas pasar dos funciones como argumentos:
1. La función `subscribe` debe suscribirse a la fuente de almacenamiento de datos externa y devolver una función que cancela dicha suscripción.
2. La función `getSnapshot` debería obtener una instantánea de los datos de la fuente de almacenamiento de datos externa.
[Ver más ejemplos arriba.](#uso)
#### Parametros {/*parameters*/}
* `subscribe`: Una función que toma un solo argumento `callback` y lo suscribe a la fuente de almacenamiento de datos externa. Cuando la fuente de almacenamiento de datos externa cambia, debe invocar el `callback` proporcionado. Esto hará que el componente se vuelva a rerenderizar. La función `subscribe` debería devolver una función que limpia dicha suscripción.
* `getSnapshot`: Una función que devuelve una instantánea de los datos de la fuente de almacenamiento externa de datos que necesita el componente. Si bien la fuente de almacenamiento externa de datos no ha cambiado, las llamadas repetidas a `getSnapshot` deben devolver el mismo valor. Si la fuente de almacenamiento externa de datos cambia y el valor devuelto es diferente (usando para la comparación [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) ), React volverá a rerenderizar el componente.
* **opcional** `getServerSnapshot`: Una función que devuelve la instantánea inicial de los datos de la fuente de almacenamiento externa de datos. Se usará solo durante la renderización desde el servidor y durante la hidratación del contenido renderizado por el servidor en el cliente. La instantánea del servidor debe ser la misma entre el cliente y el servidor, y generalmente se serializa y pasa del servidor al cliente. Si no se proporciona esta función, la representación del componente en el servidor generará un error.
#### Devuelve {/*returns*/}
La instantánea actual de la fuente de almacenamiento externa de datos que puede usar en su lógica de representación.
#### Advertencias {/*caveats*/}
* La instantánea de la fuente de almacenamiento externa de datos devuelta por `getSnapshot` debe ser inmutable. Si el almacén subyacente tiene datos mutables, devuelva una nueva instantánea inmutable si los datos han cambiado. De lo contrario, devuelva la última instantánea almacenada en caché.
* Si se pasa una función `subscribe` diferente durante un re-renderizado, React se volverá a suscribir a la fuente de almacenamiento externa de datos usando la función `subscribe` recién pasada. Puede evitar esto declarando `subscribe` fuera del componente.
---
## Solución de problemas {/*troubleshooting*/}
### Recibo un error: "El resultado de `getSnapshot` debería almacenarse en caché" {/*im-getting-an-error-the-result-of-getsnapshot-should-be-cached*/}
Si obtiene este error, significa que su función `getSnapshot` devuelve un nuevo objeto cada vez que se llama, por ejemplo:
```js {2-5}
function getSnapshot() {
	@@ -364,9 +363,9 @@ function getSnapshot() {
}
```
React volverá a rerenderizar el componente si el valor de retorno de `getSnapshot` es diferente al de la última vez. Por eso, si siempre devuelves un valor diferente, entrarás en un bucle infinito y obtendrás este error.
Tu objeto `getSnapshot` solo debería devolver un objeto diferente si algo realmente ha cambiado. Si tu fuente de almacenamiento de datos externa contiene datos inmutables, puede devolver esos datos directamente:
```js {2-3}
function getSnapshot() {
	@@ -375,52 +374,52 @@ function getSnapshot() {
}
```
Si los datos de tu fuente de almacenamiento de datos externa son mutables, tu función `getSnapshot` debería devolver una instantánea inmutable de la misma. Esto significa que *sí* necesita crear nuevos objetos, pero no debería hacer esto en cada llamada. En su lugar, debe almacenar la última instantánea calculada y devolver la misma instantánea que la última vez si los datos almacenados no han cambiado. La forma en que determina si los datos mutables han cambiado depende de cómo se implemente tu fuente de almacenamiento de datos externa mutable.
---
### Mi función `subscribe` se llama después de cada re-renderizado {/*my-subscribe-function-gets-called-after-every-re-render*/}
Esta función `subscribe` se define *dentro* de un componente, por lo que es diferente en cada re-renderizado:
```js {4-7}
function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  // 🚩 Siempre una función diferente, por lo que React se volverá a suscribir en cada re-renderizado
  function subscribe() {
    // ...
  }
  // ...
}
```
React se volverá a suscribir a su fuente de almacenamiento de datos externa si pasa una función de `subscribe` diferente entre re-renderizaciones. Si esto causa problemas de rendimiento y desea evitar volver a suscribirse a la fuente de almacenamiento de datos externa, mueva la función `subscribe` fuera:
```js {6-9}
function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  // ...
}
// ✅ Siempre la misma función, por lo que React no necesitará volver a suscribirse
function subscribe() {
  // ...
}
```
Alternativamente, puedes envolver `subscribe` con [`useCallback`](/apis/react/useCallback) para solo re-suscribirte cuando algún argumento cambie:
```js {4-8}
function ChatIndicator({ userId }) {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  // ✅ Same function as long as userId doesn't change
  const subscribe = useCallback(() => {
    // ...
  }, [userId]);
  // ...
}
```
