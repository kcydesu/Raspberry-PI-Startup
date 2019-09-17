# Raspberry-PI-Startup
Guide to setting up a raspberry pi to report its IP Address to a MQTT Broker on startup, allowing the user to SSH into the device.
## Hardware
Download balenaEtcher from the first link below and the latest version of Raspbian from the second link below:
1. https://www.balena.io/etcher/
2. https://www.raspberrypi.org/downloads/raspbian/

Use balenaEtcher to flash Raspbian onto a microSD Card, and then download the the interfaces file and wpa_supplicant.conf from this repository.

After downloading both of these files, edit the wpa_supplicant.conf file to match your WiFi settings, and move both of these files into the /bin folder on your microSD Card.

Connect a USB to Serial cable to your Raspberry Pi, allowing you to access your Raspberry Pi using the screen command.
```
sudo screen /dev/ttuUSB0 115200
```

## Software
After loading into the Raspberry Pi, run the following lines in the command script in order to install the dependencies to use MQTT protocol on your Raspberry Pi.
```
sudo apt-get install mosquitto mosquitto-clients
```
After installing mosquitto and mosquitto-clients, navigate to /etc and nano into the rc.local file. Edit the file to match the following code, getting the IP and sending it to your specified MQTT Brocker. Make sure your script ends with ```exit 0``` or the script will run into an error.
```
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.

export IPADD=`hostname -I`
export MSG='CASEY_BRUNO_VM_IP_@_$IPADD'
mosquitto_pub -h soldier.cloudmqtt.com -u USERNAME -P PASSWORD -p PORTNUM -t TOPIC -m $MSG --quiet
mosquitto_pub -h soldier.cloudmqtt.com -u USERNAME -P PASSWORD -p PORTNUM -t TOPIC -m $IPADD --quiet

exit 0
```
Change the permissions of rc.local in order to allow it to run on startup. This startup file waits for networking to be available to execute, so we don't need any additional logic to ensure that networking is available.
```
sudo chmod 755 rc.local
```
Now, after downloading and initializing MQTTBox (third link down when you Google 'MQTTBox'), you will see the IP Address of your Raspberry Pi in MQTTBox.
