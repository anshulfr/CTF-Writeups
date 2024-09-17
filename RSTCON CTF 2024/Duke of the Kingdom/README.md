**Title**: Duke of the Kingdom  
**Category**: Reverse Engineering

---

## **Problem Statement**

I love this little guy so much. Check out [the program](https://metaproblems.com/b9717b5a41c0c046391251517982739a/Duke.class) I made for them.

---

## **Solution**

We received a `.class` file. When the file is executed, it displays a text diagram of Duke, the Java Mascot, along with the message:  
`Duke is waving at you! Say Hi to Duke.`

The program responds with different outputs based on user input, as shown below:

```sh
Duke is waving at you! Say Hi to Duke.
> hi
Hi!

Duke is waving at you! Say Hi to Duke.
> hi duke
Aw, Duke liked that! Hi!

Duke is waving at you! Say Hi to Duke.
> hello
:(
```

To analyze the file, I imported it into Ghidra, which generated the decompiled code. Here's a simplified version of the logic used to retrieve the flag:

```java
bVar5 = pSVar3.contains("hi duke");
if (bVar5) {
    pPVar2 = System.out;
	pPVar2.println("Aw, Duke liked that! Hi!");
    pbVar4 = pSVar3.getBytes();
    piVar1 = new int[0x45];
    piVar1[0] = 0x3da;
    ...
    piVar1[0x44] = 0x497;
    if (pbVar4.length != piVar1.length) {
	    return;
    }
    iVar6 = 0;
    while( true ) {
	    if (pbVar4.length <= iVar6) break;
	    if (pbVar4[iVar6] * 9 + 0x32 != piVar1[iVar6]) {
		    return;
	    }
	    iVar6 = iVar6 + 1;
    }
    pPVar2 = System.out;
    pPVar2.println("And Duke LOVES the flag! Go submit it!");
}
```

The code multiplies each byte in the `pbVar` array by 9, adds `0x32`, and compares it with the corresponding value in `piVar1`. Since `pbVar` likely contains the flag, we can reverse the operation to retrieve it.

Here's a Python script to reverse the process and obtain the flag:

```py
values = [0x3da, 0x3e3, 0x152, 0x3b6, 0x44f, 0x3f5, 0x3bf, 0x152, 0x3da, 0x3bf, 0x434, 0x3bf, 0x191, 0x43d, 0x152, 0x39b, 0x152, 0x3c8, 0x3fe, 0x39b, 0x3d1, 0x152, 0x3c8, 0x419, 0x434, 0x152, 0x473, 0x419, 0x44f, 0x152, 0x407, 0x3bf, 0x446, 0x39b, 0x3ad, 0x446, 0x3c8, 0x485, 0x3b6, 0x44f, 0x3f5, 0x1fd, 0x389, 0x39b, 0x446, 0x389, 0x446, 0x3da, 0x1fd, 0x389, 0x446, 0x1e2, 0x422, 0x389, 0x1e2, 0x3c8, 0x389, 0x446, 0x3da, 0x1fd, 0x3e3, 0x434, 0x389, 0x3ad, 0x3fe, 0x206, 0x43d, 0x43d, 0x497]
flag = ""

for val in values:
    flag += chr((val - 0x32) // 9)
print(flag)

```

Running this script produces the following output:
`hi duke here's a flag for you metactf{duk3_at_th3_t0p_0f_th3ir_cl4ss}`

The final flag is `metactf{duk3_at_th3_t0p_0f_th3ir_cl4ss}`
