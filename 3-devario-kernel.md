[< Previous: Additional Repositories & Codecs](2-repositories-and-codecs.md) | [Next: Background Services Optimization >](4-services.md)

---

# 3. Devario Kernel & SCX Scheduler

Optional. Keep a standard Fedora kernel as a bootable fallback.

With Secure Boot and `sbctl`, sign each new Devario kernel as described
in [chapter 0](0-secure-boot-systemd.md) before booting it.

Measure responsiveness, task time, and battery drain on the standard
kernel first. Compare under the same conditions, changing one component
at a time; better performance or battery life is not guaranteed.

1. **Enable Sirlucjan's Copr Repository & Install Devario Kernel:**
```shell
sudo dnf copr enable sirlucjan/kernel-devario
sudo dnf install kernel-devario kernel-devario-devel-matched
```
If SELinux blocks module loading, enable this policy:
```shell
sudo setsebool -P domain_kernel_load_modules on
```

2. **Install Schedulers and Select Rusty:**
```shell
sudo dnf copr enable sirlucjan/scx-scheds-cargo
sudo dnf install scx-scheds
```

Set the scheduler in `/etc/default/scx`:
```env
SCX_SCHEDULER=scx_rusty
```

Enable the service:
```shell
sudo systemctl enable --now scx.service
```

Check with:
```shell
scxcli get
```

---

[< Previous: Additional Repositories & Codecs](2-repositories-and-codecs.md) | [Next: Background Services Optimization >](4-services.md)
