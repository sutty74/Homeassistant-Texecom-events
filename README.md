# Homeassistant-Texecom-events

# Description
Transmit texecom intruder alarm events into homeassistant. 

I am by no means a programmer and i have never used github before, i do not have much experience with YAML or Python. There is probably many other easier and tidier ways to accomplish the same task but i put this together with the knowledge i have obtained through my security knowledge and google which is always everyones best friend. 

# Disclaimer

Making changes to the programming of your intruder alarm can have serious consequences and could invalidate your insurance and even cause the intuder alarm not to activate should there be an inturder. I recommend that any programming of the alarm be carried out by a accredited security company.

## Reasons for this project
I wanted to intergrate my intruder alarm with homeassistant, this would allow me to generate automations within homeassistant when the intruder alarm was set & unset. 
I looked at SIA intergration but this has limitations as usually SIA events only transmits when the intruder alarm is set especially with texecom premiere panels, i wanted to monitor the status of zones and events when the intruder alarm was both set and unset again so i could generate automations from zones on the alarm during the unset periods.

## Requirements

Texecom Premiere Elite control panel tested on a Elite 48 Version 3.
Texecom IP module (Not smartcom)
Homeassistant running Node Red

Engineer access will be required into the elite panel, the comm ip will need to be connected to an available comm port if one is on the panel and will need to be configured .
Automation now is on the increase so most alarm installers will easily be able to supply and install this module and configure for you at a charge.

## Getting Started

The comm IP module on the elite alarm panel will need to be configured onto your network, this includes IP address, subnet and port, the default port is 10001 TCP. I recommend a static IP address for the module, this can be easily acheived through the panel programming.
The protocol the comm IP module need to be programmed as is Creston System. The protocol is simple text but also provides zone and system events both when the alarm is set and unset.

# Testing the connection to the alarm panel.

Before the alarm installer leaves i recommend a quick test to ensure the alarm panel is configured correctly, this can be achieved by using your favorite telnet program, i use putty and connect via the IP address and port. When i trigger a zone i get the following information in the putty window.

![putty connection](https://user-images.githubusercontent.com/53712651/127568327-939233dc-7dd4-4239-97d8-2adec89e4045.PNG)

## Integrating into NODE RED.

I use node red to help with the integration, there are probably smoother ways to `integrate but with my limited knowledge this was the easiest way for me to accomplish the task.

First we need to add a TCP In Node in Node red, Enter the details of your Texecom Ethernet module, example below.

![node red tcp in](https://github.com/sutty74/Homeassistant-Texecom-events/assets/53712651/74b9a6c0-8f0e-4953-8d0d-f5c2d009d338)

I added a debug node to the TCP IN Node and opened and closed a zone, just to prove that the texecom events were being seen by Node RED. Here is the event from my front door open and closing. From the debug message below we can see that events are being seen in NODE RED.

![debug event](https://github.com/sutty74/Homeassistant-Texecom-events/assets/53712651/7640c9a1-1609-46ed-90cb-0a6594dcec50)

The above message tells us the following Z = Zone, 001 is the zone number, the last digit 1 means the zone is open and a 0 means the door is closed. Simple :) 

Once events are being seen in Node RED, we can then start splitting out the messages, Creston protocol is quite good at helping us split the messages as the first letter of the message defines the type of message.
Z = Zone
A = Armed
D = Disarmed
U = User Code
T= Tag (Fob)

I use a simple switch node to start to split the messages out:

![event filtering](https://github.com/sutty74/Homeassistant-Texecom-events/assets/53712651/28acbf19-5099-4bd0-926d-4f5c30c69f24)

## Getting the messages ready for use

Starting with zone messages, i needed to adapt the message from the alarm panel slightly, i wanted to remove the preceeding " and the last NEWLINE symbols. A function node containing some code should help.

![parsed messages](https://github.com/sutty74/Homeassistant-Texecom-events/assets/53712651/689fd1e6-b864-4940-b2cb-20989287ede1)

I ended up with the following message of which i can use further in my integration.

![parsed message debug](https://github.com/sutty74/Homeassistant-Texecom-events/assets/53712651/847e2e98-e480-467f-be7e-cb3fd080371b)

I applied the same function node and code for each of the other events being sent from the switch node.

## Sending Zone message to homeassistant

SO next job is to get the message into homeassistant, there maybe a much more efficent way to get them into homeassistant, but with my limited programming knowledge this was the only way i could think of acheiving my goal, this was using MQTT and setting up a MQTT message for each zone from the alarm panel going closed and open.

I created a switch, each zone on the alarm panel had two items with that switch, Item 1 was the zone going closed, and Item 2 was the zone going open, See example below for zones 1 and 2.

![switch node](https://github.com/sutty74/Homeassistant-Texecom-events/assets/53712651/a24c1434-f14c-47a5-b1e2-9de1284d914c)

You will need to do this for every zone on the alarm panel you want to monitor.

Next i wanted to change the message to something that MQTT could use, so i opted for a simple message from each zone, which was open and close.

![change node](https://github.com/sutty74/Homeassistant-Texecom-events/assets/53712651/03cbf8cc-2a76-4fd1-a7dd-6556f5318d8a)

Now we have changed the name of the message, we can simply put this into a MQTT message and send into home assistant.

![mqtt message](https://github.com/sutty74/Homeassistant-Texecom-events/assets/53712651/72fe578d-2437-4c5f-afd1-fe0cf6e7194a)

The MQTT OUT Node will need to be created for each zone you want to monitor in home assistant.

Now the zone status is into homeassistant and you can create your sensors and automations as needed.


Next we look at arming/ Disarming messages and then user codes

TBC.
