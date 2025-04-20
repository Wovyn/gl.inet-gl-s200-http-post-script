# gl.inet-gl-s200-http-post-script
A script to post the GL-S200 Thread sensor data to an HTTP Endpoint as JSON

## Overview
The [GL.iNet GL-S200](https://www.gl-inet.com/products/gl-s200/) is an impressive Dual-Protocol Thread Border Router.  It can be bought by itself, or with a set of three Thread Development Boards.  The Thread Development Boards contain an optional testing module that includes a variety of sensors and control interfaces, such as push & turn dial, RGB light, PIR, Humidity, Temperature, and atmospheric pressure.  What is fantastic is that these Thread Development Boards fully support mesh networking, and can be configured in both "router" and "end node" modes.  I have have both in my networks to extend the range of the mesh network.

This product has so much potential, but in my opinion the firmware is only ~80% complete.  This includes the firmware for the router, and the firmware for the Thread Development Boards.  I have written a lot of suggested improvements to GL.iNet, and continue to do so, and there is slow progress.  But too slow in my opinion to make this the successful product it could be.

The webpage and documentation are quite confusing as they mention support for MQTT, but ONLY Bluetooth to MQTT.  I quickly found that there was no way to get the Thread Dev Board data to my cloud applications.  Within the web interface, when viewing the Thread Boards (GL Dev Boards -> Devices), there is an Action column with the (...) menu.  Clicking that will present the option to `</> Get Code` where you can view sample code that you can use and modify. The first script(`wovyn_get_sensor_data_post`) in my `/src` directory is based on the script they provided to `Read sensor data from devices`, modified to also POST the data vi HTTP as JOSN to an endpoint.

## Installation
Installation of my script is simple.

1. First, edit the script and change the following line to set your endpoint:
   * `http_endpoint="http://{your.server.hostname}:{port number}/{path}"`
   * Replace `{your.server.hostname}` with your hostname
   * Replace `{port number}` with the desired port. (or remove `:{port number}` to use port 80
   * Replace `{path}` with your desired path
3. Connect to the GL-S200 via SSH or any tool that allows you to copy files to the router, and to set the file permissions
   * I use WinSCP to do this
   * Use the username "root" with the password you set for the web interface
5. Once connected, copy the `wovyn_get_sensor_data_post` script into the `/user/bin` directory
6. Use `chmod` to set the file permissions for execute.  I use the command `chmod 755 /usr/bin/wovyn_get_sensor_data_post`
   * The script can now be run from the command line, but we want it scheduled to run via cron
7.  As `root` you could simply edit the file at: `/etc/crontabs/root` and add the following line:
    * `*/5 * * * * /usr/bin/wovyn_get_sensor_data_post`
    * This will cause the script to be run every 5 minutes
    * This is the same interval as the sensor reporting interval to the router
    * Run the command `service cron restart` (apply changes and restart)
8. The other way to edit the crontab is using the folling set of commands:
   * crontab -e  (edit the crontab for root)
   * <esc> i  (enter insert mode)
   * (move to the end of the file)
   * Enter the new line: `*/5 * * * * /usr/bin/wovyn_get_sensor_data_post`
   * <esc> :wq  (write the file and exit)
   * crontab -l  (verify the new line was saved)
   * service cron restart  (apply changes and restart)
9. The script should now begin running every 5 minutes and reporting the data from your Thread Development Boards

## Sample JSON
The following is an example of the JSON tht arrives at my HTTP endpoint:
```
{
   "report_intervel":300,
   "dev_sw_ver":"2.1.1",
   "dev_proto_ver":3,
   "addr":"fd0b:5fc9:3138:5258:41d0:b6b0:b6d9:332c",
   "connected":true,
   "is_upgrading":false,
   "dev_id":"9483c477aca475c8",
   "dev_rloc16":52224,
   "dev_name":"TBD - Test1",
   "dev_fw_type":"FTD",
   "type":"A0+A1",
   "is_new":false,
   "dev_data":{
      "temperature":22.66777,
      "pressure":83.295992,
      "humidity":29.512023,
      "brightness":76,
      "battery_level":100
   },
   "joined_time":1743351938,
   "timestamp":1745176030,
   "dev_sys_ver":"OPENTHREAD/57ef721ee-dirty; Zephyr; Dec 23 2024 11:46:10",
   "dev_extaddr":"5efeb295c5b6e994"
}
```

## TODO
* Add script to report knob press or turn
* Add script to report PIR Human Detection
* Possibly create an MQTT version of this script
* If MQTT, then add commands to control LEDs and GPIO
* 

## Feedback
Please feel free to open issues against this code.  If you have requests please feel free to add them.
