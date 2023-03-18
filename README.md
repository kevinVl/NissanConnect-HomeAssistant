# NissanConnect

## Nodered
The Nodered flow that you can import will add all required sub-flows and requires minimal config.
The only place where you need to change the credentials is in the following section 
"Session & Token"
![image](https://user-images.githubusercontent.com/6417524/226113614-13dc2775-2496-47db-866e-d51b8332d7b7.png)



You do this by opening the Set flow-vars -config 
![image](https://user-images.githubusercontent.com/6417524/226113713-e1021af9-ccd7-493e-946c-bec2f009ba07.png)
The following dialog is shown :

![image](https://user-images.githubusercontent.com/6417524/226113788-29b8ac02-04fe-4fbc-b15e-54ee89243bcd.png)

Obviously you need to put in your own credentials.

The flows added will make it possible for you to get the following information from your car and will be pulished to MQTT
And this means yuo will also have to add/Change the MQTT credentials 
You do this to double-click on one of the NissanConnect-Status nodes and edit Subflow Template
![image](https://user-images.githubusercontent.com/6417524/226114023-cd57ecfd-6a41-4ffd-aa11-3f1f44ab3dd5.png)
Following flow will be shown.

![image](https://user-images.githubusercontent.com/6417524/226114042-469c403f-9d44-4df8-8a77-06d4328f8e34.png)

We are interested in the MQTT Node where you will change the details to match your own MQTT server



## Adding the Sensors/info on the HomeAssistant Dashboard
As all information is MQTT based you will need to base your sensors on the MQTT topic that get's published, I'm lazy and did not bother to remove leaf from the topic...
The topic will be something like:  **leaf/YOURVIN/location/json**

### Device tracker

Knowing where your beloved Nissan is.
We will add a Device tracker to your configuration.yaml located /config/configuration.yaml with the tool of your choice.

As all info is MQTT based you will create a device tracker based on [MQTT_JSON](https://www.home-assistant.io/integrations/mqtt_json)
Your config should look lije this :
```
device_tracker:
  - platform: mqtt_json
    devices:
    Ariya: leaf/YOURVIN/location/json
```

