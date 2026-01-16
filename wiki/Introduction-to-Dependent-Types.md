# Introduction to Dependent Types

Dependent types are the foundation of how we make Malbolge safe. This page introduces the concept.

## Types That Depend on Values

In most languages, types and values are separate:
```typescript
// TypeScript: type is String, value is "hello"
const message: String = "hello";
```

In a dependently-typed language, **types can contain values**:
```idris
-- Idris 2: The type depends on the value 5
message : Vect 5 Char
message = ['h', 'e', 'l', 'l', 'o']
```

`Vect 5 Char` is a vector of **exactly 5** characters. The number 5 is part of the type.

## Why This Matters

### Catching Errors at Compile Time

```idris
-- This compiles: 5-element vector
good : Vect 5 Char
good = ['h', 'e', 'l', 'l', 'o']

-- This is a compile error: only 4 elements!
bad : Vect 5 Char
bad = ['o', 'o', 'p', 's']  -- ERROR!
```

The error is caught **before the program runs**.

### For Malbolge: Memory Bounds

```idris
-- Memory address must be in range [0, 59048]
data Address : Type where
  MkAddr : (n : Nat) -> {auto prf : LT n 59049} -> Address
```

This type **cannot represent** an invalid address. You literally can't create an out-of-bounds Address.

## Key Concepts

### 1. Types as Propositions

A type is like a mathematical proposition:
- `Nat` = "there exists a natural number"
- `Vect n a` = "there exists a list of exactly n elements of type a"
- `LT a b` = "a is less than b"

### 2. Programs as Proofs

A value of a type is a **proof** that the proposition is true:
```idris
-- The value 5 proves "there exists a natural number"
five : Nat
five = 5

-- This value proves "2 < 5"
twoLessThanFive : LT 2 5
twoLessThanFive = LTESucc (LTESucc (LTESucc LTEZero))
```

### 3. Compile-Time Verification

If your program compiles, the proofs are valid. No runtime checks needed for properties encoded in types.

## Example: Safe Indexing

```idris
-- Look up element at position i in a vector of length n
-- The constraint (i < n) is part of the type
index : (i : Fin n) -> Vect n a -> a
```

`Fin n` is a type for natural numbers less than n. It's **impossible** to pass an out-of-bounds index.

Compare to unsafe indexing:
```python
# Python: This can crash at runtime
def index(i, vec):
    return vec[i]  # IndexError if i >= len(vec)
```

## For Malbolge

We use dependent types to encode:

| Safety Property | Type Encoding |
|-----------------|---------------|
| Memory in bounds | `Address : Fin 59049` |
| Valid trit | `data Trit = T0 \| T1 \| T2` |
| Valid tryte | `Tryte : Fin 59049` |
| Valid opcode | `data Op = J \| I \| Star \| P \| Lt \| Slash \| V \| O` |

By construction, invalid states are unrepresentable.

## The Power of Precision

Traditional type: "this is an integer"
Dependent type: "this is an integer between 0 and 59048 that represents a valid Malbolge memory address"

The more precise our types, the more bugs we catch at compile time.

## Next: [[Total Functions]]
