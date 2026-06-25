al usar turso y expo-sqlite y habilitar `useLibSQL` en `app.json`

```json

"plugins": [
    [
        "expo-sqlite",{
            "useLibSQL": true
        }
    ]
]
```

te puede aparecer un error como este:

```bash
ERROR  [Error: Call to function 'NativeDatabase.closeAsync' has been rejected.
→ Caused by: java.lang.UnsatisfiedLinkError: No implementation found for void expo.modules.sqlite.NativeDatabaseBinding.sqlite3_finalize_all_statement() (tried Java_expo_modules_sqlite_NativeDatabaseBinding_sqlite3_1finalize_1all_1statement and Java_expo_modules_sqlite_NativeDatabaseBinding_sqlite3_1finalize_1all_1statement__) - is the library loaded, e.g. System.loadLibrary?]

Code: _layout.tsx
  33 |   return (
  34 |     <SafeAreaView edges={["top"]} style={{ flex: 1 }}>
> 35 |       <SQLiteProvider
     |       ^
  36 |         databaseName="app-database"
  37 |         options={{
  38 |           libSQLOptions: {
Call Stack
  RootLayout (src/app/_layout.tsx:35:7) 
```

la sulucion es:
- limpiar la cache `npx expo start -c`
- crear una preconstruccion limpia `npx expo prebuild --clean`
- contruir la app para el emulador `npx expo run:android`


---

android puede mostrarte un error como este:

```
Android App Compatibiliy

This app isn't 16 KB compatible. ELF alignment check filed. This app will be run using page size compatible mode. For best compatibility, please recompile the application with 16 KB support. For more information, see https://devloper.android.com/16kb-page-size.

```

seguido de una lista no ordenada con los errores. 
en su totalidad o mayoria seran `Unknown error`

Ese mensaje es una advertencia que introdujo Google recientemente en las versiones más nuevas de Android. No te preocupes, **no va a romper tu aplicación en desarrollo**, pero es un aviso importante para cuando vayas a subir la app a producción.

Aquí te explico de qué se trata y cómo solucionarlo:

### ¿Por qué sale este aviso?

Tradicionalmente, Android ha utilizado un tamaño de página de memoria de **4 KB**. Sin embargo, los dispositivos Android más modernos están empezando a utilizar páginas de **16 KB** para mejorar el rendimiento del sistema y reducir el consumo de batería.

El error `ELF alignment check failed` significa que alguna de las librerías nativas en C/C++ de tu proyecto (en este caso, muy probablemente el motor nativo de `expo-sqlite` o `libSQL`) fue compilada con la alineación antigua de 4 KB.

---

### Cómo solucionarlo en Expo

Para solucionar esto de raíz, las librerías nativas y el motor de compilación de Android de tu proyecto deben configurarse para soportar la alineación de 16 KB.

#### Opción 1: Actualizar Gradle (La solución definitiva)

La forma más fácil de heredar el soporte de 16 KB automáticamente es asegurándote de que tu proyecto use una versión moderna de **Android Gradle Plugin (AGP)** y habilitar la opción en tu archivo de configuración.

Si estás usando un flujo de trabajo gestionado con Expo (Managed Workflow), puedes forzar esto en tu `app.json` mediante la propiedad `expo.android`:

1. Abre tu `app.json`.
2. Añade o edita el bloque de configuración de Android para empaquetar las librerías con la alineación correcta:

```json
{
  "expo": {
    "name": "TuApp",
    "slug": "tu-app",
    "android": {
      "packagingOptions": {
        "jniLibs": {
          "useLegacyPackaging": false
        }
      }
    }
  }
}

```

#### Opción 2: Recompilar todo desde cero

Como acabas de hacer un `prebuild`, limpia por completo los archivos nativos generados previamente para que Android Studio aplique las reglas de alineación correctas desde cero:

```bash
# Elimina las carpetas nativas anteriores para evitar residuos
rm -rf android ios

# Vuelve a generar la carpeta nativa limpia
npx expo prebuild

# Compila de nuevo en el emulador
npx expo run:android

```

> **Nota de tranquilidad:** En el emulador puedes simplemente presionar "Aceptar" o ignorar el cartel. El propio mensaje te dice que la app se ejecutará en un modo de compatibilidad (*"page size compatible mode"*), por lo que puedes seguir programando tu base de datos sin problemas.

Si dejas pasar esta advertencia y subes la aplicación a producción tal como está, los efectos negativos se dividirán en dos categorías: **lo que experimentará el usuario** y **el riesgo de que tu app deje de funcionar por completo** en el futuro cercano.

Aquí tienes los efectos negativos reales en producción:

### 1. Caídas inmediatas (Crash) en dispositivos de 16 KB

El emulador te está salvando la vida ahora mismo porque tiene un "modo de compatibilidad" (*page size compatible mode*). Sin embargo, **los teléfonos físicos modernos de gama alta que ya vienen configurados con páginas de 16 KB de forma nativa no tienen este modo**.

* **El efecto:** Si un usuario con un teléfono moderno (como un Pixel reciente o un Galaxy de última generación configurado a 16 KB) descarga tu app, **la app se cerrará inmediatamente (crash) al abrirse** o fallará en el instante exacto en que intentes usar la base de datos SQLite. El sistema operativo se niega a cargar el binario mal alineado.

### 2. Rechazo o advertencias en Google Play Store

Google ha comenzado a escanear de forma estricta los archivos `.apk` y `.aab` (Android App Bundle) que se suben a la consola de desarrolladores.

* **El efecto:** Al intentar subir tu actualización, Google Play Console podría **rechazar el archivo** o mostrarte un error de validación bloqueando el lanzamiento hasta que alinees correctamente los binarios ELF a 16 KB.

### 3. Degradación del rendimiento (Si se usa el modo de compatibilidad)

En los pocos dispositivos que admitan el modo de compatibilidad que ves en el emulador:

* **Mayor consumo de batería:** El sistema operativo tiene que hacer un trabajo extra de traducción en la memoria para que tu app de 4 KB funcione en un entorno de 16 KB.
* **Menor rendimiento general:** Notarás que la base de datos responde más lento y que la app consume más memoria RAM de la que realmente necesita.

---

### ¿Qué debes hacer para que no afecte a producción?

La buena noticia es que **no tienes que cambiar nada de tu código de React Native**. Solo debes asegurarte de que cuando exportes tu app para producción (usando EAS Build o `npx expo run:android --variant release`), el compilador de Android haga bien su trabajo.

Asegúrate de:

1. Tener actualizado Expo a su versión más reciente posible (las versiones de Expo lanzadas a partir de finales de 2024 y durante 2025/2026 ya manejan esto de forma nativa).
2. Si usas **EAS Build**, el entorno en la nube de Expo ya utiliza las herramientas de Android actualizadas que corrigen este problema automáticamente al generar el `.aab` final.


La solución definitiva para producción:
No te quiebres la cabeza peleando con el emulador local. Cuando vayas a generar tu aplicación para producción, utiliza EAS Build (eas build --platform android).

Los servidores en la nube de Expo están actualizados con las últimas herramientas de Google (Android Gradle Plugin 8.5+) y se encargan de parchear y alinear los archivos .aab (Android App Bundles) a 16 KB automáticamente, eliminando este problema por completo para tus usuarios finales.

Si vas a usar EAS Build para producción, puedes estar tranquilo: el entorno de Expo en la nube se encargará de que el archivo final (.aab) cumpla con todas las reglas de Google y de los dispositivos modernos.

Ahora, respecto a tu duda sobre la Internal Preview (Previsualización Interna) en un dispositivo real, aquí te detallo exactamente qué va a pasar y en qué afecta:

¿Qué afectará en la Preview Interna?
El impacto dependerá exclusivamente de qué tan nuevo sea el teléfono físico donde vayas a instalar esa preview:

Si el teléfono de prueba tiene Android 14 o inferior (o es un gama media/baja reciente):

Efecto: Ninguno. La app se instalará y funcionará al 100% de manera normal. No verás advertencias ni bloqueos. Esto se debe a que el 99% de los teléfonos actuales todavía operan internamente con páginas de 4 KB.

Si el teléfono de prueba es un gama alta súper moderno configurado a 16 KB (por ejemplo, un Pixel 8/9 o similar con Android 15+ configurado para desarrollo en 16 KB):

El cartel de advertencia: Al abrir la app instalada vía EAS Preview, te saldrá el mismo cartel flotante que ves en el emulador.

Rendimiento: La app funcionará porque se activará el "modo de compatibilidad", pero podría consumir un poco más de batería de lo normal durante las pruebas.

¿Por qué la Preview local/EAS puede mostrar el cartel pero Producción no?
Hay una diferencia clave en cómo compila EAS según el objetivo:

EAS Build para Preview (--profile preview): Suele generar un archivo .apk. Los archivos APK a veces arrastran librerías nativas sin comprimir o con alineaciones heredadas para que la instalación sea más rápida y directa en el dispositivo de prueba.

EAS Build para Producción (--profile production): Genera un archivo .aab (Android App Bundle). Al subirlo, Google Play desarma ese paquete y optimiza de forma ultra estricta cada librería nativa, aplicando la alineación de 16 KB de manera perfecta para cada teléfono que lo descargue.

Resumen: ¿Qué debes hacer?
Puedes dejar el código y la configuración tal como están. No necesitas romperte la cabeza modificando nada más. Para tu tranquilidad, cuando envíes la build de producción a través de EAS, el ecosistema de Expo y Google Play se encargarán del trabajo sucio. Puedes ignorar el aviso en el emulador y en tus dispositivos de prueba internos; tu base de datos SQLite funcionará perfectamente.

---
