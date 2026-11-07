[< Previous: Main Page](README.md) | [Next: DNF Optimization >](1-dnf.md)

---

# 0. Secure Boot for an Existing systemd-boot Installation (`sbctl`)

## **Install sbctl via Copr:**
```shell
sudo dnf copr enable chenxiaolong/sbctl
sudo dnf install sbctl
```

## **Enter Setup Mode in UEFI (BIOS)**

For an existing systemd-boot setup only; this guide does not install or
convert Fedora's default GRUB bootloader. Identify the active boot files:
```shell
bootctl status
bootctl list
lsblk -o NAME,FSTYPE,UUID,MOUNTPOINTS
```
Continue only if `bootctl status` shows systemd-boot as the active
bootloader; its presence on the ESP alone is not sufficient.

ESP and kernel paths vary; do not assume `/boot/vmlinuz-*` or
`/boot/efi`.

Use the vendor's firmware procedure to enter Setup Mode, normally by
removing the Platform Key (PK). Disabling Secure Boot alone is not
enough; menu names vary by device.

Before changing keys, know how to restore factory keys. Have the
BitLocker/device-encryption recovery key available if applicable.

Boot Linux again and check:
```shell
sbctl status
```

Confirm Setup Mode before enrolling keys.

## **Create and Enroll Keys:**
```shell
sudo sbctl create-keys
sudo sbctl enroll-keys -m
```
(`-m` also enrolls Microsoft keys for Windows dual-boot.)

## **Sign the Active Bootloader and Kernel Images:**

Use `bootctl status`, `bootctl list`, ESP mount points, and `sbctl verify`
to identify unsigned boot files. Replace example paths and sign only
the files used by your boot entries:
```shell
sudo sbctl sign --save /actual/path/to/systemd-bootx64.efi
sudo sbctl sign --save /actual/path/to/kernel-image
sudo sbctl sign-all
```

`sign-all` re-signs saved files; it does not discover new versioned
kernel paths. After updates, inspect `sbctl verify` and save new paths.

## **Verify Status:**

After enrolling keys and signing boot files, enable Secure Boot in UEFI
and reboot.

```shell
sbctl status
sudo sbctl verify
```
*(Expected output: `Secure Boot: Enabled`)*

After kernel or bootloader updates, verify and sign new files before
booting with Secure Boot enabled; verify signatures again.

## **Showing Windows entry from another drive while dual-booting:**

Identify the Windows EFI System Partition:
```shell
sudo fdisk -l
```

Mount it read-only and copy its Microsoft boot files. Replace the
example device and `/path/to/esp` with your actual partitions/mount point:
```shell
sudo mkdir -p /mnt/winefi
sudo mount -o ro /dev/nvme0n1p2 /mnt/winefi
sudo cp -a /mnt/winefi/EFI/Microsoft /path/to/esp/EFI/
sudo umount /mnt/winefi
sudo rmdir /mnt/winefi
```

This copy will not receive Windows boot-file updates automatically.

---

[< Previous: Main Page](README.md) | [Next: DNF Optimization >](1-dnf.md)
