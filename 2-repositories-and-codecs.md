[< Previous: DNF Optimization](1-dnf.md) | [Next: Devario Kernel & Schedulers >](3-devario-kernel.md)

---

# 2. Additional Repositories & Codecs

## RPM-Fusion
Add **free** and **nonfree** repositories and update core system groups:
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
Install complete multimedia codecs (FFmpeg):
```shell
sudo dnf swap ffmpeg-free ffmpeg --allowerasing
sudo dnf install ffmpeg ffmpeg-libs libva libva-utils
sudo dnf install @multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
```

When using the full FFmpeg package from RPM Fusion, do not additionally
install libavcodec-freeworld; it complements Fedora's ffmpeg-free.

### Hardware Acceleration
Enable hardware-level decoding to save CPU usage and battery life.

Follow only the subsection matching your GPU, on either a laptop or
desktop PC. Choose the Intel driver according to the GPU generation.

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

Use these packages only with the AMD/Mesa setup above, when a
32-bit application requires the corresponding video codecs.
```shell
sudo dnf install mesa-va-drivers-freeworld.i686
sudo dnf swap mesa-vulkan-drivers{,-freeworld}.i686
```

### Verify Hardware Video Decoding

Check the decoding profiles exposed by your VA-API driver:
```shell
vainfo
```

In Firefox, open `about:support` and inspect `HARDWARE_VIDEO_DECODING`
and the media codec support information. WebRender alone does not
confirm hardware video decoding.

Play a video using a codec supported by your GPU and compare CPU usage
and battery discharge rate under the same conditions. Installing codec
packages alone does not guarantee hardware decoding in the browser.

For troubleshooting, see the
[Fedora Firefox hardware acceleration guide](https://fedoraproject.org/wiki/Firefox_Hardware_acceleration).

### OpenH264 Installation
To enable H.264 support in browsers like Firefox:
```shell
sudo dnf install gstreamer1-plugin-openh264 mozilla-openh264
```

---

[< Previous: DNF Optimization](1-dnf.md) | [Next: Devario Kernel & Schedulers >](3-devario-kernel.md)
