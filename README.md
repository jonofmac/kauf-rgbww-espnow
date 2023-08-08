# kauf-rgbww-espnow
Additional package that adds ESPNow functionality to Kauf bulbs for use with the wizmote's that can be found for cheap on amazon.
This allows the Kauf bulbs to be used/controlled without home assistant. Since ESPNow does not utilize tcp/ip, and does not rely on your wifi, using the wizmote with a paired bulb will funtion even if home assistant or wifi go down.

Since the Kauf bulbs use a 1 MB flash variant, the web-server must be disabled in order to have enough room for OTA flashing. The first flash might successfully upload depending on the version you are upgrading from. Subsequent uploads will likely fail.

For the most part, follow the readme for the Kauf bulbs, but add my package and substitutions to add the espnow functionality

```
substitutions:
  ...
  disable_webserver: "true"

  remote_mac: '"001122334455"' # See below on how to obtain the remote mac address
  remote_groups: "{1,2,0,0}"
  remote_enable_always: "true"
  remote_brightness_stepsize: "0.2"
  ...

packages:
  kauf.rgbww: github://KaufHA/kauf-rgbww-bulbs/kauf-bulb.yaml
  jv.kauf-espnow: github://jonofmac/kauf-rgbww-espnow/kauf-espnow.yaml

```

## Repo Contents
This repo contains files for the ESPNow functionality addition

## Advanced Settings
When using kauf-bulb.yaml as a package in the ESPHome dashboard, you can configure the following aspects by adding substitutions. The substitutions section of kauf-bulb.yaml has comments with more explanation as well.

***remote_enable_always*** - If set to "true", then the remote functionality is always enabled. If set to "false", then it is only active when home assistant is NOT connected. This is useful if you utilize another automation for listening to remote commands, but you want a back up incase home assistant goes down.

***remote_brightness_stepsize*** - The step size for the brightness control in the range of 0.00-1.00. 0.1 = 10%, 0.2 = 20%, etc

***remote_groups*** - The groups on the remote (1-4) that a bulb can belong to. This is an array and must be 4 elements long. 0 = not used. 1 = group 1, 2 = group 2, and so forth. "{1,3,0,0}" means that the bulb belongs in groups 1 and 3 on the remote


- **Note:** There is not enough memory on the ESPs used in Kauf bulbs to do over the air (OTA) updates with all functionality enabled. Disabling the web server is the easiest way to free up enough space (see substitution above)


## How to get remote's MAC address?
Once you add the package and upload to your bulb (You can use any/default mac address until you know what yours is), connect to it through ESPHome's log functionality. By default, the logger is set to "debug", which will print all ESPNow packets received by the bulb. You will see the MAC address listed (and the data) when you press any button on the remote. Put the mac address without any : or . into the field.
For example, you might see a line that shows `[D][esp_now:071]: mac: 44.4F.8E.BA.AD.2E (6), data: 81.69.0A.00.00.20.10.01.59.BB.8F.32.7D (13)`
The mac address field would be `remote_mac: '"444f8ebaad2e"'`
Note that there is a ' and " next to each other. This is needed for ESPHome to properly parse the string.

## Troubleshooting
If you accidentally uploaded with web server enabled, and now you get a "not enough space" when trying to upload again, you need to reduce the size of the binary. Comment out the espnow stuff and make sure you add the substitution which disables the webserver (see above). This smaller binary should upload. Once that is complete, then you can uncomment (add back) the espnow code and try again.

## Links
- [Kauf Bulb Github](https://github.com/KaufHA/common/blob/main/README.md#troubleshooting).
- [KaufHA Website](https://kaufha.com/blf10/)
