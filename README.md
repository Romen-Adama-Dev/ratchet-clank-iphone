# ratchet-clank-iphone

Ratchet & Clank (PS2, 2002) nativo en iPhone, sin emulador. Está basado en
[ReRAC](https://github.com/re-rac/rerac), una reescritura fan-made del juego en Rust/Bevy.
No incluye nada del juego: usa **tu propia ISO** (NTSC-U, `SCUS-97199`, v1.00).

| Carpeta | Qué contiene |
| --- | --- |
| `rerac/` | ReRAC upstream (commit `92bf5eb`, licencia ISC) con el soporte de iOS añadido |
| `ios/` | `Info.plist`, icono, `build.sh` (iPhone / simulador) y `run-simulator.sh` |
| `.github/workflows/ios.yml` | Compila en macOS el `.ipa` y la app del simulador (artefactos de cada ejecución) |
| `docs/instalar-en-iphone.md` | Guía para instalarlo en un iPhone con Sideloadly y un Apple ID gratuito |
| `docs/REA-viabilidad.md` | Estudio inicial (REA y alternativas) |

## Qué añade el port de iOS

- **Arranque en iOS** (`rerac/crates/rc-engine/src/ios.rs`): pantalla completa, sin barra de estado.
  La primera vez busca una `.iso` en la carpeta de la app (app Archivos › En mi iPhone › ReRAC),
  la extrae y verifica dentro del iPhone, y pide cerrar y volver a abrir la app.
- **Controles táctiles** (`touch_pad.rs`): stick izquierdo flotante (mitad izquierda), cámara
  (arrastrar en la mitad derecha), ✕ ○ □ △, L1/L2/R1/R2, cruceta, SELECT y START.
- **Mandos Bluetooth** (`gc_pad.rs`): DualSense, DualShock 4, Xbox y MFi mediante el framework
  GameController. Los controles táctiles se ocultan al conectar uno.
- **Sonido** (`ios.rs`): la sesión de audio usa la categoría *Playback*, así que el juego se oye aunque el
  iPhone esté en modo silencio (como un reproductor de vídeo).
- Partidas guardadas y ajustes en `Library/Application Support/rerac`, igual que en macOS.
- Resolución de render por defecto: 720p (se puede cambiar en Opciones › Port Options).

## Probarlo en el simulador de Xcode (Mac con Apple Silicon)

Requisitos: Xcode con un runtime de iOS instalado y Rust (`curl https://sh.rustup.rs -sSf | sh`).

```bash
git clone https://github.com/Romen-Adama-Dev/ratchet-clank-iphone.git
cd ratchet-clank-iphone
ios/run-simulator.sh /ruta/a/tu/Ratchet.iso   # 1ª vez: compila, instala, copia la ISO y la extrae
ios/run-simulator.sh                            # después: arranca el juego
```

El script abre un simulador **iPhone 16 Pro Max**. Puedes elegir otro con
`DEVICE="iPhone 15" ios/run-simulator.sh`. Para no compilar, descarga el artefacto
`ReRAC-simulator` de GitHub Actions, descomprímelo y usa `APP=/ruta/ReRAC.app ios/run-simulator.sh`.
En el simulador, los clics del ratón cuentan como toques y el teclado del Mac también funciona
(WASD, Espacio = ✕, J/K/L, Q/E, Enter = START).

## Instalarlo en tu iPhone

Con un Mac, un cable y un Apple ID gratuito. **Guía completa, con los errores típicos y cómo
resolverlos: [docs/instalar-en-iphone.md](docs/instalar-en-iphone.md).** En resumen:

1. Consigue `ReRAC.ipa`: descarga el artefacto `ReRAC-ipa` de la última ejecución del workflow **iOS**,
   o compílalo con `ios/build.sh device` (crea `out/ios/ReRAC.ipa`, sin firmar).
2. En [Sideloadly](https://sideloadly.io): **arrastra** el `.ipa` a la ventana (el botón IPA puede colgarlo),
   elige tu iPhone, pon tu Apple ID, *Advanced options › Anisette Authentication › Remote* y pulsa **Start**.
3. En el iPhone: activa *Ajustes › Privacidad y seguridad › Modo de desarrollador* y confía en tu Apple ID en
   *Ajustes › General › VPN y gestión de dispositivos*.
4. Abre ReRAC una vez, copia tu `.iso` a su carpeta (Finder › tu iPhone › Archivos › ReRAC, o la app Archivos)
   **sin abrir la app mientras se copia**, y ábrela: extrae el disco (unos minutos). Deja libre al menos el
   doble del tamaño de la ISO.
5. Con una cuenta gratuita la firma caduca a los 7 días: repite el paso 2 (se conservan el juego y las partidas).

## Estado

El juego en sí es el de ReRAC: según su web (5 de octubre de 2026) todos los planetas están implementados,
y Veldin, Novalis, Aridia y Kerwan están completos. El port de iOS compila para iPhone y para el simulador.
En un iPhone 16 Pro Max con iOS 26.7.1 se instala con Sideloadly, extrae la ISO y reproduce la cinemática
inicial con sonido; el rendimiento y los controles durante el juego están **sin probar** todavía.

ReRAC es un proyecto no oficial, sin relación con Sony Interactive Entertainment ni Insomniac Games.
