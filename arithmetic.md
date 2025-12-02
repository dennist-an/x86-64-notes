# Arithmetic Instructions

## ADD and SUB

### Purpose
Adds or subtracts values as expected.

### Operand Rules
- **Destination operand**: Can be r/mX or register
- **Source operand**: Can be r/mX, register, or immediate
- **Restriction**: No source and destination as r/mXs (would allow memory-to-memory transfer, which isn't allowed on x86)

## IMUL - Signed Multiply

- **Three forms**. One, two, or three operands:
  - imux r/mX (rdx:rax = rax * r/mX)
  - imul reg, r/mX (reg = reg * r/mX)
  - imul reg ,r/mX, immediate (reg = r/mX * immediate)


**Group 1 - Single Operand**:

IMUL r/m8: AX = AL * r/m8

```
Assembly Instruction Example:
imul r12b

Assuming:

r12 = 0x84
rax = 0x609966C1A977E177

Step 1: Identify the operands
The imul r12b instruction is a single-operand form that:

Multiplies AL (lowest 8 bits of RAX) by r12b (lowest 8 bits of R12)
Stores the 16-bit result in AX

Operands:
AL = lowest 8 bits of rax = 0x77
r12b = lowest 8 bits of r12 = 0x84

Step 2:
Identify if the operands are negative values
In 8-bit signed integers, the most significant bit (MSB) is the sign bit:
- If MSB = 0, the number is positive
- If MSB = 1, the number is negative

AL = 0x77 in binary:
0111 0111
^
└─ MSB = 0, so AL is positive

r12b = 0x84 in binary:
1000 0100
^
└─ MSB = 1, so r12b is negative

Step 3: Calculate the signed multiplication
Convert 0x84 to two's complement to find its negative.
Multiply the value: 119 × 124 = 14,756 decimal = 0x39A4 in hexadecimal.

Apply two's complement to make the result negative (positive × negative = negative). Invert bits of 0x39A4: 0xC65B, add 1: 0xC65C.

Step 4: Store result in AX
Since we're storing the result in a 16-bit register (AX), the result is already 16 bits: 0xC65C.
Final Result: AX = 0xC65C (which is -14,756 in decimal)

The full RAX becomes: 0x609966C1A977C65C (only the lower 16 bits are modified)
```
**Screen Recording Example:**

![alt text](imul-r12b.gif)

IMUL r/m16: DX:AX = AX * r/m16

IMUL r/m32: EDX:EAX = EAX * r/m32

IMUL r/m64 = RDX:RAX = RAX * r/m64

**Group 2 - Two Operand:**

Warning: *Truncation*

IMUL r16, r/m16: r16 = r16 * r/m16

IMUL r32, r/m32: r32 = r32 * r/m32

```
Assembly Instruction Example:
imul r12d, eax

Assuming:
r12 = 0x84
rax = 0x609966C1A977E177

Step 1:
The 'd' suffix in r12d means we're using the lower 32-bit portions:
- r12d = lower 32 bits of r12 = 0x00000084
- eax = lower 32 bits of rax = 0xA977E177

Step 2:
Identify if RAX is a negative value.
In 32-bit signed integers, the most significant bit (MSB) is the sign bit
- If MSB = 0, the number is positive
- If MSB = 1, the number is negative

0xa977e177 in binary:
1010 1001 0111 0111 1110 0001 0111 0111
^
└─ MSB = 1, so this is negative

Step 3:
Take A977E177 and convert it to two's complement to find its negative magnitude. This gives us 568E1E89.
Multiply 568E1E89 by 84, which results in A89E2FBEA4.
Apply two's complement again to make the result negative (since we're multiplying a negative by a positive). This gives us 5761D0415C.
Since we're storing the result in a 32-bit register, perform a bitwise AND operation between 5761D0415C and FFFFFFFF to keep only the lower 32 bits. This gives us the final result: 61D0415C.
```
**Screen Recording Example:**

![alt text](imul-r12d-eax.gif)

IMUL r64, r/m64 = r64 = r64 * r/m64

**Group 3 - Three Operands, 8 Bit Immediate:**

Warning: *Truncation*

IMUL r16, r/m16, imm8: r16 = r/m16 * single-extended imm8

IMUL r32, r/m32, imm8: r32 = r/m32 * single-extended imm8

IMUL r64, r/m64, imm8: r64 = r/m64 * single-extended imm8

**Group 4 - Three Operands, 16 Bit Immediate:**

Warning: *Truncation*

IMUL r16, r/m16, imm16: r16 = r/m16 * imm16

**Group 5 - Three Operands, 32 Bit Immediate:**

Warning: *Truncation*

IMUL r32, r/m32, imm23 = r32 = r/m32 * imm32

IMUL r64, r/m64, imm32 = r64 = r/m64 * sign-extended imm32