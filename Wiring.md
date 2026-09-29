## Wiring

### Components

#### 24v DC Magnet (1500KG) ~3amps
![alt text](magnet_website.png)


#### MOSFET Module IRF520 3.3-5V Turn on, rated for 0-24V
![alt text](image.png)
#### Flyback Diode - MBR10100 (REQUIRED)
![alt text](image-1.png)
#### 18AWG Wire
![alt text](image-2.png)
#### Arduino Uno
![alt text](image-3.png)

Arduinos are expensive! But they are also open source! I bought some arduino clones off Alibaba (10 for $35, 1/10 of the price as of 2026)

#### Male to Female Connectors
![alt text](image-11.png)

#### Wire Stripper/Crimper
![alt text](image-5.png)
#### Fork-type Terminal(also called terminal lug)
![alt text](image-6.png)
#### Block Terminal
![alt text](image-7.png)
#### 24V 10A Switching Power Supply
![alt text](image-8.png)

#### Bare Wire
![alt text](image-9.png)

### Assembly

The basic diagram looks something like the following.
![alt text](image-10.png)


The mosfet module has three pins that help to control the gate. SIGNAL connects to pwm 9, VCC connects to 5V, and GND connects to ground.


To connect the magnet and power supply to the mosfet module I used 4 18awg wires and connected a fork type terminal to one end of each wire. 
The wires on the magnet came bare so I also added fork type terminals to those as well. 



Switching power supplies typically have 2 terminals for AC power. One will be marked as L and the other as N. It doesn't matter much when connecting to the outlet with wire connects to what but following spec helps to keep things clear.

![alt text](<assembly power supply AC_1.384.1.png>)


The rest of the terminals on the power supply are for DC power, the terminals on my power supply are separated by a line with the terminals to left being V+ and the right being V-. 

![alt text](<assembly power supply DC_1.391.1.png>)




#### Connecting the magnet

I used the remaining two wires that I crimped and connected them to the load side of the mosfet module. To connect the two cables coming from the mosfet and the two cables from the magnet with the flyback diode, I used a terminal block like so.

![alt text](<assembly terminal block magnet 3_1.423.1.png>)

### Tip
I screwed on the wires coming from the mosfet into the block terminal first. On the other side, I connected one of the wires from the magnet, screwed it down halfway, put the flyback diode in, and then plugged the other wire coming from the magnet. With each wire in place, I screwed down both wires coming from the magnet. 


![alt text](<assembly terminal block magnet 1_1.428.1.png>)

![alt text](<assembly terminal block magnet 2_1.422.1.png>)



Note, it is very important that the diode is placed the right way. This will ensure that the inductive spikes have a safe return path. The diode should be in a bridge configuration against the current flow. Be warned, I fried my computer because of this. 


 
![alt text](<assembly terminal block magnet 4_1.424.1.png>)


My biggest advice is that if you, like me, are finding yourself tearing down and putting the setup back together, always have the v- wire on the same place (last terminal slot on the block terminal ), that way you can't misplace it. 
