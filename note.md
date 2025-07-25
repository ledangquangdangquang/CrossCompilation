# IP
-	Ubutu: `192.168.30.40`
-	Raspberry Pi Os: `192.168.30.44`
# SSH send file
> [!NOTE]
> * uncomment: PasswordAuthentication yes
> * uncomment: PubkeyAuthentication yes
> * change password: sudo passwd quang
-   Send file `hello` to raspberry pi: 
```
scp hello quang@192.168.30.44:/home/quang/hello
```
-   Send folder `hello` to raspberry pi: 
```
scp -r /path/to/local/hello username@raspberrypi_ip:/path/to/remote/destination
```

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
# See a path `$PATH` 
```
echo "$PATH" | tr ':' '\n' 
```

# Alias
```
git config --global alias.acp '!f() { git add . && git commit -m "$1" && git push origin main; }; f'
```
