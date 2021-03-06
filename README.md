# Home Assistant Configuration
Here's my [Home Assistant](https://home-assistant.io/) configuration. I am using the easy to install [Hass.io](https://www.home-assistant.io/hassio/) running on a [Raspberry Pi](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/).

## Some of the devices and services that I use with HA
* [ISY-994i](https://www.home-assistant.io/components/isy994/) for Insteon control.
* Lots of SmartHome [Insteon](https://www.smarthome.com/insteon.html) devices controlled through ISY-994i.
* Presence detection.
  * [Unifi Controller](https://home-assistant.io/components/device_tracker.unifi/) for network device tracking.
  * [iOS app](https://itunes.apple.com/us/app/home-assistant-companion/id1099568401?mt=8) for location tracking and notifications.
* Climate
  * Three [Nest](https://www.home-assistant.io/components/nest/) thermostats.
  * [Davis Vantage Pro2](https://www.davisnet.com/solution/vantage-pro2/) connected to [weeWX](http://www.weewx.com/) which publishes to [Wunderground](https://www.wunderground.com/weather/us/tx/elgin/KTXELGIN7) and local MQTT server so HA can pick up current conditions.
* Cameras
  * Multiple [Unifi Video Cameras](https://www.home-assistant.io/components/camera.uvc/).
* Custom MQTT devices
  * WiFi Gate Controller for controlling my [US Automatic](https://www.usautomatic.com/) gate opener.
    * KiCAD board design and software is available on [GitHub](https://github.com/mikelawrence/WiFi-Gate-Controller).
  * WiFi Septic Controller for controlling and monitoring my Aerobic Septic System.
    * KiCAD board design and software is available on [GitHub](https://github.com/mikelawrence/WiFi-Septic-Controller).
  * Raspberry Pi RGBW LED Controller HAT for controlling a high power RGB LED Floodlight.
    * KiCAD board design and software is available on [GitHub](https://github.com/mikelawrence/RPi-HAT-RGBW-LED-Controller).
* Custom Haiku with SenseME fan component

## Custom Haiku with SenseME fan component
The Haiku with SenseME fan is a WiFi connected fan and installable light. This custom component uses TomFaulkner's [SenseMe](https://github.com/TomFaulkner/SenseMe) library to communicate with the fan.

### Installation
There are three senseme.py files that must be installed in the config/custom_components directory. Note the location of the three senseme.py files in their respective folders (fan/ and light/) is important. The SenseMe library will be automatically installed by Home Assistant.

### Configuration
The Haiku with SenseME fan component will automatically discover and create a fan and light (if installed) for each discovered fan. Setting ```max_number_fans``` to the number of Haiku fans on your network will speed up the discovery process but is not required. If ```include:``` is specified, discovered fans with a matching name will be added. If ```exclude``` is specified, discovered fans with a matching name will NOT be added. If both ```include:``` and ```exclude``` are specified, only ```include:``` will be honored. If neither ```include:``` and ```exclude``` are specified, all auto-detected fans will be added.

For included fans you can now specify a ```friendly_name``` to use instead of ```name``` in Home Assistant. This is handy for grouped fans. Controlling any fan in a group will affect all fans of that group. Default value is the same as ```name ```. Also new in the include section is the ```has_light``` boolean which when ```true``` will add a light component along with the fan. The default for ```has_light``` is ```true```. The included fan section must have a ```name ``` variable and it must must match the name in the Haiku app.
```yaml
# enable Haiku with SenseMe ceiling fans
senseme:
  max_number_fans: 2
  # used to include only specific fans
  include:
    - name: "Studio Vault Fan"
      friendly_name: "Studio Fan"
      has_light: true
    - name: "Family Room Fan"
  # or use exclude to prevent specific auto-detected fan
  exclude:
    - "Studio Beam Fan"
senseme:
  max_number_fans: 2
  # used to include specific fan
  include:
    - "Studio Vault Fan"
    - "Studio Beam Fan"
```

### Problems
* Occasionally changes to the fan state fail to connect to fan and make the change, usually as a network (python socket) error. Same thing is true for the SenseMe background task which gets the complete fan state every minute.
* Originally the Senseme custom component auto-detected both the existence of a light and the fan's group but longer term usage showed a problem with consistently auto-detecting these values. This version no longer auto-detects these values and requires the user to specify them in advance.
