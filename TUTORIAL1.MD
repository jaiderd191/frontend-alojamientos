TUTORIAL 01 — Construir una SPA Vanilla desde cero
Sprint 1 · Frontend Alojamientos · HTML5 + CSS3 + JavaScript ES Modules + Bootstrap 5 + Vite
Cómo usar este material: este tutorial es la receta completa. Lea antes SPRINT-01.md para comprender los conceptos. Cree su propio repositorio vacío. No copie la carpeta solucion-referencia, no mezcle código de un frontend anterior y no implemente funcionalidades de Sprints 2 o 3 todavía. Cuando se indique «cree» o «reemplace», coloque exactamente el contenido publicado. Todos los bloques de código final están incluidos.
0. Qué construirá y cuál es el backend correcto
El repositorio frontend resultante se llamará frontend-alojamientos y será distinto de api-alojamientos. Tendrá un único index.html, cinco vistas públicas (/, /alojamientos, /alojamientos/:id, /comodidades, /estado), navegador con History API, componentes reutilizables, estilos responsive y peticiones con fetch. Una ruta desconocida mostrará una página 404.
Contratos verificados contra la API entregada:
Ruta de la API Flask
¿Pública?
Uso concreto en este sprint
GET http://localhost:5000/health
Sí
Comprobar estado
GET http://localhost:5000/api/v1/alojamientos
Sí
Listado de alojamientos
GET http://localhost:5000/api/v1/alojamientos/ID
Sí
Detalle (ID positivo)
GET http://localhost:5000/api/v1/comodidades
Sí
Listado de comodidades

/health responde un objeto con status, service, version. Los otros GET responden { "success": true, "data": ... }. Un alojamiento tiene titulo, descripcion, precio_noche, ciudad, usuario_id, id y, en el backend final, comodidades. No existe un endpoint de fotos ni reservas en la API entregada; no los implementaremos en el frontend.
La estructura de salida debe ser:
frontend-alojamientos/
├── .env                   ← local, ignorado por Git
├── .env.example
├── .gitignore
├── README.md
├── index.html
├── package.json
├── package-lock.json      ← generado por npm install
├── vite.config.js
├── src/
│   ├── css/main.css
│   └── js/
│       ├── main.js
│       ├── config.js
│       ├── api/http.js
│       ├── api/publica.api.js
│       ├── components/Navbar.js
│       ├── components/Footer.js
│       ├── components/Estados.js
│       ├── components/AlojamientoCard.js
│       ├── pages/InicioPage.js
│       ├── pages/AlojamientosPage.js
│       ├── pages/DetallePage.js
│       ├── pages/ComodidadesPage.js
│       ├── pages/EstadoPage.js
│       ├── pages/NoEncontradaPage.js
│       ├── router/routes.js
│       ├── router/router.js
│       └── utils/formatters.js
└── tests/
    ├── formatters.test.js
    ├── routes.test.js
    └── api.test.js
node_modules/ aparecerá tras instalar dependencias y dist/ tras compilar. Ambos se ignoran en Git.
1. Comprobar las herramientas antes de escribir código
Abra una terminal en Windows PowerShell, Linux o macOS. Compruebe:
node --version
npm --version
git --version
Se necesita una versión de Node compatible con Vite 8; se recomienda Node 22.12 o posterior. Si node o npm no aparecen, instale Node.js desde su fuente oficial y vuelva a abrir la terminal. Si git no aparece, instale Git. Necesitará VS Code u otro editor que permita crear archivos con sus nombres exactos.
Prepare también el backend que ya construyó en sus tutoriales previos: abra otra terminal dentro de api-alojamientos, active su .venv, verifique que MySQL esté iniciado y ejecute:
python -m scripts.verificar_mysql
python -m flask --app app.py db current
# flask --app app.py db current
python app.py
El comando de migración debe indicar la revisión 003 si está usando el backend terminado. El servidor Flask debe quedar activo en http://localhost:5000. Mantenga abierta esa terminal; frontend y backend funcionarán simultáneamente.
Abra http://localhost:5000/health en el navegador. Debe ver un JSON con status: ok, service: alojamientos-api y version: v1. Este endpoint puede responder aun cuando MySQL no esté disponible: la verificación de MySQL anterior es independiente y necesaria.
En el .env del backend, compruebe que exista:
```env
CORS_ALLOWED_ORIGINS=http://localhost:5173,http://localhost:3000
```

Si modifica este valor, reinicie Flask. No escriba esta variable en el frontend. No comparta ni copie al frontend el .env del backend, que contiene secretos.
2. Preparar un repositorio Git desde una carpeta vacía
Este paso supone que no hay todavía un repositorio frontend-alojamientos. No trabaje dentro del ZIP del backend. En PowerShell, Linux o macOS, desde la carpeta donde guarda sus proyectos:
mkdir frontend-alojamientos
cd frontend-alojamientos
git init
git branch -M main
Abra la carpeta con VS Code mediante Archivo → Abrir carpeta → frontend-alojamientos (alternativamente ejecute code . si tiene habilitado el comando).
En el panel Explorador de VS Code, pulse Nuevo archivo, cree .gitignore en la raíz y pegue:
Archivo: .gitignore — copie el bloque completo (no omita líneas ni imports).
```gitignore
node_modules/
dist/
.env
.env.*
!.env.example
coverage/
.DS_Store
.vscode/
.idea/
```

Cree también README.md con este contenido temporal, solo para que la primera rama tenga un commit de arranque:
```markdown
# Frontend Alojamientos


Proyecto académico: SPA Vanilla que consumirá la API Flask Alojamientos.
```

En terminal configure su identidad personal de Git si no existe (sustituya los valores, no copie los de otro desarrollador):
git config user.name "TU_NOMBRE"
git config user.email "TU_CORREO"
git add .gitignore README.md
git commit -m "chore: iniciar frontend de alojamientos"
En GitHub, cree un repositorio vacío llamado frontend-alojamientos en su propia cuenta, sin generar README ni .gitignore desde GitHub. En la terminal agregue la URL real de su cuenta, sustituyendo TU_USUARIO:
git remote add origin https://github.com/TU_USUARIO/frontend-alojamientos.git
git push -u origin main
Para comprobarlo, ejecute git remote -v. Si su repositorio ya tiene remoto origin, no vuelva a agregarlo: compruébelo con git remote -v. Si usa GitHub Classroom o un repositorio asignado por el docente, utilice la URL y permisos de ese repositorio en lugar del ejemplo.
Cree en GitHub una Issue titulada Sprint 1 frontend: construir SPA pública conectada a Flask con criterios de aceptación tomados de SPRINT-01.md.
El proyecto inicia en un repositorio nuevo y usa un único documento HTML.
El navegador consume únicamente los cuatro GET públicos documentados.
Navbar y enlaces de tarjetas cambian vistas sin recargar el documento.
Atrás/Adelante y URL directa a un detalle válido funcionan en desarrollo.
Catálogo muestra tarjetas, filtro, vacío, carga y error.
Detalle muestra alojamiento y comodidades reales, o 404 si el ID no existe.
El diseño es responsive, accesible y no interpreta HTML ajeno.
El código está separado en ES Modules; la URL de Flask se configura en .env local.
npm test ejecuta nueve tests en verde; npm run build termina sin errores.
Existe Issue, branch, commit, push, Pull Request y revisión antes de fusionar a main.
Luego, desde main, cree su rama:
git checkout -b feat/frontend-sprint-01-spa-publica
git branch --show-current
Debe verse feat/frontend-sprint-01-spa-publica. Desde este momento todos los archivos restantes se crean en esta rama.
3. Crear package.json y descargar las dependencias
En la raíz del proyecto, cree package.json. No ejecute npm create vite, porque este ejercicio construye los archivos desde cero y evita conflictos con plantillas ajenas.
Archivo: package.json — copie el bloque completo (no omita líneas ni imports).
{
  "name": "frontend-alojamientos",
  "version": "0.0.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "test": "node --test"
  },
  "dependencies": {
    "bootstrap": "5.3",
    "bootstrap-icons": "^1.13.1"
  },
  "devDependencies": {
    "vite": "^8.0.14"
  }
}
Desde la raíz frontend-alojamientos/ ejecute:
npm install
npm creará node_modules/ y package-lock.json. No cree ninguno manualmente. Verifique: El comando sirve para mostrar las dependencias instaladas directamente en un proyecto Node.js, junto con sus versiones. Limita el listado al primer nivel, sin mostrar las dependencias internas de cada paquete.
npm ls --depth=0
Debe identificar Bootstrap, Bootstrap Icons y Vite. El lockfile del alumno puede contener subversiones más recientes dentro de los rangos permitidos; el ZIP de referencia conserva uno concreto. No se exige copiar el lockfile de la referencia: es un archivo generado por npm y se debe subir a Git junto con package.json. Si la instalación falla, compruebe conectividad a npm y su versión de Node antes de seguir.
4. Definir Vite y la variable pública de conexión
Cree vite.config.js en la raíz. Elegimos localhost y el puerto 5173 de manera explícita para coincidir con el CORS del backend. strictPort evita que Vite cambie silenciosamente a otro puerto que Flask no autoriza.
Archivo: vite.config.js — copie el bloque completo (no omita líneas ni imports).
import { defineConfig } from 'vite';

export default defineConfig({
  server: {
    host: 'localhost',
    port: 5173,
    strictPort: true,
  },
});
Cree .env.example en la raíz. Es una plantilla sin contraseñas que Git sí versionará:
Archivo: .env.example — copie el bloque completo (no omita líneas ni imports).
```env
# Dirección de la API Flask; no incluya /api/v1 aquí porque /health está en la raíz.
VITE_API_URL=http://localhost:5000
```

Haga una copia local a .env (la real, no versionada):
Windows PowerShell
```powershell
Copy-Item .env.example .env
```

Linux o macOS
# Dirección de la API Flask; no incluya /api/v1 aquí porque /health está en la raíz.
VITE_API_URL=http://localhost:5000
Verifique que .env conserva VITE_API_URL=http://localhost:5000. No agregue /api/v1 a ese valor; /health vive en la raíz de Flask. Tampoco agregue JWT, SECRET_KEY ni credenciales MySQL.
Compruebe que Git ignora .env:
git check-ignore -v .env
Debe indicar una regla de .gitignore. El prefijo VITE_ significa que el valor es accesible en el navegador y, por tanto, no es secreto.
5. Crear la estructura de carpetas antes de pegar código
En la raíz del frontend ejecute uno de estos dos bloques, según su sistema. No copie un comando Bash dentro de PowerShell:
Windows PowerShell
```powershell
New-Item -ItemType Directory -Force -Path src\css, src\js\api, src\js\components, src\js\pages, src\js\router, src\js\utils, tests | Out-Null
```

Linux/macOS (Bash o Zsh)
mkdir -p src/css src/js/api src/js/components src/js/pages src/js/router src/js/utils tests
Todos los demás archivos se crean en VS Code: seleccione la carpeta indicada, pulse Nuevo archivo, escriba el nombre exacto y pegue el bloque correspondiente. Por ejemplo, src/js/api/http.js va dentro de la carpeta src/js/api; no en la raíz. No es necesario crear archivos vacíos previamente. El tutorial proporcionará el contenido completo de cada fichero.
En terminal compruebe dónde está situado:
PowerShell
```powershell
Get-Location
Get-ChildItem src -Recurse
```

Linux/macOS
pwd
find src -type d | sort
pwd Print Working Directory (mostrar el directorio de trabajo actual). /c/Users/LENOVO/Documents/clases/frontend-alojamientos
find	   Busca archivos y carpetas dentro de una ruta. src	     Carpeta donde comienza la búsqueda. -type d	 Indica que solamente debe mostrar directorios (directories), no archivos. | 	     Operador pipe(tubería). Envía la salida del primer comando al segundo. sort	   Ordena alfabéticamente los resultados recibidos.
Debe encontrar src/css, src/js/api, src/js/components, src/js/pages, src/js/router, src/js/utils.
6. Crear el único documento HTML de la SPA
Cree index.html en la raíz. Es el único documento HTML del proyecto. #app será reemplazado con cada vista; los otros contenedores permanecerán para la navbar y el footer. type="module" carga el archivo principal que crearemos al final de la implementación.
Archivo: index.html — copie el bloque completo (no omita líneas ni imports).
<!doctype html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="description" content="Catálogo público de alojamientos y comodidades." />
    <meta name="theme-color" content="#123c35" />
    <title>Aloja | Catálogo de alojamientos</title>
  </head>
  <body>
    <div id="site-header"></div>
    <main id="app" tabindex="-1" aria-live="polite" aria-busy="false"></main>
    <div id="site-footer"></div>
    <script type="module" src="/src/js/main.js"></script>
  </body>
</html>
7. Aplicar el diseño CSS responsive
Cree src/css/main.css. Bootstrap proporcionará grid, utilidades, botones y navbar; este archivo define identidad visual propia (paleta verde, hero, tarjetas e ilustraciones sin fotografías), y aplica media queries para móvil. El código completo está a continuación. No omita el final: allí están las reglas responsive.
Archivo: src/css/main.css — copie el bloque completo (no omita líneas ni imports).
:root {
  --ink: #17312c;
  --pine: #113d34;
  --forest: #245c4c;
  --accent: #c9e887;
  --canvas: #f7f8f3;
  --muted: #62756e;
  --line: #dfebe2;
}

* { box-sizing: border-box; }
html { scroll-behavior: smooth; }
body { min-height: 100vh; display: flex; flex-direction: column; color: var(--ink); background: var(--canvas); font-family: system-ui, -apple-system, 'Segoe UI', sans-serif; }
#app { flex: 1; outline: none; }
a:focus-visible, button:focus-visible, input:focus-visible { outline: 3px solid #8caf36; outline-offset: 3px; }

.site-navbar { background: #fff; border-bottom: 1px solid var(--line); padding: 1rem 0; position: sticky; top: 0; z-index: 30; box-shadow: 0 2px 18px #17312c08; }
.navbar-brand { font-size: 1.6rem; font-weight: 800; color: var(--pine); letter-spacing: -.06em; }
.navbar-brand:hover { color: var(--forest); }
.brand-dot { color: #9ac75f; }
.brand-mark { border-radius: 13px; background: var(--pine); color: var(--accent); font-size: 1.15rem; width: 37px; height: 37px; display: inline-flex; align-items: center; justify-content: center; }
.site-navbar .nav-link { color: #50665e; font-weight: 600; font-size: .94rem; padding: .55rem .75rem !important; border-radius: 9px; }
.site-navbar .nav-link:hover, .site-navbar .nav-link.active { color: var(--pine); background: #f0f6ea; }
.navbar-note { color: #638475; font-size: .78rem; font-weight: 600; border-left: 1px solid var(--line); padding-left: 1.2rem; }

.hero-wrap { overflow: hidden; background: radial-gradient(ellipse at 80% 30%, #286956 0, transparent 48%), linear-gradient(128deg, #12392f, #17463a 60%, #275849); color: #fff; }
.hero-grid { min-height: 540px; display: grid; grid-template-columns: 1.05fr .95fr; align-items: center; gap: 3rem; padding-top: 5rem; padding-bottom: 5rem; }
.hero-copy { position: relative; z-index: 2; }
.pill-label { width: fit-content; font-size: .75rem; font-weight: 700; letter-spacing: .07em; text-transform: uppercase; padding: .55rem .9rem; border: 1px solid #ffffff50; border-radius: 99px; background: #ffffff16; display: flex; align-items: center; gap: .6rem; }
.pulse-dot { width: 8px; height: 8px; border-radius: 50%; background: var(--accent); box-shadow: 0 0 0 5px #c9e8872a; }
.hero-copy h1 { font-size: clamp(2.7rem, 5vw, 5rem); max-width: 680px; line-height: 1.07; font-weight: 800; letter-spacing: -.065em; margin-top: 1.75rem; margin-bottom: 1.3rem; }
.hero-copy h1 em { font-style: normal; color: var(--accent); }
.hero-copy p { font-size: 1.05rem; line-height: 1.7; max-width: 520px; color: #d5e7de; }
.btn-accent { color: #183d32; background: var(--accent); border: 2px solid var(--accent); border-radius: 12px; font-weight: 700; }
.btn-accent:hover { background: #b3d36a; border-color: #b3d36a; color: #112a22; }
.hero-copy .btn-outline-light { border-radius: 12px; font-weight: 600; border-width: 2px; }
.hero-connect { display: inline-flex; align-items: center; gap: .5rem; margin-top: 2.4rem; padding: .7rem .9rem; color: #e0ebe4; font-size: .85rem; background: #00000024; border-radius: 11px; }
.hero-connect.is-online::before { content: ''; display: inline-block; width: 8px; height: 8px; background: var(--accent); border-radius: 50%; }
.hero-connect.is-offline { background: #854d3d; }
.hero-art { min-height: 400px; position: relative; display: grid; place-items: center; }
.hero-orbit { border: 1px solid #bfe3cc39; border-radius: 50%; position: absolute; }
.orbit-one { height: 440px; width: 440px; }
.orbit-two { height: 330px; width: 330px; }
.hero-building { width: 250px; height: 300px; position: relative; z-index: 1; background: linear-gradient(150deg, #e7f0d2 0%, #9fbfa9 90%); transform: rotate(-8deg); border-radius: 100px 100px 21px 21px; border: 14px solid #c9dfc9; box-shadow: 30px 40px 75px #09241e7b; display: flex; align-items: center; justify-content: center; gap: 11px; }
.building-window { height: 95px; width: 50px; background: var(--pine); border-radius: 45px 45px 5px 5px; margin-top: -50px; box-shadow: inset -8px 2px 0 #1d6454; }
.hero-art-badge { position: absolute; z-index: 2; bottom: 10px; right: 12px; color: var(--pine); background: #f6f9ed; border-radius: 17px; padding: 1rem 1.25rem; box-shadow: 0 16px 35px #0f2d2868; display: flex; align-items: center; gap: .8rem; font-size: .87rem; line-height: 1.45; }
.hero-art-badge i { color: var(--forest); font-size: 1.8rem; }

.section-block { padding-top: 4.8rem; padding-bottom: 6rem; }
.feature-tile { height: 100%; padding: 2rem; background: white; border: 1px solid var(--line); border-radius: 18px; box-shadow: 0 10px 35px #123c3508; }
.feature-tile > i { font-size: 1.7rem; color: var(--forest); padding: .65rem; background: #eaf4df; border-radius: 14px; }
.feature-tile h2 { font-weight: 750; }
.feature-tile p { color: var(--muted); margin: 0; line-height: 1.65; }
.page-heading { margin-bottom: 2rem; }
.page-heading h1 { font-size: clamp(2.2rem, 4vw, 3.5rem); letter-spacing: -.05em; font-weight: 800; margin: .6rem 0; }
.page-heading p { color: var(--muted); max-width: 660px; line-height: 1.6; }
.eyebrow { color: #3b8061; font-size: .77rem; font-weight: 800; letter-spacing: .08em; text-transform: uppercase; }
.search-panel { background: #fff; border: 1px solid var(--line); padding: 1.25rem; border-radius: 18px; max-width: 650px; }
.search-panel .form-control, .search-panel .input-group-text { border: 1px solid #dbe6df; background: #fff; }
.search-panel .input-group-text { color: #507968; border-right: 0; }
.search-panel input { border-left: 0 !important; }
.search-panel small { display: block; margin-top: .75rem; }
.stay-card { background: #fff; border: 1px solid var(--line); overflow: hidden; border-radius: 20px; transition: transform .2s, box-shadow .2s; box-shadow: 0 4px 22px #123c3508; }
.stay-card:hover { transform: translateY(-4px); box-shadow: 0 18px 35px #123c3515; }
.stay-illustration { height: 190px; position: relative; background: linear-gradient(155deg, #e5f0df, #b3d4c2); overflow: hidden; display: grid; place-items: center; }
.art-sun { position: absolute; width: 82px; height: 82px; border-radius: 50%; background: #f9f1ca; top: 20px; right: 25px; }
.art-hill { position: absolute; width: 125%; height: 100px; bottom: -55px; left: -10px; transform: rotate(-8deg); border-radius: 50%; background: #78a78f; }
.art-hill-back { left: 100px; bottom: -25px; background: #96bd9e; transform: rotate(10deg); }
.art-house { font-size: 6rem; z-index: 1; color: #356953; text-shadow: 0 12px 10px #28564230; }
.illustration-tag { position: absolute; z-index: 2; top: 15px; left: 17px; letter-spacing: .09em; font-size: .64rem; font-weight: 900; padding: .35rem .55rem; background: #ffffffbf; color: #2a6350; border-radius: 6px; }
.stay-content { padding: 1.5rem; }
.stay-content h3 { font-weight: 800; letter-spacing: -.02em; }
.stay-description { line-height: 1.6; min-height: 3rem; color: var(--muted); }
.stay-bottom { border-top: 1px solid var(--line); padding-top: 1.2rem; margin-top: 1.35rem; }
.stay-bottom strong { white-space: nowrap; font-size: .98rem; }
.stay-bottom .btn { white-space: nowrap; border-radius: 9px; }
.state-panel { border: 1px dashed #bbd2c2; background: #eef6ed; color: #426253; padding: 2.5rem 1.5rem; text-align: center; border-radius: 17px; }
.state-empty { background: white; }
.app-alert { border-radius: 14px; padding: 1.3rem; }
.back-link { color: var(--forest); text-decoration: none; font-weight: 700; }
.back-link:hover { text-decoration: underline; }
.detail-banner { height: 240px; background: radial-gradient(circle at 80% 20%, #aecfa7, transparent 50%), linear-gradient(135deg, #2c6555, #153e35); color: #e1f1ce; border-radius: 24px; display: flex; justify-content: center; align-items: center; gap: 1.5rem; }
.detail-banner i { font-size: 5rem; }
.detail-banner span { max-width: 120px; letter-spacing: .1em; font-size: .8rem; font-weight: 800; }
.detail-description { color: var(--muted); white-space: pre-wrap; }
.amenity-pill { display: inline-flex; align-items: center; background: #e7f2e6; padding: .65rem .85rem; border-radius: 30px; color: #295744; font-weight: 650; font-size: .88rem; }
.price-panel { background: #fff; border: 1px solid var(--line); padding: 2rem; border-radius: 20px; box-shadow: 0 15px 30px #113d3410; }
.price-value { font-size: 2rem; letter-spacing: -.03em; font-weight: 800; margin-top: 1rem; }
.amenity-card { background: #fff; border-radius: 16px; border: 1px solid var(--line); padding: 1.1rem 1.3rem; display: flex; gap: 1rem; align-items: center; font-weight: 700; min-height: 92px; }
.amenity-icon { display: grid; place-items: center; border-radius: 11px; width: 42px; height: 42px; background: #e4f4e2; color: #2a765a; font-size: 1.4rem; }
.status-card { background: white; padding: 2.5rem; border-radius: 20px; border: 1px solid var(--line); max-width: 650px; }
.status-icon { color: #328161; font-size: 2.8rem; }
.status-card dt, .status-card dd { padding: .4rem 0; }
.site-footer { padding: 1.6rem 0; background: #eaf1e9; border-top: 1px solid #d7e6d7; color: #476456; font-size: .85rem; }
.site-footer strong { color: var(--pine); }

@media (max-width: 991px) {
  .hero-grid { grid-template-columns: 1fr; padding-top: 4rem; padding-bottom: 3rem; }
  .hero-art { min-height: 300px; }
  .hero-building { transform: scale(.78) rotate(-8deg); }
  .orbit-one { width: 310px; height: 310px; }
  .orbit-two { width: 240px; height: 240px; }
  .hero-art-badge { right: 14%; }
  .navbar-note { display: none; }
}
@media (max-width: 575px) {
  .section-block { padding-top: 3.4rem; padding-bottom: 3.4rem; }
  .hero-copy h1 { font-size: 2.65rem; }
  .hero-art { min-height: 240px; }
  .hero-building { transform: scale(.58) rotate(-8deg); }
  .hero-art-badge { right: 2px; bottom: 0; font-size: .74rem; }
  .stay-bottom { flex-direction: column; align-items: flex-start !important; }
  .stay-bottom .btn { width: 100%; }
  .detail-banner { height: 180px; }
}
8. Centralizar la URL de Flask
Cree src/js/config.js. Lee VITE_API_URL desde el entorno de Vite y elimina las barras finales. El valor alternativo permite ejecutar unit tests con Node.js aun sin un servidor Vite. Al ejecutar Vite se utilizará el .env local.
Archivo: src/js/config.js — copie el bloque completo (no omita líneas ni imports).
const valorEntorno = import.meta.env?.VITE_API_URL?.trim();

// La API entrega /health fuera de /api/v1; aquí se configura solo el origen.
export const API_BASE_URL = (valorEntorno || 'http://localhost:5000').replace(/\/+$/, '');
9. Crear utilidades src/js/utils/formatters.js seguras para mostrar información
Cree . escapeHtml es obligatorio para campos de texto provenientes del backend, porque innerHTML interpreta etiquetas. formatearCOP representa precios en pesos colombianos. resumen evita descripciones muy extensas en las tarjetas.
Archivo: src/js/utils/formatters.js — copie el bloque completo (no omita líneas ni imports).
/** Escapa datos que podrían venir de usuarios; nunca inyecte texto remoto sin escaparlo. */
export function escapeHtml(valor) {
  return String(valor ?? '').replace(/[&<>"']/g, (caracter) => {
    const mapa = {
      '&': '&amp;',
      '<': '&lt;',
      '>': '&gt;',
      '"': '&quot;',
      "'": '&#39;',
    };
    return mapa[caracter];
  });
}

export function formatearCOP(valor) {
  const numero = Number(valor);
  if (!Number.isFinite(numero)) return 'Precio no disponible';
  return new Intl.NumberFormat('es-CO', {
    style: 'currency',
    currency: 'COP',
    maximumFractionDigits: 0,
  }).format(numero);
}

export function resumen(texto, limite = 125) {
  const contenido = String(texto ?? '').trim();
  if (contenido.length <= limite) return contenido;
  return `${contenido.slice(0, limite).trimEnd()}…`;
}
10. Implementar el cliente HTTP público
Cree src/js/utils/formatters.js. Centralizaremos fetch para no repetir tratamiento de conexión, JSON y status HTTP en cada página. La función obtenerJSON no agrega token porque Sprint 1 solo consume rutas públicas. Observe que una respuesta 404 se identifica con !respuesta.ok y que AbortError se propaga sin convertirlo en fallo de red.
Archivo: src/js/api/http.js — copie el bloque completo (no omita líneas ni imports).
import { API_BASE_URL } from '../config.js';

/**
 * Cliente para rutas públicas. Devuelve el objeto de la API completo,
 * por ejemplo { success: true, data: [...] } o { status: 'ok', ... }.
 */
export async function obtenerJSON(ruta, { signal } = {}) {
  let respuesta;
  try {
    respuesta = await fetch(`${API_BASE_URL}${ruta}`, {
      method: 'GET',
      headers: { Accept: 'application/json' },
      signal,
    });
  } catch (error) {
    if (error?.name === 'AbortError') throw error;
    const fallo = new Error('No fue posible conectar con Flask. Revise que la API esté iniciada y que CORS permita http://localhost:5173.');
    fallo.status = 0;
    throw fallo;
  }

  let cuerpo;
  try {
    cuerpo = await respuesta.json();
  } catch {
    const fallo = new Error('La API no devolvió un JSON válido.');
    fallo.status = respuesta.status;
    throw fallo;
  }

  if (!respuesta.ok) {
    const fallo = new Error(cuerpo?.error?.message || `Error HTTP ${respuesta.status}.`);
    fallo.status = respuesta.status;
    fallo.details = cuerpo?.error?.details;
    throw fallo;
  }

  return cuerpo;
}
Cree src/js/api/publica.api.js. Cada función declara una ruta existente de Flask. No escriba /api/v1/health ni /api/v1/comodidades/:id porque no existen en este backend.
Archivo: src/js/api/publica.api.js — copie el bloque completo (no omita líneas ni imports).
import { obtenerJSON } from './http.js';

export function consultarSalud(signal) {
  return obtenerJSON('/health', { signal });
}

export function listarAlojamientos(signal) {
  return obtenerJSON('/api/v1/alojamientos', { signal });
}

export function consultarAlojamiento(id, signal) {
  return obtenerJSON(`/api/v1/alojamientos/${id}`, { signal });
}

export function listarComodidades(signal) {
  return obtenerJSON('/api/v1/comodidades', { signal });
}
11. Construir los componentes reutilizables
La arquitectura separa presentación repetida de páginas completas. Navbar.js muestra los enlaces y marca la sección activa. Aún no existe enlace de Login ni controles de administración; se incorporarán en los siguientes sprints.
Archivo: src/js/components/Navbar.js — copie el bloque completo (no omita líneas ni imports).
import { escapeHtml } from '../utils/formatters.js';

const enlaces = [
  { ruta: '/', nombre: 'Inicio' },
  { ruta: '/alojamientos', nombre: 'Alojamientos' },
  { ruta: '/comodidades', nombre: 'Comodidades' },
  { ruta: '/estado', nombre: 'Estado de API' },
];

export function pintarNavbar(rutaActual) {
  const links = enlaces.map(({ ruta, nombre }) => {
    const activo = ruta === '/' ? rutaActual === '/' : rutaActual.startsWith(ruta);
    return `<li class="nav-item"><a class="nav-link ${activo ? 'active' : ''}"
      ${activo ? 'aria-current="page"' : ''} href="${ruta}" data-link>${escapeHtml(nombre)}</a></li>`;
  }).join('');

  return `
    <nav class="navbar navbar-expand-lg site-navbar" aria-label="Navegación principal">
      <div class="container">
        <a class="navbar-brand d-flex align-items-center gap-2" href="/" data-link>
          <span class="brand-mark" aria-hidden="true"><i class="bi bi-house-heart-fill"></i></span>
          <span>Aloja<span class="brand-dot">.</span></span>
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarMenu"
          aria-controls="navbarMenu" aria-expanded="false" aria-label="Mostrar navegación">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarMenu">
          <ul class="navbar-nav ms-auto align-items-lg-center gap-lg-2">${links}</ul>
          <span class="ms-lg-4 navbar-note d-none d-lg-inline">Explora sin registrarte</span>
        </div>
      </div>
    </nav>`;
}
Cree src/js/components/Footer.js:
Archivo: src/js/components/Footer.js — copie el bloque completo (no omita líneas ni imports).
export function pintarFooter() {
  return `
    <footer class="site-footer">
      <div class="container d-flex flex-column flex-md-row justify-content-between gap-2 align-items-md-center">
        <div><strong>Aloja.</strong> Plataforma académica · Frontend SPA Vanilla</div>
        <small>Catálogo público conectado a la API Flask · Sprint 1</small>
      </div>
    </footer>`;
}
Cree src/js/components/Estados.js. Nos permitirá mostrar estados de carga, vacío y error de manera consistente:
Archivo: src/js/components/Estados.js — copie el bloque completo (no omita líneas ni imports).
import { escapeHtml } from '../utils/formatters.js';

export function estadoCargando(mensaje = 'Cargando información…') {
  return `<div class="state-panel" role="status"><span class="spinner-border spinner-border-sm me-2" aria-hidden="true"></span>${escapeHtml(mensaje)}</div>`;
}

export function estadoVacio(mensaje) {
  return `<div class="state-panel state-empty"><i class="bi bi-inbox me-2" aria-hidden="true"></i>${escapeHtml(mensaje)}</div>`;
}

export function estadoError(error, mensaje = 'No fue posible cargar los datos.') {
  return `<div class="alert alert-danger app-alert" role="alert"><strong>Atención.</strong> ${escapeHtml(error?.message || mensaje)}</div>`;
}
Cree src/js/components/AlojamientoCard.js. Cada tarjeta obtiene el ID, nombre, ciudad, descripción y precio del objeto real de la API. La ilustración es CSS decorativa. Los enlaces apuntan a /alojamientos/ID en el frontend, mientras que DetallePage utilizará el endpoint Flask correcto.
Archivo: src/js/components/AlojamientoCard.js — copie el bloque completo (no omita líneas ni imports).
import { escapeHtml, formatearCOP, resumen } from '../utils/formatters.js';

export function pintarTarjetaAlojamiento(alojamiento) {
  const id = Number(alojamiento?.id);
  if (!Number.isSafeInteger(id) || id < 1) return '';
  const titulo = escapeHtml(alojamiento.titulo);
  const ciudad = escapeHtml(alojamiento.ciudad);
  const descripcion = escapeHtml(resumen(alojamiento.descripcion));
  return `
    <article class="stay-card h-100">
      <div class="stay-illustration" aria-hidden="true">
        <div class="art-sun"></div><div class="art-hill art-hill-back"></div><div class="art-hill art-hill-front"></div>
        <i class="bi bi-house-door-fill art-house"></i>
        <span class="illustration-tag">CATÁLOGO</span>
      </div>
      <div class="stay-content">
        <span class="eyebrow"><i class="bi bi-geo-alt me-1"></i>${ciudad}</span>
        <h3 class="h5 mt-2 mb-2">${titulo}</h3>
        <p class="stay-description">${descripcion}</p>
        <div class="stay-bottom d-flex justify-content-between align-items-center gap-3">
          <div><strong>${escapeHtml(formatearCOP(alojamiento.precio_noche))}</strong><span class="text-muted small"> / noche</span></div>
          <a class="btn btn-outline-dark btn-sm" href="/alojamientos/${id}" data-link>Ver detalle <i class="bi bi-arrow-right ms-1"></i></a>
        </div>
      </div>
    </article>`;
}
12. Crear las cinco páginas públicas y la página 404
Una página de este ejercicio exporta una función async que recibe { contenedor, signal } (y params si tiene parámetros). El router pasará esos datos. La página pinta primero un HTML inicial y luego espera la respuesta del backend. No cree otros archivos HTML dentro de pages/.
12.1 Inicio
Contiene el hero y consulta /health para presentar el estado efectivo de Flask; no inventa cifras de disponibilidad.
Archivo: src/js/pages/InicioPage.js — copie el bloque completo (no omita líneas ni imports).
import { consultarSalud } from '../api/publica.api.js';

export async function mostrarInicio({ contenedor, signal }) {
  contenedor.innerHTML = `
    <section class="hero-wrap">
      <div class="container hero-grid">
        <div class="hero-copy">
          <div class="pill-label"><span class="pulse-dot"></span> Una nueva forma de explorar</div>
          <h1>Encuentra tu próximo <em>espacio favorito.</em></h1>
          <p>Explora alojamientos registrados en la plataforma. Consulta su ciudad, precio por noche y comodidades; sin necesidad de iniciar sesión.</p>
          <div class="d-flex gap-3 flex-wrap mt-4">
            <a href="/alojamientos" data-link class="btn btn-accent btn-lg">Explorar alojamientos <i class="bi bi-arrow-up-right ms-1"></i></a>
            <a href="/comodidades" data-link class="btn btn-outline-light btn-lg">Ver comodidades</a>
          </div>
          <div class="hero-connect" id="home-health" role="status">Comprobando conexión con Flask…</div>
        </div>
        <div class="hero-art" aria-hidden="true">
          <div class="hero-orbit orbit-one"></div><div class="hero-orbit orbit-two"></div>
          <div class="hero-building"><span class="building-window"></span><span class="building-window"></span><span class="building-window"></span></div>
          <div class="hero-art-badge"><i class="bi bi-compass"></i><span>Tu próxima estancia<br><strong>comienza aquí</strong></span></div>
        </div>
      </div>
    </section>
    <section class="container section-block">
      <div class="row g-4">
        <div class="col-md-4"><div class="feature-tile"><i class="bi bi-search"></i><h2 class="h5 mt-3">Explora el catálogo</h2><p>Consulta los alojamientos disponibles en los registros públicos de la API.</p></div></div>
        <div class="col-md-4"><div class="feature-tile"><i class="bi bi-card-checklist"></i><h2 class="h5 mt-3">Revisa los detalles</h2><p>Conoce la ubicación, descripción y precio por noche de cada alojamiento.</p></div></div>
        <div class="col-md-4"><div class="feature-tile"><i class="bi bi-wifi"></i><h2 class="h5 mt-3">Descubre comodidades</h2><p>Conoce los servicios registrados y las comodidades asociadas a cada lugar.</p></div></div>
      </div>
    </section>`;

  const mensaje = contenedor.querySelector('#home-health');
  try {
    const salud = await consultarSalud(signal);
    if (signal.aborted) return;
    const correcto = salud?.status === 'ok';
    mensaje.className = `hero-connect ${correcto ? 'is-online' : 'is-offline'}`;
    mensaje.textContent = correcto
      ? `API conectada · ${salud.service || 'alojamientos-api'} · ${salud.version || 'v1'}`
      : `La API respondió: ${salud?.status || 'estado no reconocido'}`;
  } catch (error) {
    if (signal.aborted) return;
    mensaje.className = 'hero-connect is-offline';
    mensaje.textContent = error.message || 'La API no está disponible.';
  }
}
12.2 Listado de alojamientos
La consulta se realiza una vez al abrir la vista. Al escribir en el buscador se filtra localmente por título o ciudad: no existe búsqueda remota en la API de estos sprints. Si el backend devuelve una lista vacía, muestre un aviso, no un error.
Archivo: src/js/pages/AlojamientosPage.js — copie el bloque completo (no omita líneas ni imports).
import { listarAlojamientos } from '../api/publica.api.js';
import { pintarTarjetaAlojamiento } from '../components/AlojamientoCard.js';
import { estadoCargando, estadoError, estadoVacio } from '../components/Estados.js';

export async function mostrarAlojamientos({ contenedor, signal }) {
  contenedor.innerHTML = `
    <section class="container section-block">
      <div class="page-heading"><span class="eyebrow">EXPLORA</span><h1>Todos los alojamientos</h1>
        <p>Consulta los lugares publicados en la API. El filtro se aplica a los datos ya cargados.</p></div>
      <div class="search-panel mb-4">
        <label for="filtro" class="form-label fw-semibold mb-2">Buscar por nombre o ciudad</label>
        <div class="input-group"><span class="input-group-text"><i class="bi bi-search"></i></span>
          <input class="form-control" id="filtro" type="search" placeholder="Ej.: Medellín o apartamento" autocomplete="off" /></div>
        <small class="text-muted" id="cantidad" aria-live="polite"></small>
      </div>
      <div id="lista" aria-live="polite">${estadoCargando('Consultando alojamientos…')}</div>
    </section>`;

  const lista = contenedor.querySelector('#lista');
  const filtro = contenedor.querySelector('#filtro');
  const cantidad = contenedor.querySelector('#cantidad');
  try {
    const respuesta = await listarAlojamientos(signal);
    if (signal.aborted) return;
    if (!Array.isArray(respuesta?.data)) throw new Error('La API respondió con un formato inesperado para alojamientos.');
    const alojamientos = respuesta.data;

    function actualizar() {
      const consulta = filtro.value.toLocaleLowerCase('es').trim();
      const visibles = alojamientos.filter((item) =>
        `${item.titulo ?? ''} ${item.ciudad ?? ''}`.toLocaleLowerCase('es').includes(consulta));
      cantidad.textContent = `${visibles.length} de ${alojamientos.length} alojamientos`;
      lista.innerHTML = visibles.length
        ? `<div class="row g-4">${visibles.map((alojamiento) => `<div class="col-md-6 col-xl-4">${pintarTarjetaAlojamiento(alojamiento)}</div>`).join('')}</div>`
        : estadoVacio(alojamientos.length ? 'No hay coincidencias para la búsqueda.' : 'La API todavía no tiene alojamientos registrados.');
    }
    filtro.addEventListener('input', actualizar);
    actualizar();
  } catch (error) {
    if (signal.aborted) return;
    cantidad.textContent = '';
    lista.innerHTML = estadoError(error);
  }
}
12.3 Detalle de alojamiento
La vista recibe params.id, consulta GET /api/v1/alojamientos/:id, muestra sus comodidades reales cuando existen y trata un 404 del backend como «Alojamiento no encontrado». No agregue un botón Reservar, porque la API no dispone de ese servicio.
Archivo: src/js/pages/DetallePage.js — copie el bloque completo (no omita líneas ni imports).
import { consultarAlojamiento } from '../api/publica.api.js';
import { estadoCargando, estadoError } from '../components/Estados.js';
import { escapeHtml, formatearCOP } from '../utils/formatters.js';

export async function mostrarDetalle({ contenedor, params, signal }) {
  contenedor.innerHTML = `<section class="container section-block"><a href="/alojamientos" class="back-link" data-link><i class="bi bi-arrow-left me-2"></i>Volver al catálogo</a>
    <div id="detalle" class="mt-4">${estadoCargando('Cargando el alojamiento…')}</div></section>`;
  const region = contenedor.querySelector('#detalle');
  try {
    const respuesta = await consultarAlojamiento(params.id, signal);
    if (signal.aborted) return;
    const alojamiento = respuesta?.data;
    if (!alojamiento || typeof alojamiento !== 'object') throw new Error('La API no devolvió los datos del alojamiento.');
    const comodidades = Array.isArray(alojamiento.comodidades) ? alojamiento.comodidades : [];
    region.innerHTML = `
      <div class="detail-banner" aria-hidden="true"><i class="bi bi-buildings-fill"></i><span>DETALLE DEL ALOJAMIENTO</span></div>
      <div class="row g-5 mt-1">
        <div class="col-lg-8">
          <span class="eyebrow"><i class="bi bi-geo-alt me-1"></i>${escapeHtml(alojamiento.ciudad)}</span>
          <h1 class="display-6 fw-bold mt-2">${escapeHtml(alojamiento.titulo)}</h1>
          <p class="lead detail-description">${escapeHtml(alojamiento.descripcion)}</p>
          <h2 class="h5 mt-5 mb-3">Comodidades del alojamiento</h2>
          <div class="d-flex flex-wrap gap-2">${comodidades.length
            ? comodidades.map((item) => `<span class="amenity-pill"><i class="bi bi-check-circle me-2"></i>${escapeHtml(item.nombre)}</span>`).join('')
            : '<p class="text-muted">Este alojamiento todavía no tiene comodidades asociadas.</p>'}</div>
        </div>
        <div class="col-lg-4"><aside class="price-panel"><span class="eyebrow">PRECIO DE REFERENCIA</span>
          <div class="price-value">${escapeHtml(formatearCOP(alojamiento.precio_noche))}</div>
          <p class="text-muted">por noche</p><hr /><p class="small mb-0">El backend de estos sprints no incluye reservas ni pagos. Este precio es informativo.</p>
        </aside></div>
      </div>`;
  } catch (error) {
    if (signal.aborted) return;
    region.innerHTML = error.status === 404
      ? `<div class="state-panel state-empty"><h1 class="h4">Alojamiento no encontrado</h1><p>El identificador solicitado no existe.</p><a href="/alojamientos" data-link>Volver al catálogo</a></div>`
      : estadoError(error);
  }
}
12.4 Catálogo de comodidades
Esta vista lista exclusivamente los elementos de GET /api/v1/comodidades; crear comodidades será un caso administrativo del Sprint 3.
Archivo: src/js/pages/ComodidadesPage.js — copie el bloque completo (no omita líneas ni imports).
import { listarComodidades } from '../api/publica.api.js';
import { estadoCargando, estadoError, estadoVacio } from '../components/Estados.js';
import { escapeHtml } from '../utils/formatters.js';

export async function mostrarComodidades({ contenedor, signal }) {
  contenedor.innerHTML = `<section class="container section-block">
    <div class="page-heading"><span class="eyebrow">SERVICIOS</span><h1>Catálogo de comodidades</h1>
      <p>Este catálogo proviene de <code>GET /api/v1/comodidades</code>. En Sprint 3 se incorporará su administración.</p></div>
    <div id="comodidades-lista">${estadoCargando('Consultando comodidades…')}</div></section>`;
  const lista = contenedor.querySelector('#comodidades-lista');
  try {
    const respuesta = await listarComodidades(signal);
    if (signal.aborted) return;
    if (!Array.isArray(respuesta?.data)) throw new Error('Formato inesperado de comodidades.');
    lista.innerHTML = respuesta.data.length
      ? `<div class="row g-3">${respuesta.data.map((item) => `<div class="col-sm-6 col-lg-4"><div class="amenity-card"><span class="amenity-icon"><i class="bi bi-check2-circle"></i></span><span>${escapeHtml(item.nombre)}</span></div></div>`).join('')}</div>`
      : estadoVacio('La API todavía no tiene comodidades registradas.');
  } catch (error) {
    if (signal.aborted) return;
    lista.innerHTML = estadoError(error);
  }
}
12.5 Página de estado del backend
La página muestra los campos exactos del JSON devuelto por /health.
Archivo: src/js/pages/EstadoPage.js — copie el bloque completo (no omita líneas ni imports).
import { consultarSalud } from '../api/publica.api.js';
import { estadoCargando, estadoError } from '../components/Estados.js';
import { escapeHtml } from '../utils/formatters.js';

export async function mostrarEstado({ contenedor, signal }) {
  contenedor.innerHTML = `<section class="container section-block">
    <div class="page-heading"><span class="eyebrow">CONECTIVIDAD</span><h1>Estado del servicio</h1>
    <p>Prueba real del endpoint público <code>GET /health</code>.</p></div>
    <div id="estado-api">${estadoCargando('Comprobando la conexión…')}</div>
  </section>`;
  const region = contenedor.querySelector('#estado-api');
  try {
    const datos = await consultarSalud(signal);
    if (signal.aborted) return;
    region.innerHTML = `<div class="status-card"><i class="bi bi-check-circle-fill status-icon"></i>
      <h2 class="h4 mt-3">Respuesta recibida</h2>
      <dl class="row mt-4 mb-0">
        <dt class="col-sm-3">Estado</dt><dd class="col-sm-9">${escapeHtml(datos.status)}</dd>
        <dt class="col-sm-3">Servicio</dt><dd class="col-sm-9">${escapeHtml(datos.service)}</dd>
        <dt class="col-sm-3">Versión</dt><dd class="col-sm-9">${escapeHtml(datos.version)}</dd>
      </dl></div>`;
  } catch (error) {
    if (signal.aborted) return;
    region.innerHTML = estadoError(error);
  }
}
12.6 Página 404 del frontend
Esta página se mostrará cuando no haya una ruta cliente registrada. No debe confundirse con el 404 del endpoint de detalle de Flask.
Archivo: src/js/pages/NoEncontradaPage.js — copie el bloque completo (no omita líneas ni imports).
export async function mostrarNoEncontrada({ contenedor }) {
  contenedor.innerHTML = `<section class="container section-block text-center"><span class="eyebrow">ERROR 404</span>
    <h1 class="display-4 fw-bold mt-2">Página no encontrada</h1>
    <p class="text-muted">No existe una vista frontend para esta dirección.</p>
    <a href="/" data-link class="btn btn-accent mt-3">Volver al inicio</a></section>`;
}
13. Registrar rutas y parámetros de URL
Cree src/js/router/routes.js. Es un mapa explícito de vistas. Las expresiones regulares permiten que /alojamientos/4 capture id=4. Normalizamos la ruta /alojamientos para admitir la barra final; una dirección inexistente muestra la página 404.
La SPA no inventa una ruta /comodidades/:id: el backend solo proporciona listado público de comodidades.
Archivo: src/js/router/routes.js — copie el bloque completo (no omita líneas ni imports).
import { mostrarInicio } from '../pages/InicioPage.js';
import { mostrarAlojamientos } from '../pages/AlojamientosPage.js';
import { mostrarDetalle } from '../pages/DetallePage.js';
import { mostrarComodidades } from '../pages/ComodidadesPage.js';
import { mostrarEstado } from '../pages/EstadoPage.js';
import { mostrarNoEncontrada } from '../pages/NoEncontradaPage.js';

const rutas = [
  { patron: /^\/$/, vista: mostrarInicio },
  { patron: /^\/alojamientos\/$/, vista: mostrarAlojamientos },
  { patron: /^\/alojamientos\/([1-9]\d*)$/, vista: mostrarDetalle, parametro: 'id' },
  { patron: /^\/comodidades$/, vista: mostrarComodidades },
  { patron: /^\/estado$/, vista: mostrarEstado },
];

/** Permite /alojamientos y /alojamientos/ sin duplicar una página. */
export function normalizarRuta(ruta) {
  const limpia = String(ruta || '/').split(/[?#]/)[0].replace(/\/+$/, '') || '/';
  return limpia === '/alojamientos' ? '/alojamientos/' : limpia;
}

export function resolverRuta(ruta) {
  const normalizada = normalizarRuta(ruta);
  for (const { patron, vista, parametro } of rutas) {
    const coincidencia = normalizada.match(patron);
    if (coincidencia) {
      return { vista, params: parametro ? { [parametro]: coincidencia[1] } : {} };
    }
  }
  return { vista: mostrarNoEncontrada, params: {} };
}
14. Programar navegación SPA con History API
Cree src/js/router/router.js. Este es el núcleo de la SPA: intercepta clics de enlaces data-link, llama a history.pushState, resuelve la vista y escucha popstate (Atrás/Adelante). Antes de cada nueva vista cancela la solicitud anterior mediante AbortController para evitar que una respuesta tardía altere otra página.
Observe que no se usa React Router: este router es un módulo JavaScript Vanilla.
Archivo: src/js/router/router.js — copie el bloque completo (no omita líneas ni imports).
import { pintarNavbar } from '../components/Navbar.js';
import { pintarFooter } from '../components/Footer.js';
import { resolverRuta } from './routes.js';

let controladorActual = null;
let navegacionIniciada = false;

async function dibujarRuta() {
  if (controladorActual) controladorActual.abort();
  controladorActual = new AbortController();
  const signal = controladorActual.signal;
  const app = document.querySelector('#app');
  document.querySelector('#site-header').innerHTML = pintarNavbar(window.location.pathname);
  document.querySelector('#site-footer').innerHTML = pintarFooter();
  const { vista, params } = resolverRuta(window.location.pathname);
  app.setAttribute('aria-busy', 'true');
  try {
    await vista({ contenedor: app, params, signal });
  } catch (error) {
    if (!signal.aborted) {
      app.textContent = 'Ocurrió un problema al mostrar la página.';
      console.error(error);
    }
  } finally {
    if (!signal.aborted) app.setAttribute('aria-busy', 'false');
  }
}

export function navegar(ruta) {
  if (ruta !== `${window.location.pathname}${window.location.search}`) {
    window.history.pushState({}, '', ruta);
  }
  window.scrollTo({ top: 0, behavior: 'auto' });
  void dibujarRuta();
}

export function iniciarRouter() {
  if (navegacionIniciada) return;
  navegacionIniciada = true;
  document.addEventListener('click', (evento) => {
    if (evento.defaultPrevented || evento.button !== 0 || evento.metaKey || evento.ctrlKey || evento.shiftKey || evento.altKey) return;
    const enlace = evento.target.closest('a[data-link]');
    if (!enlace || enlace.hasAttribute('download') || enlace.target === '_blank') return;
    const destino = new URL(enlace.href, window.location.href);
    if (destino.origin !== window.location.origin) return;
    evento.preventDefault();
    navegar(`${destino.pathname}${destino.search}${destino.hash}`);
    const menu = document.querySelector('#navbarMenu');
    if (menu?.classList.contains('show')) {
      // Bootstrap administra las clases y atributos del menú móvil.
      const instancia = window.bootstrap?.Collapse.getInstance(menu);
      instancia?.hide();
    }
  });
  window.addEventListener('popstate', () => void dibujarRuta());
  void dibujarRuta();
}
15. Conectar Bootstrap y arrancar la aplicación
Cree src/js/main.js. Importa CSS de Bootstrap, Bootstrap Icons, su JavaScript (necesario para el menú colapsable en móvil), estilos propios y finalmente inicia el router. Este es el único módulo referenciado directamente desde index.html.
Archivo: src/js/main.js — copie el bloque completo (no omita líneas ni imports).
import 'bootstrap/dist/css/bootstrap.min.css';
import 'bootstrap-icons/font/bootstrap-icons.css';
import * as bootstrap from 'bootstrap';
import '../css/main.css';
import { iniciarRouter } from './router/router.js';

// Bootstrap gestiona el menú de navegación responsive; no gestiona el router SPA.
window.bootstrap = bootstrap;
iniciarRouter();
16. Primer arranque y comprobaciones desde el navegador
Mantenga Flask ejecutándose en su terminal separada. En otra terminal, situada en el frontend y sobre la rama del Sprint 1, ejecute:
npm run dev
Debe mostrarse una URL similar a http://localhost:5173/. Ábrala en Chrome, Edge o Firefox. La vista Inicio debe mostrar un hero, navbar, botones, tres bloques informativos y un aviso de API conectada si /health está en ok.
Pruebe sin cerrar Vite:
Haga clic en Alojamientos. La URL debe cambiar a /alojamientos y el navegador no debe cargar otro index.html.
Si MySQL todavía no tiene alojamientos, debe leer un mensaje de catálogo vacío. Eso es correcto: la SPA muestra datos reales.
Haga clic en Comodidades; debe verse lista o estado vacío.
Haga clic en Estado de API y verifique ok, alojamientos-api y v1.
Visite http://localhost:5173/reservas: la SPA debe mostrar 404 frontend; el backend de estos sprints no implementa reservas.
Pulse Atrás y Adelante. Debe cambiar la vista sin recargar completamente la web.
Abra DevTools → Network y confirme que las peticiones van a localhost:5000 por GET; no deben verse llamadas ficticias a otros endpoints.
Reduzca el ancho a un tamaño móvil. El botón de menú de Bootstrap debe desplegar los enlaces.
Si todo el catálogo está vacío, continúe a la sección 17 para crear registros reales en la API y validar tarjetas y detalles. No inserte arrays hardcodeados en el frontend.
17. Preparar datos REALES de la API usando Postman (sin modificar el frontend)
Este paso es indispensable para probar listado y detalle con registros si su base de datos está vacía. Es una actividad de preparación con la API que ya se construyó en el backend; no significa que el frontend Sprint 1 implemente registro o administración.
Inicie Flask (python app.py), con MySQL activo y migración 003. Abra Postman y cree un Environment con baseUrl = http://localhost:5000.
17.1 Registrar un usuario (solo si no existe). En una nueva request seleccione método POST, URL {{baseUrl}}/api/v1/usuarios/registro, pestaña Body → raw → JSON, y pegue:
{
  "correo": "frontend-sprint01@ejemplo.com",
  "contrasena": "ClaveDemo2026!"
}
Seleccione Send. Debe obtener 201 Created. Si ya había registrado ese correo, puede recibir 409 Conflict; en ese caso no repita el registro y continúe con el login del usuario existente. Si desea usar otro correo, actualícelo también en los pasos siguientes.
17.2 Iniciar sesión. Cree POST {{baseUrl}}/api/v1/usuarios/login, Body → raw → JSON:
{
  "correo": "frontend-sprint01@ejemplo.com",
  "contrasena": "ClaveDemo2026!"
}
Espere 200 OK y copie solo el valor de data.access_token. En Postman, Authorization → Type: Bearer Token → Token pegue ese token sin escribir la palabra Bearer. Los JWT expiran: el backend de los tutoriales usa por defecto 15 minutos; si obtiene 401, repita el login.
17.3 Crear un alojamiento de prueba. Cree POST {{baseUrl}}/api/v1/alojamientos, configure el Bearer Token de la sección anterior y Body → raw → JSON:
{
  "titulo": "Apartamento de práctica en Medellín",
  "descripcion": "Alojamiento de ejemplo creado para validar el frontend del Sprint 1.",
  "precio_noche": 180000,
  "ciudad": "Medellín"
}
Espere 201 Created, copie el data.id de la respuesta; el servidor asigna este ID, no asuma que es 1. La ruta /alojamientos del frontend debe mostrar una tarjeta con ese registro y su filtro debe encontrar Medellín. Haga clic en Ver detalle y compruebe que se abre /alojamientos/ID (el ID real) y que el backend devuelve su descripción y precio.
17.4 Comprobar comodidad pública y asociación (opcional si desea ver la página poblada). El backend permite crear comodidades únicamente a administradores. Desde otra terminal dentro de api-alojamientos, con .venv activo, promueva solo el usuario de práctica:
python -m scripts.promover_admin frontend-sprint01@ejemplo.com
El mismo token previamente emitido identifica al usuario y el backend consulta su rol vigente; si el token expiró, vuelva a iniciar sesión. En Postman, usando su token y Body → raw → JSON, ejecute:
```http
POST {{baseUrl}}/api/v1/comodidades
```

{
  "nombre": "WiFi de práctica"
}
Espere 201 y anote el data.id de la comodidad. Si ya existe, el backend responderá 409; puede usar el ID existente obtenido desde GET /api/v1/comodidades o crear un nombre diferente. Ahora envíe:
```http
PATCH {{baseUrl}}/api/v1/alojamientos/ID_DEL_ALOJAMIENTO/comodidades
```

Con Bearer Token y Body → raw → JSON, sustituyendo por los IDs reales:
{
  "comodidades_ids": [1]
}
En el body anterior, 1 es solo un ejemplo. Sustitúyalo por el data.id real de la comodidad. Debe responder 200 y el objeto del alojamiento debe incluir comodidades. Refresque /comodidades y el detalle en el navegador para ver los datos. Este paso no requiere escribir código nuevo en el frontend.
17.5 Verificación de lista y detalle directamente en Postman:
```http
GET {{baseUrl}}/api/v1/alojamientos
GET {{baseUrl}}/api/v1/alojamientos/ID_DEL_ALOJAMIENTO
GET {{baseUrl}}/api/v1/comodidades
```

Son peticiones públicas: no necesitan JWT. Contraste sus respuestas con lo que pinta la SPA. No suba el token real, contraseñas ni exportaciones de Postman con credenciales al repositorio frontend.
18. Escribir y ejecutar las pruebas automatizadas
Estas pruebas se ejecutan con el test runner incorporado en Node (node --test). No requieren Jest ni Vitest. Parte de las pruebas simulan la función fetch para comprobar sin levantar Flask que se utilizan las URLs correctas y que los errores conservan su status. No reemplazan la prueba de navegador real de las secciones 16 y 17.
Cree tests/formatters.test.js:
Archivo: tests/formatters.test.js — copie el bloque completo (no omita líneas ni imports).
import test from 'node:test';
import assert from 'node:assert/strict';
import { escapeHtml, formatearCOP, resumen } from '../src/js/utils/formatters.js';

test('escapeHtml neutraliza contenido HTML recibido de la API', () => {
  assert.equal(escapeHtml('<script>alert("x")</script>'), '&lt;script&gt;alert(&quot;x&quot;)&lt;/script&gt;');
  assert.equal(escapeHtml(null), '');
});

test('formatearCOP muestra moneda colombiana', () => {
  assert.match(formatearCOP(180000), /180[.,]000/);
  assert.equal(formatearCOP('no-numero'), 'Precio no disponible');
});

test('resumen acorta textos largos sin alterar textos cortos', () => {
  assert.equal(resumen('Hola', 8), 'Hola');
  assert.equal(resumen('ABCDEFGHIJK', 5), 'ABCDE…');
});
Cree tests/routes.test.js:
Archivo: tests/routes.test.js — copie el bloque completo (no omita líneas ni imports).
import test from 'node:test';
import assert from 'node:assert/strict';
import { normalizarRuta, resolverRuta } from '../src/js/router/routes.js';
import { mostrarDetalle } from '../src/js/pages/DetallePage.js';
import { mostrarNoEncontrada } from '../src/js/pages/NoEncontradaPage.js';

test('la ruta de catálogo acepta barra final y parámetros de URL', () => {
  assert.equal(normalizarRuta('/alojamientos?filtro=cali'), '/alojamientos/');
  assert.notEqual(resolverRuta('/alojamientos').vista, mostrarNoEncontrada);
  assert.notEqual(resolverRuta('/alojamientos/').vista, mostrarNoEncontrada);
});

test('el detalle reconoce solo IDs enteros positivos', () => {
  assert.equal(resolverRuta('/alojamientos/25').vista, mostrarDetalle);
  assert.equal(resolverRuta('/alojamientos/25').params.id, '25');
  assert.equal(resolverRuta('/alojamientos/abc').vista, mostrarNoEncontrada);
  assert.equal(resolverRuta('/alojamientos/0').vista, mostrarNoEncontrada);
});

test('una dirección desconocida muestra página 404', () => {
  assert.equal(resolverRuta('/reservas').vista, mostrarNoEncontrada);
});
Cree tests/api.test.js:
Archivo: tests/api.test.js — copie el bloque completo (no omita líneas ni imports).
import test from 'node:test';
import assert from 'node:assert/strict';
import { obtenerJSON } from '../src/js/api/http.js';
import { consultarSalud, listarAlojamientos, consultarAlojamiento, listarComodidades } from '../src/js/api/publica.api.js';

const originalFetch = globalThis.fetch;

test.after(() => { globalThis.fetch = originalFetch; });

test('usa las cuatro rutas reales y conserva los contratos JSON', async () => {
  const rutas = [];
  globalThis.fetch = async (url, opciones) => {
    rutas.push(new URL(url).pathname);
    assert.equal(opciones.method, 'GET');
    return { ok: true, json: async () => ({ success: true, data: [] }) };
  };
  await consultarSalud();
  await listarAlojamientos();
  await consultarAlojamiento(4);
  const resultado = await listarComodidades();
  assert.deepEqual(rutas, ['/health', '/api/v1/alojamientos', '/api/v1/alojamientos/4', '/api/v1/comodidades']);
  assert.deepEqual(resultado, { success: true, data: [] });
});

test('preserva status y mensaje de error HTTP del backend', async () => {
  globalThis.fetch = async () => ({ ok: false, status: 404, json: async () => ({ error: { message: 'Alojamiento no encontrado.' } }) });
  await assert.rejects(obtenerJSON('/api/v1/alojamientos/999'), (error) => error.status === 404 && error.message === 'Alojamiento no encontrado.');
});

test('informa cuando Flask no está disponible', async () => {
  globalThis.fetch = async () => { throw new TypeError('Failed to fetch'); };
  await assert.rejects(consultarSalud(), (error) => error.status === 0 && /Flask/.test(error.message));
});
Desde la raíz ejecute:
npm test
Resultado esperado: # tests 9, # pass 9, # fail 0. Si el módulo no se encuentra, revise la carpeta y nombre exacto del archivo con el Explorador de VS Code. A continuación ejecute:
npm run build
Debe crear dist/ sin errores, siempre que npm install haya concluido correctamente. Después puede verificar la distribución con:
npm run preview
Abra la URL que imprima Vite Preview. npm run preview no inicia Flask; debe mantener el backend funcionando si quiere ver información real. Tenga en cuenta que la configuración CORS del backend autoriza inicialmente localhost:5173, no necesariamente el puerto de Preview. Si Preview elige localhost:4173, Flask deberá permitir también ese origen si quiere probar sus GET desde Preview. No modifique el CORS solo para simular pruebas de producción; conserve el alcance del Sprint 1 y pruebe con npm run dev sobre 5173.
19. Escribir el README definitivo del repositorio
Al principio creamos un README temporal únicamente para generar la rama main. Ahora reemplace todo README.md en la raíz del frontend con este contenido. Será la documentación básica de cualquier persona que clone el repo: cómo iniciar, qué endpoints usa, alcance y estructura.
Archivo: README.md — copie el bloque completo (no omita líneas ni imports).
````markdown
# Frontend Alojamientos — Sprint 1


SPA pública construida desde cero con **HTML5, CSS3, JavaScript ES Modules, Bootstrap 5 y Vite**, sin React. Consume la API Flask del proyecto `api-alojamientos` (backend final hasta su Sprint 3).


## Preparación


1. Inicie MySQL y el backend Flask en `http://localhost:5000` y compruebe `GET /health`.
2. Requiera Node.js compatible con Vite 8 (por ejemplo 22.12+).
3. Instale dependencias con `npm ci` (o `npm install` si ha regenerado el lockfile).
4. Cree `.env` a partir de `.env.example` y deje `VITE_API_URL=http://localhost:5000`.
5. Ejecute `npm run dev` y abra `http://localhost:5173`.


```bash
npm ci
npm run dev
```


Otros comandos: `npm test` (9 tests automatizados), `npm run build` (genera `dist/`), `npm run preview` (vista previa de build).


## Contrato HTTP de este sprint


| Método | Ruta Flask | Vista |
|---|---|---|
| GET | `/health` | Inicio, Estado |
| GET | `/api/v1/alojamientos` | Catálogo |
| GET | `/api/v1/alojamientos/:id` | Detalle |
| GET | `/api/v1/comodidades` | Comodidades |


El estado se obtiene de Flask: no hay datos simulados. Si MySQL no tiene alojamientos, la lista estará vacía. Para poblarla siga los pasos de Postman del tutorial. El backend permite por defecto el origen `http://localhost:5173`; no use `127.0.0.1:5173` sin configurar CORS adicionalmente.


## Alcance


Incluye navegación SPA con History API, tarjetas ilustrativas CSS, filtro cliente por ciudad/título, control de errores y cancelación de solicitudes anteriores. **No incluye** autenticación, acciones de escritura, reservas ni subida de imágenes. Las funciones protegidas se incorporarán en los Sprints 2 y 3.


**Seguridad:** nunca coloque secretos en variables `VITE_*`; son públicas en el navegador. `package-lock.json` se versiona y `.env`, `node_modules/` y `dist/` se excluyen.


## Estructura


- `src/js/api/`: peticiones a las rutas reales de Flask.
- `src/js/components/`: navbar, footer, tarjetas, estados.
- `src/js/pages/`: vistas públicas.
- `src/js/router/`: mapa URL-vista y History API.
- `src/js/utils/`: escape HTML, moneda, resumen.
- `src/css/main.css`: diseño responsive.
- `tests/`: pruebas Node sin dependencias externas de testing.


La ruta directa `/alojamientos/1` funciona con Vite en desarrollo. Al desplegar en otro hosting, configure reescritura de rutas SPA hacia `index.html`.
````

20. Diagnóstico de problemas frecuentes
A. npm install falla. Compruebe que puede acceder a npm, que Node sea compatible con Vite 8 y que se encuentra en el directorio que contiene package.json. npm ls --depth=0 ayuda a revisar módulos instalados. No copie la carpeta node_modules de otro equipo.
B. No aparece el menú o Bootstrap. Revise el contenido de src/js/main.js: importa las hojas de estilo y * as bootstrap. Compruebe que index.html contiene <script type="module" src="/src/js/main.js"></script>.
C. La SPA muestra “No fue posible conectar con Flask”. Compruebe http://localhost:5000/health, inicie python app.py desde el backend y confirme VITE_API_URL=http://localhost:5000. Si corrige .env, reinicie Vite para recargar variables.
D. Error CORS en la consola del navegador. Abra .env del backend, confirme CORS_ALLOWED_ORIGINS=http://localhost:5173,..., reinicie Flask. No confunda localhost con 127.0.0.1. Postman no demuestra el comportamiento CORS del navegador.
E. /alojamientos muestra lista vacía. No es fallo si la tabla MySQL está vacía. Cree el alojamiento real de la sección 17 y recargue. No agregue datos de prueba directamente en AlojamientosPage.js.
F. /alojamientos/99999 muestra “Alojamiento no encontrado”. Es correcto si el ID no existe. En Network debe aparecer 404 desde Flask. La página /reservas debe mostrar un 404 del router cliente: son casos diferentes.
G. Al abrir /alojamientos/5 directamente en un hosting diferente de Vite, el servidor responde 404. Configure fallback de rutas SPA hacia index.html en ese hosting. En desarrollo Vite ya lo hace.
H. El frontend funciona pero no ve fotos. Es esperado: los modelos y endpoints de la API entregada no exponen URL de imágenes. Las tarjetas emplean arte CSS para no inventar campos.
I. npm test da menos de 9 pruebas. Compruebe que existen los tres archivos en tests/ y que se escribió todo su contenido. Puede ejecutar node --test tests/api.test.js para aislar un grupo.
J. npm run dev responde que el puerto está ocupado. Detenga el servidor anterior o libere 5173; strictPort: true evita cambiar a otro puerto y romper CORS inadvertidamente.
21. Validación final, Git, Pull Request y revisión
21.1 Pruebas finales. Desde el directorio raíz del frontend ejecute:
npm test
npm run build
git check-ignore -v .env
git status --short
npm test debe mostrar 9 pruebas exitosas y build debe crear dist/. .env debe aparecer como ignorado. node_modules/ y dist/ nunca deben incluirse en staging; package-lock.json sí.
21.2 Comprobación manual (checklist):
Flask en localhost:5000 y GET /health correcto.
Backend con migración 003 y conexión MySQL confirmadas.
npm run dev en localhost:5173.
Inicio indica estado real de la API.
Alojamientos: cargando, listado o estado vacío; filtro título/ciudad.
Detalle con ID real y manejo de ID inexistente.
Comodidades: listado real o vacío.
Atrás/Adelante sin recarga completa del documento.
Ruta inexistente /reservas muestra 404 frontend.
Responsive en móvil (navbar, tarjetas).
API apagada muestra mensaje de error; al reiniciar Flask puede volver a consultar.
No existen JWT, contraseñas ni claves secretas en los archivos versionables.
21.3 Revise el diff:
git status
git diff --stat
git add .
git status
Compruebe que no se agregaron .env, node_modules/ o dist/. Cree el commit y publique la rama:
git commit -m "feat: construir SPA publica del frontend alojamientos"
git push -u origin feat/frontend-sprint-01-spa-publica
En GitHub cree un Pull Request desde feat/frontend-sprint-01-spa-publica hacia main. Título sugerido: Sprint 1 Frontend: SPA pública, catálogo y estado API. En la descripción pegue el objetivo, los cuatro GET reales, npm test: 9 passed, npm run build: exitoso (solo si efectivamente lo comprobó), capturas de Inicio/listado/detalle y el vínculo a su Issue.
Revise que el diff no contenga secretos, que las rutas concuerden con Flask y que el catálogo se muestre sin token. Cuando el docente apruebe, haga merge a main. Después ejecute:
git checkout main
git pull origin main
npm test
Ese main será el punto exacto de partida del Sprint 2 frontend. No cree un proyecto nuevo para la siguiente entrega.
22. Sprint Review, retrospectiva y transferencia al Sprint 2
Durante la Review muestre el backend funcionando, las rutas HTTP en Network, la navegación SPA, tarjetas reales y detalle, filtro, comodidades, estado vacío y error al apagar Flask, visual móvil, npm test y el Pull Request.
Escriba en su Issue o nota de retrospectiva: ¿qué fue más difícil, componentes o router? ¿cómo identificó la diferencia entre rutas cliente y rutas Flask? ¿por qué una respuesta HTTP 404 no provoca automáticamente un catch de fetch? ¿qué mejoraría antes de incorporar JWT?
Punto de continuidad: Sprint 2 reutilizará main.js, el cliente HTTP, el router, la navbar, las páginas públicas y los estilos; agregará auth/, pantallas de registro/login, sesión, guardias de navegación y vistas protegidas. No debe reemplazar este código por la solución completa del frontend de ejemplo.
Fin del Tutorial 01.
