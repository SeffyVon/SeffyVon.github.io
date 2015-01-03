---
layout: post
title:  "CBC Padding Oracle Attacks"
date:   2015-08-20 08:08
categories: Cryptography
---

In the week 4 programming assignment ([Coursera Crypto I Padding Oracle Attack Lecture 7.6 video](https://class.coursera.org/crypto-011/lecture/view?lecture_id=38), may need to register for class to seethe video), we broke Cipher-Block Chaining (CBC) mode using Padding Orracle Attacks. The main idea is that the attacker modifies some bits of the ciphertext, and to see the reaction of the server to learn some information of the ciphertext. In this padding oracle attack, this information is very essential to guess the bits of the plaintext.

**The notation of this blog:**

1. Cipher text means the encrypted text, denoted as c[i] or c(i)

2. Cipher text means the decrypted text that can be read by human, denoted as p[i] or p(i)

2. c[i], p[i] without &#39;modified&#39; will all be referred to the original unmodified cipher text/ plain text.

> ** [Cipher-Block Chaining (CBC) mode:](http://en.wikipedia.org/wiki/Cipher_block_chaining)**
> 
> CBC mode is a type of the **block cipher**, which seperates the cipher into n fixed-size blocks so that it becomes a[0], a[1], a[2], .. a[n-1]. The blocks are usually 128 bits or 256 bits a block, depending on the encryption suite. As the block size is fixed for the blocking mode, the last block needs **padding to make sure each block has a fixed size.**
> 
> The CBC works as follow: 
> 
> There is an** Initialization Vector (IV) **which sends in plaintext. 
> 
> **Enc** is a function that is specified by the cipher suite, maybe AES or some other functions. But as the server does the decryption for us, we are **free of trouble guessing or learning the information about Enc**!
> 
> **Dec **is the inverse function of **Enc**.
> 
> **K** is the private key.
> 
> **Encryption of CBC:    **
> 
> ![](http://imglf2.ph.126.net/UnVi3ZY0IM7oF8VTetjvKw==/6608766371352765476.png)
> 
>      c[0] := Enc(K, IV XOR p[0])
> 
>      c[i] := Enc(K, c[i-1] XOR p[i])   ( i &gt; 0 )      
> 
> **Decryption of CBC:**![](http://imglf0.ph.126.net/kMQ4JIdGWNHZy-Q6YEkmWw==/6619128168932564967.png)
> 
>     p[0] := IV XOR Dec(K, c[0])
> 
>     p[i] := c[i-1] XOR Dec(K, c[i]) ( i &gt; 0 )

Note that the CBC needs padding in the last block. Thus we have a Padding Oracle Attack based on the padding.

> [**Padding Oracle Attack:**](http://en.wikipedia.org/wiki/Padding_oracle) 
> 
> To some vulnerable website, the attacker submits ciphertext and **learns if last bytes of plaintextare a valid pad** by the behavior (error code, returning time difference etc.)

> ** [PKCS5/7 Padding Scheme:](http://en.wikipedia.org/wiki/PKCS)**
> 
> **PKCS5 has 8 bytes/block. PKCS7 has 16 bytes/block.**
> 
> **![](http://imglf0.ph.126.net/npak0ILzp3AC4neaWfK8VA==/6608904909817864893.png)
> 
> **
> 
> **If there are n(n&gt;0) bytes left for becoming a full block, then the last block would be ended with n 0x0n&#39;s.**
> 
> Eg. In PKCS7, if the last block is &#39;123456789abcde&#39;, which has 14 bytes, n = 16 - 14 = 2, the last block will be ended with 0x0202. The byte will all be stored as ASCII code, so the bytes of last block **&#39;123456789abcde&#39;** in hex will be:
> 
> **0x 31 32 33 34 35 36 37 38 39 61 62 63 64 65 02 02.**
> 
> **If the last block of orginal plain text is full(8 bytes for PKCS5, 16 bytes for PKCS7), then append a new block of 16 0x10&#39;s to the orginal plain text.**
> 
> Eg. In PKCS7, if the last block is **&#39;123456789abcdef0&#39;**, which has 16 bytes, n = 16-16 = 0, there will be a new block of** **0x 10 10 10 10 10 10 10 10 10 10 10 10 10 10 10 10 (16 bytes of 0x10&#39;s) append to the cipher text, and the last two blocks will be:
> 
> **0x 31 32 33 34 35 36 37 38 39 61 62 63 64 65 66 30 10 10 10 10 10 10 10 10 10 10 10 10 10 10 10 10.**

For the CBC training mode, padding oracle attack works because if attackers change some bit in the previous block(the last byte of p[n-2]), it will affect the next block (the last byte of p[n-1]).

![](http://imglf0.ph.126.net/0-G9AS8o_62qoWrND2xvag==/6619512998002287408.png)

The attacker can guess the value of** the red byte **by trying all the bytes from 0x00 to 0xff ( totally 256 attempts), by modify the cipher text, or by XORing the cipher text with the cipher block more specifically.

Let  modified_c[i] := c[i] XOR **guessed_byte** XOR **0x01**

so, modified_p[i+1] := p[i+1] XOR **guessed_byte **XOR **0x01**

Especially, if guessed_byte equals to corresponding bits (say last byte) of p[i+1], we could get:

**modified_p[i+1] ends with 0x01 in the last byte, **and it is a **valid padding** for PKCS5 padding scheme. And we get **the right guessed_<strong>byte** </strong>for the corresponding bits (say last byte of c[i]).

Once we get the last byte of c[i], e.g. 0x32, the attacker can** set the last byte of the guessed_bytes **0x3**2, and move on to the second last byte.**

For the last but second byte:

![](http://imglf0.ph.126.net/LFaWNOIZcoM4ysgOYMdoQA==/6619522893606936948.png)

The attacker can generate** guessed_byte (<strong> from 0x 00 32 to 0x ff 32 )** </strong>for the second last byte. 

And the valid padding for last block will be ended with 0x 02 02.

Let&#39;s set the **guessed_byte_for_n-2 := XX **, and the last byte of the block we had already calculated it as** 0x32. **

**guessed_bytes := 0x XX 32.**

So, modified_c[i] := c[i] XOR **0x XX 32** XOR **0x 02 02**

modified_p[i+1] := p[i+1] XOR **0x <strong>XX 32** </strong>XOR **0x 02 02**

if **guessed_byte_for_n-2 **is correct ( in this case the 0x XX 32 equals to the last two bytes of p[i+1] )**, **then **the modified_p[i+1] will be ended with 0x 02 02,** making it a valid padding. The server will return a valid padding response. In my code, it&#39;s 404, because its plaintext has a valid padding, but it is not the correct context.

> Thus, the attacker can work from the last byte to the first byte of a block, and also move on from the first block to the last block.
> 
> **{modified iv}**+c0-&gt;**{modified p0}**,
> 
> iv+**{modified c0}**+c1 --&gt; p0+**{modified p1}**,
> 
> iv+c0+**{modified c1}**+c2 --&gt; p0+p1+**{modified p2}**,
> 
> ...
> 
> iv+c0+c1+...+**{modified c(i-1)}**+ci--&gt; p0+p1+...+**{modified pi}**

**

**

We can see the attacker can get all the modified blocks p0 to p(n-1), as long as he removes the blocks after ci, and modified c(i-1), he can submit the ciphertext  **iv+c0+c1+...+{modified c(i-1)}+ci **and observe if **p0+p1+...+{modified pi} **is a valid padding.

For programming design, we can have:

1.  Seperate the cipher text into 16 bytes/block. We got n blocks.

2.  An outer loop from block[0] to block [n-1].

3.  For each block, An inner loop from byte[15] to byte[0].

> Note that before the attacker modify the cipher text, the last block will be a valid padding already.

 iv+c0+...{modified c(n-2)}+c(n-1)--&gt; p0+p1+...+{modified p(n-1)}

{modified c(n-2)} :=  c(i-2) XOR **0x0j...****0j (j bytes of 0x0j&#39;s)** XOR **guessed_bytes**

{modified p(n-1)} :=  p(n-1) XOR **<strong>0x0j...****0j (j bytes of 0x0j&#39;s)**</strong> XOR **guessed_bytes**

**

**

> So {modified pi} may be ened with either **guessed_bytes** or **<strong>0x0j...****0j (j bytes of 0x0j&#39;s).**</strong>

When guessing the (16-j) th byte, then {modified pi} will always be a valid pad when the attacker attempts XOR with the byte **to the left of (16-j )th** byte., 

A simple solution is to ignore this special case, and run the program [general.py](https://github.com/SeffyVon/Crypto-011/blob/master/ex4_CBCPaddingOracleAttack_General.py) The program return error code 404 when you submit a variable that has the valid padding.

Once the program has found **<strong>0x0j 0j ... ****0j (j bytes of 0x0j&#39;s) **</strong>bytes at the end of the last block, we learn that these bytes actually a valid padding. 

Let&#39;s say it&#39;s **0x08 08 08 08 08 08 08 08** ( 8 bytes of 0x08 &#39;s) as in the program [last_block.py](https://github.com/SeffyVon/Crypto-011/blob/master/ex4_CBCPaddingOracleAttack_LastBlock.py).

I calculate iv+c[0]+c[1] specially, and I already set padding equals to **<strong>0x0j...****0j (j bytes of 0x0j&#39;s) **</strong>, in this case 0x08 08 08 08 08 08 08 08.

> Because I need to verify the padding of the last block when attempts Padding Oracle Attack, so I need to **get rid of the padding <strong>0x08 08 08 08 08 08 08 08**</strong>. **<strong><strong><strong><strong><strong>(8 bytes of 0x08&#39;s)**</strong></strong></strong></strong></strong>

Recalled that to the right of the 8th byte(included),

**{modified c(i-1)} := **c(i-1) XOR **<strong>0x0k 0k ... ****0k (k bytes of 0x0k&#39;s)**</strong> XOR guessed_bytes

But this cass, I xor one more** <strong><strong><strong>0x0j 0j...****0j (j bytes of 0x0j&#39;s)**</strong></strong></strong> to the {modified c(i-1)} when attempting guesses to the left of the (16-j)th byte.

**{modified c(i-1)} := **c(i-1) XOR **<strong>0x0k 0k ... ****0k (k bytes of 0x0k&#39;s) **</strong>XOR guessed_bytes  **XOR <strong><strong><strong><strong><strong><strong>0x08 08 08 08 08 08 08 08**</strong>. **<strong><strong><strong><strong><strong>(8 bytes of 0x08&#39;s)**</strong></strong></strong></strong></strong></strong></strong></strong></strong></strong>

And the attacker submits requests to the server to wait for the padding valid response.

That&#39;s it! The padding oracle attack is there! Just write your own program and try it! 

One more things, let&#39;s analyse the complexity of the algorithm:

For a cipher text of length n bits:

> There are **(n/128+1)** blocks, as the block size is 128 bits per block.
> 
> For each block, scan the 16 bytes one by one from the right. Each bytes try all thebyte from 0x00 to 0xff, 16*16 = 256 attempts/byte. So there are 16*256 = 2^12 bytes.
> 
> So there are O( (n/128+1) * 2 ^12) = **O(n). **(OMG, how amazing! ^^ )

Comparing to Brute Force will be **O(2^n)** in total. 

See more in my github: [Seffy&#39;s github for Crypto](https://github.com/SeffyVon/Crypto-011/)