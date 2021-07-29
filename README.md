# Homeassistant-Texecom-events

# Description
Transmit texecom intruder alarm events into homeassistant. 

I am by no means a programmer and i have never used github before, i do not have much experience with YAML or Python. There is probably many other easier and tidier ways to accomplish the same task but i put this together with the knowledge i have obtained through my security knowledge and google which is always everyones best friend. 

# Disclaimer

please compelete

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
