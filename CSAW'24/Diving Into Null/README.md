**Title**: Diving Into Null  
**Category**: Pwn

---

## **Problem Statement**

Oops, I rm -rf 'ed my binaries

`author: sproutmaster`

`nc null.ctf.csaw.io 9191`

---

## **Solution**

In this challenge, we are dropped into a minimal container environment with limited access to typical Linux commands and utilities.

```sh
anshul@Anshul:~$ nc null.ctf.csaw.io 9191
bash: cannot set terminal process group (1): Inappropriate ioctl for device
bash: no job control in this shell
groot@diving-into-null-6859fcf8d4-gl2bw:/$ 
```

I connected to the shell and began testing basic commands like `ls`, `cd`, `cat`, etc., but none of them worked except for `echo`. With only `echo` available, I had to explore the file system and find the flag.

So, I began my search with exploring the directories and files using `echo`. This command helps you see what files and directories are available without using commands like `ls`.

```sh
groot@diving-into-null-6859fcf8d4-gl2bw:/$ echo *
echo *
bin boot dev etc home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var
groot@diving-into-null-6859fcf8d4-gl2bw:/$ echo home/*
echo home/*
home/bin home/groot
groot@diving-into-null-6859fcf8d4-gl2bw:/$ echo home/groot/*
echo home/groot/*
home/groot/*
```

Within the `home` directory, I found a user directory named `groot`, which appeared to be empty. Let's check for hidden files.

```sh
groot@diving-into-null-6859fcf8d4-gl2bw:/$ echo home/groot/.*
echo home/groot/.*
home/groot/.bash_history home/groot/.bashrc home/groot/.flag home/groot/.profile
```

Interesting, there is a file named `.flag`. Since `cat` wasn’t available, I used `echo` to display the contents of the `.flag` file:

```sh
groot@diving-into-null-6859fcf8d4-gl2bw:/$ echo $(<home/groot/.flag)
echo $(<home/groot/.flag)
||||||||||||||||||||||||||||||||||||||||||||||||||||| ||| csawctf{penguins_are_just_birds_with_tuxedos} ||| |||||||||||||||||||||||||||||||||||||||||||||||||||||
```

So, our flag is `csawctf{penguins_are_just_birds_with_tuxedos}`
