# Fedora Post-Installation Tips & Optimization Guide

Personal Fedora 45 setup notes for Sway: Secure Boot, DNF, codecs,
kernel/scheduler, services, power, firmware, tools, fonts, shell, and theming.
KDE guidance is in the optional final chapter.

## Scope

These instructions target Fedora Linux 45 with Sway as the primary
desktop environment. The final KDE chapter is optional reference
material for a future KDE setup.

## Recommended Power Management

- **Desktop PC:** TuneD with tuned-ppd for power profile selection.
- **Laptop:** TLP for battery-focused power management. Optional CPU
  power limits and fan control require hardware-specific configuration
  and testing.

Choose one power management setup; do not run TuneD and TLP together.

Shell configuration and swayidle settings are maintained separately
in my dotfiles repository.

Apply only the sections relevant to your hardware and chosen setup.

[Next: Secure Boot for an Existing systemd-boot Installation >](0-secure-boot-systemd.md)

---

## Contents / Table of Contents

0. [Secure Boot for an Existing systemd-boot Installation (`sbctl`)](0-secure-boot-systemd.md)
1. [DNF Optimization](1-dnf.md)
2. [Additional Repositories & Codecs](2-repositories-and-codecs.md)
3. [Devario Kernel & SCX Scheduler](3-devario-kernel.md)
4. [Background Services Optimization](4-services.md)
5. [Power Management Configuration (TuneD or TLP)](5-power.md)
6. [Firmware Updates](6-firmware.md)
7. [Personal Tools & Tweaks](7-personal.md)
8. [System Fonts Setup](8-fonts.md)
9. [Shell Color Scripts](9-colorscripts.md)
10. [Zsh Shell & Plugins Setup](10-zsh.md)
11. [SDDM Login Manager Configuration](11-sddm.md)
12. [System Theming & Appearance](12-theming.md)
13. [KDE Optimization & Speedups](13-kde.md)

---

## Credits

<https://github.com/odziom91/fedora-tips-and-tricks>  
<https://rpmfusion.org/Howto>

---

[Next: Secure Boot for an Existing systemd-boot Installation >](0-secure-boot-systemd.md)
