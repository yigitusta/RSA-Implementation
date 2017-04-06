# RSA Implementation in C
Probably the most inefficient rsa algorithm out there. I programmed this in couple of days during Linux Summer Camp 2016 (Turkey).

Consists of three parts.
* rsa.c
* encrypt_text.c
* decrypt_text.c

In order to encrypt and decrypt some text you must first compile all of these files.

Copy all three files to the directory you want to work in.

Open a terminal, and press enter after typing each line (assuming gcc is installed):
```bash
gcc rsa.c -o rsa
gcc encrypt_text.c -o encrypt_text
gcc decrypt_text.c -o decrypt_text
```

Then write the following to the terminal in order:
1. ./rsa
2. ./encrypt_text (write stuff you want to encrypt here leave no spaces)
2. ./decrypt_text

## How does it work?
[The guide I followed](https://www.cs.utexas.edu/~mitra/honors/soln.html)

1. **./rsa**

   Running the ./rsa command will generate p and q values which are prime numbers, and sets e to 3 (it was normally generating e automatically but it caused program to encrypt big numbers incorrectly because due to 64 bit limitation during calculations.)

   n = p * q
   
   e has to be coprime to φ(n) = (p - 1) * (q - 1) and 1 < e < φ(n)

   Then d is calculated so that (d * e) % φ(n) = 1

   Then 3 files are created.

    * public.txt

       This file has public key, n and e, which will is used by encrypt_text program.
      
    * private.txt

       This file has private key, n and d, which will be used by decrypt_text program.

    * pq.txt

       This file contains p and q values which will also be used by decrypt_text program.
       
2. **./encrypt_text (message to be encrypted)**

    This program will print all characters' ascii values' cipher values of the argument passed, to a file called ciphertext.txt according to the rule below.
    
    c: cipher value, m: ascii value of the character given, e and n are taken from the public.txt file.
    
    c = m<sup>e</sup> % n

3. **./decrypt_text**

    This program will print original message by applying decryption algorithm to each value in ciphertext.txt file to the console. The rule is given below.

    c: cipher value, m: ascii value of the character to be printed, d and n are taken from the private.txt file.

    m = c<sup>d</sup> % n

    NOTE: I have no idea how I used p and q values from the file pq.txt, i wrote this a long time ago, with not many comments, couldn't figure out what happened in that decrypt_text.c file, if you can figure out please make a pull request by changing readme.md or email me so i can update this file. Thanksss