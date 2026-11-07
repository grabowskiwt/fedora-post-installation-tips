[< Previous: SDDM Login Manager Configuration](11-sddm.md) | [Next: KDE Optimization & Speedups >](13-kde.md)

---

# 12. System Theming & Appearance

1. **Install Theme & Cursor Packages:**
   ```shell
   sudo dnf install adwaita-cursor-theme breeze-cursor-theme adwaita-icon-theme xdg-desktop-portal-gtk qt5ct qt6ct
   ```

2. **Install Tela-Circle Icon Theme (Green):**
   ```shell
   theme_tmp="$(mktemp -d)"
   git clone --depth 1 https://github.com/vinceliuice/Tela-circle-icon-theme.git "$theme_tmp/Tela-circle-icon-theme"
   "$theme_tmp/Tela-circle-icon-theme/install.sh" -c green -d "$HOME/.local/share/icons"
   rm -rf -- "$theme_tmp"
   ```

3. **Configure GTK Appearance and Desktop Preferences:**
   ```shell
   gsettings set org.gnome.desktop.interface color-scheme 'prefer-dark'
   gsettings set org.gnome.desktop.interface gtk-theme 'Adwaita'
   gsettings set org.gnome.desktop.interface icon-theme 'Tela-circle-green'
   gsettings set org.gnome.desktop.interface cursor-theme 'breeze_cursors'
   gsettings set org.gnome.desktop.interface cursor-size 32
   gsettings set org.gnome.desktop.interface font-name 'IBM Plex Sans 18'
   ```

   Match GTK and Sway settings; use the same cursor theme and size in
   `seat * xcursor_theme` and the session environment.

4. **Configure Native Qt Applications:**

   Open both installed configuration tools:
   ```shell
   qt5ct
   qt6ct
   ```

   Set Tela-circle-green icons, IBM Plex Sans, and a dark style in both.

   Export these variables in the Sway session launcher:
   ```shell
   export QT_QPA_PLATFORMTHEME=qt5ct
   export XCURSOR_THEME=breeze_cursors
   export XCURSOR_SIZE=32
   ```

   `qt6ct` accepts `qt5ct` as an alias. D-Bus environment updates alone
   do not reach apps launched directly by Sway. Log out and back in.

5. **Configure Flatpak Theming & Overrides:**
   ```shell
   flatpak install flathub org.gtk.Gtk3theme.Adwaita-dark
   flatpak install flathub org.kde.KStyle.Adwaita
   flatpak override --user --env=XCURSOR_THEME=breeze_cursors
   flatpak override --user --env=XCURSOR_SIZE=32
   flatpak override --user --filesystem=xdg-data/icons:ro
   flatpak override --user --filesystem=xdg-config/gtk-3.0:ro
   flatpak override --user --filesystem=xdg-config/gtk-4.0:ro
   flatpak override --user --filesystem=xdg-config/qt5ct:ro
   flatpak override --user --filesystem=xdg-config/qt6ct:ro
   flatpak override --user --unset-env=QT_QPA_PLATFORMTHEME
   ```

   Keep host Qt settings separate from Flatpak runtime plugins; the
   override prevents Flatpak apps inheriting an unavailable host plugin.

   Check app runtimes and installed theme branches:
   ```shell
   flatpak list --app --columns=application,runtime
   flatpak list --runtime --columns=application,branch
   ```

   Fedora's Sway portals already use GTK for settings and WLR for capture;
   change them only when troubleshooting.

   Match theme extensions to each app's runtime. Dark preference is sent
   through the Settings portal and may vary by toolkit.

---

[< Previous: SDDM Login Manager Configuration](11-sddm.md) | [Next: KDE Optimization & Speedups >](13-kde.md)
