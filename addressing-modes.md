# Addressing Modes

## r/mX Addressing Examples

### Immediate to Memory
```asm
mov [rbx], imm32
mov [rbx+rcx*X], imm32
mov [rbx+rcx*X+Y], imm32
```

### Immediate to Register
```asm
mov rbx, imm64
```

### Register to Register
```asm
mov rbx, rax
```

### Register to Memory
```asm
mov [rbx], rax
mov [rbx+rcx*X], rax
mov [rbx+rcx*X+Y], rax
```

### Memory to Register
```asm
mov rax, [rbx]
mov rax, [rbx+rcx*X]
mov rax, [rbx+rcx*X+Y]
```

## Addressing Mode Notation

- **r/mX**: Register or memory operand
- **X**: Scale factor (1, 2, 4, or 8)
- **Y**: Displacement (offset)
- **imm32/imm64**: Immediate values (32-bit or 64-bit)

## Important Note

x86 does not allow direct memory-to-memory transfers.