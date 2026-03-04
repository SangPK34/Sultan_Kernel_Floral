# Sultan Kernel (Floral) Build Guide

## 1. Cài đặt môi trường (Fix các lỗi thiếu tool)

```bash
sudo apt update
sudo apt update
sudo apt install bc bison build-essential flex g++-multilib gcc-multilib git gnupg gperf libncurses-dev libreadline-dev libssl-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev lib32z1-dev libelf-dev
sudo apt install device-tree-compiler python-is-python3 -y
sudo apt install python2 lz4 liblz4-tool zip -y
```
# Link fake python2 để dùng python3
```bash
sudo ln -s /usr/bin/python3 /usr/bin/python2
```
Check: *`python2 --version`* xem nó có hiện ra Python 3.x.x không.
---

## 2. Chuẩn bị Toolchain & Source
Tạo thư mục làm việc:
```bash
mkdir -p ~/Sangdz/workspace/toolchains
cd ~/Sangdz/workspace
```
Clone repo:
```bash
git clone --depth=1 https://github.com/SangPK34/Sultan_Kernel_Floral.git
```

Thiết lập tools:
```bash
cd ~/Sangdz/workspace/toolchains
#Tải và dọn:
curl -L -O "https://developer.arm.com/-/media/Files/downloads/gnu/12.2.rel1/binrel/arm-gnu-toolchain-12.2.rel1-x86_64-aarch64-none-linux-gnu.tar.xz"

wget -U "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36" https://developer.arm.com/-/media/Files/downloads/gnu/12.2.rel1/binrel/arm-gnu-toolchain-12.2.rel1-x86_64-arm-none-linux-gnueabihf.tar.xz

tar -xf arm-gnu-toolchain-12.2.rel1-x86_64-aarch64-none-linux-gnu.tar.xz
rm arm-gnu-toolchain-12.2.rel1-x86_64-aarch64-none-linux-gnu.tar.xz
tar -xf arm-gnu-toolchain-12.2.rel1-x86_64-arm-none-linux-gnueabihf.tar.xz
rm arm-gnu-toolchain-12.2.rel1-x86_64-arm-none-linux-gnueabihf.tar.xz
```
# Thiết lập biến môi trường (PATH này trỏ thẳng tới toolchain vừa giải nén)
```bash
cd ~/Sangdz/workspace/Sultan_Kernel_Floral

export ARCH=arm64
export CROSS_COMPILE=~/Sangdz/workspace/toolchains/arm-gnu-toolchain-12.2.rel1-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
export CROSS_COMPILE_ARM32=~/Sangdz/workspace/toolchains/arm-gnu-toolchain-12.2.rel1-x86_64-arm-none-linux-gnueabihf/bin/arm-none-linux-gnueabihf-

# Kiểm tra xem "thằng đệ" GCC đã sẵn sàng chưa
${CROSS_COMPILE}gcc --version
```
---

## 3. Cấu hình & Build

```bash
make O=out floral_defconfig

# Bypass lỗi dtbo/python2
make O=out -j$(nproc) Image.lz4
```

---

## 4. Đóng gói bằng AnyKernel3
Chui vào folder chứa sẵn Anykernel xóa lõi cũ, nhét lõi mới rồi đóng gói
```bash
cd ~/Sangdz/workspace/Sultan_Kernel_Floral/Sultan-R-20220616
rm Image.lz4
cp ~/Sangdz/workspace/Sultan_Kernel_Floral/out/arch/arm64/boot/Image.lz4 .
zip -r9 ../Custom-Kernel-CucThan_4XL.zip *
```
