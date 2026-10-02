---
layout: post
title: Ferrofluid Display - Wiring
date: 2026-10-01 05:36 -0400
---


## Components

#### 24v DC Magnet (1500KG) ~3amps

![alt text](<images/magnet display_1.365.1.png>)

https://www.alibaba.com/product-detail/P20063-Diameter-20cm-24v-DC-Strong_1601396493953.html?spm=a2700.galleryofferlist.normal_offer.d_title.6ecf13a0tg9dEy&priceId=8a2e60d7f84d4ac5af6b5c1b5c3df37

f
#### MOSFET Module IRF520 3.3-5V Turn on, rated for 0-24V
![alt text](images/image.png)

#### Flyback Diode - MBR10100
![alt text](images/image-1.png)

#### 18AWG Wire
![alt text](images/image-2.png)

#### Arduino Uno
![alt text](images/image-3.png)

Arduinos are expensive! But they are also open source! I bought some arduino clones off Alibaba (10 for $35, 1/10 of the price as of 2026)

#### Male to Female Connectors
![alt text](images/image-11.png)

#### Wire Stripper/Crimper
![alt text](images/image-5.png)

#### Fork-type Terminal(also called terminal lug)
![alt text](images/image-6.png)

#### Block Terminal
![alt text](images/image-7.png)

#### 24V 10A Switching Power Supply
![alt text](images/image-8.png)

#### Bare Wire
![alt text](images/image-9.png)

## Assembly

![alt text](images/image-10.png)


The goal is to be able to vary the strength of the magnet. A MOSFET is a gate that can open and close very quickly depending on the current/voltage supplied. By closing and opening the gate at certain rates, it can control how much power on average is given to the magnet. A MOSFET 'module' helps to connect all the components together in a neat/safe fashion by providing terminals and pin connectors. An alternative is to solder the mosfet with all the components. 


### Mosfet Module to Arduino
The mosfet module has three pins that help to control the gate. 

|MOSFET  |  Arduino |
|------------------ |
|SIGNAL  |  ~9      |
|VCC     |  5V      |
|GND     |  GND     |

![alt text](<images/mosfet control wires_1.410.1.png>)


![alt text](<images/mosfet module diagram.png>)




### Power Supply


**DO NOT PLUG ANYTHING INTO THE WALL OUTLET YET**

Switching power supplies take in AC power and output DC power. 
There are two terminals for AC power input. These connect to the outlet on your wall. Of the two AC terminals, one terminal will be marked as L and the other as N.

Connect the bare outlet wire to the Power supply. One cable in L and the other in N.  

**DO NOT PLUG ANYTHING INTO THE WALL OUTLET YET**


![alt text](<images/assembly power supply AC_1.384.1.png>)



### MOSFET Setup

The rest of the terminals on the power supply are for DC power, the terminals on my power supply are separated by a line with the terminals to left being V+ and the right being V-. 


There are four terminals on the MOSFET module. One pair connects to the load and the other pair connects power. 

Cut four 18awg wires. Attach fork terminals to one end of each wire.

![alt text](<images/wires crimped_1.570.1.png>)


### MOSFET to Power

Two terminals on MOSFET module are for the power. On my MOSFET module they are labeled GND and VIN. Connect two of the cut wires here. 



![alt text](<images/mosfet load wires_1.427.2.png>)

 
Connect the GND terminal on the MOSFET module to a  V- terminal on the power supply. 

Connect the VIN terminal on the mosfet MODULE to a V+ terminal on the power supply.


![alt text](<images/assembly power supply DC_1.391.1.png>)



### MOSFET to Magnet

The remaining two terminals on the MOSFET module are for the load. 

Connect the remaining two cut out wires to the load side of the MOSFET module. 


![alt text](<images/mosfet power wires_1.427.2_1.427.1.png>)


Connect the two cables coming from the MOSFET load side and the two cables from the magnet with the flyback diode through a terminal block like so. 
The flyback diode is on the side of the magnet wires in the image below. 

![alt text](<images/assembly terminal block magnet 3_1.423.1.png>)


Note, it is very important that the diode is placed the right way. This will ensure that the inductive spikes have a safe return path. The diode should be in a bridge configuration against the current flow. Be warned, I fried my computer because of this. 

 
![alt text](<images/assembly terminal block magnet 4_1.424.1.png>)


The flyback diode helps to protect the MOSFET module against inductive spikes. The block terminal connects the magnet, flyback diode, and the MOSFET module together. 



### Tip

Screw on the wires coming from the mosfet into the block terminal first. On the other side, connect one of the wires from the magnet, screw it down halfway, put the flyback diode in, and then plug the other wire coming from the magnet. With each wire in place, screw down both wires coming from the magnet. Noe the orientation of the flyback diode (close up at the bottom). 


![alt text](<images/assembly terminal block magnet 1_1.428.1.png>)

![alt text](<images/assembly terminal block magnet 2_1.422.1.png>)




My biggest advice is that if you, like me, are finding yourself tearing down and putting the setup back together, always have the v- wire on the same place (last terminal slot on the block terminal ), that way you can't misplace it. 


**With everything in place you can now plug the power supply to the wall**

