[< Previous: SDDM Login Manager Configuration](11-sddm.md) | [Next: KDE Optimization & Speedups >](13-kde.md)

---

# 12. System Theming & Appearance

1. **Install Theme & Cursor Packages:**
   ```shell
   sudo dnf install -y adwaita-cursor-theme breeze-cursor-theme adwaita-icon-theme xdg-desktop-portal-gtk qt5ct qt6ct
   ```

2. **Install Tela-Circle Icon Theme (Green):**
   ```shell
   git clone https://github.com/vinceliuice/Tela-circle-icon-theme.git /tmp/Tela-circle-icon-theme
   sudo /tmp/Tela-circle-icon-theme/install.sh -c green -d /usr/share/icons
   rm -rf /tmp/Tela-circle-icon-theme
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

   Keep these values consistent with your GTK settings and Sway
   dotfiles. In particular, use the same cursor theme and size in
   `seat * xcursor_theme` and the session environment.

4. **Configure Native Qt Applications:**

   Install the qt5ct and qt6ct packages from step 1, then open both tools:
   ```shell
   qt5ct
   qt6ct
   ```

   Configure Tela-circle-green icons, IBM Plex Sans, and an available
   dark style or color scheme in both tools.

   Export these variables in your session launcher before starting Sway:
   ```shell
   export QT_QPA_PLATFORMTHEME=qt5ct
   export XCURSOR_THEME=breeze_cursors
   export XCURSOR_SIZE=32
   ```

   The qt6ct plugin accepts `qt5ct` as a compatibility alias.
   Updating the D-Bus activation environment alone does not export
   these variables to applications launched directly by Sway.
   Log out and back in after changing the session environment.

5. **Configure Flatpak Theming & Overrides:**
   ```shell
   flatpak install flathub org.gtk.Gtk3theme.Adwaita-dark
   flatpak install flathub org.kde.KStyle.Adwaita
   flatpak override --user --env=XCURSOR_THEME=breeze_cursors
   flatpak override --user --env=XCURSOR_SIZE=32
   flatpak override --user --filesystem=/usr/share/icons:ro
   flatpak override --user --filesystem=xdg-config/gtk-3.0:ro
   flatpak override --user --filesystem=xdg-config/gtk-4.0:ro
   flatpak override --user --filesystem=xdg-config/qt5ct:ro
   flatpak override --user --filesystem=xdg-config/qt6ct:ro
   flatpak override --user --unset-env=QT_QPA_PLATFORMTHEME
   ```

   Keep native Qt configuration separate from Flatpak runtime plugins.
   The command above prevents Flatpak applications from inheriting a
   host platform theme plugin that may be unavailable in their runtime.

   Install theme extension branches matching your applications' runtimes:
   ```shell
   flatpak list --app --columns=application,runtime
   flatpak list --runtime --columns=application,branch
   ```

   Fedora's Sway portal configuration already selects GTK for settings
   and WLR for screen capture. Avoid replacing it unless troubleshooting
   a specific problem.

   Theme extensions must support the application's runtime branch.
   The dark preference is communicated through the Settings portal;
   applications may render it differently depending on their toolkit.

---

[< Previous: SDDM Login Manager Configuration](11-sddm.md) | [Next: KDE Optimization & Speedups >](13-kde.md)
