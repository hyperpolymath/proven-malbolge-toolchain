# The Proven Methodology

How to make ANY toolchain safe using dependent types. A systematic approach.

## The Five Steps

### Step 1: Identify the Chaos

For any toolchain, identify:
- What can go out of bounds?
- What can overflow?
- What inputs are invalid?
- What states should be unreachable?
- What operations can fail?

**For Malbolge:**
| Chaos | Description |
|-------|-------------|
| Memory | 59049 words, any access could be out of bounds |
| Arithmetic | Ternary values 0-59048, can overflow |
| Instructions | 8 valid opcodes, many invalid |
| Execution | Can loop forever |
| Self-modification | Code changes unpredictably |

### Step 2: Define the Domain Types

Create types that **exactly represent** valid states:

```idris
-- For Malbolge:
data Trit = T0 | T1 | T2                    -- Exactly 3 values
Tryte : Type
Tryte = Fin 59049                           -- Exactly 59049 values
Address : Type
Address = Fin 59049                         -- Same range for addresses
data Op = J | I | Star | P | Lt | Slash | V | O  -- Exactly 8 opcodes
```

**Key principle:** Invalid states should be unrepresentable.

### Step 3: Define Safe Operations

For each operation, make its safety properties part of the type signature:

```idris
-- Safe memory read: address must be valid, always returns a value
readMem : (addr : Address) -> Memory -> Tryte

-- Safe crazy operation: always produces valid output
crazy : Trit -> Trit -> Trit

-- Safe instruction decode: may fail, explicitly
decode : Char -> Position -> Maybe Op
```

Notice:
- `readMem` doesn't return `Maybe` because `Address` guarantees validity
- `decode` returns `Maybe` because input might not be a valid instruction

### Step 4: Prove Key Properties

Write proofs for critical properties:

```idris
-- Memory read always returns a value in the valid range
readMemInRange : (addr : Address) -> (mem : Memory) ->
                 InRange 0 59048 (value (readMem addr mem))

-- Crazy operation is total
crazyTotal : (a, b : Trit) -> crazy a b `Elem` [T0, T1, T2]

-- Arithmetic doesn't overflow
addTryteNoOverflow : (a, b : Tryte) ->
                     value (addTryte a b) <= 59048
```

### Step 5: Compose Verified Components

Build larger systems from verified pieces:

```idris
-- VM step: composed of verified operations
step : VMState -> Either Error VMState
step state = do
    instr <- decode (readMem state.c state.mem) state.c
    case instr of
        J => pure $ state { d = readMem state.d state.mem }
        I => pure $ state { c = readMem state.d state.mem }
        Star => let r = rotate (readMem state.d state.mem)
                in pure $ state { a = r, mem = writeMem state.d r }
        -- ...
```

Each piece is verified, so the composition is verified.

## Applying to Your Toolchain

### Example: JSON Parser

1. **Identify chaos:** Malformed input, nesting too deep, numbers too large
2. **Domain types:** `ValidJson`, `Depth : Fin MaxDepth`, `JsonNumber : Bounded Int`
3. **Safe operations:** `parse : String -> Either ParseError ValidJson`
4. **Prove:** Parser terminates, output is well-formed
5. **Compose:** Build document transformations from verified parsing

### Example: Compiler

1. **Identify chaos:** Invalid syntax, type errors, undefined variables, infinite loops
2. **Domain types:** `WellTypedAST`, `ScopedExpr`, `TypedValue`
3. **Safe operations:** `typecheck : AST -> Either TypeError WellTypedAST`
4. **Prove:** Type preservation, progress, termination (for total sublanguage)
5. **Compose:** Verified compilation pipeline

### Example: Network Protocol

1. **Identify chaos:** Invalid packets, buffer overflow, timeouts
2. **Domain types:** `ValidPacket`, `BoundedBuffer n`, `Timeout`
3. **Safe operations:** `parse : Bytes -> Either ProtocolError ValidPacket`
4. **Prove:** Parser is constant-time, buffers never overflow
5. **Compose:** Verified protocol handler

## The Malbolge Lesson

If this methodology can tame Malbolge - a language **designed** to be impossible - it can handle your toolchain.

The key insight: **chaos is just unexpressed structure**. Dependent types let us express and enforce that structure.

## Common Patterns

### The Result Pattern
```idris
data Result e a = Err e | Ok a
```
Every operation that can fail uses `Result`. No exceptions.

### The Indexed Type Pattern
```idris
data Vect : Nat -> Type -> Type
data Fin : Nat -> Type
data Bounded : (lo : a) -> (hi : a) -> Type
```
Types carry their constraints.

### The Refinement Pattern
```idris
data Positive : Type where
  MkPositive : (n : Nat) -> {auto prf : GT n 0} -> Positive
```
Wrap values with their proofs.

## Next: [[Case Study: Malbolge Interpreter]]
