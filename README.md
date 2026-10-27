# electronCRAP

Proyecto **scaffolding** (plantilla base) para empaquetar y ejecutar como aplicación de escritorio con [Electron](https://www.electronjs.org/) una aplicación web ya compilada (por ejemplo, un proyecto React + Vite).

La idea es sencilla: compilas tu proyecto web de origen, copias el resultado de la build a la carpeta `dist/` de este proyecto y Electron se encarga de abrirlo en una ventana de escritorio.

## Estructura

```
electronCRAP/
├── main.js          # Proceso principal de Electron: crea la ventana y carga dist/index.html
├── package.json     # Dependencias (electron) y script de arranque
└── dist/            # Build de la aplicación web (no se versiona, ver .gitignore)
    ├── index.html
    ├── favicon.ico
    └── assets/
```

## Uso

1. Instala las dependencias:

   ```bash
   npm install
   ```

2. En el **proyecto de origen** (la app web), genera la build de producción:

   ```bash
   npm run build
   ```

3. Copia el contenido de la carpeta de build del proyecto de origen (normalmente `dist/` o `build/`) dentro de la carpeta `dist/` de este proyecto.

4. Arranca la aplicación con Electron:

   ```bash
   npm start
   ```

## ⚠️ Advertencia: es posible que no funcione a la primera

Electron carga la aplicación mediante el protocolo `file://` (`win.loadFile('dist/index.html')`), no desde un servidor web. Esto provoca que **algunas aplicaciones web no funcionen tal cual**, mostrando una pantalla en blanco o fallando al navegar entre rutas.

### Cambiar `BrowserRouter` por `HashRouter`

Si el proyecto de origen usa React Router con `BrowserRouter` (enrutado basado en la ruta de la URL, p. ej. `/usuarios/3`), **deberás cambiarlo en el proyecto de origen por `HashRouter`** (enrutado basado en el hash, p. ej. `#/usuarios/3`). Con `file://` no hay servidor que resuelva las rutas, así que solo el enrutado por hash funciona correctamente.

```jsx
// Antes
createWebHistory

// Después
createWebHashHistory
```


Después de hacer el cambio, vuelve a compilar el proyecto de origen y copia de nuevo la build a `dist/`.

### Otros problemas habituales

- **Rutas absolutas a los assets**: si la consola muestra errores al cargar los `.js`/`.css` de `assets/`, configura rutas relativas en el proyecto de origen. En Vite, añade `base: './'` en `vite.config.js`.
- Para depurar, abre las DevTools de la ventana de Electron con `Ctrl+Shift+I`.
