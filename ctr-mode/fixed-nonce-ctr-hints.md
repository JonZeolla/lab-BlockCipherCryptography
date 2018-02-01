First look for repeated values in a particular column;
these amount to the same plaintext character.  Then look
at their context.

Do they appear to be part of the same word?

How long is the word?

Where in the ciphertext is the word located?  It makes the
most sense to start this process at the beginning of a ciphertext
because that presents a clear boundary where we can safely assume
a word is beginning as well.

What words are often found at a sentence's beginning?

More concretely, given some lists like these...
```
[250, 53, 254, 221, 150,  38, 107, 212, ...
[250, 68, 191, 178,  12, 138, 104,   2, ...
[  3, 34, 114, 168, 129, 198,  12, 230, ...
...
```
... I might surmise that `250` represents `S`.  Thus the ASCII decimal value for `S` XOR'd with `250`, if I'm correct, will present me with a keystream byte.  At a Python repl:
```
>>> 250 ^ ord('S')
169
```
(`ord()` saves us from needing to look at an ASCII table.)

If `169` is the correct keystream byte for each same index of any ciphertext, then `3 ^ 169` ought to reveal a reasonable value for the beginning of an English word...
```
>>> chr(3 ^ 169)
'ª'
```
(`chr()` gives us the character for the result of the XOR, saving a step.)

Clearly my guess of `S` is off-base, so I should start over with another guess.

Should you find that you have a keystream byte that returns "legitimate" characters for that index, but they don't seem to make sense in the context of what you've already decrypted, your guess (the `S` in the above example) is likely just a character or two away from the correct one.
