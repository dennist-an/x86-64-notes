# x64 Stack Alignment and Function Prologs

## Overview
Understanding how RSP (stack pointer) changes through function calls and why specific stack allocation sizes (0x18h, 0x28h, 0x38h) are used.

## Question - why does the stack shifts in 18h, 38h, and 28h?
```assembly
func3():
0000000140001000  sub         rsp,18h  
0000000140001004  mov         dword ptr [rsp],7A11h  
000000014000100B  mov         eax,dword ptr [rsp]  
000000014000100E  add         rsp,18h  
0000000140001012  ret

func2():
0000000140001020  sub         rsp,38h  
0000000140001024  mov         dword ptr [j],7A1Eh  
000000014000102C  call        func3 (0140001000h)  
0000000140001031  add         rsp,38h  
0000000140001035  ret 

func():
0000000140001040  sub         rsp,28h  
0000000140001044  call        func2 (0140001020h)  
0000000140001049  add         rsp,28h  
000000014000104D  ret

main():
0000000140001060  sub         rsp,28h  
0000000140001064  call        func (0140001040h)  
0000000140001069  add         rsp,28h  
000000014000106D  ret
```

## Initial Conditions
Assuming the following scenario of a program.
- **Starting RSP at main() entry**: `0x000000000014FE08`
- **Stack grows downward**: Lower memory addresses

---

## Stack Allocation Sizes Summary

| Function | Allocation | Bytes | Reason |
|----------|------------|-------|--------|
| main() | 0x28 | 40 | Shadow space (32) + alignment (8) |
| func() | 0x28 | 40 | Shadow space (32) + alignment (8) |
| func2() | 0x38 | 56 | Shadow space (32) + locals (16) + alignment (8) |
| func3() | 0x18 | 24 | Locals (16) + alignment (8) - no shadow space needed |

---

## 16-Byte Alignment Rule

### The Rule
**The stack pointer (RSP) must be aligned to a 16-byte boundary (divisible by 16) before most instructions, especially before `call` instructions.**

- RSP should end in `0x...0` (aligned)
- Example: `0x14FDE0` ✓ aligned, `0x14FDD8` ✗ misaligned

### Why 16-byte Alignment?
- Modern CPU instructions (SSE/AVX) require or perform better with 16-byte aligned memory
- x64 Windows ABI requirement
---

## Key Concepts

### Shadow Space
- **x64 Windows calling convention** requires 32 bytes reserved for the first 4 register parameters
- Required even if parameters aren't used
- Functions that call others need shadow space

### Leaf Functions
- Functions that **don't call other functions** (like func3)
- Don't need shadow space (only local storage + alignment)
- Can use smaller stack allocations (0x18 minimum)

### The Pattern
Every function follows this pattern:
1. **Before `call`**: RSP is 16-byte aligned
2. **After `call`**: RSP is misaligned by 8 bytes (exception allowed)
3. **Prolog (`sub rsp`)**: Restores 16-byte alignment using odd multiple of 8
4. **Function body**: RSP stays aligned
5. **Epilog (`add rsp`)**: Restores RSP before returning

---

## Quick Reference

### Alignment Check
- **Aligned**: RSP ends in `0` (e.g., 0x...FE00, 0x...FD70)
- **Misaligned**: RSP ends in `8` (e.g., 0x...FDD8, 0x...FDA8)

### Common Stack Allocations
- **0x18 (24)**: Minimum for leaf functions
- **0x28 (40)**: Standard with shadow space
- **0x38 (56)**: Shadow space + extra locals
- **0x48 (72)**: Shadow space + more locals

### Formula
`Stack allocation = Shadow space (32 if needed) + Local variables + Alignment adjustment`

Where alignment adjustment ensures the total is an **odd multiple of 8**.