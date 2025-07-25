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

# Alias git (add, commit, push) 
```
git config --global alias.acp '!f() { git add . && git commit -m "$1" && git push origin main; }; f'
```

# Explain linux
## Kernel
Là nhân hệ điều hành.
## Cấu trúc thu mục
```
/
├── bin        -> Chứa các lệnh cơ bản (ls, cp, mv, cat...)
├── boot       -> Tập tin khởi động hệ thống (GRUB, kernel)
├── dev        -> Thiết bị ảo (ổ cứng, USB, thiết bị đầu vào)
├── etc        -> Tập tin cấu hình hệ thống và dịch vụ
├── home       -> Thư mục cá nhân của người dùng thường
│   └── user1  -> Ví dụ: /home/alice, /home/bob
├── lib        -> Thư viện dùng cho chương trình trong /bin và /sbin
├── lib64      -> Tương tự /lib nhưng cho hệ thống 64-bit
├── media      -> Mount thiết bị gắn ngoài tự động (USB, CD...)
├── mnt        -> Mount thiết bị thủ công (tạm thời)
├── opt        -> Chứa phần mềm bên thứ ba (Chrome, Zoom...)
├── proc       -> Hệ thống tệp ảo, thông tin về tiến trình và kernel
├── root       -> Thư mục cá nhân của tài khoản root (superuser)
├── run        -> Dữ liệu tạm thời (runtime), khởi tạo khi boot
├── sbin       -> Lệnh hệ thống dành cho quản trị viên (fdisk, shutdown...)
├── srv        -> Dữ liệu phục vụ dịch vụ (web, ftp, ...)
├── sys        -> Hệ thống tệp ảo hiển thị thông tin phần cứng
├── tmp        -> Tập tin tạm, xóa khi khởi động lại
├── usr        -> Ứng dụng và thư viện của người dùng
│   ├── bin    -> Lệnh không thiết yếu khi khởi động (gcc, firefox...)
│   ├── lib    -> Thư viện cho /usr/bin
│   └── share  -> Dữ liệu dùng chung (icons, docs…)
└── var        -> Dữ liệu thay đổi thường xuyên (log, mail, cache)
```
## Disk
Xem các ổ đĩa hiện có, dung lượng và mount point
```
lsbk 
```
* sda: ổ đĩa thứ nhất (có thể được chia ra là sda1, sda2,...)
Kiểm tra phân vùng dạng bảng (MBR/GPT)
```
sudo parted -l
```
## Boot 
Bao gồm 2 loại 
1. UEFI (unified extensible firmware interface)
2. Legacy icons
| Đặc điểm               | **UEFI**                            | **BIOS (Legacy)**                             |
| ---------------------- | ----------------------------------- | --------------------------------------------- |
| Giao diện              | Giao diện đồ họa, dùng chuột        | Chỉ hỗ trợ bàn phím, giao diện văn bản        |
| Kiểu phân vùng         | GPT (GUID Partition Table)          | MBR (Master Boot Record)                      |
| Hỗ trợ ổ cứng          | >2 TB (qua GPT)                     | Tối đa 2 TB                                   |
| Phân vùng khởi động    | **/boot/efi** (định dạng FAT32)     | Không có hoặc dùng phân vùng `/boot` đơn giản |
| Tốc độ khởi động       | Nhanh hơn                           | Chậm hơn                                      |
| Hệ điều hành hỗ trợ    | Linux, Windows 8 trở lên, macOS mới | Cũ hơn, Windows 7 trở về trước                |
| Tên gọi trong firmware | "UEFI"                              | "Legacy Boot" hoặc "CSM"                      |
| Vị trí lưu bootloader  | Trong EFI System Partition (ESP)    | Trong MBR (sector đầu tiên của ổ đĩa)         |

