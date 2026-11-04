TUTORIAL 02 — Autenticación y autorización sin React
Sprint 2 · Frontend Alojamientos · Construcción incremental sobre el Sprint 1 verificado
Instrucciones: lea primero SPRINT-02.md. Este tutorial no crea un frontend nuevo: utiliza su propio frontend-alojamientos del Sprint 1, ya integrado en main. No copie solucion-referencia al trabajo del estudiante. Cada archivo reemplazado aparece completo, incluidos imports. Los archivos que no se nombran expresamente se conservan intactos.
0. Qué va a construir y qué NO va a inventar
Al finalizar conservará el catálogo y agregará estas rutas de interfaz: /registro (registro), /login (autenticación), /perfil (lectura protegida) y /admin/usuarios (lectura solo administrador); la barra mostrará enlaces según la sesión actual y permitirá salir. Los servicios reales que consumirá son:
Método
URL relativa de Flask
Respuesta clave
POST
/api/v1/usuarios/registro
201, data.id, data.correo, data.rol
POST
/api/v1/usuarios/login
200, data.access_token, data.usuario
GET
/api/v1/usuarios/perfil
200, data.id, data.usuario_id, nombre, apellido, telefono
GET
/api/v1/admin/usuarios
200, data: [{id,correo,rol}]

En registro, envíe solo correo y contrasena. En login, envíe esos dos campos y nunca el token. En los dos GET protegidos envíe Authorization: Bearer <token>. Los errores 400, 401, 403 y 409 deben mostrarse con mensajes del backend. No existen endpoints de editar perfil, promover a admin desde el navegador ni logout remoto; no añada esas funcionalidades.
Estructura adicional respecto al Sprint 1:
src/js/
├── api/auth.api.js              [NUEVO]
├── api/http.js                  [REEMPLAZADO]
├── state/sesion.js              [NUEVO]
├── components/Navbar.js        [REEMPLAZADO]
├── router/routes.js             [REEMPLAZADO]
├── router/router.js             [REEMPLAZADO]
├── utils/validaciones.js        [NUEVO]
└── pages/
    ├── RegistroPage.js         [NUEVO]
    ├── LoginPage.js            [NUEVO]
    ├── PerfilPage.js           [NUEVO]
    ├── AdminUsuariosPage.js    [NUEVO]
    └── NoAutorizadoPage.js      [NUEVO]

src/css/main.css                 [AÑADIR BLOQUE AL FINAL]
tests/auth-api.test.js           [NUEVO]
tests/sesion.test.js             [NUEVO]
tests/validaciones.test.js       [NUEVO]
tests/permisos.test.js           [NUEVO]
README.md                        [REEMPLAZADO]
No cambie package.json, package-lock.json, vite.config.js, index.html, .env.example, src/js/main.js, src/js/config.js ni src/js/api/publica.api.js. No necesitaremos instalar nuevos paquetes: las pruebas usan node:test.
1. Abrir el proyecto del Sprint 1 y comprobar regresión
Abra la terminal en su proyecto frontend (no en el backend). En Windows PowerShell:
```powershell
cd C:\ruta\frontend-alojamientos
node --version
npm --version
git status
```

En Linux/macOS:
cd /ruta/frontend-alojamientos
node --version
npm --version
git status
La ruta es de ejemplo; sustitúyala por la ruta real de su equipo. Si no existe node_modules/, ejecute desde la raíz:
npm ci
npm clean install. Es un comando de Node.js que instala las dependencias de un proyecto de forma limpia y reproducible, utilizando las versiones registradas en package-lock.json.
Compruebe que finalizó el Sprint 1 con 9 pruebas:
npm test
Debe mostrar # pass 9 y # fail 0. Si falla, no siga: el Sprint 2 se construye sobre el código que ya desarrolló. Verifique en Git que el código está integrado en main y que no existen cambios sin confirmar. No se inicializa Git de nuevo.
2. Encender y comprobar la API Flask
En una segunda terminal, vaya al repositorio backend api-alojamientos. Active el entorno virtual Python (según su sistema):
Windows PowerShell:
```powershell
cd C:\ruta\api-alojamientos
.\.venv\Scripts\Activate.ps1
```

Linux/macOS:
cd /ruta/api-alojamientos
source .venv/Scripts/activate
Compruebe MySQL y la migración 003 de su backend ya terminado:
python -m scripts.verificar_mysql
flask --app app.py db current
python app.py
Mantenga Flask corriendo en http://localhost:5000. La revisión 003 debe estar aplicada. Si no está en 003, complete las migraciones de los tutoriales del backend antes de continuar.
En .env del backend confirme:
```env
CORS_ALLOWED_ORIGINS=http://localhost:5173,http://localhost:3000
```

No copie el .env del backend al frontend. El .env del frontend Sprint 1 tiene únicamente VITE_API_URL=http://localhost:5000, sin claves MySQL ni JWT. Abra la URL http://localhost:5000/health en el navegador para verificar que la API está activa. El endpoint de salud no sustituye la verificación de MySQL: ambas comprobaciones son distintas.
3. Actualizar main, crear Issue y crear feature branch
Con la terminal frontend abierta, ejecute:
git checkout main
git pull origin main
npm test
git status
En GitHub, sobre su propio repositorio frontend, cree una Issue con título:
Sprint 2 frontend: registro, login, sesión y autorización
Pegue como descripción:
```markdown
## Objetivo
Incorporar autenticación JWT y vistas protegidas sin React a la SPA del Sprint 1.


## Alcance
- Registro y login con endpoints reales Flask.
- SessionStorage, Navbar dinámica y cierre de sesión local.
- Guardas del router, perfil y listado admin de solo lectura.
- Reutilización de fetch centralizado y manejo de 400/401/403/409.
- Regresión de las 9 pruebas públicas y 14 pruebas nuevas.


## Criterios de aceptación
- registro válido 201; inválido 400; repetido 409.
- login correcto 200 y se guarda access_token; incorrecto 401.
- perfil sin sesión redirige a login; con Bearer muestra perfil.
- usuario normal no ve panel admin; API admin devuelve 403.
- administrador consulta lista de usuarios 200.
- cerrar sesión limpia token; npm test: 23 passed.
```

De main actualizado cree la rama:
git checkout -b feat/frontend-sprint-02-autenticacion
git branch --show-current
Debe verse feat/frontend-sprint-02-autenticacion. Trabaje únicamente en esa rama; no pegue código de la referencia sobre main.
4. Crear carpetas y archivos nuevos
La estructura del Sprint 1 ya incluye src/js/api, src/js/pages, src/js/utils, src/js/router y tests. Únicamente falta src/js/state y algunos archivos. Sitúese en la raíz frontend-alojamientos/.
Windows PowerShell:
```powershell
New-Item -ItemType Directory -Force src\js\state | Out-Null
New-Item -ItemType File -Force src\js\state\sesion.js, src\js\api\auth.api.js, src\js\utils\validaciones.js, src\js\pages\RegistroPage.js, src\js\pages\LoginPage.js, src\js\pages\PerfilPage.js, src\js\pages\AdminUsuariosPage.js, src\js\pages\NoAutorizadoPage.js, tests\auth-api.test.js, tests\sesion.test.js, tests\validaciones.test.js, tests\permisos.test.js | Out-Null
```

Linux/macOS:
mkdir -p src/js/state
touch src/js/state/sesion.js src/js/api/auth.api.js src/js/utils/validaciones.js
touch src/js/pages/RegistroPage.js src/js/pages/LoginPage.js src/js/pages/PerfilPage.js
touch src/js/pages/AdminUsuariosPage.js src/js/pages/NoAutorizadoPage.js
touch tests/auth-api.test.js tests/sesion.test.js tests/validaciones.test.js tests/permisos.test.js
En VS Code abra cada archivo cuando se solicite, seleccione todo (Ctrl+A / Cmd+A), pegue el bloque exacto y guarde. No cree ningún __init__.py: esos archivos corresponden al backend Python, no a módulos JavaScript.
5. Crear el estado de sesión en sessionStorage
Este módulo será independiente del DOM para poder probarlo con Node. Conserva token y {id,correo,rol} únicamente en la pestaña. Es información para mostrar la interfaz; los roles verdaderos los controla Flask. Una notificación sesion-cambiada informa al router que la Navbar debe repintarse. La sesión se borra al cerrar sesión o ante una respuesta 401 de una petición que llevaba token.
Cree src/js/state/sesion.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
/**
 * Estado de sesión por pestaña. sessionStorage se vacía al cerrar la pestaña;
 * su contenido nunca concede permisos: cada petición es validada en Flask.
 */
const CLAVE = 'alojamientos-sesion-v1';

function almacenamiento() {
  try { return globalThis.sessionStorage ?? null; } catch { return null; }
}

function avisarCambio() {
  if (typeof window !== 'undefined' && typeof window.dispatchEvent === 'function') {
    window.dispatchEvent(new Event('sesion-cambiada'));
  }
}

export function obtenerSesion() {
  const almacen = almacenamiento();
  if (!almacen) return null;
  try {
    const valor = almacen.getItem(CLAVE);
    if (!valor) return null;
    const datos = JSON.parse(valor);
    if (typeof datos?.token !== 'string' || !datos.token ||
        !datos.usuario || typeof datos.usuario.correo !== 'string' ||
        !['admin', 'usuario'].includes(datos.usuario.rol)) {
      almacen.removeItem(CLAVE);
      return null;
    }
    return datos;
  } catch {
    try { almacen.removeItem(CLAVE); } catch { /* Storage restringido */ }
    return null;
  }
}

export function guardarSesion(accessToken, usuario) {
  if (typeof accessToken !== 'string' || !accessToken ||
      !usuario || typeof usuario.correo !== 'string' ||
      !['admin', 'usuario'].includes(usuario.rol)) {
    throw new Error('El login no devolvió una sesión válida.');
  }
  const almacen = almacenamiento();
  if (!almacen) throw new Error('El navegador impide guardar la sesión en esta pestaña.');
  const sesion = {
    token: accessToken,
    usuario: { id: usuario.id, correo: usuario.correo, rol: usuario.rol },
  };
  almacen.setItem(CLAVE, JSON.stringify(sesion));
  avisarCambio();
  return sesion;
}

export function cerrarSesion() {
  try { almacenamiento()?.removeItem(CLAVE); } catch { /* Bloqueo de Storage */ }
  avisarCambio();
}

export function obtenerToken() {
  return obtenerSesion()?.token ?? null;
}

export function esAdmin() {
  return obtenerSesion()?.usuario?.rol === 'admin';
}
Comprenda: obtenerSesion() valida el formato guardado; guardarSesion() comprueba los campos del login real; cerrarSesion() elimina el almacenamiento; obtenerToken() da acceso al servicio HTTP; esAdmin() es un indicador de interfaz, nunca autorización del servidor. No copie SECRET_KEY del backend ni decodifique el JWT para suponer privilegios.
6. Ampliar el cliente HTTP sin romper GET del Sprint 1
Ya existe src/js/api/http.js del Sprint 1. Ahora lo reemplazamos completamente por una versión que también admite POST, JSON, token Bearer y respuesta vacía 204; se mantiene el export obtenerJSON() usado por las cuatro rutas públicas. No añada una segunda dirección base al código: el origen seguirá saliendo de src/js/config.js. El 401 de login sin token solo informa error; el 401 de una petición con JWT borra la sesión.
Reemplace src/js/api/http.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
import { API_BASE_URL } from '../config.js';
import { cerrarSesion } from '../state/sesion.js';

/** Peticiones públicas y protegidas con el contrato JSON real de Flask. */
export async function solicitarJSON(ruta, { method = 'GET', body, token, signal } = {}) {
  const headers = { Accept: 'application/json' };
  if (body !== undefined) headers['Content-Type'] = 'application/json';
  if (token) headers.Authorization = `Bearer ${token}`;

  let respuesta;
  try {
    respuesta = await fetch(`${API_BASE_URL}${ruta}`, {
      method, headers, signal,
      ...(body !== undefined ? { body: JSON.stringify(body) } : {}),
    });
  } catch (error) {
    if (error?.name === 'AbortError') throw error;
    const fallo = new Error('No fue posible conectar con Flask. Compruebe el servidor y CORS para http://localhost:5173.');
    fallo.status = 0;
    throw fallo;
  }

  // Se prevé DELETE 204 para el Sprint 3; no debe interpretarse como JSON roto.
  if (respuesta.status === 204) return null;
  let cuerpo;
  try {
    cuerpo = await respuesta.json();
  } catch {
    const fallo = new Error('La API no devolvió un JSON válido.');
    fallo.status = respuesta.status;
    throw fallo;
  }
  if (!respuesta.ok) {
    // No cerrar sesión al recibir 401 de /login: todavía no se envió JWT.
    if (respuesta.status === 401 && token) cerrarSesion();
    const fallo = new Error(cuerpo?.error?.message || `Error HTTP ${respuesta.status}.`);
    fallo.status = respuesta.status;
    fallo.details = cuerpo?.error?.details;
    throw fallo;
  }
  return cuerpo;
}
en publica.api.js actualizar las importaciones:
  import { obtenerJSON } from '../api/cliente.js';
Debe cambiarlo por:
  import { solicitarJSON } from '../api/cliente.js';
y remplazar
  return solicitarJSON ('/health', { signal });
  return solicitarJSON ('/api/v1/alojamientos', { signal });
  return solicitarJSON (`/api/v1/alojamientos/${id}`, { signal });
  return solicitarJSON ('/api/v1/comodidades', { signal });
en api.test.js actualizar a:
import { solicitarJSON  } from '../src/js/api/http.js';
y
await assert.rejects(solicitarJSON ('/api/v1/alojamientos/999'), (error) => error.status === 404 && error.message === 'Alojamiento no encontrado.');
7. Crear los servicios de autenticación y administración
Un servicio representa una operación concreta del backend. No agregue PATCH /perfil, POST /logout ni POST /refresh: no existen en los controladores entregados. consultarPerfil() y listarUsuariosAdmin() obtienen el token de sesion.js y lo envían al cliente HTTP; los métodos públicos no lo envían.
Cree src/js/api/auth.api.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
import { solicitarJSON } from './http.js';
import { obtenerToken } from '../state/sesion.js';

/** Registro 201 {success,data:{id,correo,rol}}; errores 400 o 409. */
export function registrarUsuario(datos, signal) {
  return solicitarJSON('/api/v1/usuarios/registro', { method: 'POST', body: datos, signal });
}

/** Login 200 {success,data:{access_token,usuario}}; error 401. */
export function iniciarSesionAPI(datos, signal) {
  return solicitarJSON('/api/v1/usuarios/login', { method: 'POST', body: datos, signal });
}

/** Perfil 200 {success,data:{id,nombre,apellido,telefono,usuario_id}}. */
export function consultarPerfil(signal) {
  return solicitarJSON('/api/v1/usuarios/perfil', { token: obtenerToken(), signal });
}

/** Administración 200 {success,data:[{id,correo,rol}]}; error 403. */
export function listarUsuariosAdmin(signal) {
  return solicitarJSON('/api/v1/admin/usuarios', { token: obtenerToken(), signal });
}
8. Validar formularios y destinos de navegación
La interfaz comprobará de inmediato formato de correo, longitud mínima seis y confirmación. El backend repetirá sus validaciones propias mediante Marshmallow. La confirmación se usa solo en el frontend, no se envía en JSON. Para evitar redirecciones inseguras, el parámetro next solo admite la ruta de administración cuando el usuario tiene rol admin; el destino por defecto es /perfil.
Cree src/js/utils/validaciones.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
/** Validaciones inmediatas de UX; Marshmallow sigue siendo la autoridad. */
export function validarRegistro({ correo, contrasena, confirmar }) {
  if (!correo || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(correo)) {
    return 'Escriba un correo con formato válido.';
  }
  if (!contrasena || contrasena.length < 6) {
    return 'La contraseña debe tener al menos 6 caracteres.';
  }
  if (contrasena !== confirmar) {
    return 'La confirmación no coincide con la contraseña.';
  }
  return null;
}

export function validarLogin({ correo, contrasena }) {
  if (!correo?.trim() || !contrasena) return 'Ingrese correo y contraseña.';
  return null;
}

/** Solo se permite redirigir a vistas privadas conocidas, nunca a otra web. */
export function destinoTrasLogin(search, rol) {
  const destino = new URLSearchParams(search).get('next');
  if (destino === '/admin/usuarios' && rol === 'admin') return destino;
  return '/perfil';
}
9. Construir la vista pública de registro
La vista muestra un <form> Bootstrap con correo, contrasena y confirmar. Su evento submit usa preventDefault(), valida campos, deshabilita el botón mientras espera, envía el body correcto al backend y muestra 201 o errores HTTP (400, 409). No inicia sesión automáticamente: el backend de registro únicamente devuelve el usuario creado. Con los campos correo=ana@ejemplo.com, contrasena=123456, confirmar=123456, el usuario debe ver una confirmación y después ir a Login.
Cree src/js/pages/RegistroPage.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
import { registrarUsuario } from '../api/auth.api.js';
import { estadoError } from '../components/Estados.js';
import { escapeHtml } from '../utils/formatters.js';
import { validarRegistro } from '../utils/validaciones.js';

export async function mostrarRegistro({ contenedor, signal }) {
  contenedor.innerHTML = `<section class="container section-block auth-section">
    <div class="auth-card"><span class="eyebrow">CREAR CUENTA</span><h1 class="h2 fw-bold mt-2">Regístrese en Aloja.</h1>
    <p class="text-muted">El servidor creará un usuario con rol <strong>usuario</strong>. El registro no inicia sesión automáticamente.</p>
    <form id="registro-form" novalidate>
      <div class="mb-3"><label class="form-label" for="registro-correo">Correo electrónico</label>
        <input class="form-control" id="registro-correo" name="correo" type="email" autocomplete="email" required maxlength="255" /></div>
      <div class="mb-3"><label class="form-label" for="registro-contrasena">Contraseña (mínimo 6 caracteres)</label>
        <input class="form-control" id="registro-contrasena" name="contrasena" type="password" autocomplete="new-password" minlength="6" required /></div>
      <div class="mb-3"><label class="form-label" for="registro-confirmar">Confirmar contraseña</label>
        <input class="form-control" id="registro-confirmar" name="confirmar" type="password" autocomplete="new-password" minlength="6" required /></div>
      <div id="registro-feedback" aria-live="polite"></div>
      <button class="btn btn-accent w-100" type="submit">Crear cuenta</button>
    </form>
    <p class="small text-muted mt-3 mb-0">¿Ya tiene cuenta? <a href="/login" data-link>Iniciar sesión</a>.</p>
    </div></section>`;

  const form = contenedor.querySelector('#registro-form');
  const feedback = contenedor.querySelector('#registro-feedback');
  const boton = form.querySelector('button[type="submit"]');
  form.addEventListener('submit', async (evento) => {
    evento.preventDefault();
    const datos = {
      correo: form.elements.correo.value.trim(),
      contrasena: form.elements.contrasena.value,
      confirmar: form.elements.confirmar.value,
    };
    const fallo = validarRegistro(datos);
    if (fallo) { feedback.innerHTML = estadoError(new Error(fallo)); return; }
    boton.disabled = true;
    feedback.textContent = 'Creando cuenta…';
    try {
      const respuesta = await registrarUsuario({ correo: datos.correo, contrasena: datos.contrasena }, signal);
      if (signal.aborted) return;
      if (!respuesta?.data?.id) throw new Error('El registro no devolvió el identificador esperado.');
      feedback.innerHTML = `<div class="alert alert-success" role="status">Cuenta creada para <strong>${escapeHtml(respuesta.data.correo)}</strong>. Ahora puede <a href="/login" data-link>iniciar sesión</a>.</div>`;
      form.reset();
    } catch (error) {
      if (!signal.aborted) feedback.innerHTML = estadoError(error);
    } finally {
      if (!signal.aborted) boton.disabled = false;
    }
  });
}
10. Construir la vista pública de login
Esta página recibe la respuesta real data.access_token y data.usuario, guarda la sesión y navega a /perfil. Si se llegó desde un guard de ruta, admite un destino privado conocido. El JWT nunca aparece en el HTML, en un parámetro de URL ni en la consola. Contraseña incorrecta produce 401 y debe mostrarse un error en la página, sin navegar.
Cree src/js/pages/LoginPage.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
import { iniciarSesionAPI } from '../api/auth.api.js';
import { estadoError } from '../components/Estados.js';
import { guardarSesion } from '../state/sesion.js';
import { navegar } from '../router/router.js';
import { destinoTrasLogin, validarLogin } from '../utils/validaciones.js';

export async function mostrarLogin({ contenedor, signal }) {
  contenedor.innerHTML = `<section class="container section-block auth-section">
    <div class="auth-card"><span class="eyebrow">ÁREA PERSONAL</span><h1 class="h2 fw-bold mt-2">Iniciar sesión</h1>
    <p class="text-muted">Utilice el correo y la contraseña registrados en la API Flask.</p>
    <form id="login-form" novalidate>
      <div class="mb-3"><label class="form-label" for="login-correo">Correo electrónico</label>
        <input class="form-control" id="login-correo" name="correo" type="email" autocomplete="username" required /></div>
      <div class="mb-3"><label class="form-label" for="login-contrasena">Contraseña</label>
        <input class="form-control" id="login-contrasena" name="contrasena" type="password" autocomplete="current-password" required /></div>
      <div id="login-feedback" aria-live="polite"></div>
      <button class="btn btn-accent w-100" type="submit">Ingresar</button>
    </form>
    <p class="small text-muted mt-3 mb-0">¿No tiene cuenta? <a href="/registro" data-link>Registrarse</a>.</p>
    </div></section>`;
  const form = contenedor.querySelector('#login-form');
  const feedback = contenedor.querySelector('#login-feedback');
  const boton = form.querySelector('button[type="submit"]');
  form.addEventListener('submit', async (evento) => {
    evento.preventDefault();
    const datos = {
      correo: form.elements.correo.value.trim(),
      contrasena: form.elements.contrasena.value,
    };
    const fallo = validarLogin(datos);
    if (fallo) { feedback.innerHTML = estadoError(new Error(fallo)); return; }
    boton.disabled = true;
    feedback.textContent = 'Validando credenciales…';
    try {
      const respuesta = await iniciarSesionAPI(datos, signal);
      if (signal.aborted) return;
      const { access_token: token, usuario } = respuesta?.data ?? {};
      guardarSesion(token, usuario);
      navegar(destinoTrasLogin(window.location.search, usuario.rol));
    } catch (error) {
      if (!signal.aborted) feedback.innerHTML = estadoError(error);
    } finally {
      if (!signal.aborted) boton.disabled = false;
    }
  });
}
11. Construir el perfil protegido de solo lectura
El endpoint GET /api/v1/usuarios/perfil devuelve el perfil, no un usuario completo. Los campos nombre, apellido y telefono pueden estar en null, y eso se representa como «Sin registrar». El correo y rol se recuperan de la información del último login. No se dibuja formulario de edición, porque el backend no ofrece una ruta para guardar esos cambios.
Cree src/js/pages/PerfilPage.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
import { consultarPerfil } from '../api/auth.api.js';
import { estadoCargando, estadoError } from '../components/Estados.js';
import { obtenerSesion } from '../state/sesion.js';
import { escapeHtml } from '../utils/formatters.js';

function campo(valor) { return valor == null || valor === '' ? 'Sin registrar' : escapeHtml(valor); }

export async function mostrarPerfil({ contenedor, signal }) {
  const usuario = obtenerSesion()?.usuario;
  contenedor.innerHTML = `<section class="container section-block">
    <div class="page-heading"><span class="eyebrow">CUENTA</span><h1>Mi perfil</h1>
    <p>Consulta protegida: <code>GET /api/v1/usuarios/perfil</code> con Bearer Token.</p></div>
    <div id="perfil-region" aria-live="polite">${estadoCargando('Cargando el perfil…')}</div>
  </section>`;
  const region = contenedor.querySelector('#perfil-region');
  try {
    const respuesta = await consultarPerfil(signal);
    if (signal.aborted) return;
    const perfil = respuesta?.data;
    if (!perfil || typeof perfil !== 'object') throw new Error('La API devolvió un perfil inesperado.');
    region.innerHTML = `<div class="account-card">
      <div class="account-avatar" aria-hidden="true"><i class="bi bi-person-circle"></i></div>
      <div><span class="eyebrow">DATOS DE LA CUENTA</span>
        <h2 class="h4 mt-2">${campo(usuario?.correo)}</h2>
        <p class="text-muted">Rol al iniciar sesión: <span class="badge text-bg-light">${campo(usuario?.rol)}</span></p>
        <dl class="row mb-0">
          <dt class="col-sm-5">ID de usuario</dt><dd class="col-sm-7">${campo(perfil.usuario_id)}</dd>
          <dt class="col-sm-5">Nombre</dt><dd class="col-sm-7">${campo(perfil.nombre)}</dd>
          <dt class="col-sm-5">Apellido</dt><dd class="col-sm-7">${campo(perfil.apellido)}</dd>
          <dt class="col-sm-5">Teléfono</dt><dd class="col-sm-7">${campo(perfil.telefono)}</dd>
        </dl>
        <p class="small text-muted mb-0 mt-3">Estos campos son solo de lectura: el backend proporcionado no expone PATCH de perfil.</p>
      </div>
    </div>`;
  } catch (error) {
    if (!signal.aborted) region.innerHTML = estadoError(error);
  }
}
12. Construir la lista administrativa sin inventar operaciones
La administración usa GET /api/v1/admin/usuarios y renderiza exclusivamente id, correo, rol, escapando los valores. No muestra contraseñas ni edición de roles. Antes de llegar aquí, el router revisará el rol reflejado en la sesión; Flask siempre hará una comprobación independiente contra MySQL.
Cree src/js/pages/AdminUsuariosPage.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
import { listarUsuariosAdmin } from '../api/auth.api.js';
import { estadoCargando, estadoError, estadoVacio } from '../components/Estados.js';
import { escapeHtml } from '../utils/formatters.js';

export async function mostrarAdminUsuarios({ contenedor, signal }) {
  contenedor.innerHTML = `<section class="container section-block">
    <div class="page-heading"><span class="eyebrow">ADMINISTRACIÓN</span><h1>Usuarios registrados</h1>
    <p>Vista de solo lectura. Flask valida en MySQL el rol actual de quien envía el JWT.</p></div>
    <div id="admin-region" aria-live="polite">${estadoCargando('Consultando usuarios…')}</div>
  </section>`;
  const region = contenedor.querySelector('#admin-region');
  try {
    const respuesta = await listarUsuariosAdmin(signal);
    if (signal.aborted) return;
    const usuarios = respuesta?.data;
    if (!Array.isArray(usuarios)) throw new Error('La API devolvió un listado de usuarios inesperado.');
    region.innerHTML = usuarios.length ? `<div class="table-responsive account-table"><table class="table table-hover align-middle mb-0">
      <caption class="visually-hidden">Usuarios registrados en la aplicación</caption>
      <thead><tr><th scope="col">ID</th><th scope="col">Correo</th><th scope="col">Rol</th></tr></thead>
      <tbody>${usuarios.map((usuario) => `<tr><td>${escapeHtml(usuario.id)}</td><td>${escapeHtml(usuario.correo)}</td>
        <td><span class="badge ${usuario.rol === 'admin' ? 'text-bg-success' : 'text-bg-secondary'}">${escapeHtml(usuario.rol)}</span></td></tr>`).join('')}</tbody>
      </table></div>` : estadoVacio('Todavía no hay usuarios registrados.');
  } catch (error) {
    if (!signal.aborted) region.innerHTML = estadoError(error);
  }
}
13. Crear la vista explicativa para acceso restringido
Un usuario autenticado sin rol admin que introduzca /admin/usuarios verá un mensaje claro de restricción. Esta pantalla no sustituye el HTTP 403 del backend: es una decisión de interfaz. Para verificar la protección efectiva haga la petición directamente con un JWT de usuario normal desde Postman.
Cree src/js/pages/NoAutorizadoPage.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
export async function mostrarNoAutorizado({ contenedor }) {
  contenedor.innerHTML = `<section class="container section-block"><div class="state-panel state-empty">
    <i class="bi bi-shield-lock display-4" aria-hidden="true"></i>
    <h1 class="h3 mt-3">Acceso restringido (403)</h1>
    <p>Esta vista requiere rol administrador. Su cuenta no tiene ese permiso.</p>
    <p class="small text-muted">El servidor vuelve a validar cada operación; ocultar botones no concede ni revoca permisos.</p>
    <a href="/perfil" data-link class="btn btn-outline-success">Volver al perfil</a>
  </div></section>`;
}
14. Actualizar el mapa de rutas SPA
Se agregan rutas públicas (/registro, /login) y privadas (/perfil, /admin/usuarios). No pierda las cinco vistas del Sprint 1, el soporte para IDs de detalle ni la normalización de /alojamientos/. El export resolverRuta conservará vista y params y añadirá requiereAuth y requiereAdmin.
Reemplace src/js/router/routes.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
import { mostrarInicio } from '../pages/InicioPage.js';
import { mostrarAlojamientos } from '../pages/AlojamientosPage.js';
import { mostrarDetalle } from '../pages/DetallePage.js';
import { mostrarComodidades } from '../pages/ComodidadesPage.js';
import { mostrarEstado } from '../pages/EstadoPage.js';
import { mostrarNoEncontrada } from '../pages/NoEncontradaPage.js';
import { mostrarRegistro } from '../pages/RegistroPage.js';
import { mostrarLogin } from '../pages/LoginPage.js';
import { mostrarPerfil } from '../pages/PerfilPage.js';
import { mostrarAdminUsuarios } from '../pages/AdminUsuariosPage.js';

const rutas = [
  { patron: /^\/$/, vista: mostrarInicio },
  { patron: /^\/alojamientos\/$/, vista: mostrarAlojamientos },
  { patron: /^\/alojamientos\/([1-9]\d*)$/, vista: mostrarDetalle, parametro: 'id' },
  { patron: /^\/comodidades$/, vista: mostrarComodidades },
  { patron: /^\/estado$/, vista: mostrarEstado },
  { patron: /^\/registro$/, vista: mostrarRegistro },
  { patron: /^\/login$/, vista: mostrarLogin },
  { patron: /^\/perfil$/, vista: mostrarPerfil, requiereAuth: true },
  { patron: /^\/admin\/usuarios$/, vista: mostrarAdminUsuarios, requiereAuth: true, requiereAdmin: true },
];

export function normalizarRuta(ruta) {
  const limpia = String(ruta || '/').split(/[?#]/)[0].replace(/\/+$/, '') || '/';
  return limpia === '/alojamientos' ? '/alojamientos/' : limpia;
}

export function resolverRuta(ruta) {
  const normalizada = normalizarRuta(ruta);
  for (const { patron, vista, parametro, requiereAuth = false, requiereAdmin = false } of rutas) {
    const coincidencia = normalizada.match(patron);
    if (coincidencia) {
      return { vista, params: parametro ? { [parametro]: coincidencia[1] } : {}, requiereAuth, requiereAdmin };
    }
  }
  return { vista: mostrarNoEncontrada, params: {}, requiereAuth: false, requiereAdmin: false };
}
15. Actualizar navegación, guardas y cierre de sesión
El router evalúa el estado de sesión en cada navegación y al usar Atrás/Adelante. Si la vista requiere login, usa replaceState para enviar a /login?next=.... Si requiere admin y la sesión tiene rol usuario, muestra la página 403. También detecta botones [data-logout] mediante delegación de eventos y escucha sesion-cambiada para repintar la Navbar sin recargar index.html. La opción AbortController del Sprint 1 sigue cancelando peticiones de la vista anterior.
Reemplace src/js/router/router.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
import { pintarNavbar } from '../components/Navbar.js';
import { pintarFooter } from '../components/Footer.js';
import { obtenerSesion, cerrarSesion } from '../state/sesion.js';
import { mostrarNoAutorizado } from '../pages/NoAutorizadoPage.js';
import { resolverRuta } from './routes.js';

let controladorActual = null;
let navegacionIniciada = false;

function refrescarNavbar() {
  const cabecera = document.querySelector('#site-header');
  if (cabecera) cabecera.innerHTML = pintarNavbar(window.location.pathname);
}

async function dibujarRuta() {
  controladorActual?.abort();
  controladorActual = new AbortController();
  const signal = controladorActual.signal;
  const app = document.querySelector('#app');
  document.querySelector('#site-footer').innerHTML = pintarFooter();
  let { vista, params, requiereAuth, requiereAdmin } = resolverRuta(window.location.pathname);
  const sesion = obtenerSesion();

  if (requiereAuth && !sesion) {
    const destino = window.location.pathname;
    window.history.replaceState({}, '', `/login?next=${encodeURIComponent(destino)}`);
    ({ vista, params } = resolverRuta('/login'));
  } else if (requiereAdmin && sesion?.usuario?.rol !== 'admin') {
    vista = mostrarNoAutorizado;
    params = {};
  }

  refrescarNavbar();
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
    const salir = evento.target.closest('[data-logout]');
    if (salir) {
      evento.preventDefault();
      cerrarSesion();
      navegar('/');
      return;
    }
    const enlace = evento.target.closest('a[data-link]');
    if (!enlace || enlace.hasAttribute('download') || enlace.target === '_blank') return;
    const destino = new URL(enlace.href, window.location.href);
    if (destino.origin !== window.location.origin) return;
    evento.preventDefault();
    navegar(`${destino.pathname}${destino.search}${destino.hash}`);
    const menu = document.querySelector('#navbarMenu');
    if (menu?.classList.contains('show')) {
      window.bootstrap?.Collapse.getInstance(menu)?.hide();
    }
  });
  window.addEventListener('popstate', () => void dibujarRuta());
  window.addEventListener('sesion-cambiada', () => {
    refrescarNavbar();
    const { requiereAuth } = resolverRuta(window.location.pathname);
    if (requiereAuth && !obtenerSesion()) navegar('/login');
  });
  void dibujarRuta();
}
16. Actualizar la Navbar según la sesión
La navbar pública del Sprint 1 se conserva. Sin login mostrará «Ingresar» y «Registrarse». Con login mostrará «Mi perfil» y «Cerrar sesión», además del correo; para admin también «Administración». Los correos provenientes de datos se escapan con escapeHtml. Un enlace oculto no es una barrera de seguridad; el API valida cada token y rol.
Reemplace src/js/components/Navbar.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
import { obtenerSesion } from '../state/sesion.js';
import { escapeHtml } from '../utils/formatters.js';

const enlacesPublicos = [
  { ruta: '/', nombre: 'Inicio' },
  { ruta: '/alojamientos', nombre: 'Alojamientos' },
  { ruta: '/comodidades', nombre: 'Comodidades' },
  { ruta: '/estado', nombre: 'Estado de API' },
];

export function pintarNavbar(rutaActual) {
  const sesion = obtenerSesion();
  const enlaces = [...enlacesPublicos];
  if (sesion) {
    enlaces.push({ ruta: '/perfil', nombre: 'Mi perfil' });
    if (sesion.usuario.rol === 'admin') {
      enlaces.push({ ruta: '/admin/usuarios', nombre: 'Administración' });
    }
  }
  const links = enlaces.map(({ ruta, nombre }) => {
    const activo = ruta === '/' ? rutaActual === '/' : rutaActual.startsWith(ruta);
    return `<li class="nav-item"><a class="nav-link ${activo ? 'active' : ''}"
      ${activo ? 'aria-current="page"' : ''} href="${ruta}" data-link>${escapeHtml(nombre)}</a></li>`;
  }).join('');
  const acciones = sesion
    ? `<li class="nav-item"><span class="navbar-note navbar-identidad">${escapeHtml(sesion.usuario.correo)}</span></li>
       <li class="nav-item"><button class="btn btn-outline-secondary btn-sm ms-lg-2" type="button" data-logout>Cerrar sesión</button></li>`
    : `<li class="nav-item"><a href="/login" data-link class="btn btn-outline-success btn-sm ms-lg-2">Ingresar</a></li>
       <li class="nav-item"><a href="/registro" data-link class="btn btn-accent btn-sm ms-lg-1">Registrarse</a></li>`;

  return `<nav class="navbar navbar-expand-lg site-navbar" aria-label="Navegación principal">
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
        <ul class="navbar-nav ms-auto align-items-lg-center gap-lg-2">${links}${acciones}</ul>
      </div>
    </div>
  </nav>`;
}
17. Ampliar el CSS sin borrar Sprint 1
Abra src/css/main.css. No reemplace el archivo ni borre sus reglas anteriores. Vaya al final del archivo, después de la última llave } del Sprint 1, y añada exactamente estas reglas nuevas. No use otro archivo CSS ni instale librerías extras; main.js ya importa main.css y Bootstrap desde el Sprint 1.
/* Sprint 2 — Autenticación y autorización: continuidad visual de Sprint 1. */
.auth-section { display: grid; place-items: start center; }
.auth-card { width: min(100%, 510px); background: #fff; border: 1px solid var(--line); border-radius: 22px; padding: clamp(1.5rem, 3vw, 2.5rem); box-shadow: 0 20px 55px #14372d14; }
.auth-card .form-label { color: var(--pine); font-weight: 700; }
.auth-card .form-control { border-radius: 10px; padding: .8rem .9rem; }
.auth-card .btn { padding: .76rem; }
.auth-card a { color: var(--forest); font-weight: 700; }
.auth-card .alert { margin: 1rem 0; overflow-wrap: anywhere; }
.navbar-identidad { display: inline-block; max-width: 180px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; vertical-align: middle; }
.account-card { max-width: 790px; background: #fff; border: 1px solid var(--line); border-radius: 20px; padding: 2.3rem; display: grid; grid-template-columns: auto 1fr; gap: 1.8rem; }
.account-avatar { font-size: 4rem; color: var(--forest); }
.account-card dt { font-weight: 700; }
.account-card dd { overflow-wrap: anywhere; }
.account-table { background: #fff; padding: 1.2rem; border: 1px solid var(--line); border-radius: 18px; }
.account-table td { overflow-wrap: anywhere; }
@media (max-width: 767px) { .account-card { grid-template-columns: 1fr; padding: 1.4rem; } .navbar-identidad { max-width: 100%; } }
18. Crear pruebas automáticas del contrato HTTP
Añadiremos pruebas con node:test. Se simula la respuesta de fetch: estas pruebas comprueban método, URL, formato JSON y uso correcto de Authorization sin conectar MySQL. No reemplace tests/api.test.js, tests/routes.test.js ni tests/formatters.test.js, que deben mantenerse para la regresión.
Cree tests/auth-api.test.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
import test from 'node:test';
import assert from 'node:assert/strict';
import { registrarUsuario, iniciarSesionAPI, consultarPerfil, listarUsuariosAdmin } from '../src/js/api/auth.api.js';
const originalFetch = globalThis.fetch;
const originalStorage = globalThis.sessionStorage;
test.after(() => { globalThis.fetch = originalFetch; globalThis.sessionStorage = originalStorage; });

test('registro envía POST JSON con los campos existentes en Flask', async () => {
  globalThis.fetch = async (url, opciones) => {
    assert.equal(new URL(url).pathname, '/api/v1/usuarios/registro');
    assert.equal(opciones.method, 'POST');
    assert.equal(opciones.headers['Content-Type'], 'application/json');
    assert.equal(opciones.headers.Authorization, undefined);
    assert.deepEqual(JSON.parse(opciones.body), { correo: 'ana@example.com', contrasena: '123456' });
    return { ok: true, status: 201, json: async () => ({ success: true, data: { id: 1, correo: 'ana@example.com', rol: 'usuario' } }) };
  };
  const respuesta = await registrarUsuario({ correo: 'ana@example.com', contrasena: '123456' });
  assert.equal(respuesta.data.rol, 'usuario');
});

test('login no adjunta un token y conserva access_token', async () => {
  globalThis.fetch = async (url, opciones) => {
    assert.equal(new URL(url).pathname, '/api/v1/usuarios/login');
    assert.equal(opciones.headers.Authorization, undefined);
    return { ok: true, status: 200, json: async () => ({ success: true, data: { access_token: 'jwt', usuario: { id: 1, correo: 'ana@example.com', rol: 'usuario' } } }) };
  };
  assert.equal((await iniciarSesionAPI({ correo: 'ana@example.com', contrasena: '123456' })).data.access_token, 'jwt');
});

test('perfil y administración adjuntan Bearer Token al consultar Flask', async () => {
  const almacen = new Map();
  globalThis.sessionStorage = { getItem: (k) => almacen.get(k) ?? null, setItem: (k, v) => almacen.set(k, v), removeItem: (k) => almacen.delete(k) };
  const { guardarSesion } = await import('../src/js/state/sesion.js');
  guardarSesion('token-test', { id: 9, correo: 'admin@example.com', rol: 'admin' });
  const rutas = [];
  globalThis.fetch = async (url, opciones) => {
    rutas.push(new URL(url).pathname);
    assert.equal(opciones.headers.Authorization, 'Bearer token-test');
    return { ok: true, status: 200, json: async () => ({ success: true, data: [] }) };
  };
  await consultarPerfil();
  await listarUsuariosAdmin();
  assert.deepEqual(rutas, ['/api/v1/usuarios/perfil', '/api/v1/admin/usuarios']);
});

test('401 en login sin token no cierra la sesión previamente guardada', async () => {
  const { obtenerSesion } = await import('../src/js/state/sesion.js');
  globalThis.fetch = async () => ({ ok: false, status: 401, json: async () => ({ error: { message: 'Credenciales inválidas.' } }) });
  await assert.rejects(iniciarSesionAPI({ correo: 'x', contrasena: 'bad' }), (e) => e.status === 401);
  assert.equal(obtenerSesion()?.token, 'token-test');
});

test('401 en petición protegida invalida la sesión', async () => {
  const { obtenerSesion } = await import('../src/js/state/sesion.js');
  globalThis.fetch = async () => ({ ok: false, status: 401, json: async () => ({ error: { message: 'Token expirado.' } }) });
  await assert.rejects(consultarPerfil(), (e) => e.status === 401);
  assert.equal(obtenerSesion(), null);
});
19. Crear pruebas de sesión y validaciones
Estos tests simulan un sessionStorage temporal en memoria, de modo que Node puede ejecutarlos sin un navegador. Comprueban limpieza, datos inválidos y lógica de formulario; el usuario siempre deberá comprobar el recorrido real por la interfaz con Flask al final.
Cree tests/sesion.test.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
import test from 'node:test';
import assert from 'node:assert/strict';
import { obtenerSesion, guardarSesion, cerrarSesion, obtenerToken, esAdmin } from '../src/js/state/sesion.js';

function memoria() {
  const mapa = new Map();
  return { getItem: (k) => mapa.get(k) ?? null, setItem: (k, v) => mapa.set(k, v), removeItem: (k) => mapa.delete(k) };
}
const originalStorage = globalThis.sessionStorage;
test.before(() => { globalThis.sessionStorage = memoria(); });
test.after(() => { globalThis.sessionStorage = originalStorage; });

test('almacena usuario y token en sessionStorage y puede cerrar la sesión', () => {
  guardarSesion('jwt-ejemplo', { id: 1, correo: 'u@example.com', rol: 'usuario' });
  assert.equal(obtenerSesion().usuario.correo, 'u@example.com');
  assert.equal(obtenerToken(), 'jwt-ejemplo');
  assert.equal(esAdmin(), false);
  cerrarSesion();
  assert.equal(obtenerSesion(), null);
});

test('permite reconocer un rol admin sólo para la interfaz', () => {
  guardarSesion('jwt-admin', { id: 2, correo: 'a@example.com', rol: 'admin' });
  assert.equal(esAdmin(), true);
  cerrarSesion();
});

test('rechaza datos corruptos, sin usuario o rol permitido', () => {
  assert.throws(() => guardarSesion('jwt', { correo: 'x', rol: 'superadmin' }));
  globalThis.sessionStorage.setItem('alojamientos-sesion-v1', 'no-es-json');
  assert.equal(obtenerSesion(), null);
});
Cree tests/validaciones.test.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
import test from 'node:test';
import assert from 'node:assert/strict';
import { validarRegistro, validarLogin, destinoTrasLogin } from '../src/js/utils/validaciones.js';

test('registro valida email, mínimo de contraseña y confirmación', () => {
  assert.match(validarRegistro({ correo: 'incorrecto', contrasena: '123456', confirmar: '123456' }), /correo/);
  assert.match(validarRegistro({ correo: 'a@example.com', contrasena: '123', confirmar: '123' }), /6/);
  assert.match(validarRegistro({ correo: 'a@example.com', contrasena: '123456', confirmar: '123000' }), /coincide/);
  assert.equal(validarRegistro({ correo: 'a@example.com', contrasena: '123456', confirmar: '123456' }), null);
});

test('login exige ambos campos', () => {
  assert.ok(validarLogin({ correo: '', contrasena: '123' }));
  assert.equal(validarLogin({ correo: 'a@example.com', contrasena: 'clave' }), null);
});

test('redirige únicamente a vistas privadas conocidas e impide URLs externas', () => {
  assert.equal(destinoTrasLogin('?next=%2Fadmin%2Fusuarios', 'admin'), '/admin/usuarios');
  assert.equal(destinoTrasLogin('?next=%2Fadmin%2Fusuarios', 'usuario'), '/perfil');
  assert.equal(destinoTrasLogin('?next=https%3A%2F%2Fejemplo.com', 'admin'), '/perfil');
  assert.equal(destinoTrasLogin('?next=%2F%2Fejemplo.com', 'admin'), '/perfil');
});
20. Crear pruebas del mapa de rutas
Las rutas privadas deben declararse expresamente, y la navegación no debe inventar endpoints o vistas. El test de rutas comprueba que existen las pantallas previstas y que /perfil/editar y /reservas permanecen como páginas desconocidas.
Cree tests/permisos.test.js y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
import test from 'node:test';
import assert from 'node:assert/strict';
import { resolverRuta } from '../src/js/router/routes.js';
import { mostrarPerfil } from '../src/js/pages/PerfilPage.js';
import { mostrarAdminUsuarios } from '../src/js/pages/AdminUsuariosPage.js';
import { mostrarLogin } from '../src/js/pages/LoginPage.js';
import { mostrarRegistro } from '../src/js/pages/RegistroPage.js';
import { mostrarNoEncontrada } from '../src/js/pages/NoEncontradaPage.js';

test('registro y login son rutas públicas', () => {
  assert.equal(resolverRuta('/registro').vista, mostrarRegistro);
  assert.equal(resolverRuta('/login').vista, mostrarLogin);
  assert.equal(resolverRuta('/login').requiereAuth, false);
});

test('perfil requiere sesión y administración también requiere rol', () => {
  assert.equal(resolverRuta('/perfil').vista, mostrarPerfil);
  assert.equal(resolverRuta('/perfil').requiereAuth, true);
  assert.equal(resolverRuta('/perfil').requiereAdmin, false);
  assert.equal(resolverRuta('/admin/usuarios').vista, mostrarAdminUsuarios);
  assert.equal(resolverRuta('/admin/usuarios').requiereAdmin, true);
});

test('no inventa actualización de perfil ni rutas de reservas', () => {
  assert.equal(resolverRuta('/perfil/editar').vista, mostrarNoEncontrada);
  assert.equal(resolverRuta('/reservas').vista, mostrarNoEncontrada);
});
21. Actualizar el README de su repositorio
Para que otro estudiante o docente sepa ejecutar este incremento acumulado, reemplace README.md de su repositorio frontend por el contenido siguiente (mantendrá el detalle del Sprint 1 y sumará las nuevas vistas). No copie ninguna contraseña real.
Reemplace README.md y copie íntegramente el siguiente bloque; no omita imports ni mezcle versiones.
````markdown
# Frontend Alojamientos — Sprint 2 (SPA Vanilla)


Incremento acumulado **Sprint 1 + Sprint 2** del frontend de `api-alojamientos`. HTML5, CSS3, Bootstrap 5, JavaScript ES Modules, Vite, `fetch`, History API. Sin React, Vue ni Angular.


## Requisitos


- Node compatible con Vite 8 (preferiblemente 22.12 o posterior), npm y Git.
- API Flask real operativa en `http://localhost:5000`; MySQL disponible y migración `003` aplicada.
- En el `.env` **del backend**: `CORS_ALLOWED_ORIGINS=http://localhost:5173,http://localhost:3000`.


## Preparación


Desde este directorio:


```bash
npm ci
```


En Windows PowerShell:


```powershell
Copy-Item .env.example .env
```


En Linux/macOS:


```bash
cp .env.example .env
```


La única variable necesaria para Vite es `VITE_API_URL=http://localhost:5000`. No copie las claves MySQL ni `SECRET_KEY` del backend al frontend. Vite expone las variables con prefijo `VITE_` al navegador.


```bash
npm test
npm run dev
```


Abra `http://localhost:5173`, manteniendo Flask disponible en otra terminal. Para comprobar la compilación:


```bash
npm run build
npm run preview
```


## Vistas


- Sprint 1 público: `/`, `/alojamientos`, `/alojamientos/:id`, `/comodidades`, `/estado`.
- Sprint 2 público: `/registro`, `/login`.
- Sprint 2 protegido: `/perfil` (JWT), `/admin/usuarios` (JWT + rol `admin`).


La sesión se guarda solo en `sessionStorage` y desaparece al cerrar la pestaña. No existe logout remoto ni renovación del JWT en la API real. Para obtener un admin, registre un usuario y ejecute `python -m scripts.promover_admin admin@ejemplo.com` **en la carpeta backend** con su `.venv`; cierre sesión e inicie nuevamente para refrescar el menú si cambió el rol.


## Contratos backend


`POST /api/v1/usuarios/registro` (201/400/409), `POST /api/v1/usuarios/login` (200/401), `GET /api/v1/usuarios/perfil` (200/401), `GET /api/v1/admin/usuarios` (200/401/403). No se implementa edición de perfiles ni de roles: esas rutas no existen en el backend entregado.


## Pruebas


`npm test` debe informar **23 pruebas superadas**, incluidos los 9 casos del Sprint 1. Las pruebas automatizadas simulan llamadas a `fetch` y no sustituyen el recorrido manual completo contra Flask/MySQL. Consulte `SPRINT-02.md` y `TUTORIAL-02.md` del paquete del docente.
````

22. Ejecutar tests y verificar cada archivo
Desde la terminal del frontend, con su rama Sprint 2 activa:
npm test
Resultado esperado:
# tests 23
# pass 23
# fail 0
Para ver nombres y comprobar código de salida:
node --test tests/auth-api.test.js
node --test tests/sesion.test.js
node --test tests/validaciones.test.js
node --test tests/permisos.test.js
Compruebe que los nueve tests del Sprint 1 continúan exitosos y que el total nuevo es 23. También puede revisar la sintaxis de archivos JS concretos:
node --check src/js/state/sesion.js
node --check src/js/router/router.js
node --check src/js/pages/LoginPage.js
Si falta un import, observe las rutas relativas y el uso exacto de mayúsculas/minúsculas: Linux distingue LoginPage.js de loginPage.js. Los paths comienzan en la carpeta que contiene el archivo importador, no en la raíz del proyecto.
23. Arrancar el frontend y validar la navegación SPA
Con Flask todavía iniciado en http://localhost:5000, abra otra terminal en la raíz del frontend (o use la que ejecutó los tests):
npm run dev
Vite debe mostrar http://localhost:5173. Abra esa URL y observe:
En la Navbar aparecen Inicio, Alojamientos, Comodidades, Estado de API, Ingresar y Registrarse.
Pulse Alojamientos; los datos públicos deben seguir cargando con GET sin token.
Pulse Registrarse; la URL cambia a /registro sin recargar el documento index.html.
Desde otra pestaña del navegador, introduzca http://localhost:5173/perfil sin sesión: debe terminar en /login?next=%2Fperfil.
Pulse Atrás/Adelante; el router debe seguir mostrando páginas correctas.
Si Vite utiliza otro puerto, no continúe: el backend solo permite los orígenes definidos en CORS. El fichero vite.config.js de Sprint 1 fija 5173 con strictPort. Si aparece una pantalla blanca, abra DevTools → Console y revise imports; si aparece Failed to fetch, verifique Flask/MySQL y DevTools → Network.
24. Caso manual A: registro 201, validación 400 y duplicado 409
En /registro, escriba un correo de prueba realista, por ejemplo usuario@ejemplo.com, contraseña de práctica 123456 y la misma confirmación. Pulse Crear cuenta. La API debe responder 201 y la interfaz debe mostrar el correo y el enlace a login.
En DevTools → Network examine POST http://localhost:5000/api/v1/usuarios/registro. En Payload deben aparecer solo:
{"correo":"usuario@ejemplo.com","contrasena":"123456"}
El JSON no debe incluir confirmar. La respuesta tiene success:true y data:{id,correo,rol}. Vuelva a enviar el mismo correo: la API responderá 409 y se mostrará el error.
Pruebe después un correo sin @ o una contraseña de cinco caracteres: el navegador mostrará un mensaje antes de llamar al backend (validación UX). Para ver el 400 del servidor independientemente de la validación frontend, use Postman o DevTools → Network con un cliente HTTP y envíe { "correo":"incorrecto", "contrasena":"123456" } al endpoint; Flask debe devolver 400 desde Marshmallow.
Nota de seguridad: 123456 es exclusivamente una contraseña de laboratorio, nunca una recomendación para una cuenta real. No suba capturas que expongan credenciales.
25. Caso manual B: login 200 y 401, perfil 200
Abra /login y use las credenciales recién registradas. El servicio debe ejecutar:
```http
POST /api/v1/usuarios/login
Content-Type: application/json
```

Con body:
{"correo":"usuario@ejemplo.com","contrasena":"123456"}
La respuesta de Flask debe ser 200 con data.access_token y data.usuario; no publique el token. Debe navegar a /perfil, actualizar la Navbar y ocultar «Registrarse/Ingresar». En DevTools → Network, la solicitud GET /api/v1/usuarios/perfil llevará Authorization: Bearer .... Debe devolver 200 e incluir usuario_id. nombre, apellido y telefono podrán aparecer como «Sin registrar» porque el backend los crea vacíos.
Cierre sesión y repita el login con contraseña incorrecta. Debe mostrar el error 401 y no iniciar sesión. Recargue manualmente /perfil sin token: debe redirigir a login. Con login válido, refresque la página /perfil: mientras la pestaña siga abierta y el JWT no haya expirado, la sesión seguirá disponible en sessionStorage.
Límite real: el JWT de la API tiene 15 minutos por defecto. Si está expirado, el backend devuelve 401, la interfaz borra sesión y pide otro login. No existe endpoint de renovación.
26. Caso manual C: usuario normal frente a administración
Con un usuario normal autenticado, escriba http://localhost:5173/admin/usuarios en la barra del navegador. Debe ver una pantalla de acceso restringido sin cargar datos administrativos. El servidor también impediría la operación: para comprobarlo, en Postman realice GET {{baseUrl}}/api/v1/admin/usuarios con Authorization → Bearer Token y el JWT del usuario normal. Debe responder 403. En Postman, el campo Token incluye únicamente el JWT; no escriba Bearer dentro del campo, porque Postman lo antepone.
La diferencia importante es que la SPA decide qué pantalla mostrar, pero Flask valida el permiso. Nadie adquiere el rol admin por editar el HTML o sessionStorage.
27. Caso manual D: crear un administrador con el backend real
Un administrador no se registra enviando "rol":"admin": el DTO del backend no acepta ese campo y siempre crea rol usuario. Siga este flujo:
En /registro cree admin@ejemplo.com (o use un correo de práctica distinto si ya existe).
Abra una terminal nueva en la carpeta backend api-alojamientos, con .venv activo y MySQL conectado.
Ejecute:
python -m scripts.promover_admin admin@ejemplo.com
Debe aparecer admin@ejemplo.com ahora tiene rol admin.
En la SPA, cierre sesión si está autenticado y haga login con admin@ejemplo.com.
La Navbar debe mostrar Administración. Visite /admin/usuarios y compruebe una tabla con id, correo y rol, entregada por GET /api/v1/admin/usuarios con Bearer JWT.
Debe responder 200. No verá botones para cambiar roles ni para revelar contraseñas porque el backend no tiene esos endpoints.
Si promociona la cuenta después de haber iniciado sesión, el backend consultará el rol actualizado, pero el menú puede mostrar el rol anterior almacenado en la pestaña: cierre sesión y vuelva a ingresar para actualizar la representación del frontend. Para la demostración, evite copiar el JWT en capturas o materiales públicos.
28. Comprobar cierre de sesión, expiración y navegador
Desde la Navbar del usuario autenticado, pulse Cerrar sesión: desaparece «Mi perfil», reaparecen «Ingresar» y «Registrarse», y en DevTools → Application → Session Storage ya no habrá entrada de sesión. Nunca copie el token de esta pantalla al repositorio.
Pruebe un caso de expiración desde Flask (deje transcurrir la vigencia del JWT durante la práctica o use un token inválido en un cliente HTTP). La respuesta protegida debe indicar HTTP 401. El cliente limpia la sesión y conduce a login. No afirme que logout revoca tokens en el servidor: la API carece de esa capacidad.
Revise la respuesta de /health y los recursos públicos después de hacer logout: deben seguir accesibles. Para un escenario sin conexión, detenga temporalmente Flask e intente usar /perfil con sesión: aparecerá un mensaje de red; vuelva a iniciar Flask antes de continuar.
29. Ejecutar compilación y revisar seguridad
Con las dependencias de Sprint 1 instaladas correctamente:
npm run build
El comando debe generar dist/ sin errores. No suba node_modules ni dist/ al repositorio del ejercicio: .gitignore del Sprint 1 ya los excluye. Compruebe que no exista un .env del backend, un token JWT real o una contraseña real dentro del código:
git status --short
git ls-files .env
git ls-files .env no debe mostrar ninguna ruta. Los tokens nunca deben aparecer en código, README, HTML estático ni capturas de pantalla. El código puede contener credenciales ficticias explícitas de pruebas automáticas, sin uso real.
npm test sigue siendo una prueba lógica con fetch simulado; npm run build verifica ensamblado del frontend, pero ninguno sustituye las pruebas integradas de los pasos 24–28.
30. Commit, push y Pull Request del Sprint 2
En la rama del Sprint 2 revise los cambios:
git diff --stat
git diff
git status
Confirme que no se agregaron rutas inventadas, secretos, archivos generados de compilación ni carpetas node_modules. Después:
git add .
git status
git commit -m "feat: frontend registro login jwt y autorizacion"
git push -u origin feat/frontend-sprint-02-autenticacion
En GitHub cree un Pull Request:
base: main
compare: feat/frontend-sprint-02-autenticacion
Título: Sprint 2 frontend — autenticación y autorización SPA
Descripción sugerida para copiar:
```markdown
## Entregables
- Registro y login sobre endpoints reales Flask.
- sessionStorage, Navbar por sesión y logout local.
- Router protegido, perfil solo lectura y lista administrativa.
- Manejo 400/401/403/409 con fetch centralizado.
- Pruebas del Sprint 1 preservadas y 14 nuevas.


## Verificación
- npm test: 23 passed.
- npm run build sin errores.
- Registro 201, repetido 409, login 200, inválido 401.
- Perfil 200 con Bearer, usuario normal 403 en API admin, admin 200.


## Seguridad
- Ningún token, contraseña real ni SECRET_KEY versionados.
- No se inventaron rutas de editar perfil, refresh ni logout remoto.
```

Realice Sprint Review demostrando las operaciones en navegador y Network sin mostrar el JWT. Cuando el PR haya sido revisado, haga merge a main desde GitHub; luego, en terminal:
git checkout main
git pull origin main
npm test
Debe seguir obteniendo 23 tests. Este main será el punto de partida obligatorio del Sprint 3.
31. Lista de comprobación final y retrospectiva
Antes de declarar terminado el Sprint 2 compruebe:
[ ] Sprint 1 actualizado y 9 pruebas anteriores preservadas
[ ] API Flask y MySQL disponibles; revisión 003 aplicada
[ ] Issue y feature branch creadas
[ ] sesion.js, auth.api.js y validaciones.js incluidos
[ ] http.js amplía métodos y conserva obtenerJSON
[ ] Registro 201 / repetido 409 / datos inválidos 400
[ ] Login 200 + token / contraseña incorrecta 401
[ ] Navbar dinámica y botón de logout funcionando
[ ] /perfil requiere JWT; datos opcionales tratados correctamente
[ ] /admin/usuarios oculta vista para usuario normal y Flask devuelve 403
[ ] Cuenta admin promovida desde backend y GET admin da 200
[ ] npm test: 23 passed
[ ] npm run build sin errores
[ ] .env, JWT, contraseñas reales y SECRET_KEY fuera de Git
[ ] Commit, push, PR, Review y merge completados
Retrospectiva: ¿qué diferencia hay entre autenticación y autorización?, ¿por qué el rol del navegador no es fiable?, ¿qué ocurre cuando caduca un JWT?, ¿qué parte de sessionStorage facilita la SPA y qué riesgo mantiene?, ¿qué mejoraríamos en el Sprint 3?


