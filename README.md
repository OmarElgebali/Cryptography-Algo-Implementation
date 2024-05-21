# Cryptography Algorithms Implementation

## Overview

This project contains implementations of various classical and modern cryptographic algorithms. Each algorithm is implemented with functions for `encryption`, `decryption`, and `analysis` *if applicable*. `Test cases` are also provided for each implementation to ensure correctness.

## Algorithms Implemented

#### *1. **Caesar Cipher**:*

The Caesar cipher is a substitution cipher where each letter in the plaintext is shifted a fixed number of places down the alphabet. For example, with a shift of 3, A would be replaced by D, B would become E, and so on.

#### *2. **Monoalphabetic Cipher**:*

This is a substitution cipher where each letter of the plaintext is mapped to a corresponding letter of ciphertext using a fixed substitution alphabet. Unlike the Caesar cipher, the substitution is not necessarily systematic and can be any permutation of the alphabet.

#### *3. **Playfair Cipher**:*

The Playfair cipher encrypts pairs of letters (digraphs) using a 5x5 matrix of letters built from a keyword. Each pair of letters is encrypted by finding the letters in the matrix and forming a rectangle; the letters at the opposite corners of this rectangle form the ciphertext.

#### *4. **Hill Cipher**:*

The Hill cipher is a polygraphic substitution cipher based on linear algebra. It uses matrix multiplication to encrypt blocks of plaintext, where each letter is represented by a number (e.g., A=0, B=1, ..., Z=25).

#### *5. **Auto-Key Vigenère Cipher**:*

This is a Vigenère cipher that uses the plaintext itself as part of the key. The key is extended by appending the plaintext itself to the initial keyword, providing better security against frequency analysis.

#### *6. **Repeating Key Vigenère Cipher**:*

This cipher is similar to the Auto-Key Vigenère cipher, but the key is repeated cyclically. The plaintext is encrypted by shifting each letter according to the corresponding letter in the repeated key.

#### *7. **Columnar Transposition Cipher**:*

In this cipher, the plaintext is written out in rows of a fixed length, and then columns are permuted according to a defined key to produce the ciphertext.

#### *8. **Rail Fence Cipher**:*

The Rail Fence cipher is a transposition cipher that writes the plaintext in a zigzag pattern along multiple "rails" and then reads off each rail to get the ciphertext.

#### *~~9. **Advanced Encryption Standard (AES)**:~~*

AES is a symmetric encryption algorithm that uses fixed block sizes (128 bits) and key sizes of 128, 192, or 256 bits. It operates on a 4x4 column-major order matrix of bytes with a series of linked operations involving substitution, transposition, and mixing.

#### *~~10. **Data Encryption Standard (DES)**:~~*

DES is a symmetric-key algorithm for the encryption of digital data. It uses a 56-bit key and operates on 64-bit blocks. The algorithm involves 16 rounds of permutation and substitution based on the key.

#### *11. **RSA (Rivest-Shamir-Adleman)**:*

RSA is an asymmetric encryption algorithm based on the difficulty of factoring large integers. It uses a pair of keys (public and private) for encryption and decryption, providing security through the computational difficulty of the key-related mathematical operations.

#### *12. **Diffie-Hellman Key Exchange**:*

This is a method of securely exchanging cryptographic keys over a public channel. It allows two parties to generate a shared secret key, which can be used for symmetric encryption, based on their private and public keys.

#### *13. **ElGamal Encryption**:*

ElGamal is an asymmetric key encryption algorithm based on the Diffie-Hellman key exchange. It provides security through the difficulty of solving discrete logarithm problems and consists of three components: key generation, encryption, and decryption.
