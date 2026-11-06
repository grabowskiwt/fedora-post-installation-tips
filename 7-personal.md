[< Previous: Firmware Updates](6-firmware.md) | [Next: System Fonts Setup >](8-fonts.md)

---

# 7. Personal Tools & Tweaks

Install essential personal utilities:
```shell
sudo dnf install net-tools pip htop fastfetch kitty git unzip btop zathura feh ranger vim emacs figlet lolcat perl-Archive-Extract-lzma-IO-Uncompress-UnLzma tar xz p7zip zip gzip cpio unace inxi stow sl cmus
```

Set hardware clock to UTC:
```shell
sudo timedatectl set-local-rtc '0'
```

Disable terminal bell (pcspkr):
```shell
echo "blacklist pcspkr" | sudo tee /etc/modprobe.d/blacklist-pcspkr.conf > /dev/null
```

---

[< Previous: Firmware Updates](6-firmware.md) | [Next: System Fonts Setup >](8-fonts.md)
