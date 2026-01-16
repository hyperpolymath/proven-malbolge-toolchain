# Why Safety Matters

This isn't just academic exercise. Unsafe code kills.

## The Cost of Unsafe Code

### Real World Examples

**Therac-25 (1985-1987)**
- Medical radiation therapy machine
- Race condition in unsafe code
- 6 patients received massive overdoses
- 3 deaths

**Ariane 5 (1996)**
- Integer overflow in guidance system
- 64-bit to 16-bit conversion without bounds check
- $370 million rocket exploded 37 seconds after launch

**Heartbleed (2014)**
- Buffer over-read in OpenSSL
- 17% of "secure" servers compromised
- Estimated cost: $500 million

**Boeing 737 MAX (2018-2019)**
- MCAS software with inadequate redundancy
- 346 people killed in two crashes

### The Pattern

1. Programmer assumes input is valid
2. Edge case occurs in production
3. Undefined behavior triggers
4. System fails catastrophically

## What "Safe" Means

| Safety Type | Definition | Malbolge Example |
|-------------|------------|------------------|
| **Memory Safety** | No out-of-bounds access | Address type is `Fin 59049` |
| **Type Safety** | Values match their types | `Trit` can only be 0, 1, 2 |
| **Resource Safety** | Resources properly managed | VM state transitions are valid |
| **Termination** | Program eventually finishes | Fuel-bounded execution |

## The Verification Spectrum

From least to most rigorous:

1. **Hope** - "I think this is right"
2. **Testing** - "It worked for these inputs"
3. **Static Analysis** - "No obvious bugs detected"
4. **Type Checking** - "Types align correctly"
5. **Dependent Types** - "Proofs ensure correctness"
6. **Full Formal Verification** - "Mathematical proof of all properties"

Proven operates at level 5, with optional level 6 for critical paths.

## Why Dependent Types?

### Testing Can't Cover Everything

For Malbolge memory access:
- 59049 possible addresses
- 59049 possible values per address
- That's 59049^59049 possible memory states

You can't test them all. But you can **prove** the property holds for all states.

### Types Are Checked at Compile Time

| Approach | When Checked | Failure Mode |
|----------|--------------|--------------|
| Testing | After code runs | Runtime crash |
| Assertions | At runtime | Runtime crash |
| Dependent types | At compile time | Won't compile |

A program with dependent types that compiles has **already** been verified.

## The Malbolge Argument

Malbolge was designed to be:
- Cryptographic
- Self-modifying
- Chaotic
- Hostile

If we can verify safety properties for **this**, we can verify them for anything.

The Proven Malbolge Toolchain demonstrates:
- Memory bounds are enforced by types
- Arithmetic overflow is impossible
- Invalid instructions can't execute
- Termination is guaranteed (with fuel)

## From Chaos to Confidence

Before verification:
```
"I think the interpreter works correctly"
"It passed my tests"
"It hasn't crashed yet"
```

After verification:
```
"Memory access is bounds-checked by construction"
"Arithmetic cannot overflow by the type of Tryte"
"The interpreter terminates because of the fuel parameter"
"These properties are proven, not hoped for"
```

## The Investment

Learning dependent types takes effort. But:

| Investment | Payoff |
|------------|--------|
| Learn Idris 2 basics | Safe code for life |
| Understand totality | No infinite loops |
| Master indexed types | No bounds errors |
| Practice proof writing | Mathematical confidence |

The alternative is hoping bugs don't happen.

## Next: [[Introduction to Dependent Types]]
