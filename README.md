# RSA Implementation in C
Probably the most inefficient rsa algorithm out there. I programmed this in couple of days during Linux Summer Camp 2016 (Turkey).

Consists of three parts.
* rsa.c
* encrypt_text.c
* decrypt_text.c

In order to encrypt and decrypt some text you must first compile all of these files.

Copy/Move all three files to the directory you want to work in.

Open a terminal, change your current working directory to the directory with the files and press enter after typing each line (assuming gcc is installed):
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

    Running the ./rsa command will generate p and q values which are prime numbers, and sets e to 3 (it was normally generating e automatically but it caused program to encrypt big numbers incorrectly  during calculations due to 64 bit limitation.)

    n = p * q
   
    e has to be coprime to φ(n) = (p - 1) * (q - 1) and 1 < e < φ(n)

    ```c
    void setprimes(uint16_t e, uint16_t *p, uint16_t *q, uint32_t *n, uint32_t *phi) //φ(n) = phi
    {
	    do
	    {
		    *p = getprime();
		    do
			    *q = getprime();
		    while(*p == *q);

		    *n = *p * *q;
		    *phi = *n - *p - *q + 1;
	    }while (gcd(e,*phi) != 1);
    }
    ```
    Note: % is means modular operation

    d is calculated so that (d * e) % φ(n) = 1
    
    Notice that d is modular inverse of e % φ(n)

    [How to find modular inverses?](https://www.khanacademy.org/computing/computer-science/cryptography/modarithmetic/a/modular-inverses)

    This function calculates modular inverse of e % φ(n) which is d.
    ```c
    uint32_t findD(uint16_t e, uint32_t phi)
    {
	    uint32_t eprev, dprev, d = 1, etemp, dtemp;

	    eprev = phi, dprev = phi;
	    while (e != 1)
	    {
		    etemp = e;
		    dtemp = d;
		    e = eprev - eprev / etemp * e;
		    d = dprev - eprev / etemp * d;
		    eprev = etemp;
		    dprev = dtemp;
		    while (d < 0)
			    d += phi;
	    }
	    return d;
    }
    ```

    Then 3 files are created.

    * public.txt

       This file has public key, n and e, which will is used by encrypt_text program.
      
    * private.txt

       This file has private key, n and d, which will be used by decrypt_text program.

    * pq.txt

       This file contains p and q values which will also be used by decrypt_text program.
    
    Example console output of this part:

    ```c
    p and q must be prime numbers, e must be coprime to (p - 1)*(q - 1)

    e: 3
    p: 6599
    q: 6143
    n: 40537657
    phi: 40524916

    d: 27016611

    Public Key:  (n,e) = (40537657, 3)
    Private Key: (n,d) = (40537657, 27016611)
    ```
    
2. **./encrypt_text (message to be encrypted)**

    This program will print all characters' ascii values' cipher values of the argument passed, to a file called ciphertext.txt according to the rule below.
    
    c: cipher value, m: ascii value of the character given, e and n are taken from the public.txt file.
    
    c = m<sup>e</sup> % n

    ```c
    //base = m, power = e, mod = n, c = result
    unsigned long long int modpow(int base, int power, int mod)
    {
        int i;
        unsigned long long int result = 1;
        for (i = 0; i < power; i++)
        {
                result = (result * base) % mod;
        }
        return result;
    }
    ```

3. **./decrypt_text**

    This program will print the original message by applying decryption algorithm to each value in ciphertext.txt file to the console. The rule is given below.

    c: cipher value, m: ascii value of the character to be printed, d and n are taken from the private.txt file.

    m = c<sup>d</sup> % n

    **NOTE: I have no idea how I used p and q values from the file pq.txt, I wrote this a long time ago, not with many comments. I couldn't figure out what I had written at the time I decided to upload this project to GitHub, if you can figure out please make a pull request by changing readme.md or email me so I can update this file.**
    
    ###### Code Section I am talking about is here:

    ```c
        dP = d % (p - 1);
        dQ = d % (q - 1);
        qInv = inverse(q,p); //modular inverse of q % p
        m1 = modpow(c,dP,p);
        m2 = modpow(c,dQ,q);
        m1m2 = m1 - m2;
        if (m1m2 < 0)
            m1m2 += p;
        h = (qInv * m1m2) % p;
        m = m2 + h * q;
	    printf("%c", m);
    ```
