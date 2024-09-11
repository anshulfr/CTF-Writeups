**Title**: Covert  
**Category**: Forensics

---

## **Problem Statement**

It appears there's been some shady communication going on in our network...

`author: cpan57`

Attachments:
- [chall.pcapng](files/chall.pcapng)
- [keys.log](files/keys.log)


---

## **Solution**


We have received a `.pcapng` and `.log` file. On looking at the pattern of the keys.log, we can identify that it contains multiple TLS session keys used to encrypt and decrypt traffic in the TLS sessions. To decrypt the TLS sessions using this log, we will open Wireshark and go to `Edit > Preferences > Protocols > TLS > (Pre)-Master-Secret log filename` and add this log file.

![](https://i.imgur.com/j85DAdD.png)

With the traffic decrypted, the next step was to analyze the packets for any clues. First, I came across this [URL](https://www.csawcovert.xyz/). 

![](https://i.imgur.com/4wTgt8W.png)

This URL shows us the method for encoding a covert message and transmitting it over a network using the Scapy library.

![](https://i.imgur.com/oU8OVWX.jpg)

Another URL I found is of this [PDF](https://people.cs.georgetown.edu/~clay/classes/spring2009/555/papers/Embedding_Covert_Channels_into_TCPIP.pdf) explaining how covert channels can be embedded in TCP/IP headers (like sequence numbers, IDs, or timestamps). This suggested that the secret message might be hidden in the traffic, specifically in the IP ID fields of the packets.

I focused on these packets with TCP Retransmission between `172.20.10.5` and `172.57.57.57` only.

I created a new IP ID column from `Edit > Preferences > Appearance > Columns` and added a new column with the Title `IP ID`, Type `Number` and Fields `ip.id`.

![](https://i.imgur.com/wnERtzr.jpg)

Next, I used `tshark` to copy the IP IDs of the packets with sequence numbers 265 to 305.

```sh
tshark -r chall.pcapng -Y "frame.number >= 265 && frame.number <= 305" -T fields -e ip.id | xclip -sel clip
```

Now we will write the script and for decoding message.

In the encoding script, all of these numbers are multiplied with a common number i.e., key. So the common number could be the GCD of all numbers.

```py
key = reduce(gcd, ids)
```

Next step is to divide each character with the key and append to the result.

```py
res = ""
for id in ids:
    res += chr(int(id/key))
print(res)
```

On running the script, we will obtain this flag `csawctf{licen$e_t0_tr@nsmit_c0vertTCP$$$}`

[Here](files/decode.py) is the final script.
