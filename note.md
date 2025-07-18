# IP
-	Ubutu: `192.168.30.40`
-	Raspberry Pi Os: `192.168.30.44`
# SSH send file
> [!NOTE]
> uncomment: PasswordAuthentication yes
> uncomment: PubkeyAuthentication yes
> change password: sudo passwd quang
-   Send file `hello` to raspberry pi: 
        ```
        scp hello quang@192.168.30.44:/home/quang/hello
        ```
-   Send folder `hello` to raspberry pi: 
        ```
        scp -r /path/to/local/hello username@raspberrypi_ip:/path/to/remote/destination
        ```

    > [!NOTE]
    > `-r` is all file in folder hello.
# SSH control 
-   Ubutu user control raspberry pi os:
        ```
        ssh quang@192.168.30.44
        ```
---
# ARM compile (toolchain)
-   Install:
        ```
        sudo apt update
        sudo apt install gcc-aarch64-linux-gnu g++-aarch64-linux-gnu
        ``` 
-   Compile: 
        ```
        arch64-linux-gnu-gcc hello.c -o hello_rpi
        ```
---
# Cross compile
## Raspberry Pi Os 
    -   Ver:
        -   gcc: 10.2.1
        -   ld:  2.35.2 
        -   ldd: 2.31
    -   Ver (now):
        -   gcc: 12.2.0 
        -   ld:  2.40 
        -   ldd: 2.36 
## Ubuntu 
    -   Ver:
        -   gcc: 10.3.0 
        -   ld:  2.35.2 
        -   ldd: 2.31 
    -   Ver (now):
        -   gcc: 12.2.0 
        -   ld (binutils):  2.40 
        -   ldd (glibc): 2.36 

[!NOTE] We same Ubuntu and raspberry pi os ver gcc ld ldd 
>   Ver (now): gcc: 12.2.0 ld:  2.40 ldd: 2.36 

--- 
# See a path: `$PATH` ``` echo "$PATH" | tr ':' '\n' ```
