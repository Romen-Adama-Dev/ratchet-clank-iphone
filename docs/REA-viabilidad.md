# REA y un port nativo de Ratchet & Clank

## Qué se instaló

`.mcp.json` registra el servidor MCP de [REA](https://github.com/morluto/rea)
(`rea-agents@6.5.0`). Al abrir Claude Code en este repo te pedirá aprobar el
servidor `rea`. También se puede usar desde la terminal:

```bash
npx -y rea-agents@6.5.0 --help
```

Para análisis nativo REA necesita Ghidra, Hopper o IDA instalado en tu máquina
(ver `docs/installation.md` del repo de REA).

## ¿Sirve REA para generar un ejecutable nativo?

**No por sí solo.** REA es una herramienta de *análisis*: devuelve pseudocódigo,
ensamblador, símbolos y referencias para que un agente o una persona entienda
cómo funciona un binario. No recompila, no traduce y no empaqueta nada en un
ejecutable.

Además, con el ejecutable de PS2 hay límites concretos:

- Ratchet & Clank (2002) es un ELF MIPS **R5900** (Emotion Engine). El carril
  MIPS de REA con Ghidra solo admite MIPS32/MIPS32R2 o32 estándar y rechaza
  explícitamente variantes con flags específicos de máquina, como el R5900
  (`docs/ghidra-mips.md`). Como mucho daría un análisis parcial con
  objdump/Rizin.
- Gran parte del juego no está en código MIPS normal: microcódigo de las VU0/VU1,
  instrucciones MMI de 128 bits, el sintetizador gráfico (GS), el IOP y los
  formatos propios de Insomniac. REA no cubre nada de eso.
- El `DISCLAIMER.md` de REA excluye copiar software propietario o reproducir sus
  assets. Un port tiene que funcionar con **tu propia copia** del juego y no
  puede distribuir código ni assets de Insomniac/Sony.

## Caminos realistas

| Objetivo | Opción | Estado |
| --- | --- | --- |
| Jugar en Mac/Linux | Emulador **PCSX2** (nativo en macOS Apple Silicon y Linux) con tu ISO | Funciona hoy |
| Jugar en iPhone | Emulador **Play!** (iOS); en iOS hace falta JIT (AltStore/SideStore + StikDebug o similar) para que vaya fluido | Posible, con un rendimiento que varía según el juego |
| Ejecutable "nativo" de verdad | Recompilación estática (p. ej. proyectos tipo *PS2Recomp*) o una decompilación completa que coincida byte a byte | Proyecto de años; experimental |

Una decompilación nativa (como las de Mario 64 o Zelda OoT) es donde REA sí
ayuda: sirve para entender funciones concretas y reescribirlas en C.
Pero eso es un proyecto de muchos meses o años de trabajo humano, no un paso
automático.

## Actualización: ya existe un port nativo en curso

Hay proyectos de la comunidad que ya hacen exactamente esto, sin incluir assets
(cada uno usa su propia ISO):

| Proyecto | Qué es | Versión del disco |
| --- | --- | --- |
| [ReRAC](https://github.com/re-rac/rerac) | Reescritura nativa en Rust + Bevy, sin emulador. Todos los niveles cargan y se renderizan; aún no se puede jugar de principio a fin. Licencia ISC. | NTSC-U `SCUS-97199` v1.00 |
| [OpenRAC/rac1-decomp](https://github.com/OpenRAC/rac1-decomp) | Decompilación *matching* (C/C++ que compila byte a byte al original); paso previo a un port. GPL-3.0. | PAL `SCES_509.16` v2.00 |
| [Lombyte](https://github.com/mateuszklysz/Lombyte) | Decompilación *matching*; port nativo en Rust a largo plazo. | NTSC-U `SCUS_971.99` |
| [PS2Recomp](https://github.com/ran-j/PS2Recomp) | Recompilador estático genérico de PS2. Experimental, sin juegos jugables. | — |

### Pruebas hechas con ReRAC (2026-10-14)

- `cargo check --workspace` en Linux x86_64: **compila** (con `libwayland-dev`,
  `libasound2-dev`, `libudev-dev`, `libxkbcommon-dev`).
- `cargo check --target aarch64-apple-ios`: se para en el build script de
  `blake3` porque necesita `xcrun` (Xcode). Hace falta un Mac para seguir.
- Bevy (el motor que usa) soporta iOS oficialmente, así que el camino a iPhone es
  añadir un target iOS a ReRAC: proyecto Xcode, controles táctiles/mando,
  rutas de guardado y de datos en el sandbox de iOS (`saves.rs` y
  `render_settings.rs` solo distinguen macOS) y cargar los datos ya extraídos
  de la ISO en el dispositivo.

### Plan propuesto

1. Extraer los datos con tu ISO en un Mac o PC:
   `cargo xtask regen-data --iso <tu.iso>` (ReRAC solo admite NTSC-U v1.00).
2. Hacer funcionar ReRAC en macOS (es la plataforma que ya prueban) y en Linux.
3. Añadir el target iOS (fork de ReRAC): proyecto Xcode, entrada táctil y
   soporte de mandos MFi/Bluetooth, y empaquetar los datos extraídos.
4. Contribuir los cambios a ReRAC, porque la parte jugable sigue avanzando allí.
