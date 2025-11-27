# Control Flow Instructions

## CALL Instruction

### Purpose
Transfer control to a different function in a way that control can later be resumed where it left off.

### Behavior
1. Pushes the address of the next instruction onto the stack (for use by RET when the procedure is done)
2. Changes RIP to the address given in the instruction

## RET Instruction

### Two Forms

#### Form 1: Simple Return
```asm
ret
```
Pops the top of the stack into RIP (remember, pop implicitly increments stack pointer, RSP)

#### Form 2: Return with Stack Adjustment
```asm
ret 0x8
ret 0x20
```
Pops the top of the stack into RIP and also adds a constant number of bytes to RSP