# refind-config

rEFInd 配置文件备份及一些问题的记录。

## LinuxMint 安装后未被 rEFInd 识别的解决办法

原因：LinuxMint 现阶段使用的是 Ubuntu 的引导，它的引导文件会写入 /EFI/Boot，导致 rEFInd 将其忽略。

1. 从 Ventoy 启动 LinuxMint 镜像，进入 Live 环境

2. 启动终端

3. 查看分区

   ```bash
   lsblk -f
   ```

4. 查看分区大小

   ```bash
   lsblk
   ```

5. 将安装 LinuxMint 的分区挂载到 `/mnt`

   ```bash
   sudo mount /dev/nvme0n1p5 /mnt
   ```

   其中 `/dev/nvme0n1p5` 是 LinuxMint 安装的分区

6. 将 EFI 分区挂载到 `/mnt/boot/efi`

   ```bash
   sudo mount /dev/nvme0n1p1 /mnt/boot/efi
   ```

   其中 `/dev/nvme0n1p1` 是 EFI 分区

7. 在 EFI 分区中的 `EFI` 文件夹下新建子文件夹 `LinuxMint`，用于存储引导程序

   ```bash
   sudo mkdir /mnt/boot/efi/EFI/LinuxMint
   ```

8. 安装 grub 引导到 EFI/LinuxMint 中

   ```bash
   sudo grub-install --root-directory=/mnt --efi-directory=/mnt/boot/efi/EFI/LinuxMint
   ```

   EFI 文件夹下有多个子文件夹，每个子文件夹都存放一个操作系统的启动文件，这样便于 rEFInd 配置。

9. 在 `refind.conf` 中手动添加启动项

   ```txt
   menuentry "LinuxMint" {
       icon /EFI/refind/themes/rEFInd-glassy/icons/os_linuxmint.png
       loader /EFI/LinuxMint/EFI/ubuntu/shimx64.efi
   }
   ```
