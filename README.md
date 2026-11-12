# GCC 2.7.2 for MIPS (KMC-compatible): native on Apple Silicon

GCC 2.7.2 tailored to reproduce the output of KMC's GCC, the compiler many N64 games were built with. Decompilation
projects use it to match those games' code byte for byte. Pair it with binutils 2.6 for the assembler, for example
[ianderse/mips-binutils-2.6](https://github.com/ianderse/mips-binutils-2.6).

This is a fork of [decompals/mips-gcc-2.7.2](https://github.com/decompals/mips-gcc-2.7.2). Upstream's last release
(v0.1) has an Intel-only macOS build. Its workflow gained an Apple Silicon job afterwards but never released it. This
fork releases the **Apple Silicon** build. The sources are upstream's v0.1, unchanged; only the build workflow
differs.

## Download

[Releases](https://github.com/ianderse/mips-gcc-2.7.2/releases):

| Archive | For |
|---|---|
| `gcc-2.7.2-mac-arm64.tar.gz` | macOS on Apple Silicon |
| `gcc-2.7.2-linux.tar.gz` | Linux x86 (static, 32-bit) |

Intel Macs aren't built for.

## Same code as upstream's build

Checked before the first release against upstream's v0.1 macOS (Intel) build, at `-O2 -G0 -mips3 -mgp32 -mfp32`:

- **Assembly:** all 4,040 C files of a game's exported decompilation give identical assembly.
- **Objects:** compiled all the way to object files through binutils 2.6, they are identical too (400 checked).
- **Float and double constants:** the right words in both builds (`0x3ff80000 00000000` for 1.5, `0x40400000` for
  3.0f). Every CI run checks those.

One known difference is cosmetic. Some of the compiler's warning texts and assembly comments come out garbled on
arm64, because variadic arguments are passed differently there. Examples: `(null) makes pointer from integer` and
`# 8.09e-320` where the Intel build says `# 1.5`. The generated code is unaffected.

## Building

```sh
./configure --target=mips-mips-gnu --prefix=/opt/cross --with-gnu-as --disable-gprof --disable-gdb \
  --disable-werror --host=arm-apple-darwin --build=arm-apple-darwin
touch c-parse.c   # it's committed; regenerating it with modern tools fails
make CFLAGS='-DDARWIN -std=gnu89 -Wno-return-type -Wno-error -Wno-implicit-function-declaration -Wno-int-conversion' \
  xgcc cc1 cc1plus cpp cccp g++
```

## License

GPL-2.0, as upstream (`COPYING`). GCC's own README is in `README`.
