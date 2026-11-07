[< Previous: Background Services Optimization](4-services.md) | [Next: Firmware Updates >](6-firmware.md)

---

# 5. Power Management Configuration

Choose TuneD or TLP; do not use both.

## Option 1: Keep TuneD and tuned-ppd

Enable TuneD and tuned-ppd:
```shell
sudo systemctl enable --now tuned.service tuned-ppd.service
```

Select a power profile in Sway:

```shell
# Power saving
busctl --system set-property net.hadess.PowerProfiles \
  /net/hadess/PowerProfiles net.hadess.PowerProfiles \
  ActiveProfile s power-saver

# Balanced
busctl --system set-property net.hadess.PowerProfiles \
  /net/hadess/PowerProfiles net.hadess.PowerProfiles \
  ActiveProfile s balanced

# Performance
busctl --system set-property net.hadess.PowerProfiles \
  /net/hadess/PowerProfiles net.hadess.PowerProfiles \
  ActiveProfile s performance
```

Check the selected profile:
```shell
busctl --system get-property net.hadess.PowerProfiles \
  /net/hadess/PowerProfiles net.hadess.PowerProfiles ActiveProfile
```

- **Power Save:** lower power use.
- **Balanced:** everyday use.
- **Performance:** maximum performance.

Compare profiles with your workload; the lowest-power profile may not
use the least energy to finish a task.

Check active and available TuneD profiles:
```shell
tuned-adm active
tuned-adm list
```

Mappings are in `/etc/tuned/ppd.conf`; behavior varies by version and
hardware, so test profiles with a repeatable workload.

## Option 2: Replace TuneD with TLP

1. **Install TLP:**
   ```shell
   sudo dnf install --allowerasing tlp tlp-pd tlp-rdw
   ```

   Review the transaction; conflicting power tools will be removed.
   `tlp-pd` (desktop profiles) and `tlp-rdw` (radio switching) are optional.

   Battery defaults are CPU `balance_power` and platform `balanced`, when
   supported. `low-power` is for the `SAV` profile. Test other changes.

2. **Enable TLP and Desktop Profile Switching:**
   ```shell
   sudo systemctl enable --now tlp.service
   sudo systemctl enable --now tlp-pd.service
   ```

3. **Mask rfkill services:**
   ```shell
   sudo systemctl mask systemd-rfkill.service systemd-rfkill.socket
   ```
   TLP recommends this for radio-device handling, especially with `tlp-rdw`.

## Avoid Conflicting Power Management Tools

Do not run TLP alongside TuneD, power-profiles-daemon, or auto-cpufreq.
TuneD and TLP do not manage brightness or suspend; configure these in
your desktop or compositor, such as Sway.

Reference: [TLP conflicts](https://linrunner.de/tlp/faq/conflicts.html).

## Thermal Management with thermald (Optional)

Optional thermal management for supported Intel laptops and PCs;
thermald does not improve battery life by itself or replace power settings.

Install and enable it:
```shell
sudo dnf install thermald
sudo systemctl enable --now thermald.service
```

Check status and startup logs:
```shell
systemctl status thermald.service --no-pager
journalctl -u thermald.service -b --no-pager
```

If hardware is unsupported or thermal control is firmware-managed,
disable thermald.

Thermald may adjust custom CPU limits; verify them under load.

To disable it:
```shell
sudo systemctl disable --now thermald.service
```

References: [Fedora thermald package](https://packages.fedoraproject.org/pkgs/thermald/thermald/)
and [TLP compatibility](https://linrunner.de/tlp/faq/conflicts.html).

## Battery Charge Limits (Optional, TLP)

Charge limits reduce battery wear, not runtime per charge. Check supported
thresholds:
```shell
sudo tlp-stat -b
```

If supported, configure thresholds in `/etc/tlp.conf` (BAT0 example):
```ini
START_CHARGE_THRESH_BAT0=75
STOP_CHARGE_THRESH_BAT0=80
```

Use the battery name and values reported by `tlp-stat -b`; some laptops
support only a stop threshold or fixed values.

Apply and verify:
```shell
sudo tlp start
sudo tlp-stat -b
```

Before a trip, temporarily allow a full charge:
```shell
sudo tlp fullcharge BAT0
```

Restore your configured thresholds afterwards:
```shell
sudo tlp setcharge BAT0
```

See [TLP battery care](https://linrunner.de/tlp/faq/battery.html)
and [hardware-specific support](https://linrunner.de/tlp/settings/bc-vendors.html).

## Quiet Laptop Setup: TLP, Power Limits and thinkfan

Use TuneD on a desktop PC and TLP on a laptop.

My tests found a custom fan curve quieter than firmware control; results
vary by hardware and workload. The 10/15 W limits and curve are examples,
not universal settings. Measurements used TuneD Balanced, not TLP; test
the TLP setup separately.

In `/etc/tlp.conf`, enable Turbo Boost on AC and battery, but disable it
in the power-saver (SAV) profile:
```ini
CPU_BOOST_ON_AC=1
CPU_BOOST_ON_BAT=1
CPU_BOOST_ON_SAV=0
```

Apply the configuration:
```shell
sudo tlp start
```

### Persist Laptop CPU Power Limits (Intel RAPL Example)

Requires a writable Intel RAPL CPU package interface; use the supported
interface on other hardware. These example limits (10/15 W) apply on AC
and battery, limit CPU package rather than whole-system power, and may
reduce performance under load on AC. Use suitable values; for different
AC and battery limits, configure and test a power-source-aware rule.
Values are in microwatts, not manufacturer recommendations. Compare
responsiveness, task time, temperature, and battery discharge rate.

First inspect the available RAPL zones and constraints:
```shell
for zone in /sys/class/powercap/intel-rapl:*; do
  [ -f "$zone/name" ] || continue
  printf '\n%s: ' "$zone"
  cat "$zone/name"
  for constraint in "$zone"/constraint_*_name; do
    [ -f "$constraint" ] || continue
    printf '%s: ' "$constraint"
    cat "$constraint"
  done
done
```

Confirm `intel-rapl:0` is the CPU package, constraint 0 is `long_term`,
and constraint 1 is `short_term`.

Record the original limits before enabling the service:
```shell
cat /sys/class/powercap/intel-rapl:0/constraint_0_power_limit_uw
cat /sys/class/powercap/intel-rapl:0/constraint_1_power_limit_uw
```

Create the service at `/etc/systemd/system/laptop-power-limits.service`:
```ini
[Unit]
Description=Apply laptop CPU package power limits
After=tlp.service

[Service]
Type=oneshot
ExecCondition=/usr/bin/grep -qx package-0 /sys/class/powercap/intel-rapl:0/name
ExecCondition=/usr/bin/grep -qx long_term /sys/class/powercap/intel-rapl:0/constraint_0_name
ExecCondition=/usr/bin/grep -qx short_term /sys/class/powercap/intel-rapl:0/constraint_1_name
ExecStart=/bin/sh -ec 'echo 10000000 > /sys/class/powercap/intel-rapl:0/constraint_0_power_limit_uw; echo 15000000 > /sys/class/powercap/intel-rapl:0/constraint_1_power_limit_uw'

[Install]
WantedBy=multi-user.target
```

Create the resume hook at `/usr/lib/systemd/system-sleep/laptop-power-limits`:
```sh
#!/bin/sh
if [ "$1" = post ]; then
    /usr/bin/systemctl --no-block restart laptop-power-limits.service
fi
```

Enable the hook and service:
```shell
sudo chmod 755 /usr/lib/systemd/system-sleep/laptop-power-limits
sudo systemctl daemon-reload
sudo systemctl enable --now laptop-power-limits.service
```

Verify after setup, reboot, and resume:
```shell
cat /sys/class/powercap/intel-rapl:0/constraint_0_power_limit_uw
cat /sys/class/powercap/intel-rapl:0/constraint_1_power_limit_uw
cat /sys/devices/system/cpu/intel_pstate/no_turbo
```

Expect `10000000`, `15000000`, and `no_turbo=0` on AC and battery;
`no_turbo=1` in TLP SAV. The service sets power limits and TLP controls
Turbo Boost. Firmware may override limits.

Disable automatic limiting:
```shell
sudo systemctl disable --now laptop-power-limits.service
sudo chmod -x /usr/lib/systemd/system-sleep/laptop-power-limits
```

Disabling the service does not restore previous limits. Restore the
recorded values in sysfs, or reboot after disabling both mechanisms.

## Laptop Fan Control (Optional, Compatible Hardware Only)

Requires the `thinkpad_acpi` fan interface and a `coretemp` CPU package
sensor. Other hardware needs a supported interface or firmware control;
include GPU sensors if cooling is shared.

Test power limits with firmware cooling first. If needed, install and
check thinkfan:
```shell
sudo dnf install thinkfan lm_sensors
sensors
cat /proc/acpi/ibm/fan
```

Enable fan control in `/etc/modprobe.d/99-thinkfan.conf`:
```ini
options thinkpad_acpi fan_control=1
```

Reboot.

Check the CPU sensor labels:
```shell
for hwmon in /sys/class/hwmon/hwmon*; do
  [ "$(cat "$hwmon/name")" = coretemp ] || continue
  printf '\n%s\n' "$hwmon"
  cat "$hwmon"/temp*_label
done
```

Confirm `coretemp` index 1 is the CPU package in `temp1_label`. Back up
any existing configuration before replacing it.

Create `/etc/thinkfan.yaml`:
```yaml
sensors:
  - hwmon: /sys/class/hwmon
    name: coretemp
    indices: [1]

fans:
  - tpacpi: /proc/acpi/ibm/fan

levels:
  - [0, 0, 50]
  - [1, 45, 60]
  - [2, 55, 68]
  - [3, 63, 75]
  - [5, 70, 82]
  - [7, 77, 32767]
```

Thresholds are experimental: the fan starts near 50°C and stops below
45°C; overlap reduces repeated speed changes. The CPU sensor does not
cover every component, so monitor temperatures and compare with firmware.

Stop any running thinkfan service and test it in the foreground:
```shell
sudo systemctl stop thinkfan.service
sudo thinkfan -n -c /etc/thinkfan.yaml
```

Monitor temperatures and fan speed at idle and under load. Stop with
Ctrl+C; if the curve is safe and quiet, enable the service:
```shell
sudo systemctl enable --now thinkfan.service
```

Restore firmware fan control:
```shell
sudo systemctl disable --now thinkfan.service
echo 'level auto' | sudo tee /proc/acpi/ibm/fan
```

References: [thinkfan configuration](https://github.com/vmatare/thinkfan/blob/master/examples/thinkfan.yaml)
and [ThinkPad fan control](https://www.kernel.org/doc/html/latest/admin-guide/laptops/thinkpad-acpi.html).

## Verify Power Management and Measure Battery Usage

If you chose TLP, check its status and battery information:
```shell
sudo tlp-stat -s
sudo tlp-stat -b
```

Install and run PowerTOP for diagnostics:
```shell
sudo dnf install powertop
sudo powertop
```

Measure on battery after background activity settles. Keep brightness,
refresh rate, network, and workload constant; compare idle and repeatable
loads, changing one setting at a time.

Use PowerTOP for observation. Do not run `powertop --auto-tune`
alongside TLP, as they can overwrite each other's settings.

Reference: [TLP installation on Fedora](https://linrunner.de/tlp/installation/fedora.html).

---

[< Previous: Background Services Optimization](4-services.md) | [Next: Firmware Updates >](6-firmware.md)
