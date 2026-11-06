[< Previous: Main Page](README.md) | [Next: DNF Optimization >](1-dnf.md)

---

# 0. Secure Boot for an Existing systemd-boot Installation (`sbctl`)

## **Install sbctl via Copr:**
```shell
sudo dnf copr enable chenxiaolong/sbctl
sudo dnf install sbctl
```

## **Enter Setup Mode in UEFI (BIOS)**

This section only configures Secure Boot signing for an existing
systemd-boot installation. Fedora normally installs GRUB; this guide
does not install or convert the bootloader. Confirm that systemd-boot
is installed and identify the active boot files before proceeding:
```shell
bootctl status
bootctl list
lsblk -o NAME,FSTYPE,UUID,MOUNTPOINTS
```
Continue only if `bootctl status` identifies systemd-boot as the
currently used bootloader; a systemd-boot file on the ESP alone does
not mean the system boots through it.

The ESP and kernel image paths depend on the system's boot layout.
Do not assume that the kernel image is at `/boot/vmlinuz-*` or that
the ESP is mounted at `/boot/efi`.

Enter your firmware's Secure Boot settings and follow the vendor's
procedure for entering Setup Mode. This normally requires removing
the Platform Key (PK); disabling Secure Boot alone is not sufficient.
Menu names vary between devices.

Before changing keys, confirm how to restore the factory keys.
If Windows uses BitLocker or device encryption, have its recovery
key available before changing Secure Boot settings.

Boot Linux again and check:
```shell
sbctl status
```

Confirm that Setup Mode is enabled before enrolling your keys.

## **Create and Enroll Keys:**
```shell
sudo sbctl create-keys
sudo sbctl enroll-keys -m
```
*(The `-m` flag also registers Microsoft keys required for Windows dual-boot).*

## **Sign the Active Bootloader and Kernel Images:**

Use `bootctl status`, `bootctl list`, and the ESP mount points to
identify the EFI executables used by the boot entries. Review the
unsigned files reported by `sbctl verify`; sign only the boot files
you have identified. Replace each example path with its actual path:
```shell
sudo sbctl sign --save /actual/path/to/systemd-bootx64.efi
sudo sbctl sign --save /actual/path/to/kernel-image
sudo sbctl sign-all
```

`sign-all` re-signs files already saved in sbctl's database. It does
not discover a new versioned kernel path after a kernel update; inspect
`sbctl verify` and save any new boot image path before relying on it.

## **Verify Status:**

Reboot and enable Secure Boot in UEFI after enrolling the keys and
signing the boot files.

```shell
sbctl status
sudo sbctl verify
```
*(Expected output: `Secure Boot: Enabled`)*

After installing a new kernel or updating the bootloader, check
`sudo sbctl verify` and sign any newly installed boot files before
booting them with Secure Boot enabled. Run `sudo sbctl verify` again
to confirm the signatures.

## **Showing Windows entry from another drive while dual-booting:**

List partitions and identify the Windows EFI System Partition:
```shell
sudo fdisk -l
```

Mount it read-only and copy the Microsoft EFI boot files.
Replace the example device with the Windows EFI System Partition.
Use the actual mount point of the systemd-boot ESP shown by
`bootctl status` and `lsblk` in place of `/path/to/esp`:
```shell
sudo mkdir -p /mnt/winefi
sudo mount -o ro /dev/nvme0n1p2 /mnt/winefi
sudo cp -a /mnt/winefi/EFI/Microsoft /path/to/esp/EFI/
sudo umount /mnt/winefi
sudo rmdir /mnt/winefi
```

This creates a separate copy. Windows updates on the original ESP
will not automatically update the copied files.

---

[< Previous: Main Page](README.md) | [Next: DNF Optimization >](1-dnf.md)
