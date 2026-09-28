# Ciudad OpenFn: la ciudad que se mueve sola

Visualización 3D interactiva que explica, con una metáfora urbana, el papel de **OpenFn** dentro de la infraestructura pública digital (DPI) de un país.

- **Edificios** = sistemas públicos (identidad, registro civil, pagos, salud, educación…)
- **Carreteras** = X-Road, la capa de intercambio seguro de datos
- **Vehículos autónomos** = flujos de OpenFn que mueven datos, dinero y servicios
- **Ciudadanos** = quienes reciben el valor, sin notar la complejidad

Todo vive en un único archivo, `index.html`, sin dependencias externas ni proceso de build.

---

## Contenido

- [Inicio rápido](#inicio-rápido)
- [Qué hace la aplicación](#qué-hace-la-aplicación)
- [Estructura del archivo](#estructura-del-archivo)
- [Cómo modificarla](#cómo-modificarla)
- [Publicar](#publicar)
- [Flujo de trabajo del equipo](#flujo-de-trabajo-del-equipo)
- [Créditos](#créditos)

---

## Inicio rápido

```bash
git clone https://github.com/fcruzp/ciudad-openfn-web.git
cd ciudad-openfn-web
```

### Opción 1: doble clic (recomendada)

**Haz doble clic en `index.html`** y se abrirá en tu navegador. Eso es todo.

- **Funciona 100 % offline.** No necesita internet, servidor ni instalar nada.
- El motor 3D (Three.js), las fuentes y los logos están embebidos en el mismo archivo, así que la página no hace ninguna petición de red.
- Ideal para presentaciones: puedes copiar solo el `index.html` a otra computadora o a una memoria USB.

### Opción 2: con un servidor local (opcional)

No hace falta para usar la app, pero sirve si quieres abrirla desde otro dispositivo de la misma red (por ejemplo, para probarla en el celular) o simular cómo se verá publicada.

Con Python:

```bash
python -m http.server 8000
```

O con Node.js:

```bash
npx serve .
```

Luego abre `http://localhost:8000` (con `npx serve`, usa el puerto que indique la terminal, normalmente `3000`). Desde el celular, usa la IP de tu computadora en lugar de `localhost`.

**Requisitos:** un navegador moderno con WebGL (Chrome, Edge, Firefox o Safari). Nada más.

---

## Qué hace la aplicación

### Narrativa en 4 pasos

El panel lateral cuenta la historia por capas. Cada paso revela una parte de la escena:

| Paso | Título | Qué aparece |
|---|---|---|
| 1. Edificios | Infraestructura pública digital | Solo los sistemas públicos: una ciudad impresionante, pero vacía |
| 2. Carreteras | X-Road: carreteras, puentes y reglas | Las vías y los servidores de seguridad (cubos azules) |
| 3. Vehículos | OpenFn: la flota autónoma | Los vehículos con carga y el registro de flujos en vivo |
| 4. Ciudadanos | Valor donde está la gente | Las personas que reciben avisos y servicios |

El botón **▶ Recorrer la historia** avanza solo por los pasos (6,5 s cada uno). Al cargar, la app empieza en el paso 4 (todo visible).

### Flujos automáticos simulados

Los vehículos recorren la ciudad siguiendo 5 flujos que se repiten al azar:

| Flujo | Recorrido |
|---|---|
| Nacimiento | Registro Civil → Identidad → Beneficios → Pagos → Portal |
| Atención en salud | Salud → Identidad; Salud → Beneficios → Portal |
| Año escolar | Educación → Registro social → Beneficios → Pagos → Portal |
| Recalificación | Hacienda → Registro social → Beneficios → Portal |
| Informe mensual | Beneficios → Hacienda; Pagos → Hacienda |

El color de la carga indica qué se mueve: **ámbar = datos**, **verde = dinero**, **morado = servicios**. Cada viaje completado aparece en el panel "Flujos automáticos" (se muestran los últimos 5). Cuando un vehículo llega al **Portal ciudadano**, sale una onda y "chispas" hacia los ciudadanos, que se iluminan.

### Otras funciones

- **Cámara:** arrastrar para girar; rueda o pellizco para acercar. Rota sola suavemente.
- **Tema claro/oscuro:** respeta la preferencia del sistema y se puede cambiar con el botón.
- **Versión del logo:** completo o ícono.
- Tema y logo se recuerdan en el navegador (`localStorage`, claves `ofn-mode` y `ofn-logo`).
- **Responsive:** debajo de 820 px el canvas pasa arriba y el panel abajo.
- **Accesibilidad:** si el sistema tiene "reducir movimiento", se desactivan la rotación automática y las animaciones del registro.

---

## Estructura del archivo

`index.html` pesa ~880 KB porque incluye todo. Estas son sus partes (los números de línea son aproximados):

| Líneas | Contenido | ¿Se edita? |
|---|---|---|
| 7–13 | `@font-face` con Chakra Petch e IBM Plex Sans en base64 | No |
| 14–36 | Variables CSS de color (`:root`), modo claro y oscuro | Sí |
| 37–106 | Estilos del panel, etiquetas y responsive | Sí |
| 109–137 | HTML del panel lateral y el canvas | Sí |
| 139–145 | **Three.js r128** minificado (una línea de ~600 KB) | **No** |
| 146–1191 | **OrbitControls** de Three.js | **No** |
| 1192–final | **Código de la aplicación** | Sí, aquí está todo lo importante |

> **Consejo de editor:** la línea 144 (Three.js) y las fuentes en base64 son líneas enormes. En VS Code desactiva el ajuste de línea (`Alt+Z`) y navega con `Ctrl+F` buscando los nombres de la sección siguiente (por ejemplo `const FLOWS`) en lugar de hacer scroll.

### Mapa del código de la aplicación

Busca estos nombres dentro del último `<script>`:

| Buscar | Qué controla |
|---|---|
| `const STEPS` | Títulos y textos de los 4 pasos de la historia |
| `const THEMES` | Colores de la escena 3D en modo `dark` y `light` |
| `const S=14` | Tamaño de la cuadrícula (distancia entre calles) |
| `const B={` | Los edificios: nombre, subtítulo, posición y altura |
| `// ---- 2. X-Road` | Carreteras, postes y servidores de seguridad |
| `const PAY` | Tipos de carga de los vehículos (datos, dinero, servicio) |
| `const SPEED` | Velocidad de los vehículos |
| `const FLOWS` | Los flujos automáticos y el texto de cada tramo |
| `// ---- 4. Ciudadanos` | Cantidad y ubicación de los ciudadanos |
| `function deliver` | Efecto visual al llegar al Portal ciudadano |
| `// ---- Labels` | Etiquetas flotantes sobre la escena |
| `setInterval(` (dentro de `playBtn`) | Duración de cada paso en el recorrido automático |

---

## Cómo modificarla

### Cambiar textos

- **Título, subtítulo y leyenda:** en el HTML, dentro de `<aside class="panel">`.
- **Pasos de la historia:** en `STEPS`. `k` es la etiqueta del botón, `t` el título y `d` la descripción.

```js
{k:'Carreteras', t:'X-Road: carreteras, puentes y reglas', d:'La capa de intercambio de datos...'}
```

### Agregar o cambiar un edificio

Los edificios viven en `B`. La ciudad es una cuadrícula de **4 × 4 manzanas**; `b:[columna, fila]` va de `[0,0]` a `[3,3]`, y `h` es la altura.

```js
const B={
  reg:{name:'Registro Civil', sub:'Actas y hechos vitales', b:[0,1], h:9},
  // ...
  mig:{name:'Migración', sub:'Pasaportes y visas', b:[3,1], h:10}   // nuevo
};
```

Reglas:

- Cada manzana admite **un** edificio principal. Hoy están libres `[0,0]`, `[1,0]`, `[2,1]`, `[3,1]`, `[0,2]`, `[3,2]` y `[1,3]`; las manzanas libres se rellenan con edificios pequeños decorativos.
- La clave (`mig`) es el identificador que se usa en `FLOWS`.
- La etiqueta flotante y el servidor de seguridad X-Road se crean solos.
- **No renombres `por` ni `sal`**: el código las usa directamente (`por` es el destino que dispara el efecto hacia los ciudadanos y `sal` lleva la etiqueta "servidor de seguridad"). Si cambias esas claves, actualiza también `deliver()` y la sección `// ---- Labels`.

### Agregar o cambiar un flujo

Cada flujo tiene un nombre y una lista de tramos (`hops`). Cada tramo es:

```js
['origen', 'destino', 'tipo', 'Texto que aparece en el registro']
```

- `origen` y `destino` son claves de `B`.
- `tipo` es `'datos'`, `'dinero'` o `'servicio'`.
- Si el destino es `'por'` (Portal ciudadano), se dispara la animación hacia los ciudadanos.

Ejemplo:

```js
{name:'Renovación de pasaporte', hops:[
  ['por','id','datos','Solicitud con identidad verificada'],
  ['id','mig','datos','Datos biográficos enviados a Migración'],
  ['por','pay','dinero','Pago de la tasa'],
  ['mig','por','servicio','Cita asignada al ciudadano']
]}
```

Los tramos se ejecutan en orden, uno detrás de otro. Al terminar un flujo se elige otro al azar.

### Agregar un tipo de carga nuevo

Por ejemplo, `bienes`. Hay que tocarlo en cinco lugares:

1. CSS: agregar `--bienes:#...` en los tres bloques de color de `:root` (claro, oscuro por sistema y oscuro forzado).
2. HTML: agregar una fila en `.legend`.
3. JS: agregar `bienes` en `PAY` y en `PAYCSS`.
4. JS: agregar `bienes` en `THEMES.dark.pay` y en `THEMES.light.pay`.
5. Usarlo en `FLOWS`.

### Cambiar colores

Los colores están en **dos lugares** que deben mantenerse coherentes:

- **CSS (`:root`)**: panel, textos, leyenda y etiquetas. Hay tres bloques: claro por defecto, oscuro por preferencia del sistema y oscuro forzado (`[data-mode="dark"]`).
- **JS (`THEMES`)**: todo lo que se dibuja en 3D (suelo, edificios, carreteras, vehículos, ciudadanos). Los colores van en hexadecimal `0xRRGGBB`.

Algunos materiales se crean con un color inicial en el código, pero `applyTheme()` los sobrescribe al cargar. **El color que manda es el de `THEMES`.**

### Ajustes rápidos

| Qué | Dónde | Valor actual |
|---|---|---|
| Velocidad de vehículos | `const SPEED` | `9` |
| Número de ciudadanos | `for(let n=0;n<90;n++)` | `90` |
| Duración de cada paso al recorrer la historia | `setInterval(..., 6500)` | 6,5 s |
| Paso inicial al cargar | `let step=3` | 4.º paso (todo visible) |
| Velocidad de rotación de la cámara | `controls.autoRotateSpeed` | `.35` |
| Zoom mínimo y máximo | `controls.minDistance` / `maxDistance` | `30` / `190` |
| Entradas visibles en el registro | `logEl.children.length>5` | 5 |

### Reemplazar los logos

Los tres logos (palabra en claro, palabra en oscuro e ícono) son `<img>` con `src="data:image/png;base64,..."` dentro de `.brand`. Para cambiarlos, convierte el PNG a base64 y reemplaza el contenido del `src`:

```bash
base64 -w0 logo.png
```

En PowerShell:

```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("logo.png"))
```

Otra opción es guardar el PNG junto al `index.html` y usar `src="logo.png"`, aunque así la página deja de ser un archivo único.

---

## Publicar

Como es un archivo estático, sirve cualquier hosting:

- **GitHub Pages:** en el repositorio, *Settings → Pages → Deploy from a branch → `main` / root*. La página quedará en `https://fcruzp.github.io/ciudad-openfn-web/`.
- **Netlify, Vercel o Cloudflare Pages:** arrastra la carpeta, sin comando de build.
- **Presentaciones sin internet:** basta con copiar `index.html` a una memoria USB.

---

## Flujo de trabajo del equipo

1. Crea una rama para tu cambio:

   ```bash
   git checkout -b mi-cambio
   ```

2. Edita `index.html` y recarga el navegador para ver el resultado.
3. Antes de subir, revisa:
   - [ ] Se ve bien en **modo claro y oscuro**.
   - [ ] Se ve bien en **pantalla de celular** (herramientas de desarrollador, vista móvil, menos de 820 px).
   - [ ] Los **4 pasos** y el botón "Recorrer la historia" funcionan.
   - [ ] No hay errores en la consola del navegador (`F12`).
   - [ ] Si agregaste un flujo, sus claves de origen y destino existen en `B`.
4. Sube la rama y abre un Pull Request:

   ```bash
   git add index.html
   git commit -m "Describe el cambio"
   git push -u origin mi-cambio
   ```

**Evita** editar o reformatear las secciones de Three.js, OrbitControls y las fuentes en base64. Un formateador automático (Prettier y similares) sobre todo el archivo generaría un diff gigante e ilegible.

---

## Créditos

- **Motor 3D:** [Three.js](https://threejs.org/) r128 y OrbitControls, licencia MIT (© 2010-2021 Three.js Authors).
- **Tipografías:** [Chakra Petch](https://fonts.google.com/specimen/Chakra+Petch) e [IBM Plex Sans](https://fonts.google.com/specimen/IBM+Plex+Sans), licencia SIL Open Font License.
- **OpenFn:** bien público digital certificado por la DPGA. [openfn.org](https://www.openfn.org)
