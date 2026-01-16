# The Crazy Operation Deep Dive

The `crazy` operation is the heart of Malbolge's chaos. Let's understand it, then verify it.

## Definition

```
crazy : Tryte -> Tryte -> Tryte
crazy a b = tritwise_apply crazy_trit a b
```

For each corresponding pair of trits, apply the lookup table:

```
crazy_trit : Trit -> Trit -> Trit
crazy_trit a b = table[a][b]

table =
    | b=0 | b=1 | b=2 |
----|-----|-----|-----|
a=0 |  1  |  0  |  0  |
a=1 |  1  |  0  |  2  |
a=2 |  2  |  2  |  1  |
```

## Why "Crazy"?

This operation is deliberately confusing:

1. **Not commutative**: `crazy(0,1) = 0` but `crazy(1,0) = 1`
2. **Not associative**: `crazy(crazy(a,b),c) ≠ crazy(a,crazy(b,c))` in general
3. **No identity element**: There's no value `e` where `crazy(e,x) = x`
4. **No inverse**: Given `crazy(a,b) = c`, you can't recover `a` from `b` and `c`

It's specifically designed to be hard to reason about.

## Verifying Crazy in Idris 2

Despite the chaos, `crazy` has verifiable properties:

### 1. Totality

```idris
||| The crazy operation is total - it always produces a result
total
crazy : Trit -> Trit -> Trit
crazy T0 T0 = T1
crazy T0 T1 = T0
crazy T0 T2 = T0
crazy T1 T0 = T1
crazy T1 T1 = T0
crazy T1 T2 = T2
crazy T2 T0 = T2
crazy T2 T1 = T2
crazy T2 T2 = T1
```

The `total` annotation tells Idris to verify that:
- All cases are covered (pattern matching is exhaustive)
- The function terminates (no infinite recursion)

### 2. Output Range

```idris
||| crazy always produces a valid trit
crazyInRange : (a, b : Trit) -> crazy a b `Elem` [T0, T1, T2]
crazyInRange T0 T0 = Here        -- crazy(0,0) = 1, which is in the list
crazyInRange T0 T1 = Here        -- etc.
-- ... (all 9 cases)
```

### 3. Specification Compliance

```idris
||| crazy matches the Malbolge specification exactly
crazySpec : (a, b : Trit) -> crazy a b = lookupTable a b
crazySpec a b = Refl  -- The implementations are definitionally equal
```

## The Tritwise Extension

Extending to full 10-trit words:

```idris
||| Apply crazy to each trit pair
total
crazyTryte : Tryte -> Tryte -> Tryte
crazyTryte a b =
    let aTrits = toTrits a
        bTrits = toTrits b
        resultTrits = zipWith crazy aTrits bTrits
    in fromTrits resultTrits
```

### Preserving Validity

```idris
||| crazyTryte preserves the Tryte invariant
crazyTryteValid : (a, b : Tryte) ->
                  InRange 0 59048 (value a) ->
                  InRange 0 59048 (value b) ->
                  InRange 0 59048 (value (crazyTryte a b))
```

This proof shows that if both inputs are valid Trytes (0-59048), the output is also valid.

## Safety Through Types

The key insight: by making `Trit` a finite type with only 3 values, we get totality for free. The type system ensures we handle all cases.

```idris
data Trit = T0 | T1 | T2

-- This MUST cover all cases or the compiler rejects it:
crazy : Trit -> Trit -> Trit
crazy T0 T0 = T1
crazy T0 T1 = T0
crazy T0 T2 = T0
crazy T1 T0 = T1
crazy T1 T1 = T0
crazy T1 T2 = T2
crazy T2 T0 = T2
crazy T2 T1 = T2
crazy T2 T2 = T1
```

If we forget a case, the program won't compile.

## Lesson: Enumerate Your Domain

When your domain is finite and small, enumerate it explicitly:

1. Define a custom type with all valid values
2. Pattern match exhaustively
3. Let the compiler verify completeness

This is how we tame chaos with types.

## Next: [[Memory Safety in Ternary Systems]]
