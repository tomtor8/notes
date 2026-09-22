# Connections and wifi

- `iwctl` can be used to set up and control connection including wifi

## List devices

```bash
iwctl list devices
```

-the device name is usually `wlan0`

## Turn off or on the Wifi

```bash
# turn off wifi
iwctl device [DEVICE_NAME] set-property Powered off
iwctl device wlan0 set-property Powered off
 
# turn on wifi
iwctl device [DEVICE_NAME] set-property Powered on
iwctl device wlan0 set-property Powered on
```
