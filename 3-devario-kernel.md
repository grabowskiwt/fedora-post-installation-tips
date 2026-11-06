[< Previous: Additional Repositories & Codecs](2-repositories-and-codecs.md) | [Next: Background Services Optimization >](4-services.md)

---

# 3. Devario Kernel & SCX Scheduler

This section is optional. Keep a standard Fedora kernel installed
and available in the boot menu as a fallback.

If Secure Boot is enabled with `sbctl`, follow the signing instructions
in [chapter 0](0-secure-boot-systemd.md) for each newly installed
Devario kernel before booting it.

Before switching, measure responsiveness, task completion time, and
battery discharge with the standard kernel. Compare under the same
conditions, changing the kernel and scheduler separately.

A custom kernel or scheduler does not guarantee better performance
or longer battery runtime on your hardware.

1. **Enable Sirlucjan's Copr Repository & Install Devario Kernel:**
```shell
sudo dnf copr enable sirlucjan/kernel-devario
sudo dnf install kernel-devario kernel-devario-devel-matched
```
If SELinux prevents kernel modules from being loaded, enable the required policy:
```shell
sudo setsebool -P domain_kernel_load_modules on
```

2. **Install Schedulers and Select Rusty:**
```shell
sudo dnf copr enable sirlucjan/scx-scheds-cargo
sudo dnf install scx-scheds
```

Set the default scheduler in `/etc/default/scx`:
```env
SCX_SCHEDULER=scx_rusty
```

Enable the systemd service:
```shell
sudo systemctl enable --now scx.service
```

Check with:
```shell
scxcli get
```

---

[< Previous: Additional Repositories & Codecs](2-repositories-and-codecs.md) | [Next: Background Services Optimization >](4-services.md)
