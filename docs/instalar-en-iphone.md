# Instalar ReRAC en tu iPhone

Guía paso a paso con un Mac, un cable y un Apple ID **gratuito** (no hace falta pagar la cuenta de
desarrollador). Probada el 14 de octubre de 2026 con un iPhone 16 Pro Max con iOS 26.7.1, un MacBook Pro M3
con macOS 14.5 y Sideloadly 0.70.1.

Necesitas:

- Tu propia ISO de Ratchet & Clank (NTSC-U, `SCUS-97199`, v1.00), de unos 4,2 GB.
- Espacio libre en el iPhone: la ISO más al menos el doble de su tamaño mientras se extrae (unos 13 GB en total;
  después puedes borrar la ISO).
- [Sideloadly](https://sideloadly.io) instalado en el Mac.

## 1. Consigue el `.ipa`

- **Sin compilar:** en la pestaña **Actions** del repositorio, abre la última ejecución del workflow **iOS**,
  baja hasta **Artifacts**, descarga **ReRAC-ipa** y descomprímelo. Dentro está `ReRAC.ipa`.
- **Compilándolo tú** (Xcode y Rust instalados):

  ```bash
  ios/build.sh device
  ```

  Crea `out/ios/ReRAC.ipa` (sin firmar: Sideloadly lo firma con tu Apple ID).

## 2. Conecta el iPhone al Mac

Conéctalo por cable, desbloquéalo y pulsa **Confiar** en el aviso «¿Confiar en este ordenador?» (te pedirá el
código del iPhone).

> Con un macOS o un Xcode más antiguos que el iOS del iPhone (por ejemplo macOS 14 con Xcode 16 y un iPhone
> con iOS 26), Xcode puede no llegar a ver el iPhone. No importa: Sideloadly sí lo ve.

## 3. Instala la app con Sideloadly

1. Abre Sideloadly y **arrastra `ReRAC.ipa` desde el Finder a su ventana**.
   *No uses el botón del icono IPA:* su selector de archivos puede colgar Sideloadly (hay que forzar la
   salida) cuando el Escritorio, Documentos o Descargas tardan en responder, por ejemplo con iCloud Drive.
2. En **iDevice**, elige tu iPhone; en **Apple account**, escribe tu Apple ID.
3. Abre **Advanced options** y pon **Anisette Authentication** en **Remote**.
   Con el modo local, el inicio de sesión puede fallar con
   `Login failed (-22406): Enter the correct password for this Apple Account` aunque la contraseña sea correcta.
4. Pulsa **Start**, escribe la contraseña (y el código de verificación en dos pasos si te lo pide) y espera a
   que el registro de abajo diga **Done.**

En iOS 26, Sideloadly cambia el identificador de la app (por ejemplo `com.rerac.ios.ABCDE12345`). Es normal.

## 4. Permite que se abra en el iPhone

1. **Modo de desarrollador:** *Ajustes › Privacidad y seguridad › Modo de desarrollador*, actívalo y deja que
   el iPhone se reinicie. (La opción aparece después de instalar la primera app con Sideloadly.)
2. **Confía en tu Apple ID:** *Ajustes › General › VPN y gestión de dispositivos › App de desarrollador ›
   tu Apple ID › Confiar*. Necesita conexión a internet.

Si al abrir ReRAC iOS dice que se puede desbloquear en Ajustes, te falta el paso 2.

## 5. Copia la ISO y extrae el juego

1. Abre ReRAC una vez y ciérrala: así crea su carpeta (y deja dentro `COPY YOUR ISO HERE.txt`).
2. Copia tu `.iso` a la carpeta de la app, de una de estas formas:
   - **Finder del Mac:** selecciona tu iPhone en la barra lateral › pestaña **Archivos** › arrastra la ISO
     sobre **ReRAC**.
   - **App Archivos del iPhone:** *En mi iPhone › ReRAC*.
   - **Terminal** (con [libimobiledevice](https://libimobiledevice.org), `brew install libimobiledevice ideviceinstaller`):

     ```bash
     ideviceinstaller list --user | grep -i rerac        # el identificador real de la app
     afcclient --container <identificador> put "Ratchet & Clank.iso" /Documents/RatchetClank.iso
     ```

     Usa `--container` y la ruta `/Documents/...`: con `--documents` iOS 26 devuelve *Permission denied*.
3. **No abras ReRAC mientras se copia** y deja el iPhone desbloqueado: si no, la copia se corta a medias
   («Failed to write to device file»). Si se corta, borra el archivo incompleto y vuelve a copiarlo.
4. Abre ReRAC: detecta la ISO y extrae el disco (unos minutos). Cuando diga «¡Listo!», ciérrala y vuelve a
   abrirla.
5. Ya puedes borrar la ISO de la carpeta de la app para recuperar espacio.

## 6. Cada 7 días

Con un Apple ID gratuito la firma caduca a los 7 días y la app deja de abrirse. Conecta el iPhone y repite el
paso 3 con el mismo `.ipa`: se instala encima y conserva el juego extraído, las partidas y los ajustes.

Otros límites de la cuenta gratuita: 3 apps instaladas a la vez y 10 identificadores de app nuevos por semana.
Con la cuenta de desarrollador de pago (99 €/año) la firma dura un año.

## Problemas frecuentes

| Síntoma | Solución |
| --- | --- |
| Sideloadly se cuelga al elegir el `.ipa` | Arrastra el `.ipa` a la ventana en vez de usar el botón IPA |
| `Login failed (-22406)` con la contraseña correcta | *Advanced options › Anisette Authentication › Remote* |
| Sideloadly dice Done pero la app no aparece | Comprueba que en **iDevice** está elegido tu iPhone y que el registro termina en **Done.** sin errores |
| iOS no deja abrir la app | Modo de desarrollador activado y Apple ID de confianza (paso 4) |
| La copia de la ISO se corta | No abras la app durante la copia y deja el iPhone desbloqueado |
| Las cinemáticas no se oyen | Versiones anteriores a este arreglo se silenciaban con el modo silencio: actualiza el `.ipa` o quita el modo silencio |
