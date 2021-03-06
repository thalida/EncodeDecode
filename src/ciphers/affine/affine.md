# {{cipher.NAME}}

## About
[{{cipher.NAME}}](http://en.wikipedia.org/wiki/Affine_cipher) is a monoalphabetic substitution cipher. Each letter in the alphabet is mapped to a number, then encrypted/decrypted using a math formula, and finally converted back to a letter.

## Supports
✅ Perfect Decoding
✅ Uppercase letters (`A-Z`)
✅ Lowercase letters (`a-z`)
❌ Numbers (`0-9`)
❌ Symbols (`!@#$`)
❌ Emojis (`😍🤬👩🏾‍💻`)

**Numbers, Symbols, and Emojis**
Numbers, symbols, and emoji are **outputted as-is** by this cipher.

**What is "Perfect Decoding"?**
Perfect Decoding is when the decoded text exactly matches the text that was encoded.

## Source
[View {{cipher.NAME}} on Github](https://github.com/thalida/ciphers.codes/blob/master/src/ciphers/affine/affine.js)

---

## Formulas

### Encoding
```js
((coprime * letterPosition) + shift) mod 26
```

### Decoding
```js
(modInverseOfCoprime * (letterPosition - shift)) mod 26
```

---

## Live Example
### Variables
Based on the current settings for {{cipher.NAME}}:
- `coprime = {{cipher.INPUTS_BY_NAME.coprime.value}}`
- `shift = {{cipher.INPUTS_BY_NAME.shift.value}}`
- `modInverseOfCoprime = {{cipher.modInverse(cipher.INPUTS_BY_NAME.coprime.value)}}`

### Encoding Formula
```js
(({{cipher.INPUTS_BY_NAME.coprime.value}} * letterPosition) + {{cipher.INPUTS_BY_NAME.shift.value}}) mod 26
```

### Decoding Formula
```js
({{cipher.modInverse(cipher.INPUTS_BY_NAME.coprime.value)}} * (letterPosition - {{cipher.INPUTS_BY_NAME.shift.value}})) mod 26
```

### Sample String
{{ cipher.SAMPLE_STRING }}

### When, **Encoded**
{{ cipher.encodingExample(cipher) }}

### Then, **Decoded**
{{ cipher.decodingExample(cipher) }}

---

## How it works
- `letterPosition` is the index of the letter
- `coprime` and `shift` are two variables you pick!
- `modInverseOfCoprime` is the modular multiplicative inverse of `coprime`

### "Magic" Number 26
You'll see a lot of references to `26`, it's not a magic number, just the
number of letters in the English alphabet.

### `letterPosition`
Index of the letter in the English alphabet (0-indexed). For example,
A=0, B=1, C=2, and so on until Z=25.

### `coprime`
Must be a number where the only positive integer that divides it and 26 is 1. With that limitation the only valid values are: {{cipher.ALLOWED_COPRIMES.join(', ')}}

### `shift`
Any integer, positive or negative

### `modInverseOfCoprime`
The [modular multiplicative inverse](https://www.geeksforgeeks.org/multiplicative-inverse-under-modulo-m/) of the `coprime`. To be honest, I don't fully understand the formula.

But, after _extensive searching_, I got a rough understanding that we need to find a number where the formula below would be correct.
```js
(coprime * x) % 26 = 1
```
The value of `x` is the modular multiplicative inverse of `coprime`. The following javascript function, ~~stolen~~ converted from the examples [here](https://www.geeksforgeeks.org/multiplicative-inverse-under-modulo-m/) calculates the value for `x`.

```js
function _modInverse (coprime) {
  let mod = utils.TOTAL_ALPHA
  let inverse = 1
  let y = 0

  while (coprime > 1) {
    let origMod = mod
    let origY = y
    let quotient = Math.floor(coprime / mod)
    mod = coprime % mod
    coprime = origMod
    y = inverse - quotient * y
    inverse = origY

    if (inverse < 0) {
      inverse += utils.TOTAL_ALPHA
    }
  }

  return inverse
}
```

### `mod 26`
Both formulas for encoding and decoding have `mod 26` which performs the modulo (%) operation on the output.

This is a safe-keeping action to guarantee the character is one of the 26 letters in the alphabet. [Javascript doesn't have proper support for mod](https://dev.to/maurobringolf/a-neat-trick-to-compute-modulo-of-negative-numbers-111e) so this formula is used:
```js
export function mod (a, b) {
  return ((a % b) + b) % b
}
```
