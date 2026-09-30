# BookList SPA - Editorial Nova
Módulo 6: Desarrollo de Interfaces Interactivas con Framework Vue

## 1. Demostración en video
Enlace directo: [https://youtu.be/lr8WeJAlg3Y] (https://youtu.be/lr8WeJAlg3Y)

## 2. Repositorio del proyecto
https://constanza-pixel.github.io/proyectobooklist/

### Decisiones técnicas y arquitectura

### 1. Propósito
El proyecto sustituye un esquema inicial de formularios HTML estáticos sin navegación mediante una interfaz SPA moderna y fluida. La arquitecturad desacopla la lógica de negocio, la navegación entre pantallas y el estado de la aplicación utilizando el patrón MVVM.

### 2. Arquitectura de estado y reactividad: Vuex
Para evitar la propagación compleja de propiedades y eventos entre componentes independientes, se centralizó el estado en un almacén global de Vuex:
* **Centralización del modelo de datos:** El catálogo de libros y el estado de sesión (`usuarioAutenticado`, `usuarioActual`, `libros`) se concentran en una única fuente e verdad.
* **Mutaciones puras e inmutabilidad:** La actualización de arreglos (`agregarLibro`, `eliminarLibro`) genera nuevas referencias mediante el operador spread (`state.libros = [...state.libros, nuevoLibro]`), asegurando reactividad inmediata.
* **Getters parametrizados:** Permiten filtrar y consultar elementos específicos sin mutar el arreglo original (`librosPorCategoria`,`obtenerLibroPorId`).
* **Flujo predecible (Patrón MVVM):** El estado se modifica únicamente a través de mutaciones explícitas.

### 3. Persistencia de datos: Sincronización con `localStorage`
Combina el estado reactivo con la API nativa del navegador para mantener la información tras la recarga de la página:
* **Inicialización hidratada:** Al montarse el store, el estado inicial se obtiene desde `localStorage.getItem()`, parseando el JSON existente o retornando valores por defecto.
* **Persistencia en tiempo de ejecución:** Toda mutación que añade o elimina registros sincroniza de forma inmediata la clave `libros_booklist` vía `localStorage.setItem()`.
* **Cierre de sesión seguro:** La mutación `cerrarSesion` purga las credenciales con `localStorage.removeItem('usuario_booklist')`.

### 4. Sistema de Navegación: Vue Router
Estructura de rutas desacoplada y amigable:
* **Rutas obligatorias cumplidas:** `/` (Inicio), `/libros` (Catálogo general) y la ruta dinámica `/libros/:id` (Ficha técnica del libro)[cite: 1].
* **Alias de compatibilidad:** Soporte para `/home` y `/detalle/:id`.
* **Navegación protegida:** La ruta `/agregar-libro` cuenta con una guardia local (`beforeEnter`) que verifica `estaAutenticado`; si no existe sesión, redirige a `/registrarse`.
* **Manejo de errores 404:** Redirección global ante rutas no coincidentes (`pathMatch(.*)*`).

### 5. Estructura y modularidad de componentes
Separación de responsabilidades mediante componentes de un solo archivo (Vue SFC):
* **Vistas (`src/views/`):**
  * `HomeView.vue`: Pantalla de bienvenida con nombre de usuario e interacción.
  * `LibrosView.vue`: Despliegue del catálogo, filtros y estado de lista vacía (`v-if` / `v-show`).
  * `Detalles.vue`: Resuelve parámetros dinámicos (`route.params.id`) y muestra la ficha detallada.
  * `IngresaLibro.vue`: Formulario reactivo vinculado con `v-model`, previsualización en vivo y captura de teclado (`@keyup.enter`).
* **Componentes Reutilizables (`src/components/`):**
  * `Libro.vue`: Representa cada ítem de la lista; recibe datos vía `props` (`v-bind`) y emite acciones del usuario mediante `$emit` (`ver-detalle`, `eliminar`)

### 6. Cumplimiento de buenas prácticas
* **Vue 3:** Implementación basada en Composition API con `<script setup>`.
* **Calidad de Código:** Cumplimiento estricto del estándar ESLint (Standard JS).
* **Diseño visual:** Estilo visual unificado, adaptable y centrado en la experiencia de usuario.

#### Guía de uso

#### 1. Exploración Inicial y Catálogo Público (`/` y `/libros`)
* Al acceder a la raíz (`/`), la vista de bienvenida muestra la información inicial del sistema.
* En la barra de navegación, la sección **Libros** (`/libros`) lista todos los registros disponibles mediante el componente modular `Libro.vue`.
* Al presionar **Ver Detalle**, se navega a la ruta dinámica `/libros/:id` para consultar título, autor, categoría y descripción.
* Si no existen libros registrados, el sistema despliega un mensaje condicional informativo.

#### 2. Autenticación y Registro (`/registrarse`)
* Para realizar operaciones de administración (crear o eliminar), se requiere iniciar sesión o registrarse.
* Al registrarse, los datos se almacenan en Vuex y `localStorage`, actualizando la barra de navegación para mostrar los accesos a **Agregar Libro** y el botón **Salir**.

#### 3. Registro de Nuevos Libros (`/agregar-libro`)
* El formulario incluye campos para *Nombre* (`input`), *Autor* (`input`), *Categoría* (`select`) y *Descripción* (`textarea`).
* Ofrece previsualización en tiempo real conforme se ingresan los datos[cite: 1].
* Permite enviar el formulario con el botón **Agregar** o presionando **Enter** en el campo de título.
* Al confirmar, se persiste el nuevo registro y se redirige automáticamente al catálogo `/libros`.

#### 4. Eliminación de Libros
* El botón **Eliminar** se activa en cada tarjeta del catálogo únicamente si existe una sesión autenticada.
* Al presionarlo, se confirma la acción, se actualiza el store y se sincroniza el almacenamiento local en tiempo real.

#### 5. Cierre de Sesión
* Al hacer clic en **Salir**, se limpian los datos de sesión en `localStorage`, devolviendo la interfaz al estado de invitado y ocultando los controles de administración.

##### Instalación y ejecución
```bash
# 1. Clonar el repositorio
git clone <URL_DEL_REPOSITORIO>

# 2. Ingresar a la carpeta del proyecto
cd proyectobooklist

# 3. Instalar dependencias
npm install

# 4. Iniciar el servidor de desarrollo
npm run serve

# 5. Ejecutar linter y corrección automática
npm run lint -- --fix

###### Autor
Constanza Yañez
coAutor: Gemini IA
