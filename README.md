MrrCrypt
========

The goal of this project is to provide a simple and robust command-line tool
for encrypting and decrypting data using an adaptive mirror field algorithm.

The algorithm passes testing for output patterns or biases, producing
output that is indistinguishable from random data.

The key used for encryption and decryption is highly against resilient to
brute force attacks. The total number of key permutations is: 256!\*3^(32\*32) ≈ 3.2e+995

Encryption speed is roughly 4 seconds per megabyte.

The adaptive mirror field algorithm implemented in this project is the first
of it's kind.

*Adaptive Mirror Field Animation:*

![MrrCrypt's Adaptve Mirror Field](http://i.imgur.com/Oq2Ch7S.gif)

See [ADAPTIVE_MIRROR_FIELD](ADAPTIVE_MIRROR_FIELD.md) for more details.

Table of Contents
-----------------

1. [Quick Examples](#quick-examples)
2. [Download and Install](#download-and-install)
3. [Usage](#usage)
4. [Key Management](#key-management)
5. [License](#license)

Quick Examples
--------------

**Encrypting/Decrypting a Short String**

```
# Encrypt
$ printf "Attack at Dawn" | mrrcrypt > string.encrypted

# Decrypt
$ cat string.encrypted | mrrcrypt
Attack at Dawn

```

**Encrypting/Decrypting the Contents of a File**

```
# Encrypt
$ cat secret.jpg | mrrcrypt > secret.jpg.encrypted

# Decrypt
$ cat secret.jpg.encrypted | mrrcrypt > secret.jpg
```

**Dual Key Encryption/Decryption**

Suppose Bob and John want to encrypt a file so that they each hold a
distinct key required for decryption. MrrCrypt can easily accomplish this.

```
# Encrypt
$ cat secret.txt | mrrcrypt -a -k bobs_key | mrrcrypt -a -k johns_key > secret.txt.encrypted

# Decrypt
$ cat secret.txt.encrypted | mrrcrypt -k johns_key | mrrcrypt -k bobs_key > secret.txt
```

The `-k <name>` option specifies a key file to use, and the `-a`
option auto-creates it if it doesn't exist. After performing the first
command to create two new keys and encrypt the file, they each copy
their respective key files for themselves (see [Key Management](#key-management))
and delete the originals. To decrypt, they each copy their key files back
to the original location and perform the second command.

Download and Install
--------------------

In order to download and build this program, you will need to have `git`,
`gcc`, and `make` installed. Install them from your package manager if not
already installed.

```
$ which make
/usr/bin/make

$ which gcc
/usr/bin/gcc

$ which git
/usr/bin/git
```

Download and Build:

```
$ git clone https://github.com/bartobri/mrrcrypt.git
$ cd mrrcrypt
$ make
```

Install:

```
$ sudo make install
```

Uninstall:

```
$ sudo make uninstall
```

Usage
-----

`mrrcrypt` processes data from stdin and writes the results to stdout.
Use I/O redirection for generating input and capturing output.

**Basic Encryption**

```
# Encrypt a String
$ echo "This is my secret" | mrrcrypt > secret_string.encrypted

# Encrypting a file
$ cat secret_photo.jpg | mrrcrypt > secret_photo.jpg.encrypted
```

**Basic Decryption**

```
# Decrypt a File
$ cat secret_string.encrypted | mrrcrypt
This is my secret

# Capture Output to a File
$ cat secret_photo.jpg.encrypted | mrrcrypt > secret_photo.jpg
```

**Command Line Options**

`-k key_file`

Specify a key to use. An argument is required. The argument can be the name
of a key file that exists in the default key directory, or it can be a full
path to a key file that exists elsewhere on disk. Note that when this option
is not used, mrrcrypt uses the default key. See [Key Management](#key-management)
for more info.

`-a`

Auto-create a new key if the one specified by `-k key_file` does not exist.

`-d ms`

Debug mode. This draws the mirror field and animates the decryption process
for debugging purposes. You will need a minimum terminal size of 75x40.
"ms" specifies the time in milliseconds for each step through the mirror
field. Values around the mirror field perimeter are shown in hexadecimal.
There is an inner and outer row of values for a total of 256 values represented
in the 32x32 grid, one for every possible arrangement of bits in a single byte.

Key Management
--------------

Keys are stored in `$HOME/.config/mrrcrypt/`

The first time you run `mrrcrypt` it will automatically generate a unique
default key. The default key will be used when no key is specified.

You may wish to share your default key (or any other key) with others so
they can decrypt your messages. Keys that are shared by other individuals
can be used with the `-k key_file` option.

Note that "key_file" corresponds to the name of the key file. If John
encrypts messages using his "default" key file, and gives a copy to Bob so he
can decrypt them, Bob should rename the file to "johns_key" so he does not
overwrite his own default key file. Then Bob can then use `-k johns_key` when
decrypting data sent from John.

Keys are stored in Base64 encoded format.

License
-------

This program is free software; you can redistribute it and/or modify it under the terms of the the
MIT License (MIT). See [LICENSE](LICENSE) for more details.
