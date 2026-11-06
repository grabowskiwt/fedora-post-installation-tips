[< Previous: Background Services Optimization](4-services.md) | [Next: Firmware Updates >](6-firmware.md)

---

# 5. Power Management Configuration

Choose one option: keep Fedora's TuneD setup or replace it with TLP.
Do not apply both options.

## Option 1: Keep TuneD and tuned-ppd

If TuneD is already installed, keep it and skip the TLP installation.
Ensure both services are enabled:
```shell
sudo systemctl enable --now tuned.service tuned-ppd.service
```

In Sway, select a power profile from the terminal.
Run one of the following commands:

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

Check the selected power mode:
```shell
busctl --system get-property net.hadess.PowerProfiles \
  /net/hadess/PowerProfiles net.hadess.PowerProfiles ActiveProfile
```

- **Power Save:** prioritize lower power consumption on battery.
- **Balanced:** start here for everyday use.
- **Performance:** use when you need maximum performance.

Compare Power Save and Balanced with your actual workload; the lowest
power setting does not necessarily use the least energy to finish a task.

Check the active TuneD profile and list available profiles:
```shell
tuned-adm active
tuned-adm list
```

Desktop profile names are mapped to TuneD profiles through
`/etc/tuned/ppd.conf`.
Mappings and power use can vary by TuneD version and hardware; compare
the available profiles with a repeatable workload on your own system.

## Option 2: Replace TuneD with TLP

1. **Install TLP:**
   ```shell
   sudo dnf install --allowerasing tlp tlp-pd tlp-rdw
   ```

   Review the transaction before confirming: conflicting power management
   packages will be removed.
   `tlp-pd` enables desktop profile switching; `tlp-rdw` enables automatic
   radio device switching. Both packages are optional.

   TLP's defaults are a reasonable starting point for battery use:
   `balance_power` for the CPU energy policy and `balanced` for the
   platform profile, when supported by the hardware. `low-power` is the
   default platform profile for TLP's `SAV` profile, not its normal
   battery profile. Measure any additional changes on your own system.

2. **Enable TLP and Desktop Profile Switching:**
   ```shell
   sudo systemctl enable --now tlp.service
   sudo systemctl enable --now tlp-pd.service
   ```

3. **Mask rfkill services:**
   ```shell
   sudo systemctl mask systemd-rfkill.service systemd-rfkill.socket
   ```
   TLP recommends this to avoid conflicts with radio-device state
   handling. It is especially relevant when using `tlp-rdw`.

## Avoid Conflicting Power Management Tools

The installation command above removes conflicting Fedora power
management packages. Do not run TLP alongside TuneD,
power-profiles-daemon, or auto-cpufreq.

`tlp-pd` is designed to work with TLP and provides desktop profile
switching.

TuneD and TLP do not manage display brightness or suspend policy.
Configure those through your desktop environment or compositor, such
as Sway.

Reference: [TLP conflicts](https://linrunner.de/tlp/faq/conflicts.html).

## Thermal Management with thermald (Optional)

On supported Intel laptops and PCs, thermald manages temperature.
It is an optional thermal-management tool, not a battery-life
optimization by itself. It complements TLP; it does not replace
power-saving settings.

Install and enable it:
```shell
sudo dnf install thermald
sudo systemctl enable --now thermald.service
```

Check its status and startup messages:
```shell
systemctl status thermald.service --no-pager
journalctl -u thermald.service -b --no-pager
```

If the log reports unsupported hardware or firmware-managed thermal
control, do not force thermald to run.

When using custom CPU power limits, verify them under load with
thermald active, since thermal management may adjust power limits.

To disable it:
```shell
sudo systemctl disable --now thermald.service
```

References: [Fedora thermald package](https://packages.fedoraproject.org/pkgs/thermald/thermald/)
and [TLP compatibility](https://linrunner.de/tlp/faq/conflicts.html).

## Battery Charge Limits (Optional, TLP)

Charge limits reduce battery wear; they do not increase runtime per
charge. Check which thresholds your hardware supports:
```shell
sudo tlp-stat -b
```

If your battery supports both thresholds, configure them in
`/etc/tlp.conf`. Example for BAT0:
```ini
START_CHARGE_THRESH_BAT0=75
STOP_CHARGE_THRESH_BAT0=80
```

Use the battery name and allowed values reported by `tlp-stat -b`.
Some laptops support only a stop threshold or fixed threshold values.

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

Use TuneD and tuned-ppd for the desktop PC.
For a laptop, choose the TLP installation above instead of TuneD.

In my tests, a custom fan curve was quieter than firmware control.
Results depend on the laptop, cooling system, and workload.
The 10/15 W limits and fan curve below are examples to adapt and test,
not universal settings for every laptop.
The power and fan measurements below were taken with TuneD Balanced
active. They are not measurements of the TLP configuration described
above; verify that setup separately on your laptop.

Allow Turbo Boost in all TLP profiles by setting these values
in `/etc/tlp.conf`:
```ini
CPU_BOOST_ON_AC=1
CPU_BOOST_ON_BAT=1
CPU_BOOST_ON_SAV=1
```

Apply the configuration:
```shell
sudo tlp start
```

### Persist Laptop CPU Power Limits (Intel RAPL Example)

This example requires a writable Intel RAPL CPU package interface.
For AMD or other hardware, use the interface supported by that CPU.
The example 10/15 W limits apply on both battery and AC power. They can
reduce performance under heavy load while plugged in; select values
appropriate for your processor before enabling the service. To use
different limits on battery and AC, configure and test a separate
power-source-aware rule.
This limits CPU package power, not the entire laptop's power consumption.
Values are in microwatts; 10/15 W are examples, not manufacturer
recommendations. Compare responsiveness, task completion time, temperature,
and battery discharge rate with your chosen limits.

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

For the following example, confirm that `intel-rapl:0` is the CPU
package, constraint 0 is `long_term`, and constraint 1 is `short_term`.

Before enabling the persistent service, record your original limits:
```shell
cat /sys/class/powercap/intel-rapl:0/constraint_0_power_limit_uw
cat /sys/class/powercap/intel-rapl:0/constraint_1_power_limit_uw
```

Create `/etc/systemd/system/laptop-power-limits.service`:
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

Create `/usr/lib/systemd/system-sleep/laptop-power-limits`:
```sh
#!/bin/sh
if [ "$1" = post ]; then
    /usr/bin/systemctl --no-block restart laptop-power-limits.service
fi
```

Enable the resume hook and startup service:
```shell
sudo chmod 755 /usr/lib/systemd/system-sleep/laptop-power-limits
sudo systemctl daemon-reload
sudo systemctl enable --now laptop-power-limits.service
```

Verify after installation, reboot, and suspend/resume:
```shell
cat /sys/class/powercap/intel-rapl:0/constraint_0_power_limit_uw
cat /sys/class/powercap/intel-rapl:0/constraint_1_power_limit_uw
cat /sys/devices/system/cpu/intel_pstate/no_turbo
```

Expected values: `10000000`, `15000000`, and `0` respectively.
The service manages power limits; TLP manages Turbo Boost.
Firmware may still override limits, so verify them.

To remove automatic power limiting:
```shell
sudo systemctl disable --now laptop-power-limits.service
sudo chmod -x /usr/lib/systemd/system-sleep/laptop-power-limits
```

Disabling the service does not restore the previous power limits.
Write the recorded original values back to the corresponding sysfs
files, or reboot after disabling both automatic mechanisms.

## Laptop Fan Control (Optional, Compatible Hardware Only)

This example requires the thinkpad_acpi fan interface and a coretemp
CPU package sensor. It is not a universal laptop fan configuration.
On other hardware, use a supported fan-control interface or firmware
control. Include dedicated GPU sensors when the cooling system is shared.

First test the CPU power limits with firmware-controlled cooling.
If fan behavior is still unsatisfactory, try thinkfan:
```shell
sudo dnf install thinkfan lm_sensors
sensors
cat /proc/acpi/ibm/fan
```

Enable fan control in `/etc/modprobe.d/99-thinkfan.conf`:
```ini
options thinkpad_acpi fan_control=1
```

Reboot before continuing.

Check the CPU sensor labels:
```shell
for hwmon in /sys/class/hwmon/hwmon*; do
  [ "$(cat "$hwmon/name")" = coretemp ] || continue
  printf '\n%s\n' "$hwmon"
  cat "$hwmon"/temp*_label
done
```

The example below requires `coretemp` sensor index 1 to represent
the CPU package temperature. Confirm this in `temp1_label`.
Back up any existing thinkfan configuration before replacing it.

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

These temperature thresholds are experimental starting values.
The fan starts above approximately 50°C and stops below 45°C.
Overlapping thresholds provide hysteresis to reduce repeated speed
changes. The CPU package sensor does not cover every component in the
laptop; compare temperatures and behavior with firmware-controlled cooling.

Stop any existing thinkfan service and test in the foreground:
```shell
sudo systemctl stop thinkfan.service
sudo thinkfan -n -c /etc/thinkfan.yaml
```

Monitor temperatures and fan speed with `sensors` in another terminal,
both at idle and during your normal heavy workloads.
After observing the fan and temperatures at idle and under load, stop
the foreground process with Ctrl+C. If the curve behaves safely and
quietly, enable the service in a separate command:
```shell
sudo systemctl enable --now thinkfan.service
```

To return to firmware-controlled cooling:
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

For additional diagnostics, install and run PowerTOP:
```shell
sudo dnf install powertop
sudo powertop
```

Measure on battery power after background activity has settled.
Keep screen brightness, refresh rate, network conditions, and workload
the same between tests. Compare idle usage and a repeatable workload,
such as playing the same video. Change one setting at a time.

Use PowerTOP for observation. Do not run `powertop --auto-tune`
alongside TLP, as they can overwrite each other's settings.

Reference: [TLP installation on Fedora](https://linrunner.de/tlp/installation/fedora.html).

---

[< Previous: Background Services Optimization](4-services.md) | [Next: Firmware Updates >](6-firmware.md)
