[< Previous: Devario Kernel & SCX Scheduler](3-devario-kernel.md) | [Next: Power Management Configuration >](5-power.md)

---

# 4. Background Services Optimization

Disable only unused features; running services do not necessarily use
significant power.

## System Services (`sudo`)
* **Disable Crash Reporting (ABRT):**
  ```shell
  sudo systemctl disable --now abrtd.service abrt-oops.service abrt-xorg.service abrt-journal-core.service
  ```
* **Disable Cellular Modems & Smart Cards:**

  Keep `pcscd` for smart cards or YubiKey PIV/OpenPGP/OATH. FIDO2/WebAuthn
  does not require it.

  ```shell
  sudo systemctl disable --now ModemManager.service pcscd.socket pcscd.service
  ```
* **Disable PackageKit (Optional):**

  Disables PackageKit software updates (including KDE Discover). Update
  RPMs with DNF and wait for active transactions to finish first.

  ```shell
  sudo systemctl mask --now packagekit.service
  ```

  Re-enable PackageKit:
  ```shell
  sudo systemctl unmask packagekit.service
  ```

## User Services (`systemctl --user`)
* **Disable Bluetooth File Transfer (OBEX):**
  ```shell
  systemctl --user mask --now obex.service dbus-org.bluez.obex.service
  ```
* **Disable Accessibility APIs (Optional):**

  Skip if you use a screen reader or other accessibility tools.
  ```shell
  systemctl --user mask --now at-spi-dbus-bus.service
  ```

---

[< Previous: Devario Kernel & SCX Scheduler](3-devario-kernel.md) | [Next: Power Management Configuration >](5-power.md)
