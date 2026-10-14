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
