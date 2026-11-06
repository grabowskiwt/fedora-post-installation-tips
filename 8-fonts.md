[< Previous: Personal Tools & Tweaks](7-personal.md) | [Next: Shell Color Scripts >](9-colorscripts.md)

---

# 8. System Fonts Setup

Install essential developer and system fonts (`JetBrainsMono Nerd Font` and `IBM Plex Sans`):

```shell
sudo dnf install -y curl unzip fontconfig
```

* **JetBrainsMono Nerd Font:**
  ```shell
  sudo mkdir -p /usr/share/fonts/JetBrainsMono
  curl -L "https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip" -o /tmp/jb_font.zip
  sudo unzip -o /tmp/jb_font.zip -d /usr/share/fonts/JetBrainsMono
  rm -f /tmp/jb_font.zip
  ```

* **IBM Plex Sans:**
  ```shell
  sudo mkdir -p /usr/share/fonts/IBMPlexSans
  curl -L "https://github.com/IBM/plex/releases/download/v6.4.0/OpenType.zip" -o /tmp/ibm_font.zip
  sudo unzip -o /tmp/ibm_font.zip -d /usr/share/fonts/IBMPlexSans
  rm -f /tmp/ibm_font.zip
  ```

* **Reload Font Cache:**
  ```shell
  sudo fc-cache -f
  ```

## kmscon Font Family, Size and Colorssheme

Install kmscon FreeType font backend:
```shell
sudo dnf install kmscon-freetype freetype fontconfig
```

After installing the fonts above, check the exact font family:
```shell
fc-match 'JetBrainsMono Nerd Font Mono'
```

Create the configuration directory:
```shell
sudo mkdir -p /etc/kmscon
```

Add or update these options in `/etc/kmscon/kmscon.conf`,
preserving any other existing settings:
```ini
font-engine=freetype
font-name=JetBrainsMono Nerd Font Mono
font-size=18

# Gruvbox
palette=custom

palette-foreground=235,219,178
palette-background=40,40,40

# regular0-7
palette-black=40,40,40
palette-red=204,36,29
palette-green=104,157,106
palette-yellow=214,93,14
palette-blue=69,133,136
palette-magenta=177,98,134
palette-cyan=152,151,26
palette-light-grey=168,153,132

# bright0-7
palette-dark-grey=146,131,116
palette-light-red=251,73,52
palette-light-green=142,192,124
palette-light-yellow=254,128,25
palette-light-blue=131,165,152
palette-light-magenta=211,134,155
palette-light-cyan=184,187,38
palette-white=235,219,178
```

`font-name` selects the font family. `font-size` sets its height in
pixels; increase it for larger text or decrease it for smaller text.
Use an installed monospace font.

The changes apply to newly started kmscon instances. Save your work
and reboot to apply them without restarting an active console session.
Command-line font options override settings in the configuration file.

* **Troubleshoot Missing Fonts in Flatpak Applications (Optional):**

  Standard host font directories are normally available automatically.
  Restart the application after installing fonts.

  If fonts are still missing, list application IDs:
  ```shell
  flatpak list --app --columns=application
  ```

  For fonts in your user font directory, try the following override.
  Replace `APP_ID` with the affected application's ID:
  ```shell
  flatpak override --user --filesystem=xdg-data/fonts:ro APP_ID
  ```

  For a custom font directory, grant access to its actual path instead:
  ```shell
  flatpak override --user --filesystem=/absolute/path/to/fonts:ro APP_ID
  ```

  Restart the application and check whether the fonts are available.

---

[< Previous: Personal Tools & Tweaks](7-personal.md) | [Next: Shell Color Scripts >](9-colorscripts.md)
