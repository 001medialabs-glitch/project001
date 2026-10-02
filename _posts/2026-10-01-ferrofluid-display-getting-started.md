---
layout: post
title: Ferrofluid Display - Getting Started
date: 2026-10-01 05:30 -0400
---


### OS/Hardware Configuration

Windows 10/11

USB 3.0 (Kinect requires USB 3.X)



### Kinect 2.0 SDK

Install Kinect 2.0 SDK from Microsoft

https://www.microsoft.com/en-us/download/details.aspx?id=44561

Make sure that it is version 2



####  Kinect and Adapter

![alt text](<images/kinect_1.81.2.png>)

![alt text](<images/kinect adapter.jpg>)


Plug in the Kinect to your computer. Open the Kinect Browser SDK, and run the configuration verifier program. 

![alt text](<images/Kinect Browser SDK.png>)

If the Kinect power cycles, do the following:

- Turn off microphone enhancements in windows settings
- Turn on microphone access in privacy settings




![alt text](<images/kinect allow microphone.png>)
![alt text](<images/kinect audio enhancements.png>)

### Visual Studio


Intall Visual Studio. 

Select Console App (.NET Framework)

![alt text](<images/Screenshot 2026-09-20 144217.png>)


#### Install Lightbuzz Kinect SDK to the project
In visual studio, go to: 

Tools -> nuget packet manager -> packet manager console

https://github.com/LightBuzz/Vitruvius

Type in the package manager console (noted my “PM>” )

        Install-Package lightbuzz-vitruvius


#### Add Kinect Extensions
On the right hand side of solution explorer 
Right Click on references -> add references -> extensions -> select all of the Kinect related extensions

![alt text](<images/Screenshot 2026-09-20 144719.png>)

#### Select Platform Target
Any CPU drop down -> left click the drop down under active solution platform -> < new> -> select x64(x64 64 bit / x86 32 bit) or ARM depending on your laptop. 

![alt text](<images/Screenshot 2026-09-20 152332.png>)




The next section covers how to wire all the components together. 