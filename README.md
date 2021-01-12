### Backup any Unix fs

> **IMPORTANT:** Be sure to add your mounting path to `exclude-list` before going any further!

Usage :

`sudo rsync -aAXv --exclude-from=exclude-list.txt / <distant-path>`

Restore backup by inverting `/` and `<distant-path>`

*Tip*: distant path can be accessed through *ssh* or it can also be a local path.

### Migrating to another HDD/SSD

You'll have to re-generate your Grub/EFISTUB according to your hardware.

> Nowadays you're more probably about to install it on a GPT SSD bootable in UEFI mode.

Be sure to format partitions accordingly

#### EFI system partition

Be sure to have `dosfstools`, `grub`, `efibootmgr` and something to fake root like `arch-chroot` from `arch-install-scripts`.

- Create a first partition of ~350M with EFI Filesystem
- Format it in FAT32 (in order to be able to mount it): `mkfs.fat -F32 /dev/..`
- Mount that partition and copy `/boot/{vm-linuz,initramfs*}` inside
- Mount your root partition
- Generate the corresponding `fstab`: `genfstab -U /mnt/ >> /mnt/etc/fstab` (let's assume root is mounted on `/mnt`)

If you want **grub** to be installed (ie: for dualboot purposes) follow these steps:

- Chroot into your new root partition `/` and install grub this way: `grub-install --target=x86_64-efi --efi-directory=EFI_MOUNTED_DIR --bootloader-id=GRUB`
- Generate your grub config: `grub-mkconfig -o /boot/grub/grub.cfg`

Otherwise you can boot through **UEFI** directly by generating a new entry: `efibootmgr --disk /dev/sdX --part Y --create --label "Arch Linux" --loader /vmlinuz-linux --unicode 'root=PARTUUID=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX rw initrd=\initramfs-linux.img' --verbose`

- Y should be 1
- You can find right `PARTUUID` by looking into `/dev/disk`: `ls -l /dev/disk/by-partuuid/`

Finally, do not forget to re-order your boot order wisely!
