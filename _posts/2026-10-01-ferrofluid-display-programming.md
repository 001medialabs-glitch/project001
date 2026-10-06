---
layout: post
title: Ferrofluid Display - Programming
date: 2026-10-01 05:39 -0400
---


## Code

You can find the full and latest code [here]({% post_url 2026-10-01-ferrofluid-display-full-code  %}).

### JOURNEY
- Turn the magnet on and off 
    
- Vary the strength of the magnet
    
- Make basic patterns
    - Rise and fall
    - Pulse

- Kinect dectect gesture
  
- Arduino/Kinect Connection 
    - On left wave do a pulse
    - Follow hand up and down


### Turn the Magnet On and Off

The first task is to turn the magnet on and off. Although it may sound simple, this is a integration test. This confirms that the Arduino is functioning and can connect to the computer, that there are no faulty components in the wiring, and that the wiring has been connected correctly.


```cpp
/*
  https://docs.arduino.cc/built-in-examples/basics/Blink/
*/

int outPin = 9; 
void setup() {
  // initialize digital pin LED_BUILTIN as an output.
  pinMode(outPin, OUTPUT);
}

// the loop function runs over and over again forever
void loop() {
  digitalWrite(outPin, 255);  
  delay(1000);                      // wait for a second
  digitalWrite(outPin, 0); 
  delay(1000);                      // wait for a second
}

```


### Vary The Strength of the Magnet

``` cpp
/*
  Fade

  This example shows how to fade an LED on pin 9 using the analogWrite()
  function.

  The analogWrite() function uses PWM, so if you want to change the pin you're
  using, be sure to use another PWM capable pin. On most Arduino, the PWM pins
  are identified with a "~" sign, like ~3, ~5, ~6, ~9, ~10 and ~11.

  This example code is in the public domain.

  https://docs.arduino.cc/built-in-examples/basics/Fade/
*/

int led = 9;         // the PWM pin the LED is attached to
int brightness = 0;  // how bright the LED is
int fadeAmount = 5;  // how many points to fade the LED by

// the setup routine runs once when you press reset:
void setup() {
  // declare pin 9 to be an output:
  pinMode(led, OUTPUT);
}

// the loop routine runs over and over again forever:
void loop() {
  // set the brightness of pin 9:
  analogWrite(led, brightness);

  // change the brightness for next time through the loop:
  brightness = brightness + fadeAmount;

  // reverse the direction of the fading at the ends of the fade:
  if (brightness <= 0 || brightness >= 255) {
    fadeAmount = -fadeAmount;
  }
  // wait for 30 milliseconds to see the dimming effect
  delay(30);
}

```
Notice that the ferrofliud rises and drops smoothly, meaning the magnet is "varying in power".

### Basic Patterns 

80% of the way there ( Don't think too much about the next 20%). I decided to create some basic patterns that I thought were cool. 

```cpp
int outPin = 9;
int i = 0;


void rise_max() {
  analogWrite(outPin, 255);
  delay(5000);

}

void waterfall_fall(){
  i = 80;
  analogWrite(outPin, i);
  delay(5000);

  while (i > 0){
    analogWrite(outPin, i);
    delay(200);
    i = i - 5;
  }
}


void waterfall_rise() {
  i = 0;
  analogWrite(outPin, i);
  delay(3000);
 
  while (i < 60){
    analogWrite(outPin, i);
    delay(300);
    i = i + 2;
  }
  delay(3000);
}


void pulse() {
  i = 0;

  while(i < 50){
    analogWrite(outPin, 140);
    delay(300);
    analogWrite(outPin, 100);
    delay(300);
    i = i + i;
  }

  i = 0;
  analogWrite(outPin, 0);
  delay(2000);

}
void setup() {
  pinMode(outPin, OUTPUT);
}

void loop() {
  //rise_max();
  //pulse();
  //waterfall_rise();
  //waterfall_fall();
}
//to all those obsessed with pure functions, sorry not sorry.
```



### Kinect Code 

With the arduino setup complete the next half of the equation (remeber I said 80%) is to use the Kinect.

[Kinect Setup]({% post_url 2026-10-01-ferrofluid-display-getting-started %}) 
If you are having issues power cycling check the link above. 

```cpp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Microsoft.Kinect;
using LightBuzz.Vitruvius;


namespace ConsoleApp1
{
    class Program
    {
        private static KinectSensor sensor
        private static BodyFrameReader bodyReader;
        private static Body[] bodies;

        // Gesture detector
        private static GestureController gestureController = new GestureController();
       
        static void Main(string[] args)
        {
            sensor = KinectSensor.GetDefault();
           
            if (sensor == null)
            {
                Console.WriteLine("No Kinect detected.");
                return;
            }

            sensor.Open();

            bodyReader = sensor.BodyFrameSource.OpenReader();

            bodyReader.FrameArrived += BodyReader_FrameArrived;

            gestureController.GestureRecognized += GestureController_GestureRecognized;

            Console.WriteLine("Waiting for wave...");
            Console.ReadLine();


            bodyReader.Dispose();
            sensor.Close();

        }


        private static void BodyReader_FrameArrived(object sender, BodyFrameArrivedEventArgs e)
        {
            using (BodyFrame frame = e.FrameReference.AcquireFrame())
            {
                if (frame == null)
                    return;


                if (bodies == null)
                    bodies = new Body[frame.BodyCount];


                frame.GetAndRefreshBodyData(bodies);


                foreach (Body body in bodies)
                {
                    if (body != null && body.IsTracked)
                    {
                        // Feed the tracked body into Vitruvius
                        gestureController.Update(body);

                    }
                }
            }
        }


        private static void GestureController_GestureRecognized(object sender, GestureEventArgs e)
        {
           switch (e.GestureType)
            {
                case GestureType.WaveLeft:
                    Console.WriteLine("Wave Left");
                    break;

                case GestureType.WaveRight:
                    Console.WriteLine("Wave Right");
                    break;
               
                default:
                    Console.WriteLine($"Unknown gesture: {e.GestureType}");
                    break;
            }
        }
    }
}
```

That's a lot! I get it, you don't care how it works. Just copy and paste. But if you are interested in a deeper dive in how this code actually works you can check [here]({% post_url 2026-10-01-ferrofluid-display-full-code %}).


### Rise ferrofluid on Wave Left

The next step is to have the Kinect send commands to the arduino. When the Kinect detects a wave left, it'll send it to the arduino. 

Windows only allows one program to use the COM port at a time. 


**Note: Upload the code to the arduino first and then run the C# code. Uploading the Arduino code while the C# code is running will cause an error because the COM port is already in use.**

```cpp

const int outPin = 9;

//BASIC FUNCTIONS FOR PATTERNS OMITTED HERE

void setup() {
  pinMode(outPin, OUTPUT);
  //test for full connection
  Serial.begin(9600);
  while (!Serial) {
    ;
  }
}

void rise_max() {
  analogWrite(outPin, 255);
  delay(5000);
  analogWrite(outPin, 0); 
  delay(5000);
}

//test full connection
void loop() {
  if (Serial.available() > 0) {

    String command = Serial.readStringUntil('\n');
    command.trim();

    if (command.length() > 0) {

      if (command == "WL") {
        rise_max(); 
        //Don't do this -> Serial.println("yoo"); Earth will explode
      }
      else if (command == "WR") {
	    rise_max();
      }
    }
  }
}


```

#### C# Code

```cs
...

//connecting with arduino
using System.IO.Ports; //added


namespace ConsoleApp1
{
    class Program
    {
        ...
        //arduino
        static SerialPort arduino = new SerialPort("COM3", 9600);
        //check on Device manager for actual COM


        static void Main(string[] args)
        {
            sensor = KinectSensor.GetDefault();
            arduino.Open(); //OPEN UP
            
            ...

            arduino.Close(); //CLOSE MEEEEEE

        }


        private static void BodyReader_FrameArrived(object sender, BodyFrameArrivedEventArgs e)
        {
                    ...
        }

        private static void GestureController_GestureRecognized(object sender, GestureEventArgs e)
        {
           switch (e.GestureType)
            {
                case GestureType.WaveLeft:
                    Console.WriteLine("Wave Left");
                    arduino.WriteLine("WL"); //send to Arduino
                    break;


                case GestureType.WaveRight:
                    Console.WriteLine("Wave Right");
			        arduino.WriteLine("WR"); //send to Arduino
                    break;
               
                default:
                    Console.WriteLine($"Unknown gesture: {e.GestureType}");
                    break;
            }
        }
    }
}



```



### Expanding Gestures
For now the code just detects left wave and right wave but the Lightbuzz Vitruvius SDK offers a few more gestures(and features) that we can take advantage of. 

```cs
 private static void GestureController_GestureRecognized(object sender, GestureEventArgs e)
        {
            switch (e.GestureType)
            {
                case GestureType.WaveLeft:
                    Console.WriteLine("Wave Left");
                    break;

                case GestureType.WaveRight:
                    Console.WriteLine("Wave Right");
                    break;

                case GestureType.SwipeLeft:
                    Console.WriteLine("Swipe Left");
	      arduino.WriteLine("SL");
                    break;

                case GestureType.SwipeRight:
                    Console.WriteLine("Swipe Right");
                    break;

                case GestureType.SwipeUp:
                    Console.WriteLine("Swipe Up");
                    break;

                case GestureType.SwipeDown:
                    Console.WriteLine("Swipe Down");
                    break;

                case GestureType.ZoomIn:
                    Console.WriteLine("Zoom In");
                    break;

                case GestureType.ZoomOut:
                    Console.WriteLine("Zoom Out");
                    break;

                case GestureType.JoinedHands:
                    Console.WriteLine("Hands Joined");
                    break;

                case GestureType.Menu:
                    Console.WriteLine("Menu");
                    break;

                default:
                    Console.WriteLine($"Unknown gesture: {e.GestureType}");
                    break;
            }
        }


```



### Tracking the Hand

Another cool thing to do is to move the ferrofluid with the hand. The Kinect can track body features in space and give x/y/z coordinates. First step is to be able to print the hand coordinates. 



``` cs 
...

namespace ConsoleApp1
{
    class Program
    {
        ...

        static void Main(string[] args)
        {
           ...
        }

        private static void TrackRightHand(Body body)
        {
            Joint rightHand = body.Joints[JointType.HandRight];

            float x = rightHand.Position.X;
            float y = rightHand.Position.Y;
            float z = rightHand.Position.Z;
            
            Console.WriteLine(
               $"Right hand: X={x:F2}, Y={y:F2}, Z={z:F2}"
            );

            // Console.WriteLine($"{y:F2}");
            // arduino.WriteLine($"{y:F2}");
        }


        private static void BodyReader_FrameArrived(object sender, BodyFrameArrivedEventArgs e)
        {
            using (BodyFrame frame = e.FrameReference.AcquireFrame())
            {
                if (frame == null)
                    return;

                if (bodies == null)
                    bodies = new Body[frame.BodyCount];

                frame.GetAndRefreshBodyData(bodies);

                foreach (Body body in bodies)
                {
                    if (body != null && body.IsTracked)
                    {
                        // Feed the tracked body into Vitruvius
                        gestureController.Update(body);


                        // =========================
                        // FEATURE: Right hand tracking
                        // =========================
                        TrackRightHand(body);

                        // =========================
                        // OTHER FEATURES
                        // =========================

                        // TrackWave(body);
                        // TrackSomethingElse(body);


                    }
                }
            }
        }

        private static void GestureController_GestureRecognized(object sender, GestureEventArgs e)
        {
            switch (e.GestureType)
            {
                ...
            }
        }

    }
}
```


Once the code prints the right hand coordinates positions correctly, we can change the code in TrackingRightHand function to only send Y (up down) coordinate to the arduino as such. 

``` cs 
 private static void TrackRightHand(Body body)
        {
            Joint rightHand = body.Joints[JointType.HandRight];

            float x = rightHand.Position.X;
            float y = rightHand.Position.Y;
            float z = rightHand.Position.Z;
            
            Console.WriteLine(
               $"Right hand: X={x:F2}, Y={y:F2}, Z={z:F2}"
            );

            // Console.WriteLine($"{y:F2}");
            arduino.WriteLine($"{y:F2}");
        }
```


And change the Arduino loop code to read data.

``` cpp
void loop() {
  if (Serial.available() > 0) {

    String command;

    // Read all complete commands currently in the buffer
    while (Serial.available() > 0) {
      command = Serial.readStringUntil('\n');
    }

    command.trim();

    if (command.length() > 0) {
      float value = command.toFloat();
      value = constrain(value, 0.0, 1.0);

      int pwm = (int)(value * 255.0);
      analogWrite(outPin, pwm);
    }
  }
}

```

