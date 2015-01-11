---
layout: post
title:  "Cracked Stream Cipher with Many-Time Pad"
date:   2014-08-19 08:08
categories: Cryptography
tags: ["Cryptography","Python"]
---

In the first Programming Assignment of Crypto, we are required to break a stream cipher with many-time pad. 

> **Stream Cipher:**
> 
> **c := E(k,m) = m XOR G(K)**
> 
> In this case, c is the cipher text, m is the plain text, the k is the key, and the G is a Pseudo Random Generator to generate a pad that has the equal length with m.

This cipher is safe when using only one-time, but is not safe when using many-time. And we call reusing G(k) (suppose G is deterministic) to encrypt more than one plain text **Stream Cipher with Many-Time Pad**, we can easily recover (partially or completely) the plaintext.

> **Stream Cipher with Many-Time Pad:**
> 
> For c1 := m1 xor G(K), c2 := m2 XOR G(K), **c1 xor c2 := m1 xor m2**

The task is to decrypt the cipher text c10 from 11 different plaintext (with only alpha and space), supposed it is c0, c2, ..., c10 encrypted using Stream Cipher with the same pad. 

So, how can we make use of this property? Let&#39;s make some analysis on special ASCII. So we try XOR the ciphertexts together, and consider what happens when a space is XORed with acharacter in [a-zA-Z]. 

> ASCII code of Space is: 0x20
> 
> ASCII code of A-Z are: 0x41~0x5A
> 
> ASCII code of a-z are: 0x61~0x7A

We can observe different behavior when XOR with others:

> **Space** XOR [A-Za-z] := [a-zA-Z]
> 
> **Space** XOR space := 0
> 
> **[A-Za-z]** XOR [A-Za-z] := 0b0xxx xxxx := [0x00-0x7E]
> 
> **[A-Za-z]** XOR space := [a-zA-Z]

we can see that: 

> **Space XOR with others, will be all [a-zA-Z]** 
> 
> whereas 
> 
> **[A-Za-z] XOR with others, will be anything from[0x00-0x7E], i.e. the first 126 characters in ASCII table will all be possible.**

So it is very easy to understand the difference and write the code. We wrote a function to test XOR, and to see if it is space:

{% highlight python3 linenos %}
def isAlphaNum(result):
	if result>0x60 and result<0x7b or result>0x40 and result<0x5b or result == 0: 
		return True
	return False
 
def isSpace(chr0, chrs): #(int, list of int, int)
	mask = 0xe0
	for tmp in chrs:
		result = tmp ^ chr0
		if isAlphaNum(result) == False:
			return False
	# It is a space. We should use it to decrypt c11 '?'
	return True
{% endhighlight %}

And another benefit of the space is that once we know the plaintext pi character pi[j] is Space, we can get the plain text p11 by:

> **<strong> p11[j] := ci[j] XOR c11[j] XOR ASCII(Space)</strong>**

So we just decrypt the c11[0] to c11[len(c11)-1] one by one, by looking 

> **For each c11[i], see if there is any space from p0[i] to p11[i].**
> 
> If there is, then use it to decrypt c11[i]. 
> 
> If there isn&#39;t then give it a special sign (e.g. star) to mark it is still encrypted.

By this we can decrypt many characters in c11.

For the characters left, we can also test if there is any Space in the undecrypted position. We can now see most of the plaintext characters in the cipher.

Here you are! The whole version will be found available in my git. 

[My Git For Crypto-011](https://github.com/SeffyVon/Crypto-011)