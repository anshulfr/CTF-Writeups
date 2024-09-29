**Title**: Layered Circuits  
**Category**: Hardware

---

## **Problem Statement**

Layers conceal the flag you seek, decode the PCB to speak.

 [Solve_it_if_you_can.zip](Files/Solve_it_if_you_can.zip)
 
---

## **Solution**

We are given a zip file which contains a `layers.png` and a folder named `ctf gerber` which contains Gerber files (`.gbr`). Gerber files are ASCII, vector formats for PCB design. 

```sh
anshul@Anshul:~/Desktop/ctf/h7tex/hardware$ tree
.
├── Solve_it_if_you_can.zip
├── ctf gerber
│   ├── CTF LED matrix-B_Adhesive.gbr
│   ├── CTF LED matrix-B_Cu.gbr
│   ├── CTF LED matrix-B_Mask.gbr
│   ├── CTF LED matrix-B_Paste.gbr
│   ├── CTF LED matrix-B_Silkscreen.gbr
│   ├── CTF LED matrix-Edge_Cuts.gbr
│   ├── CTF LED matrix-F_Adhesive.gbr
│   ├── CTF LED matrix-F_Cu.gbr
│   ├── CTF LED matrix-F_Mask.gbr
│   ├── CTF LED matrix-F_Paste.gbr
│   ├── CTF LED matrix-F_Silkscreen.gbr
│   ├── CTF LED matrix-In1_Cu.gbr
│   ├── CTF LED matrix-In2_Cu.gbr
│   ├── CTF LED matrix-NPTH-drl.gbr
│   ├── CTF LED matrix-NPTH-drl_map.gbr
│   ├── CTF LED matrix-PTH-drl.gbr
│   ├── CTF LED matrix-PTH-drl_map.gbr
│   ├── CTF LED matrix-drl.rpt
│   └── CTF LED matrix-job.gbrjob
└── layers.jpg
```

I noticed that all the mentioned files in the image were available here, so I viewed them one by one using [gerbv](https://gerbv.github.io/), a tool designed to view these files.

```sh
gerbv CTF\ LED\ matrix-F_Cu.gbr
```

![](https://imgur.com/l8cXijy.png)

I found some Base64 text in the circuit design layer. 

![](https://imgur.com/nhrCcI4.png)

```sh
$ echo "SDdDVEZ7" | base64 -d
H7CTF{
```

This is part of the flag. So I viewed all the mentioned files in `layers.png` image and constructed the flag.

- `SDdDVEZ7 -> H7CTF{`
- `NHIzXw== -> 4r3_`
- `dGgxbmd9 -> th1ng}`
- `Y2g0bGxzXw== -> ch4lls_`
- `aDRyZHcOcjNf -> h4rdwr3_`
- `NF8= -> 4_`
- `MW5kMzNkXw== -> 1nd33d_`

Later, I realized there was a small typo in the flag, so `h4rdwr3_` is actually `h4rdw4r3_`

Rearranging them, we get the final flag: `H7CTF{h4rdw4r3_ch4lls_4r3_1nd33d_4_th1ng}`
