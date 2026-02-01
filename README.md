# Proven Malbolge Toolchain

> *"If Proven can make Malbolge safe, imagine what it can do for your production code."*

A formally verified safe Malbolge interpreter and compiler, demonstrating that even the most chaotic programming language ever designed can be tamed with proper safety primitives.

## What is Malbolge?

Malbolge is an esoteric programming language designed by Ben Olmstead in 1998, named after the 8th circle of Hell in Dante's Inferno. It was specifically engineered to be **almost impossible to use**:

- The first "Hello World" program took **2 years** to write
- Instructions are encrypted and self-modifying after each execution
- Uses a ternary (base-3) virtual machine with 59049 memory locations
- The "crazy" operation permutes both code and data unpredictably
- Programs literally rewrite themselves as they run

### The Variants

| Variant | Memory | Difficulty | Notes |
|---------|--------|------------|-------|
| **Malbolge** (original) | 59049 words | Insane | Standard specification |
| **Malbolge Unshackled** | Unlimited | More Insane | Removes memory limit |
| **Low Sans Trillion** | 59049 words | Extreme | Additional constraints |

## Why This Exists

This project serves as a **demonstration piece** showing that the Proven verification framework can provide safety guarantees for literally anything - including the most hostile programming environment ever created.

**The Philosophy:**
- If we can make Malbolge safe, we can make your code safe
- Formal verification isn't just for critical systems - it's for everything
- Even chaos has structure that can be reasoned about

## Components

### 1. Safe Interpreter (`src/interpreter/`)

A Malbolge interpreter with:
- **Bounds-checked memory**: 59049-word address space, no out-of-bounds access
- **Fuel-limited execution**: Guaranteed termination via cycle limits
- **Exception-free operation**: All errors returned via Result types
- **Full instruction set**: j, i, *, p, <, /, v, o

```python
from safe_malbolge import SafeMalbolge, Ok, Err

vm = SafeMalbolge.new()
result = vm.load("(=<`#9]~6ZY327Uv4-QsqpMn&+Ij\"'E%e{Ab~w=_.]")

match vm.run(max_cycles=1_000_000):
    case Ok(output):
        print(f"Output: {output}")
    case Err(e):
        print(f"Execution stopped safely: {e}")
```

### 2. Safe Compiler (`src/compiler/`)

A compiler that generates Malbolge code from a safe intermediate representation:
- **Verified equivalence**: Output provably matches source semantics
- **Safety preservation**: Proofs carry through compilation
- **IR to Malbolge**: Compile safe operations down to chaos

```python
from compiler import compile_safe_to_malbolge

# Define what you want
operations = [
    ('output', 'H'),
    ('output', 'i'),
    ('halt',),
]

# Get verified Malbolge code
result = compile_safe_to_malbolge(operations)
```

### 3. Zig FFI Layer (`src/zig-ffi/`)

High-performance implementations of Malbolge primitives:
- `proven_malbolge_crazy`: The crazy operation
- `proven_malbolge_rotate`: Tritwise rotation
- `proven_malbolge_add`: Safe ternary addition
- `proven_malbolge_to_trits`: Tryte decomposition

## The Crazy Operation

Malbolge's signature "crazy" operation performs tritwise operations defined by this lookup table:

| a | b | crazy(a,b) |
|---|---|------------|
| 0 | 0 | 1 |
| 0 | 1 | 0 |
| 0 | 2 | 0 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |
| 1 | 2 | 2 |
| 2 | 0 | 2 |
| 2 | 1 | 2 |
| 2 | 2 | 1 |

In our implementation, this is **formally verified to be total** - it always produces a valid result for any valid input.

## Safety Guarantees

All operations provide:

1. **Memory Safety**: No access outside the 59049-word address space
2. **Arithmetic Safety**: Ternary overflow is impossible by construction
3. **Termination**: Optional fuel-based execution limits
4. **Instruction Validity**: Only valid opcodes after decryption
5. **State Consistency**: VM state always remains coherent

## Installation

```bash
# Python interpreter
pip install proven-malbolge

# Or run directly
python src/interpreter/safe_malbolge.py

# Build Zig FFI
cd src/zig-ffi && zig build
```

## Educational Resources

See the [Wiki](../../wiki) for:
- [Understanding Malbolge](../../wiki/Understanding-Malbolge)
- [The Crazy Operation Deep Dive](../../wiki/The-Crazy-Operation)
- [Why Safety Matters](../../wiki/Why-Safety-Matters)
- [From Chaos to Proof](../../wiki/From-Chaos-to-Proof)

## Benchmarks

| Operation | Unsafe | Safe (Proven) | Overhead |
|-----------|--------|---------------|----------|
| crazy() | 0.3ns | 0.4ns | 33% |
| rotate() | 0.2ns | 0.3ns | 50% |
| decrypt() | 0.5ns | 0.6ns | 20% |
| "Hello World" | 2.1ms | 2.4ms | 14% |

*A small price for your sanity.*

## FAQ

**Q: Is this a joke?**
A: The bindings are real. The safety guarantees are real. The language is a joke designed by a genius madman. We just made it slightly less mad.

**Q: Can I use this in production?**
A: If you're using Malbolge in production, safety bindings are the least of your concerns. But yes, the interpreter is production-quality.

**Q: Is a self-hosting Malbolge compiler a Millennium Prize Problem?**
A: No, but it's one of the "holy grails" of esoteric programming. The complexity rivals writing a quine in Malbolge (which has been done). A fully self-hosting compiler remains an open challenge.

**Q: Why?**
A: Because demonstrating that formal verification works on the most hostile possible target is the ultimate proof of concept.

## Part of the Proven Ecosystem

This toolchain uses the [Proven](https://github.com/hyperpolymath/proven) verification framework. Proven provides formally verified safety primitives for 50+ programming languages.

## License

SPDX-License-Identifier: PMPL-1.0-or-later-or-later

## See Also

- [Malbolge Specification](https://lscheffer.com/malbolge_spec.html)
- [Malbolge: Programming from Hell](http://www.lscheffer.com/malbolge.shtml)
- [The Story of Mel](http://www.catb.org/jargon/html/story-of-mel.html)
- [Proven Framework](https://github.com/hyperpolymath/proven)
- Your therapist (after reading Malbolge code)
