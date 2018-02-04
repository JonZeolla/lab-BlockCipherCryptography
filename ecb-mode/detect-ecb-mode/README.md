# Detect ECB mode lab

## Instructions

### Introduction

Electronic Codebook (ECB) mode encrypts identical blocks in an identical manner.  As such, it's hardly a mode of operation at all, revealing patterns about the plaintext in the ciphertext.

### This Lab

Find out which `Image*.ppm` in this directory has been encrypted with AES in ECB mode.  GIMP can be used to open these, but an image editor shouldn't be required to solve this.  [Hints](#hints) and [solutions](#solution) are available below.

## Hints
<details>
  <summary>Hint 1</summary>
  Keep in mind that AES has a block size of 16 bytes.
  
  
</details>
<br>
<details>
  <summary>Hint 2</summary>
  Given that ECB mode encrypts identical blocks in an identical manner, what should you be looking for in each image file?
</details>
<br>
<details>
  <summary>Hint 3</summary>
  Run `hexdump [FILENAME]` in Bash to see the byte values in a relatively friendly way.
</details>

## Solution
<details>
  <summary>Click here to find the solution</summary>
  <br>


  `Image4.ppm` is the one encrypted with ECB mode.  It's the only one containing duplicate blocks.  They're all derived from the same image, just encrypted under different modes and then cropped differently.


  One solution in Bash would be:
  `hexdump some-file | cut -d " " -f 2-17 | sort | uniq -d`


  Here's how it works:
  1. `hexdump` prints the byte values for each block
  1. `cut` uses spaces as delimiters to select only the byte values from each line of `hexdump`
  1. `sort` the output from `cut` (necessary for the next step)
  1. `uniq -d` prints only those values which repeat in the output from `sort`

  A more general way of approaching it would be:
  1. Make a list of lists from each 16-byte block in a file
  1. Make a _set_ out of the list of lists (sets contain only unique values)
  1. If the length of the original list and the length of the set differ, there were duplicate blocks in the file.

  If you're really aching to know what's to be seen in the image... It's just a forest, chosen and cropped to avoid any definitive shapes and swaths of similarly-colored pixels.
</details>

## Further Reading
_Best read after you've solved this challenge, to avoid spoilers._


An interesting approach to ECB image forensics:
https://doegox.github.io/ElectronicColoringBook/
