# Tutorial BSB LAN
Yann RITTER Home Assistant Tutorial 

When the control of the heat pump becomes possible!
Integration of an Atlantic Alfea extentia duo heat pump (2015 model not AI).

## Compatibility
See the list of compatible devices : https://github.com/fredlcore/BSB-LAN

A short tutorial that explains the key steps and important details to integrate your heating system into your home assistant.

Thank Frederik, initiator of the project, for his work and his responsiveness.

The git of the project : https://github.com/fredlcore/BSB-LAN

I am not the DEV of the necessary integrations, but I'm just a passionate person having assimilated the technique and the various manipulations and I wish to help you by sharing all that! 😉

## Warning 

If, you are reading this ! No, but it's not just the tutorial that counts! We have some important things to tell you first.
Now you're an adult, you've grown up, so you understand that all the manipulations you're going to do are your responsibility!
If your vacuum cleaner goes backwards instead of forwards, if it dances the samba, if it vacuums the bathroom instead of mopping the kitchen, if your Home Assistant crashes, or if your Raspberry explodes... It's not our fault!
Everything that follows was first installed and tested by us. It works. And we use it every day!
So you take your time, you read well, you do not make the child to want to go too fast and skip steps in mode "Yeah, I know!
This is not a tutorial on the basics, so some steps are taken for granted and will not be detailed here. There's always the group if you really need help...
Shall we go?


## Prerequisites / Installation of modules
Have Mosquitto broker installed

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/mosquitto.JPG)

I use the default configuration of the module

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/mosquitto1.JPG)
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/mosquitto2.JPG)



Do you have a compatible heating system and bought / connected the card to your heating system?
If not, you can check the git of the project (https://github.com/fredlcore/BSB-LAN) and you can contact Frederik by e-mail in German or English at the following address: bsb@code-it.de

If Yes, you can read it.

In my case, I use the ESP32 version: https://1coderookie.github.io/BSB-LPB-LAN_EN/QSG_ESP32.html

And I want to make the BSB-LAN module communicate with MQTT, so the module pushes me the values every X seconds and when changes are requested in MQTT.

### Step 1 

Create or reuse a home assistant user to enable MQTT authentication.

In home assistant go to Settings \ People then to the user tab

Click on the create a user button and complete as below:

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/utilisateur.JPG)


### Step 2

In Parameter \ Integration make sure you have MQTT integration, otherwise add it. 

 - broker : localhost
 - port : 1883
 - user : the one created previously
 - password : same as the one linked to the user

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/haintegration.JPG)


### Step 3 

Now, you have to set up the BSB-LAN module

From a web browser : http://bsb-lan.local/

The following page should appear

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/bsblan0.JPG)

Then click on the Setting menu and change the following parameters:
 - The advanced configuration will allow you to enter the MQTT settings.
 - The write access will allow modification from the web interface and MQTT / HTTP

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/bsblan1.JPG)

Then you will change the specific MQTT settings

 - Logging mode --> enable mqtt
 - Interval (seconds) --> maximum time between each upload
 - Parameters --> list of parameters you want to upload / use in HA the full list is available via the Heater Function menu
 - Usage --> format of the mqtt message, the simplest (plain text)
 - IP address broker --> the ip address of your mosquitto server (in my case my HA instance)
 - username --> username for the MQTT connection, so the one created in the previous step in HA
 - Password --> the password associated with the user

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/bsblan2.jpg)

You will be able to control the ascents with the MQTT explorer application for example: http://mqtt-explorer.com/

Log in with the user/password on the ip address of the MQTT broker and normally the settings are displayed after max 30 seconds, if you have copied my bsb lan configuration.

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/mqttexplorer.JPG)


### Step 4

You will be able to retrieve the data from the Home assistant.

In the configuration.yaml file add the configuration as below:

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/configurationha1.JPG)


````

   ############################################################
   ##                         MQTT                           ##
   ############################################################
mqtt:
    sensor: !include mqtt/mqtt.yaml
     
````

Then in the "config" folder, create a "mqtt" folder, and a "mqtt.yaml" file

And add the following configuration to allow the return of the parameters previously entered in BSB-LAN.

````
   ############################################################
   ##                         MQTT  BSB LAN                  ##
   ############################################################
    - name: "Mode de chauffe"
      state_topic: "BSB-LAN/700.00"
      
    - name: "Mode ESC"
      state_topic: "BSB-LAN/1600.00"
      
    - name: "Température consigne confort"
      state_topic: "BSB-LAN/710.00"
      unit_of_measurement: °C
      device_class: temperature
      
    - name: "Température consigne reduit"
      state_topic: "BSB-LAN/712.00"
      unit_of_measurement: °C
      device_class: temperature
      
    - name: "Température sonde exterieur"
      state_topic: "BSB-LAN/8700.00"
      unit_of_measurement: °C
      device_class: temperature
      
    - name: "Température thermosat interieur"
      state_topic: "BSB-LAN/8740.00"
      unit_of_measurement: °C
      device_class: temperature
      
    - name: "Etat du circuit de chauffage"
      state_topic: "BSB-LAN/8000.00"
      
    - name: "Etat de l'ECS"
      state_topic: "BSB-LAN/8003.00"
      
    - name: "Etat pompe à chaleur"
      state_topic: "BSB-LAN/8006.00"
      
    - name: "Température de retour pompe à chaleur"
      state_topic: "BSB-LAN/8410.00"
      unit_of_measurement: °C
      device_class: temperature
      
    - name: "Température de départ pompe à chaleur"
      state_topic: "BSB-LAN/8412.00"
      unit_of_measurement: °C
      device_class: temperature
      
    - name: "Etat pompe ECS"
      state_topic: "BSB-LAN/8820.00"
      
    - name: "Etat résistance électrique ECS"
      state_topic: "BSB-LAN/8821.00"
      
    - name: "Température mesurée ECS"
      state_topic: "BSB-LAN/8830.00"
      unit_of_measurement: °C
      device_class: temperature
````

Save the configuration and restart home assistant.

You now have the values available in sensors.

Here is an example: 

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/haresultat1.JPG)


### Step 5

The modification from home assistant. 

I am now going to test the modification of a value via MQTT to switch from frost protection mode to automatic mode.

To do this, I need to send "1" to parameter "700".

Since the MQTT integration, it is possible to test the publication of a package.

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/testpaquet.JPG)

You can see that the heating mode has switched to automatic mode.

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/testpaquetresultat.JPG)

I will show you how to change the state from a virtual button in HA

In the "configuration.yaml", add the button link as below:
````

   ############################################################
   ##                         MQTT                           ##
   ############################################################
mqtt:
    sensor: !include mqtt/mqtt.yaml
    button: !include mqtt/button.yaml
     
````

Then create the file "button.yaml" in the "mqtt" folder

and add the following content: 

````

   ############################################################
   ##                         MQTT  BSB lan bouton           ##
   ############################################################
    - unique_id: "PAC mode auto"
      name: "PAC mode auto"
      command_topic: "BSB-LAN"
      payload_press: "S700=1"
      availability:
        - topic: "BSB-LAN/status"
      qos: 0
      retain: false
      entity_category: "config"
````

Explanation:
 
- command_topic: topic to call 
- payload_press : command to send when clicking  /S for the modification then the element code to modify = the value to fill in
- entity_category : says you are acting on the configuration
     availability:
        - topic: "BSB-LAN/status" allows to manage the status if the sensor is not available

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/haresultat2.JPG)

### Step 6 

The creation of an MQTT thermosat for the control of the CAP

In the "configuration.yaml", add the climate link as below:

````

   ############################################################
   ##                         MQTT                           ##
   ############################################################
mqtt:
    cover: !include cover/volet.yaml
    sensor: !include mqtt/mqtt.yaml
    button: !include mqtt/button.yaml
    climate: !include mqtt/climate.yaml
````

Then create the file "climate.yaml" in the "mqtt" folder

and add the following content: 

````
   ############################################################
   ##                         MQTT  BSB LAN                  ##
   ############################################################
        - name: "Pompe à chaleur"
          payload_on: 1
          payload_off: 0
          modes:
            - auto
            - heat
            - cool
            - 'off'
          mode_state_topic: "BSB-LAN/700.00"
          mode_state_template: >-
             {% set values = { '0 - Mode protection':'off', '1 - Automatique':'auto', '2 - Réduit':'cool', '3 - Confort':'heat'} %}
             {{ values[value] if value in values.keys() else 'off' }}
          mode_command_topic: "BSB-LAN"
          mode_command_template: >-
             {% set values = { 'off':'S700=0', 'auto':'S700=1', 'cool':'S700=2', 'heat':'S700=3'} %}
             {{ values[value] if value in values.keys() else '0' }}
          current_temperature_topic: "BSB-LAN/8740.00"
          min_temp: 17
          max_temp: 24
          temp_step: 0.5
          temperature_state_topic: "BSB-LAN/710.00"
          temperature_command_topic: "BSB-LAN"
          temperature_command_template: "{{'S710='+ (value| string)}}"
````

Explanation:  

Here is the doc: https://www.home-assistant.io/integrations/climate.mqtt/

 - mode_state_topic: heading returning the heating mode
 - mode_state_template: template to convert responses to the known home assistant mode, I didn't find anything but cool for the reduced mode (eco)
 - mode_command_topic: topic to call for sending a message
 - mode_command_template : template to convert the choice on HA to known bsb-lan code
 - current_temperature_topic: topic to return the current temperature (thermostat)
 - min_temp: to define the selectable minimum temperature
 - max_temp: to define the max selectable temperature
 - temp_step: precision in steps of 0.5 degrees
 - temperature_state_topic: item returning the set comfort temperature
 - temperature_command_topic: topic to be called to send a message
 - temperature_command_template: template to convert the selected temperature into a bsb-lan update message

The result in image 

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/climateoff.JPG)
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/climateauto.JPG)
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/climatechauffe.JPG)
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/climatereduit.JPG)



### Step 7 


And since I'm nice, we'll create a switch to turn on or off the hot water production

In the "configuration.yaml", add the switch list as below:

The configuration will grow as the tutorial goes on
````

   ############################################################
   ##                         MQTT                           ##
   ############################################################
mqtt:
    cover: !include cover/volet.yaml
    sensor: !include mqtt/mqtt.yaml
    button: !include mqtt/button.yaml
    climate: !include mqtt/climate.yaml
    switch: !include mqtt/switch.yaml
````

Then create the file "switch.yaml" in the "mqtt" folder

and add the following content : 

````
      ############################################################
   ##                         MQTT  BSB LAN                  ##
   ############################################################
    - name: "Activation ECS"
      state_topic: "BSB-LAN/1600.00"
      command_topic: "BSB-LAN"
      payload_on: "S1600=1"
      payload_off: "S1600=0"
      state_on: "1 - Marche"
      state_off: "0 - Arrêt"
````

Explanation:  

The link to the official doc: https://www.home-assistant.io/integrations/switch.mqtt/

 - state_topic: topic returning the hot water production mode
 - command_topic: topic to call for sending a message
 - payload_on: value to be transmitted when the switch is activated
 - payload_off: value to be transmitted when the switch is deactivated
 - state_on: value transmitted by bsb-lan and to be interpreted to make the switch active
 - state_off: value transmitted by bsb-lan and to be interpreted to make the switch deactivated

In image in HA

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/switch.JPG)

### Step 8 

You can now change all kinds of values and display them.

You are free to create automations.


Here are my ideas: 

 - Mode change with alarm activation
 - Change of mode on absence programmed in the agenda
 - Change of mode if the window has been open for more than x minutes



## The last word

A little doc, a tutorial, which I hope helped you as much as possible.
I realized it with pleasure in a spirit of mutual aid, voluntarily, in parallel of my job and my life.
I don't support all the problems but I will be there if needed on the group, as usual.
If everything works out, thank me.

Yann Ritter
