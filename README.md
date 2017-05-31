# HomeAssistant Xiaomi Hub Component by Rave (Lazcad)

Credits
---------------
Credits to the following Github project
- https://github.com/fooxy/homeassistant-aqara
- https://github.com/louisZL/lumi-gateway-local-api

Description
---------------
This is an complete Home Assistant component for Xiaomi Gateway. It allows you to integrate the following devices into HA

- Motion Sensor
- Door and Window Sensor
- Button
- Plug
- Aqara Wall Switch (Single)
- Aqara Wall Switch (Double)
- Aqara Wireless Switch (Single)
- Aqara Wireless Switch (Double)
- Cube
- Gateway Light

What's not available?
- Gateway Sensor
- Gateway Radio
- Power Consumption
- Battery

![alt tag](http://lazcad.com/content/images/beer.png)
[Buy me a beer](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=R3P4SPQ7LHXMN)  if you like what you're seeing!

Installation (Raspberry Pi)
---------------------------

1. First, copy all the files into the Home Assistant location. It can now be installed either to the custom_components folder 
 ```
 /home/homeassistant/.homeassistant/custom_components
 ```
 or the root folder (using virtual environment)
 ```
 /srv/homeassistant/homeassistant_venv/lib/python3.4/site-packages/homeassistant/components
 ```

2. Add the following line to the Configuration.yaml. Make sure you're on the latest firmware. You will need to get the Hub's key in order to issue command to the hub like turning on and off plug. Follow the steps here https://github.com/fooxy/homeassistant-aqara/wiki/Enable-dev-mode

 One Gateway
  ```yaml
 #you can leave sid empty if you only have one gateway
 xiaomi:
   gateways:
     - sid:
       key: xxxxxxxxxxxxxxxx
  ```

   One Gateway (No Plug / No Gateway Light / No Aqara)
  ```yaml
 #you can leave out everything if you don't need to control the gateway. Useful if you only need to use Xiaomi Sensors
 xiaomi:
  ```

 Multiple Gateway
  ```yaml
 #12 characters sid can be obtained from the gateway's MAC address (in lowercase without semicolon).
 xiaomi:
   gateways:
     - sid: xxxxxxxxxxxx
       key: xxxxxxxxxxxxxxxx
     - sid: xxxxxxxxxxxx
       key: xxxxxxxxxxxxxxxx
  ```

3. Start HA. Pycrypto should install automatically. If not, install pycrypto manually. if you are using virtual environment, remember to install from virtual environment like below
 ```
 (homeassistant_venv) pi@raspberrypi:~ $ pip3 install pycrypto
 ```

4. Add friendly names to the Configuration.yaml like below
  ```yaml
    customize:
      binary_sensor.switch_158d000xxxxxc3:
          friendly_name: Ktichen Switch
      binary_sensor.switch_158d000xxxxxc2:
          friendly_name: Table Switch
      binary_sensor.door_window_sensor_158d000xxxxx7a:
          friendly_name: Door Sensor
  ```
        
5. Add automation. For the Button and Switch, use the following event. Available click types are 'single', 'double' and 'hold'. For door window sensor and motion sensor, you can use State or Event as trigger
  ```yaml
  automation:
  - alias: Turn on Dining Light when click
    trigger:
      platform: event
      event_type: click
      event_data:
          entity_id: binary_sensor.switch_158d000xxxxxc2
          click_type: single
    action:
      service: switch.toggle
      entity_id: switch.wall_switch_left_158d000xxxxx01
  ```
  
  ```yaml
    #trigger for motion sensor using State
	
    trigger:
      platform: state
      entity_id: binary_sensor.motion_sensor_158d000xxxxxc2
      from: 'no_motion'
	  to: 'motion'
	  
    #trigger for motion sensor using Event
	
    trigger:
      platform: event
      event_type: motion
      event_data:
          entity_id: binary_sensor.motion_sensor_158d000xxxxxc2
  ```
  
  ```yaml
    #trigger for door window sensor using State
	
    trigger:
      platform: state
      entity_id: binary_sensor.door_window_sensor_158d000xxxxxc2
      from: 'no_motion'
	  to: 'motion'

  ```

  
6. For Cube, use the following trigger. Available actions are flip90, flip180, move, tap_twice, shake_air, swing, alert, free_fall and rotate

 ```yaml
    trigger:
      platform: event
      event_type: cube_action
      event_data:
          entity_id: binary_sensor.cube_158d000xxxxxc2
          action_type: flip90
 ```

Important! Only use this if you have have issue with Socket binding or multicast. Using this when you have no problem with socket or mcast will introduce other issues. Add the IP address of the network interface to the config
 
 ```yaml
 xiaomi:
   gateways:
     - sid: xxxxxxxxxxxx
       key: xxxxxxxxxxxxxxxx
     - sid: xxxxxxxxxxxx
       key: xxxxxxxxxxxxxxxx
    interface: xx.xx.xx.xx
 ```

Other options
 ```yaml
 xiaomi:
   gateways:
     - sid: xxxxxxxxxxxx
       key: xxxxxxxxxxxxxxxx
    interface: xx.xx.xx.xx
    poll_motion: True # True (Default) or False. If False, motion sensor will be deactivated in 2 minutes. Turn on polling will shorten the time to 1 minute
 ```
