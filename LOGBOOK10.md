# Hash Length Extension

### Setup

We started by adding 10.9.0.80 www.seedlab-hashlen.com to the etc/hosts file to configure our environment.

Using Docker from Seed Labs, we initiated the server setup with the following commands:

```bash
docker-compose build
docker-compose up
```

In a separate terminal, we set up the client to communicate with the server. This setup allowed us to observe the messages exchanged between the client and the server.

## Task 1

We sent the required string to the server. First, we generated a message authentication code (MAC) using sha256sum. For this, we opened key.txt, selected 1004 as the user ID (UID), and used 88zjxc as the key, with the name set to DavidGustavo.

The command used was:

```bash
echo -n "88zjxc:myname=DavidGustavo&uid=1004&lstcmd=1" | sha256sum
```

The output was: c117a26cbfd88693ebb7779c7238b4321ba5732f015a707c1c14ec2f04b0144a

This resulted in the following URL:
http://www.seedlab-hashlen.com/?myname=DavidGustavo&uid=1004&lstcmd=1&mac=c117a26cbfd88693ebb7779c7238b4321ba5732f015a707c1c14ec2f04b0144a


![list_files](/docs/images/listing.png)


### Downloading a File:

To request secret.txt from the server, we repeated the process with an additional download parameter:

```bash
echo -n "88zjxc:myname=DavidGustavo&uid=1004&lstcmd=1&download=secret.txt" | sha256sum
```

The output was:
0930eecd64b273ed602bb3e1246013622a1ffcb437c1b1df63dce97e027853ae

Resulting URL:
http://www.seedlab-hashlen.com/?myname=DavidGustavo&uid=1004&lstcmd=1&download=secret.txt&mac=0930eecd64b273ed602bb3e1246013622a1ffcb437c1b1df63dce97e027853ae

![download_file](/docs/images/download.png)

## Task 2: Padding the Message

- The message is 88zjxc:myname=DavidGustavo&uid=1004&lstcmd=1
- Length of the message: 44 bytes.

So the padding is 64 - 44 = 20 bytes, including 8 bytes of the length field. The length of the message in term of bits is 44 * 8 = 352 = 0x160. 

To perform the length extension attack, we calculated the following padding: %80%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%01%60

## Task 3: Executing the Length Extension Attack

Using length_ext, we calculated a new MAC for the modified message:

![length_ext](/docs/images/length_ext.png)

```bash
./length_ext 
```

The output was: 76e7363f845d6af9e5ee2a32c109459df67c186da111b5842d8932317f0406d7

The final URL generated using the modified MAC and padding was: http://www.seedlab-hashlen.com/?myname=DavidGustavo&uid=1004&lstcmd=1%80%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%01%60&download=secret.txt&mac=76e7363f845d6af9e5ee2a32c109459df67c186da111b5842d8932317f0406d7

![exploit_done](/docs/images/exploit_done.png)

This demonstrates the ability to modify the request while maintaining a valid MAC, thereby exploiting the vulnerability in the hashing mechanism.



