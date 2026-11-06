[< Previous: Secure Boot for an Existing systemd-boot Installation](0-secure-boot-systemd.md) | [Next: Additional Repositories & Codecs >](2-repositories-and-codecs.md)

---

# 1. DNF Optimization

To speed up package management, edit `/etc/dnf/dnf.conf` and ensure the `[main]` section contains the following settings:

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

DNF reads the configuration on its next run; clearing the cache is not
required. Only if you encounter stale or corrupted metadata, run:
```shell
sudo dnf clean metadata
```

---

[< Previous: Secure Boot for an Existing systemd-boot Installation](0-secure-boot-systemd.md) | [Next: Additional Repositories & Codecs >](2-repositories-and-codecs.md)
