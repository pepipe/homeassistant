# Creation of MQTT graph for Home Assistant.

This is based on having HA running in a Python Virtual Enviroment on a Raspberry pi with raspbian.

### Creation of tab in frontend
1. Modification in HA configuration.yaml. Add inside the component ```panel_iframe```:

        panel_iframe:
          zigbee_graph:
            title: 'Zigbee Graph'
            url: '/local/zigbee_graph.html'
            icon: mdi:graphql
            
1. Create html file ```zigbee_graph.html``` inside homeassistant/www/ folder: 
  
    Because this is an iframe we can add the only the code for the graph image: ```<img src="/local/mqtt_graph.png" />```
    
We'll have now a new tab in HA frontend. For now it will point for an image that doesn't exist yet.

### Creation of the MQTT graph image
1. Install needed tools:
    1. Mosquitto: ```sudo apt-get install mosquitto``` and ```sudo apt-get install mosquitto-clients```
      
        [Check here](https://www.home-assistant.io/docs/mqtt/testing/) for MQTT testing for Home Assistant.
    1. inotify-tools: ```sudo apt-get install inotify-tools```
    
    1. Graphviz: ```sudo apt-get install graphviz```
    
1. Create a service for subscribing MQTT topic ```zigbee2mqtt/bridge/networkmap/graphviz```

    More info on autostart systemd [here](https://www.home-assistant.io/docs/autostart/systemd/).
    1. Create a bash script inside ```/usr/bin/``` called ```mqtt-graph-sub```
        1. By doing ```sudo nano mqtt-graph-sub```
        1. Put this inside the file:
        
                #!/bin/bash
                echo "MQTT graph subbed."
                mosquitto_sub -V mqttv311 -u homeassistant -P <mqtt_broker_pass> -t 'zigbee2mqtt/bridge/networkmap/graphviz' > <path_to_home_assistant>/www/mqtt_graph.dot
            Note: This is for HA MQTT embedded broker.
            
            Note II: Don't forget to make the script executable by doing ```sudo chmod +x mqtt-graph-sub```            
    1. Go to ```/etc/systemd/system/``` and create file ```mqtt-graph-sub@<your_user>```
        1. By doing ```sudo nano mqtt-graph-sub@<your_user>.service```
        1. Put this inside the file:
        
                [Unit]
                Description=MQTT Graph Sub

                [Service]
                Type=simple
                User=homeassistant
                ExecStart=/usr/bin/mqtt-graph-sub

                [Install]
                WantedBy=multi-user.target
        1. Enable the service so when system restart will load automatically: ```sudo systemctl enable mqtt-graph-sub@<your_user>.service```
        1. To start the service now: ```sudo systemctl start mqtt-graph-sub@<your_user>.service```
        1. To remove from boot load: ```sudo systemctl disable mqtt-graph-sub@<your_user>.service```
      
    1. Test if service is running ok:
        Do a MQTT publish: ```mosquitto_pub -V mqttv311 -u homeassistant -P <mqtt_broker_pass> -t 'zigbee2mqtt/bridge/networkmap' -m graphviz``` and go to ```<path_to_home_assistant>/www/```. If everything worked, you should have there a file called mqtt_graph.dot
        
1. Create a service for creating MQTT graph image.

    1. Create a bash script inside ```/usr/bin/``` called ```mqtt-graph-create```
        1. By doing ```sudo nano mqtt-graph-create```
        1. Put this inside the file:
        
                #!/bin/bash
                while inotifywait -q -e modify <path_to_home_assistant>/www/mqtt_graph.dot >/dev/null; do echo "mqtt_graph.dot is changed"; sudo -u homeassistant dot <path_to_home_assistant>/www/mqtt_graph.dot -Tpng -o <path_to_home_assistant>/www/mqtt_graph.png; sudo systemctl restart mqtt-graph-sub@homeassistant.service; done;
                echo "MQTT graph image created."          
    1. Go to ```/etc/systemd/system/``` and create file ```mqtt-graph-create@<your_user>```
        1. By doing ```sudo nano mqtt-graph-create@<your_user>.service```
        1. Put this inside the file:
        
                [Unit]
                Description=MQTT Create Graph 

                [Service]
                Type=simple
                ExecStart=/usr/bin/mqtt-graph-create

                [Install]
                WantedBy=multi-user.target
        1. Enable the service so when system restart will load automatically: ```sudo systemctl enable mqtt-graph-create@<your_user>.service```
        1. To start the service now: ```sudo systemctl start mqtt-graph-create@<your_user>.service```
        1. To remove from boot load: ```sudo systemctl disable mqtt-graph-create@<your_user>.service```
      
    1. Test if service is running ok:
        Do a MQTT publish: ```mosquitto_pub -V mqttv311 -u homeassistant -P <mqtt_broker_pass> -t 'zigbee2mqtt/bridge/networkmap' -m graphviz``` and go to ```<path_to_home_assistant>/www/```. If everything worked, you should have there a file called mqtt_graph.png
        
### Make an automation that will run on sunset the graph creation.
1. Create a shell command [(check)](https://www.home-assistant.io/components/shell_command/):

        shell_command:
          mosquitto_pub_mqtt_graph: "mosquitto_pub -V mqttv311 -u homeassistant -P <mqtt_broker_pass> -t 'zigbee2mqtt/bridge/networkmap' -m graphviz"
          
1. Create the automation:

        automation:
          - alias: Create MQTT Graph
            initial_state: false
            trigger:
              platform: sun
              event: sunset
            action:
              service: shell_command.mosquitto_pub_mqtt_graph
