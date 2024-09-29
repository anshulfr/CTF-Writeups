**Title**: OG Fermat  
**Category**: Cryptography

---

## **Problem Statement**

It is impossible for any number which is a power greater than the second to be written as a sum of two like powers. I have a truly marvelous demonstration of this proposition which this margin is too narrow to contain.

`Author: Abu`

[chall](Files/chall)
 
---

## **Solution**

We are given three variables:`n`, `e` and `c`, where the last four digits of `c` are missing. These variables and challenge's name points RSA cryptography. In order to find the message, we first need to find the values of prime numbers `p` and `q`. As we are given `n`, we can decompose this using [this tool](https://www.alpertron.com.ar/ECM.HTM). After obtaining the values of `p` and `q`, we need to find the value of Euler's Totient.

```py
tot = (p-1)*(q-1)
```

Now we can easily calculate d using this formula

```py
d = pow(e, -1, tot)
```

As the last four digits of `c` are unknown, we will brute force all possible combinations and check for the flag.

```py
for i in range(10000):
    c = c_given + f'{i:04d}'
    c = int(c)
    m = long_to_bytes(pow(c,d,n))
    if m.startswith(b'H7CTF'):
        print(m)
        break
```

After brute forcing we received `b'H7CTF{f3rm@t_r3@lly_l2f7_@!!_7h3se_unpr0v3d!}'` at `i = 8994`

So our final flag is `H7CTF{f3rm@t_r3@lly_l2f7_@!!_7h3se_unpr0v3d!}`

Here is the final [script](Files/script.py).
