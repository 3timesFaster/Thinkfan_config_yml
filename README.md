*The config file for my P16s fans

# Thinkfan setup

# 1. Installation

Install thinkfan
```
sudo dnf install thinkfan
```
# 2. Configuration

Find the temperature control devices with
```
find /sys/devices -type f -name "temp*_input"
```
in my case:
```
/sys/devices/platform/thinkpad_hwmon/hwmon/hwmon7/temp6_input
/sys/devices/platform/thinkpad_hwmon/hwmon/hwmon7/temp3_input
/sys/devices/platform/thinkpad_hwmon/hwmon/hwmon7/temp7_input
/sys/devices/platform/thinkpad_hwmon/hwmon/hwmon7/temp4_input
/sys/devices/platform/thinkpad_hwmon/hwmon/hwmon7/temp8_input
/sys/devices/platform/thinkpad_hwmon/hwmon/hwmon7/temp1_input
/sys/devices/platform/thinkpad_hwmon/hwmon/hwmon7/temp5_input
/sys/devices/platform/thinkpad_hwmon/hwmon/hwmon7/temp2_input
/sys/devices/pci0000:00/0000:00:18.3/hwmon/hwmon6/temp1_input
/sys/devices/pci0000:00/0000:00:08.1/0000:04:00.0/hwmon/hwmon5/temp1_input
/sys/devices/pci0000:00/0000:00:02.4/0000:03:00.0/nvme/nvme0/hwmon4/temp3_input
/sys/devices/pci0000:00/0000:00:02.4/0000:03:00.0/nvme/nvme0/hwmon4/temp1_input
/sys/devices/pci0000:00/0000:00:02.4/0000:03:00.0/nvme/nvme0/hwmon4/temp2_input
/sys/devices/pci0000:00/0000:00:02.2/0000:02:00.0/ieee80211/phy0/hwmon8/temp1_input
/sys/devices/virtual/thermal/thermal_zone0/hwmon2/temp1_input
/sys/devices/virtual/thermal/thermal_zone0/hwmon2/temp2_input
```

# the tempX_input can be substituted with indices [1-7]
for example:
```
  - hwmon: /sys/devices/platform/thinkpad_hwmon/hwmon7
    indices: [3, 6, 7]
```
*Throgh some trial and error for my laptop the other indicies would keep chaning but 3, 6 and 7 were constant.

Full example:
```
sensors:
  - hwmon: /sys/devices/platform/thinkpad_hwmon/hwmon
    indices: [3, 6, 7]

fans:
  - tpacpi: /proc/acpi/ibm/fan

levels:
  - [0, 0,  60]
  - [1, 60, 65]
  - [2, 65, 70]
  - [3, 70, 75]
  - [4, 75, 80]
  - [5, 80, 85]
  - [7, 65, 74]
  - [127, 70, 32767]
```
Add them to /etc/thinkfan.conf, including (level, min_temperature, max_temperature):

# 3. Enabling the fan control
```
echo "options thinkpad_acpi fan_control=1" > /etc/modprobe.d/thinkfan.conf
modprobe thinkpad_acpi
```
    You can check with lsmod if thinkfan_acpi is running
    To reload a module you need to remove it with sudo modprobe -r <module>, but this is not always possible, so we might need a reboot here.


Use systemctl to run/start the service
```
sudo service thinkfan start
```
And to retrieve the status, use:
```
service thinkfan status
```
# 5. Running on startup

To make it run at startup, we need to edit /etc/modules and add the lines below, to make the modules thinkpad_acpi and coretemp load at boot time. The thinkpad_acpi has to be loaded before coretemp.
```
thinkpad_acpi
coretemp
```
Enable the service
```
sudo systemctl enable thinkfan.service
```
