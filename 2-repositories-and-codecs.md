[< Previous: DNF Optimization](1-dnf.md) | [Next: Devario Kernel & SCX Scheduler >](3-devario-kernel.md)

---

# 2. Additional Repositories & Codecs

## RPM-Fusion
Add RPM Fusion Free/Nonfree and update core groups:
```shell
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
sudo dnf group upgrade core
```

## Flathub
* **Add Flathub Stable:**
```shell
sudo dnf install flatpak
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

## Codec Installation

### FFmpeg
Install FFmpeg and multimedia codecs:
```shell
sudo dnf swap ffmpeg-free ffmpeg --allowerasing
sudo dnf install ffmpeg ffmpeg-libs libva libva-utils
sudo dnf install @multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
```

With RPM Fusion's full FFmpeg, do not install `libavcodec-freeworld`;
that package complements Fedora's `ffmpeg-free`.

### Hardware Acceleration
Hardware decoding reduces CPU use and battery drain. Install only the
driver matching your GPU and generation.

#### **AMD (Mesa):**
```shell
sudo dnf install mesa-va-drivers-freeworld
sudo dnf swap mesa-vulkan-drivers{,-freeworld}
```

#### **Intel (Modern):**
```shell
sudo dnf install intel-media-driver
```

#### **Intel (Older):**
```shell
sudo dnf install libva-intel-driver
```

#### **AMD: Optional i686 Compatibility for 32-bit Applications:**

Use only with AMD/Mesa, when a 32-bit application needs these codecs.
```shell
sudo dnf install mesa-va-drivers-freeworld.i686
sudo dnf swap mesa-vulkan-drivers{,-freeworld}.i686
```

### Verify Hardware Video Decoding

Check VA-API profiles:
```shell
vainfo
```

In Firefox `about:support`, check `HARDWARE_VIDEO_DECODING` and codec
support. WebRender alone does not confirm video decoding.

Play a GPU-supported video and compare CPU use and battery drain under
the same conditions. Codec packages alone do not guarantee browser decoding.

For troubleshooting, see the
[Fedora Firefox hardware acceleration guide](https://fedoraproject.org/wiki/Firefox_Hardware_acceleration).

### OpenH264 Installation
Install browser H.264 support:
```shell
sudo dnf install gstreamer1-plugin-openh264 mozilla-openh264
```

---

[< Previous: DNF Optimization](1-dnf.md) | [Next: Devario Kernel & SCX Scheduler >](3-devario-kernel.md)
