# Metas Zona Centro — app independiente (Firebase + GitHub)

Este proyecto es tu tablero "Metas Zona Centro" convertido en una app web
independiente, lista para subir a **Firebase Hosting** (el sitio) y a
**Firestore** (la base de datos compartida en la nube), y para guardar el
código en **GitHub**.

## Qué cambió respecto al artifact original

- Antes, los datos se guardaban con la API interna de Claude (`window.claude`).
  Eso ya no existe fuera de Claude, así que ahora los datos se guardan y se
  leen en tiempo real desde **Firestore**, en el documento
  `metasZonaCentro/zona-centro`.
- Todo lo demás (diseño morado, cálculo de escalas, proyección de cierre,
  alertas, etc.) es exactamente igual.
- Cualquier persona que abra el sitio ve los mismos datos y, si los edita,
  todos los demás ven el cambio al instante (gracias a `onSnapshot`).

## 1. Antes de empezar

Necesitas:
- Una cuenta de Firebase con un proyecto ya creado.
- Un repositorio de GitHub ya creado (vacío o no).
- Tener instalado en tu computador: `node`, `npm`, `git`, y la CLI de Firebase
  (`npm install -g firebase-tools`).

## 2. Pega la configuración de tu proyecto de Firebase

Abre `public/index.html` y busca este bloque cerca del inicio (dentro de
`<script type="module">`):

```js
const firebaseConfig = {
  apiKey: "TU_API_KEY",
  authDomain: "TU_PROYECTO.firebaseapp.com",
  projectId: "TU_PROYECTO",
  storageBucket: "TU_PROYECTO.appspot.com",
  messagingSenderId: "TU_SENDER_ID",
  appId: "TU_APP_ID"
};
```

Reemplázalo por el objeto real de tu proyecto. Lo encuentras en:
**Firebase console → ⚙️ Configuración del proyecto → Tus apps → SDK de
Firebase → Configuración** (si no tienes una "app web" creada todavía, crea
una ahí mismo, es gratis y toma un minuto).

También edita `.firebaserc` y reemplaza `TU_PROYECTO_FIREBASE` por el ID real
de tu proyecto de Firebase (el mismo `projectId` de arriba).

## 3. Activa Firestore (si no lo has hecho)

En Firebase console → **Firestore Database** → "Crear base de datos" → modo
producción → elige una región. Con eso basta; las reglas de seguridad ya
están en `firestore.rules` en este proyecto y se suben en el paso 5.

> ⚠️ Importante: `firestore.rules` deja el documento del tablero abierto a
> cualquiera que tenga la URL del sitio (no hay login en la app). Es
> razonable para un equipo interno, pero cualquiera con el link puede
> editar los datos. Si más adelante quieres agregarle una clave de acceso o
> un login, lo podemos hacer.

## 4. Inicia sesión y conecta la CLI a tu proyecto

En una terminal, dentro de la carpeta de este proyecto:

```bash
firebase login
firebase use --add
```

`firebase use --add` te va a preguntar cuál de tus proyectos de Firebase usar
y qué alias ponerle: elige tu proyecto y usa el alias `default` (así coincide
con `.firebaserc`).

## 5. Sube (deploy) el sitio y las reglas de Firestore

```bash
firebase deploy --only hosting,firestore:rules
```

Al terminar te va a mostrar la URL pública, algo como
`https://TU_PROYECTO.web.app`. Esa es tu app ya en línea.

## 6. Sube el código a GitHub

Dentro de la carpeta del proyecto:

```bash
git init
git add .
git commit -m "App Metas Zona Centro lista para Firebase"
git branch -M main
git remote add origin TU_URL_DE_GITHUB.git
git push -u origin main
```

(Si el repo de GitHub no está vacío, puede que necesites `git pull origin main --allow-unrelated-histories` antes del `push`.)

## 7. Cada vez que quieras actualizar el sitio

Después de editar `public/index.html`:

```bash
firebase deploy --only hosting
git add .
git commit -m "Describe aquí el cambio"
git push
```

## Estructura del proyecto

```
metas-zona-centro-app/
├── public/
│   └── index.html        ← la app completa (HTML + CSS + JS)
├── firebase.json          ← configuración de Firebase Hosting/Firestore
├── .firebaserc             ← alias del proyecto de Firebase (edítalo)
├── firestore.rules        ← reglas de acceso a la base de datos
├── firestore.indexes.json
├── .gitignore
└── README.md               ← este archivo
```
