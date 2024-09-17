**Title**: The Internet is for Cats  
**Category**: Other

---

## **Problem Statement**

My home network is kind of weird. I have a server somewhere on it with some fun gifs and some secret data, but I can't remember where it was. Also, I forgot to install a lot of software on my client machine... it's kinda bare bones.

Use `ssh -p 7001 ctf-e9d70d8ac240@ssh.dyn.mctf.io` to connect to my network. (A unique network and challenge instance will be spawned when you connect, and will be destroyed when you disconnect.)

---

## **Solution**

In this challenge, we are provided with a command to connect to the network using SSH. On connecting, an instance is spawned which will destroy on disconnecting.

```sh
anshul@Anshul:~$ ssh -p 7001 ctf-e9d70d8ac240@ssh.dyn.mctf.io
Welcome, competitor #d22-vyL0pkAr1Y. This session will force-close after 40 minutes.
Each SSH connection spawns a fresh network for you. Disconnect and reconnect to make a new network.
Please wait, spawning network...
[######################################]

Granting console access...
root@c6f946c603fa:~#
```

I started by checking the current directory and found a file named `password.txt`. The credentials were stored in this file, but I wasn't sure where to apply them yet.

```sh
root@0ee903e0999a:~# cat password.txt
Writing down so I don't forget it...

Username: jdoe
Password: rockyou
```

Since the challenge mentioned "fun gifs," I searched for `.gif` files on the server and found `pixel.gif`. 

```sh
root@0ee903e0999a:~# find / -type f -name "*.gif"
/usr/share/nmap/nselib/data/pixel.gif
root@0ee903e0999a:~# cat /usr/share/nmap/nselib/data/pixel.gif
GIF89a�������!�!!comment!!,D;
root@0ee903e0999a:~# eog /usr/share/nmap/nselib/data/pixel.gif
bash: eog: command not found
root@0ee903e0999a:~# file /usr/share/nmap/nselib/data/pixel.gif
bash: file: command not found
root@0ee903e0999a:~# exiftool /usr/share/nmap/nselib/data/pixel.gif
bash: exiftool: command not found
```

I attempted to analyze the gif with commands like `file` or `exiftool` but it failed due to missing software. Also, transferring it back to my host system, I came to know that it is the default gif file found in Linux.

I revisited the challenge and noticed the line, "I have a server somewhere on it.". This hinted that a server might be running on the instance itself. To confirm this, I checked the running processes using `ps` to see if any were tied to active services.

```sh
root@0ee903e0999a:~# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  2.0  0.0   4632  3748 pts/0    Ss   21:28   0:00 /bin/bash
root          27  0.0  0.0   7064  1600 pts/0    R+   21:28   0:00 ps aux
```

This didn’t work, so I tried scanning the network with `nmap` using the `-sP` option to discover active hosts.

```sh
root@0ee903e0999a:~# nmap -sP 192.168.0.0/24
Starting Nmap 7.80 ( https://nmap.org ) at 2024-09-16 21:46 UTC
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for 192.168.0.144
Host is up (0.000048s latency).
MAC Address: 02:42:0A:00:2D:C2 (Unknown)
Nmap scan report for 192.168.0.200
Host is up.
Nmap done: 256 IP addresses (2 hosts up) scanned in 4.84 seconds
```

This revealed two active hosts: `192.168.0.144` and `192.168.0.200`. Next, I scanned ports of these hosts.

```sh
root@0ee903e0999a:~# nmap -p- 192.168.0.144
Starting Nmap 7.80 ( https://nmap.org ) at 2024-09-16 22:10 UTC
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for 192.168.0.144
Host is up (0.000014s latency).
Not shown: 65534 closed ports
PORT     STATE SERVICE
5000/tcp open  upnp
MAC Address: 02:42:0A:00:2E:02 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.96 seconds
root@0ee903e0999a:~# nmap -p- 192.168.0.200
Starting Nmap 7.80 ( https://nmap.org ) at 2024-09-16 22:10 UTC
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for 192.168.0.200
Host is up (0.0000090s latency).
All 65535 scanned ports on 192.168.0.200 are closed

Nmap done: 1 IP address (1 host up) scanned in 1.50 seconds
```

The scan revealed that port `5000` was open on `192.168.0.144`, while all ports were closed on `192.168.0.200`. I tried using `curl` but it wasn’t available. Instead, I used `nc` to connect to the service. When `nc 192.168.0.144 5000` didn’t return any data, I manually sent an HTTP request.

```sh
root@0ee903e0999a:~# nc 192.168.0.144 5000
GET / HTTP/1.1
Host: 192.168.0.144

HTTP/1.1 401 Unauthorized
Server: nginx
Date: Tue, 17 Sep 2024 08:09:27 GMT
Content-Type: text/html
Content-Length: 172
Connection: keep-alive
WWW-Authenticate: Basic realm="Basicauth credentials required for access!"

<html>
<head><title>401 Authorization Required</title></head>
<body>
<center><h1>401 Authorization Required</h1></center>
<hr><center>nginx</center>
</body>
```

This returned a `401 Unauthorized` error. I used the credentials provided and encoded them in Base64 with the `Basic` authentication scheme.

```sh
root@0ee903e0999a:~# echo -n 'jdoe:rockyou' | base64
amRvZTpyb2NreW91
```

I added this authorization header to the GET request.

```sh
root@0ee903e0999a:~# nc 192.168.0.144 5000
GET / HTTP/1.1
Host: 192.168.0.144
Authorization: Basic amRvZTpyb2NreW91

HTTP/1.1 200 OK
Server: nginx
Date: Tue, 17 Sep 2024 08:23:33 GMT
Content-Type: text/html
Content-Length: 68
Last-Modified: Sat, 07 Sep 2024 14:13:32 GMT
Connection: keep-alive
ETag: "66dc5f8c-44"
Accept-Ranges: bytes

###########

Almost! Your flag is at /secret_data.html

###########
```

With the flag location revealed, I fetched `/secret_data.html`.

```sh
root@0ee903e0999a:~# nc 192.168.0.144 5000
GET /secret_data.html HTTP/1.1
Host: 192.168.0.144
Authorization: Basic amRvZTpyb2NreW91

HTTP/1.1 200 OK
Server: nginx
Date: Tue, 17 Sep 2024 08:25:08 GMT
Content-Type: text/html
Content-Length: 83
Last-Modified: Sat, 07 Sep 2024 14:13:14 GMT
Connection: keep-alive
ETag: "66dc5f7a-53"
Accept-Ranges: bytes

###########

Good job!

MetaCTF{watch1ng_n3tc4t_gif5_0n_th3_intern3t}

###########
```

The final flag is `MetaCTF{watch1ng_n3tc4t_gif5_0n_th3_intern3t}`
