# Understanding Malbolge

## History

Malbolge was created by Ben Olmstead in 1998. Named after the 8th circle of Hell in Dante's Inferno, it was explicitly designed to be as difficult as possible to program in.

**Key dates:**
- 1998: Language created
- 2000: First "Hello World" program written (took 2 years!)
- 2007: Lou Scheffer creates tools to generate Malbolge programs
- 2013: Malbolge Unshackled variant created

## The Virtual Machine

Malbolge uses a **ternary** (base-3) virtual machine:

```
Memory:  59049 words (3^10)
Word:    0 to 59048 (10 trits)
Trit:    0, 1, or 2 (ternary digit)
```

### Registers
| Register | Name | Purpose |
|----------|------|---------|
| A | Accumulator | Stores operation results |
| C | Code pointer | Current instruction address |
| D | Data pointer | Data operand address |

## Why It's "Impossible"

### 1. Encrypted Instructions

The instruction at address `c` is calculated as:
```
opcode = (memory[c] - 33 + c) % 94
```

This means the **same byte at different positions executes differently**.

### 2. Self-Modifying Code

After each instruction executes, `memory[c]` is modified using a permutation table. The code literally rewrites itself.

### 3. The Crazy Operation

The `crazy(a, b)` operation performs a tritwise operation that defies intuition:

```
crazy(a, b):
    for each trit position:
        result[i] = lookup[a[i]][b[i]]
```

The lookup table:
```
    b=0  b=1  b=2
a=0  1    0    0
a=1  1    0    2
a=2  2    2    1
```

This is **not** AND, OR, XOR, or any familiar boolean operation.

### 4. Limited Instructions

Only 8 valid opcodes:
| Opcode | Char | Effect |
|--------|------|--------|
| j | d = [d] | Set data pointer |
| i | c = [d] | Jump |
| * | a = [d] = rotate([d]) | Rotate right |
| p | a = [d] = crazy(a, [d]) | The crazy operation |
| < | output(a mod 256) | Print character |
| / | a = input() | Read character |
| v | halt | Stop |
| o | nop | Do nothing |

Everything else is a no-op, but **still causes self-modification**.

## The Programming Challenge

To print "Hello World" requires:
1. Setting up initial memory so that self-modification creates useful code
2. Pre-computing what each position will become after modification
3. Carefully choosing bytes that decrypt to the right opcodes at the right time
4. Making the crazy operation produce useful values

This is why it took 2 years for the first Hello World.

## Safety Concerns

Without verification, a Malbolge interpreter can:
- Access memory out of bounds
- Loop forever with no termination
- Produce unpredictable results from arithmetic overflow
- Crash on invalid instruction decryption

**This is where Proven comes in.**

## Next: [[The Crazy Operation]]
