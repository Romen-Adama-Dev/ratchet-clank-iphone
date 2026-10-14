# ratchet-clank-iphone

Ratchet & Clank (PS2, 2002) nativo en iPhone, sin emulador. Está basado en
[ReRAC](https://github.com/re-rac/rerac), una reescritura fan-made del juego en Rust/Bevy.
No incluye nada del juego: usa **tu propia ISO** (NTSC-U, `SCUS-97199`, v1.00).

| Carpeta | Qué contiene |
| --- | --- |
| `rerac/` | ReRAC upstream (commit `92bf5eb`, licencia ISC) con el soporte de iOS añadido |
| `ios/` | `Info.plist`, icono, `build.sh` (iPhone / simulador) y `run-simulator.sh` |
| `.github/workflows/ios.yml` | Compila en macOS el `.ipa` y la app del simulador (artefactos de cada ejecución) |
| `docs/REA-viabilidad.md` | Estudio inicial (REA y alternativas) |

## Qué añade el port de iOS

- **Arranque en iOS** (`rerac/crates/rc-engine/src/ios.rs`): pantalla completa, sin barra de estado.
  La primera vez busca una `.iso` en la carpeta de la app (app Archivos › En mi iPhone › ReRAC),
  la extrae y verifica dentro del iPhone, y pide cerrar y volver a abrir la app.
- **Controles táctiles** (`touch_pad.rs`): stick izquierdo flotante (mitad izquierda), cámara
  (arrastrar en la mitad derecha), ✕ ○ □ △, L1/L2/R1/R2, cruceta, SELECT y START.
- **Mandos Bluetooth** (`gc_pad.rs`): DualSense, DualShock 4, Xbox y MFi mediante el framework
  GameController. Los controles táctiles se ocultan al conectar uno.
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

**Opción A, con Xcode (cuenta de Apple gratuita):**

```bash
ios/build.sh device                      # crea out/ios/ReRAC.app y out/ios/ReRAC.ipa (sin firmar)
```

Después firma e instala el `.ipa` con [Sideloadly](https://sideloadly.io) o AltStore usando tu Apple ID
(con una cuenta gratuita, la firma caduca a los 7 días). El `.ipa` también se puede descargar como
artefacto `ReRAC-ipa` de cada ejecución del workflow **iOS**.

Primer arranque en el iPhone:
1. Abre ReRAC una vez (crea su carpeta) y ciérrala.
2. Copia tu `.iso` a *Archivos › En mi iPhone › ReRAC* (o desde el Finder del Mac › tu iPhone › Archivos).
3. Abre ReRAC: extrae el disco (unos minutos). Cuando diga «¡Listo!», ciérrala y vuelve a abrirla.
4. Después puedes borrar la ISO para liberar espacio. Mientras se extrae, deja libre al menos el doble
   del tamaño de la ISO (la app avisa si no hay espacio suficiente).

## Estado

El juego en sí es el de ReRAC: según su web (5 de octubre de 2026) todos los planetas están implementados,
y Veldin, Novalis, Aridia y Kerwan están completos. El port de iOS compila para iPhone y para el simulador;
el rendimiento y los controles en un iPhone real están **sin probar**.

ReRAC es un proyecto no oficial, sin relación con Sony Interactive Entertainment ni Insomniac Games.
