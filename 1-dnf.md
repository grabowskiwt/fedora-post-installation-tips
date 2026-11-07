[< Previous: Secure Boot for an Existing systemd-boot Installation](0-secure-boot-systemd.md) | [Next: Additional Repositories & Codecs >](2-repositories-and-codecs.md)

---

# 1. DNF Optimization

Add these settings to `[main]` in `/etc/dnf/dnf.conf`:

```ini
[main]
defaultyes=True
countme=False
fastestmirror=False
install_weak_deps=False
max_parallel_downloads=10
minrate=10000
timeout=10
installonly_limit=3
ip_resolve=4
color=always
```

DNF uses the changes on its next run. Clear metadata only if it is stale
or corrupted:
```shell
sudo dnf clean metadata
```

---

[< Previous: Secure Boot for an Existing systemd-boot Installation](0-secure-boot-systemd.md) | [Next: Additional Repositories & Codecs >](2-repositories-and-codecs.md)
