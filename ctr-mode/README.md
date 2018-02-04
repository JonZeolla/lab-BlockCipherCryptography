# CTR mode lab

## Instructions

### Introduction

Counter (CTR) mode of operation is still a block cipher mode
at its core, but it behaves like a stream cipher.  Rather than
passing the plaintext directly to the cipher algorithm, CTR
mode encrypts COUNTER || NONCE, generating a keystream. The
keystream is then XOR'd against the plaintext.

Any CTR mode ciphertexts that have been encrypted using the
same key and nonce have also been encrypted against the same
keystream. The plaintext characters are only an XOR away.
Furthermore, any byte value in a ciphertext at the same
index as in another ciphertext (anything in the same
"column"), has necessarily been XOR'd against the same value.

The process of "manually" (non-statistically) decrypting
bytes can look something like this:

1. Look for patterns, and based on these, venture a guess
as to what plaintext value a particular byte may represent.
In this case it's safe to assume that the plaintext is in
English.

1. Find the ASCII decimal value of your guess. Typing `ascii`
at a terminal will give you the entire table; `ascii c`
will give you just the values for some character `c`.

1. XOR the value of your guess against the encrypted byte (you
can do this in a Python REPL with `GUESS ^ ENCBYTE`).
The result, if correct, is the keystream byte for that index/
column.  Test it against other values at that index/column.
You'll know you've hit gold when you're getting characters
that make sense for an English plaintext.

1. Based on what you find, repeat this process for other
positions in the ciphertexts.

### This Lab

This lab is simple in concept - look at the `fixed-nonce-ctr-ciphertexts.txt` file in this directory and attempt to decrypt it.  [Hints](#hints) and [solutions](#solution) are available below.

## Hints
<details>
  <summary>Hint 1</summary>
  First look for repeated values in a particular column;
  these amount to the same plaintext character.  Then look
  at their context.
</details>
<br />
<details>
  <summary>Hint 2</summary>
  Do they appear to be part of the same word?
</details>
<br />
<details>
  <summary>Hint 3</summary>
  How long is the word?
</details>
<br />
<details>
  <summary>Hint 4</summary>
  Where in the ciphertext is the word located?  It makes the
most sense to start this process at the beginning of a ciphertext
because that presents a clear boundary where we can safely assume
a word is beginning as well.
</details>
<br />
<details>
  <summary>Hint 5</summary>
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
([`ord()`](https://docs.python.org/3/library/functions.html#ord) saves us from needing to look at an ASCII table.)

If `169` is the correct keystream byte for each same index of any ciphertext, then `3 ^ 169` ought to reveal a reasonable value for the beginning of an English word...
```
>>> chr(3 ^ 169)
'ª'
```
([`chr()`](https://docs.python.org/3/library/functions.html#chr) gives us the character for the result of the XOR, saving a step.)

Clearly my guess of `S` is incorrect, so I should start over with another guess.

Should you find that you have a keystream byte that returns "legitimate" characters for that index, but they don't seem to make sense in the context of what you've already decrypted, your guess (the `S` in the above example) is likely just a character or two away from the correct one.
</details>

## Solution
<details>
  <summary>Click here to find the solution</summary>
  XORing this keystream ...

`[72, 200, 236, 241, 160, 67, 177, 156, 4, 223, 62, 132, 51, 104, 195, 163, 61, 242, 173, 251, 77, 182, 80, 140, 40, 35, 12, 98, 189, 253, 21, 75, 146]`

... with each of the ciphertexts, will yield these plaintexts:

```
Fighters can be placed into
two main categories:
the mechanical fighter and
the intellectual fighter.
It's easy for the mechanical
fighter to give advice because
their fighting techniques and
tactics are the result of the
mechanical repetition of strokes,
bred of a lesson which was purely
automatic and lacking an
intelligent explanation of the
'why', the 'how', and the 'when'.
Their fighting follows a similar
pattern in each successive
encounter. The intelligent
fighter will never hesitate to
change tactics in order to use
the correct strokes to deal with
their opponent. It must be plain
by now that the fighter's decision
to use any particular stroke must
be influenced by their opponent's
technique and method of fighting.
```

To verify this in Python 3, given some `line_of_ciphertext`
(keep in mind that this is solvable with nothing more than the XOR operation, albeit much more slowly):
```
line_of_ciphertext = some_list_of_strs_from_the_ciphertexts_file
keystream = [72, 200, 236, 241, 160, 67, 177, 156, 4, 223, 62, 132, 51, 104, 195, 163, 61, 242, 173, 251, 77, 182, 80, 140, 40, 35, 12, 98, 189, 253, 21, 75, 146]

# define some functions for easier reading
def xor(a, b):
    return a ^ b

def xor_lists(byte_list_a, byte_list_b):
    return list(map(xor, byte_list_a, byte_list_b))

# the ciphertexts were provided as lists of strings
# so that they could be viewed in columns,
# but we need ints again
def str_list_to_int_list(str_list):
    return [int(s) for s in str_list]

# strs to ints
ciphertext_bytes = str_list_to_int_list(line_of_ciphertext)

# XOR to recover plaintext
decoded_bytes = xor_lists(keystream, ciphertext_bytes)

# make a list of each character from its byte value,
# then make a single string
decoded_str = ''.join([chr(b) for b in decoded_bytes])

print(decoded_str)
# if you're stuck with Python 2, do this instead:
# print decoded_str
</details>

