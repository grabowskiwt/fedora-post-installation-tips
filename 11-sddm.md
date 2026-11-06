[< Previous: Zsh Shell & Plugins Setup](10-zsh.md) | [Next: System Theming & Appearance >](12-theming.md)

---

# 11. SDDM Login Manager Configuration

This section is optional. Sway can be started from a TTY or another
login manager; SDDM is not required.

If you want to use SDDM as your graphical login manager, install it:
```shell
sudo dnf install sddm
```

If another display manager is enabled, disable it before enabling SDDM.
Enable SDDM for the next boot:
```shell
sudo systemctl enable sddm.service
```

After rebooting, select the Sway session on the login screen.

---

[< Previous: Zsh Shell & Plugins Setup](10-zsh.md) | [Next: System Theming & Appearance >](12-theming.md)
