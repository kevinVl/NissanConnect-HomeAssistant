# NissanConnect
First of all, I did not create these awesome flows, I merly adapted them, and make them working again after some URL changes by Nissan.

All credit goes to [netmb](https://github.com/netmb/nodered-nissanconnect) and more so to the amazing work of [Tobias Westergaard](https://gitlab.com/tobiaswkjeldsen/dartnissanconnect), where his code gave me good pointers.

:warning:
Be careful, especially with usage of the `NissanConnectAction-` actions. These will make a connection to your car (wakeup SMS). If you use it to often you will drain your 12V-Battery or/and will be banned by the NissanConnect-Service.

:pushpin: What is not possible at this point is to Lock/Unlock the car, the API requires an additional SRP HASH key. It is currently not known how to get this yet.

Task list :
- [x] Battery status
- [x] Lock state
- [x] Location
- [ ] Set climate from HA, but is already possible in the flows

## Nodered
The Nodered flow that you can import will add all required sub-flows and requires minimal config.

The only place where you need to change the credentials is in the following section "Session & Token":
![image](https://user-images.githubusercontent.com/6417524/226113614-13dc2775-2496-47db-866e-d51b8332d7b7.png)

You do this by opening the Set `Flow-Vars` `-config `:
![image](https://user-images.githubusercontent.com/6417524/226113713-e1021af9-ccd7-493e-946c-bec2f009ba07.png)

The following dialog is shown:
![image](https://user-images.githubusercontent.com/6417524/226113788-29b8ac02-04fe-4fbc-b15e-54ee89243bcd.png)

Obviously you need to put in your own credentials.

The flows added will make it possible for you to get the following information from your car and will be pulished to MQTT.

And this means you will also have to add/change the MQTT credentials.

You do this to double-click on one of the NissanConnect-Status nodes and edit Subflow Template:
![image](https://user-images.githubusercontent.com/6417524/226114023-cd57ecfd-6a41-4ffd-aa11-3f1f44ab3dd5.png)

The following flow will be shown:
![image](https://user-images.githubusercontent.com/6417524/226114042-469c403f-9d44-4df8-8a77-06d4328f8e34.png)

We are interested in the MQTT Node where you will change the details to match your own MQTT server.

The following information is currently broadcast to MQTT:
![image](https://user-images.githubusercontent.com/6417524/226115468-f4d8bda3-c961-4179-81e9-d3b99dfa7319.png)

## Adding the Sensors/info on the HomeAssistant Dashboard
As all information is MQTT based you will need to base your sensors on the MQTT topic that get's published, I'm lazy and did not bother to remove leaf from the topic...

The topic will be something like:  **leaf/YOURVIN/location/json**.

### Device tracker
Knowing where your beloved Nissan is. We will add a Device tracker to your `configuration.yaml` located at `/config/configuration.yaml` with the tool of your choice.

As all info is MQTT based you will create a device tracker based on [MQTT_JSON](https://www.home-assistant.io/integrations/mqtt_json).

Your config should look like this :
```yaml
device_tracker:
  - platform: mqtt_json
    devices:
    Ariya: leaf/YOURVIN/location/json
```

### Sensors

```yaml
# configuration.yaml entry
mqtt:
  sensor:
###### BATTERY ########      
    - name: Ariya_battery_last_updated
      state_topic: "leaf/YOURVIN/batteryStatus/timestamp"
      device_class: timestamp

    - name: leaf_battery_level
      state_topic: "leaf/YOURVIN/batteryStatus/batteryLevel"
      unit_of_measurement: "%"
      device_class: battery

    - name: Ariya_battery_Autonomy
        # Since VIN is not specified, it will represent the state from the first vehicle in the account.
      state_topic: "leaf/YOURVIN/batteryStatus/batteryAutonomy"
      device_class: distance
      unit_of_measurement: "km"

    - name: Ariya_battery_last_updated
      state_topic: "leaf/YOURVIN/batteryStatus/timestamp"
      device_class: timestamp
###### END BATTERY ########            

###### Climate ########      
    - name: "Ariya_cabin_temperature"
      state_topic: "leaf/YOURVIN/hVacStatus/internalTemperature"
      device_class: temperature
      unit_of_measurement: "C"
      
    - name: "Ariya_cabin_temperature"
      state_topic: "leaf/YOURVIN/hVacStatus/internalTemperature"
      device_class: temperature
      unit_of_measurement: "C"     
###### Climate end ########      

###### LOCK ########      
    - name: "Ariya Sunroof state"
      state_topic: "leaf/YOURVIN/LockStatus/sunroofStatus"

    - name: "Ariya_Lock_state"
      state_topic: "leaf/YOURVIN/LockStatus/lockStatus"      
      
    - name: "Ariya_Lock_state_LastUpdate"
      state_topic: "leaf/YOURVIN/LockStatus/lastUpdateTime"
      device_class: timestamp
###### LOCK END########      
  binary_sensor:
    - name: Ariya_plugged_Status
      state_topic: "leaf/YOURVIN/batteryStatus/plugStatus"
      payload_on: "1"
      payload_off: "0"

    - name: Ariya_charging_Status
      state_topic: "leaf/YOURVIN/batteryStatus/chargingStatus"
      payload_on: "1"
      payload_off: "0"

```

Once you have the sensors you can start adding them to your HA dashboard, your imagination is the only limiting factor:
![Screenshot 2023-03-18 163535 (1)](https://user-images.githubusercontent.com/6417524/226115683-c72ed14a-1e48-4048-a215-52f7a9e41f2f.png)
