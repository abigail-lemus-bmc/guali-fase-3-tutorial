# Guali Móvil — Programación 2 (UMG)

**Proyecto Fase 3** · Front-end Android del tutor de programación **Guali**, construido con
**Kotlin + XML Views + Material 3** y persistencia local en **SQLite**.

- **Carné:** 3190-23-16494
- **Catedrático:** Ing. MGSi. César Hernández
- **Tecnologías:** Kotlin 2.0.21 · Android Gradle Plugin 8.7.3 · Gradle 8.9 · Material 3 · SQLite · ViewBinding
- **IDE:** Android Studio (Ladybug 2024.2 o superior)
- **SDK:** compileSdk 35 · minSdk 24 · targetSdk 35 · JDK 17

---

## 1. Requisitos

| Requisito | Versión mínima |
|---|---|
| Android Studio | Ladybug 2024.2.1 |
| JDK (Gradle) | 17 |
| Android SDK Platform | 35 |
| Android SDK Build-Tools | 35.0.0 |
| Emulador | Android 15 (API 35), imagen `google_apis/x86_64` |
| Gradle | 8.9 (incluido en el wrapper) |

## 2. Estructura del proyecto

```
proyecto-fase-3-3190-23-16494/
├── build.gradle.kts                 # plugins de Android y Kotlin
├── settings.gradle.kts              # repositorios y módulo :app
├── gradle.properties
├── gradlew / gradlew.bat            # Gradle Wrapper 8.9
├── app/
│   ├── build.gradle.kts             # configuración del módulo
│   └── src/main/
│       ├── AndroidManifest.xml
│       ├── assets/lecciones/        # 4 lecciones (leccion.md, config.txt, diagrama.png)
│       ├── java/com/guali/movil/
│       │   ├── MainActivity.kt      # interfaz principal
│       │   ├── HistorialActivity.kt # panel de historial
│       │   ├── datos/               # repositorios, SQLite, compilador simulado
│       │   ├── modelo/              # Leccion, Objetivo, RegistroHistorial, resultados
│       │   ├── ui/                  # adaptadores de RecyclerView
│       │   └── util/                # Markdown y consola
│       └── res/
│           ├── layout/              # pantallas y diálogos
│           ├── menu/menu_principal.xml
│           ├── drawable/            # iconos y fondos
│           └── values/              # colores, temas y textos
├── docs/
│   ├── tutorial.md                  # tutorial publicado en GitHub
│   └── imagenes/                    # capturas del tutorial
└── manual/manual-tecnico-fase-3.pdf
```

## 3. Cómo abrir el proyecto en Android Studio

1. Descomprima el zip: quedará la carpeta `proyecto-fase-3-3190-23-16494`.
2. Abra **Android Studio** y elija **File > Open**.
3. Seleccione la **carpeta raíz** del proyecto (la que contiene `settings.gradle.kts`).
4. Si aparece el aviso de confianza, elija **Trust Project**.
5. Espere la sincronización de Gradle (barra inferior). La primera vez descarga el
   Android Gradle Plugin, Kotlin y las dependencias de AndroidX.
6. Verifique en **File > Settings > Build, Execution, Deployment > Build Tools > Gradle**
   que **Gradle JDK** sea **17** (o el JDK que incluye Android Studio).
7. Compruebe en **Tools > SDK Manager** que estén instalados **Android SDK Platform 35**
   y **Android SDK Build-Tools 35.0.0**.

No hace falta crear archivos `.idea`: Android Studio los genera al abrir el proyecto.
El archivo `local.properties` también se genera automáticamente con la ruta del SDK.

## 4. Compilar y ejecutar

### 4.1 Desde Android Studio

1. Cree un dispositivo virtual en **Device Manager > Create Virtual Device** (Pixel 6,
   Android 15 / API 35).
2. Seleccione el módulo **app** y presione **Run** (triángulo verde) o `Shift + F10`.
3. El APK se instala y la aplicación arranca en el emulador.

### 4.2 Desde la línea de comandos

```bat
gradlew.bat assembleDebug
```

El APK queda en `app\build\outputs\apk\debug\app-debug.apk`. Para instalarlo en un
dispositivo o emulador conectado:

```bat
adb install -r app\build\outputs\apk\debug\app-debug.apk
```

## 5. Cómo usar la aplicación

1. **Configurar JDK** — menú **Configuración > Configurar JDK**. La ruta se guarda en la
   tabla `configuracion` de SQLite y habilita los botones **Compilar** y **Ejecutar**.
   Sin este paso ambos botones permanecen deshabilitados.
2. **Abrir lección** — menú **Lección > Abrir lección** o el panel lateral. La lección se
   carga en el panel superior y el editor queda listo.
3. **Escribir el código** — copie a mano el código indicado en la lección dentro del
   **Editor de Código**.
4. **Compilar** — el resultado (errores o éxito) aparece en el **Panel de Consola**.
5. **Ejecutar** — solo se habilita si la compilación más reciente fue exitosa. La salida
   del programa aparece en la consola.
6. **Cumplir el objetivo** — cuando se cumple la condición de la lección, el registro pasa
   a **Completada** en el historial y se muestra un aviso.
7. **Ver historial** — menú **Historial > Ver historial**: tabla con lección, hora de
   apertura, hora de cierre y estado, leída desde SQLite.
8. **Cerrar lección** — menú **Lección > Cerrar lección** limpia el panel, el editor y la
   consola.
9. **Ayuda** — menú **Ayuda**: referencia rápida de todas las funciones y objetivos.

## 6. Lecciones y objetivos

| Lección | Objetivo | Condición |
|---|---|---|
| `leccion-01-hola-mundo` | `COMPILAR_EXITOSO` | Compila sin errores |
| `leccion-02-errores-compilacion` | `COMPILAR_CON_ERROR` | El compilador reporta al menos un error |
| `leccion-03-ejecutar-exitoso` | `EJECUTAR_EXITOSO` | Compila y ejecuta sin errores |
| `leccion-04-errores-ejecucion` | `EJECUTAR_CON_ERROR` | Compila y falla en tiempo de ejecución |

Las lecciones se leen de `app/src/main/assets/lecciones/`. Cada carpeta contiene
`leccion.md`, `config.txt` y una imagen opcional. Para agregar una lección nueva basta con
copiar una carpeta con esa estructura y volver a compilar.

## 7. Compilación y ejecución en Android

El archivo `guali-dev.jar` y el JDK son componentes de escritorio: no existen en Android.
Para que el front-end móvil conserve el flujo pedagógico de la Fase 1, la aplicación
incorpora un **motor de simulación local y determinista** (`datos/CompiladorGuali.kt`) que:

- valida la sintaxis del código (delimitadores, cadenas, método `main`, punto y coma),
- reporta errores de compilación con número de línea,
- interpreta el subconjunto de Java usado en las lecciones (variables, arreglos,
  operaciones aritméticas, concatenación e `IO.println`),
- reproduce errores de ejecución como `ArrayIndexOutOfBoundsException` y `/ by zero`.

La ruta del JDK se conserva como configuración persistente para mantener la misma
interfaz y el mismo flujo que la versión de escritorio. Esta decisión está documentada en
el manual técnico y en el tutorial.

## 8. Base de datos local

La aplicación crea `guali_movil.db` con dos tablas:

```sql
CREATE TABLE configuracion (clave TEXT PRIMARY KEY, valor TEXT NOT NULL);
CREATE TABLE historial (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    leccion TEXT NOT NULL,
    apertura INTEGER NOT NULL,
    cierre INTEGER,
    estado TEXT NOT NULL
);
```

`configuracion` guarda la ruta del JDK y `historial` registra cada apertura de lección con
su estado (**Pendiente** o **Completada**). El historial sobrevive al cierre de la app.

## 9. Documentación

- **Tutorial paso a paso:** [`tutorial.md`](tutorial.md) — publicado en
  <https://github.com/abigail-lemus-bmc/guali-fase-3-tutorial/blob/main/tutorial.md>
- **Manual técnico:** `manual/manual-tecnico-fase-3.pdf`.

## 10. Solución de problemas

| Síntoma | Causa y solución |
|---|---|
| `SDK location not found` | Abra el proyecto en Android Studio para que genere `local.properties`, o cree el archivo con `sdk.dir=...`. |
| Gradle no sincroniza | Verifique la conexión a internet y que **Gradle JDK** sea 17. |
| Compilar/Ejecutar deshabilitados | Falta configurar el JDK: **Configuración > Configurar JDK**. |
| El emulador no inicia | Habilite la virtualización (VT-x/AMD-V o Hyper-V) en la BIOS y en Windows. |
| `INSTALL_FAILED_UPDATE_INCOMPATIBLE` | Desinstale la versión anterior: `adb uninstall com.guali.movil`. |
| No aparecen lecciones | Confirme que `app/src/main/assets/lecciones/` contiene las carpetas con `leccion.md` y `config.txt`. |
