# Assembly Instructions

## MOVZX/MOVSX Examples

Starting with the value in `eax`:
```assembly
mov eax, 0xF00DFACE
```

### Zero Extension (MOVZX)
```assembly
movzx rbx, eax
```
Result: `rbx = 0x00000000F00DFACE`

The upper 32 bits are filled with zeros.

### Sign Extension (MOVSXD)
```assembly
movsxd rbx, eax
```
Result: `rbx = 0xFFFFFFFFF00DFACE`

The upper 32 bits are filled with ones because the sign bit (most significant bit) of `0xF00DFACE` is 1, indicating a negative number in two's complement representation.

---

**Key Difference**: MOVZX performs zero-extension while MOVSXD performs sign-extension, preserving the sign of the source operand.