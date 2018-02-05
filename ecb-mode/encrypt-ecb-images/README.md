# Encrypt images with ECB
(adapted from [https://blog.filippo.io/the-ecb-penguin/](https://blog.filippo.io/the-ecb-penguin/))

## Instructions

### Introduction

Electronic Codebook (ECB) mode encrypts identical blocks in an identical manner.  As such, it's hardly a mode of operation at all, revealing patterns about the plaintext in the ciphertext.

### This Lab

The effects of ECB mode encryption on images are interesting.  Areas of an image that are consistently colored/shaded will be encrypted to different values but in a consistent way.


There's no "solution" persay for this lab; it's an opportunity to play with cryptography in a manner less encountered.  Follow the instructions below and apply them to any of the images in this directory.

1. Install ImageMagick if you don't already have it.
   * MacOS via Homebrew: `brew install imagemagick`
   * Debian/Ubuntu: `sudo apt-get install imagemagick`
   * Fedora: `sudo dnf install imagemagick`
   * MacOS and Linux binaries: [https://imagemagick.org/script/download.php](https://imagemagick.org/script/download.php)
2. Install OpenSSL if you don't already have it (if you run Linux, you probably do already; just type `openssl version` at a terminal to verify).
   * MacOS via Homebrew: `brew install openssl`
   * Debian/Ubuntu: `sudo apt-get install openssl`
   * Fedora: `sudo dnf install openssl`
3. Convert an image to `.ppm` format: `magick filename.jpg filename.ppm`

<details>
  <summary>Why <code>.ppm</code>?</summary>
  This is a convenient format because there's no compression and the image header is simple... Why does this matter?  Basically the header provides information about the image, and so when we go to encrypt the image, we only want to encrypt the image body, where the pixel bytes are located.  Encrypting the image header would give us a file that image viewers wouldn't know how to handle, killing all the fun.  Furthermore, `.ppm`'s simple header means that we have a reliable boundary for where the header stops and the image body begins, regardless of image contents.
  </details>

4. Separate the image header and the image body:

   `head -n 4 filename.ppm > filename.header`

   `tail -n +5 filename.ppm > filename.body`

5. Encrypt the image body:

   `openssl enc -aes-128-ecb -nosalt -pass pass:"somepassword" -in filename.body -out filename.body.ecb`
   
6. Bring the head and body together again:

   `cat filename.header filename.body.ecb > filename.ecb.ppm`
   
7. Open the original `filename.jpg` along with the new `filename.ecb.ppm` and compare/enjoy.  Any reasonably capable image viewer (MacOS's Preview, for instance) should be able to open `.ppm` files.

8. Go back to step 3 with another image, or try encrypting the same image body again with a different password.

