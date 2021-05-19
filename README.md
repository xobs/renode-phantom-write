# Renode Memory Corruption Test Case

This repository demonstrates an error in Renode where memory is corrupted.

## Behavior

The memory at address `0x40f00000` gets overwritten with garbage data. This causes errors later on.

This occurs at the virtual address `0xffd046b8` using the included `kernel` ELF file.

## Reproducing

Run the following GDB script:

```
file kernel
tar ext :3333
b *0xffd046b8
mon cpu TranslateAddress 0xffd046b8 0
# Note the returned value is 0x0000000040F6B6B8
b *0x0000000040F6B6B8+2
mon machine Reset
c
mon sysbus ReadDoubleWord 0x40f00000
# Note value is 0x103C04C7
disassemble $pc,+4
# Note the next instruction is `lw a1,0(a0)`
stepi
mon sysbus ReadDoubleWord 0x40f00000
# Note value is now 0x00000000
```

## Workarounds

Main memory is currently added in `soc.repl`. If main memory is added instead with `machine LoadPlatformDescriptionFromString`, then the problem does not occur.
