[< Previous: Devario Kernel & SCX Scheduler](3-devario-kernel.md) | [Next: Power Management Configuration >](5-power.md)

---

# 4. Background Services Optimization

Only disable features you do not use. A running service does not
necessarily consume significant CPU time or battery power.

## System Services (`sudo`)
* **Disable Crash Reporting (ABRT):**
  ```shell
  sudo systemctl disable --now abrtd.service abrt-oops.service abrt-xorg.service abrt-journal-core.service
  ```
* **Disable Cellular Modems & Smart Cards:**
  ```shell
  sudo systemctl disable --now ModemManager.service pcscd.socket pcscd.service
  ```
* **Disable PackageKit (Optional):**

  This disables PackageKit-based software management, including RPM
  updates in KDE Discover. Keep updating RPM packages manually with
  DNF. Wait for any active package transaction to finish first. This
  note applies to KDE; Sway users may not have Discover installed.

  ```shell
  sudo systemctl mask --now packagekit.service
  ```

  To allow PackageKit to start again:
  ```shell
  sudo systemctl unmask packagekit.service
  ```

## User Services (`systemctl --user`)
* **Disable Bluetooth File Transfer (OBEX):**
  ```shell
  systemctl --user mask --now obex.service dbus-org.bluez.obex.service
  ```
* **Disable Accessibility APIs (Optional):**

  Skip this step if you use a screen reader or other accessibility tools.
  ```shell
  systemctl --user mask --now at-spi-dbus-bus.service
  ```

---

[< Previous: Devario Kernel & SCX Scheduler](3-devario-kernel.md) | [Next: Power Management Configuration >](5-power.md)
