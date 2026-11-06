[< Previous: Main Page](README.md) | [Next: DNF Optimization >](1-dnf.md)

---

# 0. Systemd-boot & Secure Boot Setup (`sbctl`)

## **Install sbctl via Copr:**
```shell
sudo dnf copr enable chenxiaolong/sbctl
sudo dnf install sbctl
```

## **Enter Setup Mode in UEFI (BIOS)**

This section assumes systemd-boot is already installed.

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

## **Sign Bootloader and Kernels:**
```shell
sudo sbctl sign -s /boot/efi/EFI/systemd/systemd-bootx64.efi
sudo sbctl sign -s /boot/vmlinuz-$(uname -r)
```

## **Verify Status:**

Reboot and enable Secure Boot in UEFI after enrolling the keys and
signing the boot files.

```shell
sbctl status
sudo sbctl verify
```
*(Expected output: `Secure Boot: Enabled`)*

The command above signs only the currently running kernel.
After installing a new kernel or updating the bootloader, ensure the
new boot files are signed before booting them with Secure Boot enabled.
Use `sudo sbctl verify` to check signatures.

## **Showing Windows entry from another drive while dual-booting:**

List partitions and identify the Windows EFI System Partition:
```shell
sudo fdisk -l
```

Mount it read-only and copy the Microsoft EFI boot files.
Replace the example device with the Windows EFI System Partition.
This example assumes the systemd-boot ESP is mounted at `/boot/efi`:
```shell
sudo mkdir -p /mnt/winefi
sudo mount -o ro /dev/nvme0n1p2 /mnt/winefi
sudo cp -a /mnt/winefi/EFI/Microsoft /boot/efi/EFI/
sudo umount /mnt/winefi
sudo rmdir /mnt/winefi
```

This creates a separate copy. Windows updates on the original ESP
will not automatically update the copied files.

---

[< Previous: Main Page](README.md) | [Next: DNF Optimization >](1-dnf.md)
