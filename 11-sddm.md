[< Previous: Zsh Shell & Plugins Setup](10-zsh.md) | [Next: System Theming & Appearance >](12-theming.md)

---

# 11. SDDM Login Manager Configuration

Optional: Sway can start from a TTY or another display manager.

Install SDDM:
```shell
sudo dnf install sddm
```

Disable any other display manager, then enable SDDM:
```shell
sudo systemctl enable sddm.service
```

After reboot, select Sway at the login screen.

---

[< Previous: Zsh Shell & Plugins Setup](10-zsh.md) | [Next: System Theming & Appearance >](12-theming.md)
