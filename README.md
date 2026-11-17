# Gestión de Postres
> Panel para administrar las cuentas, el inventario, los pedidos y las compras de un emprendimiento de postres.

## Español

### ¿Qué es?

Gestión de Postres es una aplicación web de una sola página pensada para un emprendimiento de repostería. Reúne en un mismo panel el control de ingresos y gastos, el inventario de insumos, la toma de pedidos con datos de entrega y una lista de compras con presupuesto estimado. Todo el código (HTML, CSS y JavaScript) vive en un único archivo `index.html`, y los datos se guardan en Firebase (Authentication y Firestore).

Es un proyecto académico o prototipo personal: cada usuario ve únicamente sus propios datos y no hay módulos de facturación formal, impuestos ni roles administrativos.

### Características

- **Registro e inicio de sesión** con correo y contraseña mediante Firebase Authentication, con sesión persistente y cierre de sesión.
- **Aislamiento por usuario**: cada documento guarda el `userId` y todas las consultas filtran por ese identificador.
- **Cuentas**: registro de ingresos y gastos, cálculo de ganancia y margen, y filtro de estadísticas por hoy, esta semana o este mes.
- **Gasto con inventario**: al registrar un gasto se puede indicar que corresponde a la compra de un insumo; en ese caso se crea también el producto en el inventario con costo total y costo unitario calculados.
- **Inventario**: alta de productos con cantidad, unidad (kg, g, L, ml, unidad, paquete), costo y stock mínimo; tabla con costo unitario, valor total y estado; consumo de cantidades con validación de existencias; eliminación de productos.
- **Indicadores de inventario**: total de productos, valor del inventario y cantidad de productos con stock bajo.
- **Pedidos**: registro de cliente, teléfono, postre, precio, ruta de entrega y fecha de entrega; al crear un pedido se registra automáticamente un ingreso; permite editar, completar (elimina el pedido) y eliminar; la lista se ordena por fecha de entrega.
- **Lista de compras**: ítems con nombre, cantidad en texto libre, costo estimado, categoría (ingredientes, envases, decoración, herramientas, otros) y notas; presupuesto total; filtro por categoría; marcar ítems como comprados, marcar todos y eliminar los comprados.
- **Manejo de errores de Firebase**: mensajes específicos cuando faltan permisos en Firestore o cuando una consulta requiere un índice, con enlace para crearlo.

### Stack técnico

| Componente | Uso |
|---|---|
| HTML5 + CSS3 | Interfaz y estilos dentro del mismo `index.html` |
| JavaScript (ES modules) | Toda la lógica en un `<script type="module">` |
| Firebase 10.7.1 (modular) | Authentication (correo/contraseña) y Cloud Firestore |
| SDK web por CDN | Importaciones directas desde `gstatic.com`; no hay build ni `package.json` |

### Estructura del proyecto

```
Postres/
└── index.html   # Estructura, estilos, pantallas de login/registro y lógica de la app
```

Dentro de `index.html` el código se organiza así:

- Estilos en el `<head>`.
- Pantallas de login, registro y aplicación (`loginScreen`, `registerScreen`, `appScreen`).
- Cuatro pestañas: `cuentas`, `inventario`, `pedidos` y `compras`.
- Un módulo de JavaScript que inicializa Firebase, autentica, carga datos y expone las acciones como funciones globales (`registrarGasto`, `agregarProducto`, `registrarPedido`, etc.).

### Cómo ejecutarlo

1. Sirve el archivo con un servidor estático; los módulos ES y Firebase no funcionan correctamente con `file://`:

   ```bash
   python -m http.server 5500
   ```

2. Abre `http://localhost:5500`.
3. Crea una cuenta desde la pantalla de registro o inicia sesión con un usuario existente.
4. En la consola de Firebase, habilita el proveedor **Correo electrónico/contraseña** en Authentication y crea la base de datos de Firestore.
5. La aplicación necesita conexión a internet para cargar los SDK y acceder a los datos.

### Datos y configuración

**Firebase**: se usan **Authentication** (correo y contraseña) y **Cloud Firestore**. La configuración del proyecto está en el objeto `firebaseConfig`, al inicio del script de `index.html`. El repositorio incluye la configuración del proyecto del autor; no se reproduce aquí. Para reutilizar el código, crea tu propio proyecto y reemplaza esos valores.

**Colecciones de Firestore** (todas con `userId`):

| Colección | Contenido principal |
|---|---|
| `gastos` | `concepto`, `monto`, `esInventario`, `fecha`, `timestamp` |
| `ingresos` | `cliente`, `monto`, `fecha`, `timestamp` |
| `inventario` | `nombre`, `cantidad`, `unidad`, `costoTotal`, `costoUnitario`, `stockMin`, `fecha`, `timestamp` |
| `pedidos` | `nombre`, `telefono`, `postre`, `precio`, `ruta`, `fechaEntrega`, `fecha`, `timestamp` |
| `listaCompras` | `nombre`, `cantidad`, `costoEstimado`, `categoria`, `notas`, `comprado`, `fecha`, `timestamp` |

No se usa `localStorage`: la fuente de verdad es Firestore. Solo se mantiene una caché en memoria (`listaComprasCache`) para filtrar la lista de compras.

### Notas y limitaciones

- Toda la aplicación está en un solo archivo, lo que dificulta el mantenimiento a medida que crece.
- Las consultas con `where` + `orderBy` pueden requerir índices compuestos; la interfaz muestra el enlace de creación cuando ocurre.
- La lista de compras no edita ítems: solo permite agregarlos, marcarlos como comprados y eliminarlos.
- Completar un pedido elimina el documento, de modo que no queda un historial de pedidos entregados.
- El ingreso se registra al crear el pedido, no al entregarlo; editar un pedido no actualiza el ingreso asociado.
- No hay control de impuestos, moneda distinta al colón costarricense ni reportes exportables.
- Las reglas de Firestore que la propia aplicación sugiere solo exigen estar autenticado; no restringen el acceso por propietario, aunque las consultas sí filtran por `userId`. Conviene ajustar las reglas para validar `request.auth.uid == resource.data.userId`.

---

## English

### What is it?

Gestión de Postres is a single-page web app for a home baking business. It brings together income and expense tracking, ingredient inventory, order taking with delivery details, and a shopping list with an estimated budget. All the code (HTML, CSS, and JavaScript) lives in a single `index.html`, with data stored in Firebase (Authentication and Firestore).

It is an academic project or personal prototype: each user only sees their own data, and there are no formal invoicing, tax, or admin-role modules.

### Features

- **Sign-up and login** with email and password through Firebase Authentication, with persistent sessions and logout.
- **Per-user isolation**: every document stores a `userId`, and every query filters by that identifier.
- **Accounts**: income and expense entry, profit and margin calculation, and statistics filtered by today, this week, or this month.
- **Expense with inventory**: when recording an expense, the user can mark it as an ingredient purchase; a product is then created in inventory with computed total and unit cost.
- **Inventory**: product creation with quantity, unit (kg, g, L, ml, unit, pack), cost, and minimum stock; table with unit cost, total value, and status; quantity consumption with stock validation; product deletion.
- **Inventory metrics**: total products, inventory value, and low-stock count.
- **Orders**: customer, phone, dessert, price, delivery route, and delivery date; creating an order automatically records income; supports editing, completing (deletes the order), and deleting; the list is sorted by delivery date.
- **Shopping list**: items with name, free-text quantity, estimated cost, category (ingredients, packaging, decoration, tools, other), and notes; total budget; category filter; mark items as purchased, mark all, and delete purchased items.
- **Firebase error handling**: specific messages for missing Firestore permissions or missing query indexes, with a link to create them.

### Tech stack

| Component | Purpose |
|---|---|
| HTML5 + CSS3 | UI and styles inside the same `index.html` |
| JavaScript (ES modules) | All logic in one `<script type="module">` |
| Firebase 10.7.1 (modular) | Authentication (email/password) and Cloud Firestore |
| Web SDK via CDN | Direct imports from `gstatic.com`; no build step or `package.json` |

### Project structure

```
Postres/
└── index.html   # Markup, styles, login/register screens, and app logic
```

Inside `index.html` the code is organized as:

- Styles in the `<head>`.
- Login, register, and app screens (`loginScreen`, `registerScreen`, `appScreen`).
- Four tabs: `cuentas`, `inventario`, `pedidos`, and `compras`.
- One JavaScript module that initializes Firebase, authenticates, loads data, and exposes actions as global functions (`registrarGasto`, `agregarProducto`, `registrarPedido`, etc.).

### How to run

1. Serve the file with a static server; ES modules and Firebase do not work properly over `file://`:

   ```bash
   python -m http.server 5500
   ```

2. Open `http://localhost:5500`.
3. Create an account from the registration screen or log in with an existing user.
4. In the Firebase console, enable the **Email/Password** provider under Authentication and create a Firestore database.
5. The app needs an internet connection to load the SDKs and access data.

### Data and configuration

**Firebase**: **Authentication** (email and password) and **Cloud Firestore** are used. The project configuration lives in the `firebaseConfig` object at the beginning of the script in `index.html`. The repository ships the author's own project configuration; it is not reproduced here. To reuse the code, create your own project and replace those values.

**Firestore collections** (all include `userId`):

| Collection | Main fields |
|---|---|
| `gastos` | `concepto`, `monto`, `esInventario`, `fecha`, `timestamp` |
| `ingresos` | `cliente`, `monto`, `fecha`, `timestamp` |
| `inventario` | `nombre`, `cantidad`, `unidad`, `costoTotal`, `costoUnitario`, `stockMin`, `fecha`, `timestamp` |
| `pedidos` | `nombre`, `telefono`, `postre`, `precio`, `ruta`, `fechaEntrega`, `fecha`, `timestamp` |
| `listaCompras` | `nombre`, `cantidad`, `costoEstimado`, `categoria`, `notas`, `comprado`, `fecha`, `timestamp` |

`localStorage` is not used: Firestore is the source of truth. Only an in-memory cache (`listaComprasCache`) is kept to filter the shopping list.

### Notes and limitations

- The whole app sits in one file, which makes maintenance harder as it grows.
- Queries combining `where` and `orderBy` may require composite indexes; the UI shows the creation link when that happens.
- The shopping list cannot edit items: it only supports adding, marking as purchased, and deleting.
- Completing an order deletes the document, so there is no history of delivered orders.
- Income is recorded when the order is created, not when it is delivered; editing an order does not update the related income entry.
- There is no tax handling, no currency other than Costa Rican colones, and no exportable reports.
- The Firestore rules suggested by the app itself only require authentication; they do not restrict access by owner, even though queries do filter by `userId`. Rules should be tightened to validate `request.auth.uid == resource.data.userId`.

---

Autor / Author: Jafeth — GitHub: [@jafetht07](https://github.com/jafetht07)
