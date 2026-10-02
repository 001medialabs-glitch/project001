---
layout: post
title: Ferrofluid Display - Full Code
date: 2026-10-01 05:43 -0400
---



# C# Kinect Code

```cpp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;


using Microsoft.Kinect;
using LightBuzz.Vitruvius;


//connecting with arduino
using System.IO.Ports;


namespace ConsoleApp1
{
    class Program
    {
        private static KinectSensor sensor;
        private static BodyFrameReader bodyReader;
        private static Body[] bodies;

        // Gesture detector
        private static GestureController gestureController = new GestureController();


        //arduino 
        static SerialPort arduino = new SerialPort("COM4", 9600);


        static void Main(string[] args)
        {
            sensor = KinectSensor.GetDefault();
            arduino.Open();

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
            arduino.Close();

        }

        private static void TrackRightHand(Body body)
        {
            Joint rightHand = body.Joints[JointType.HandRight];

            float x = rightHand.Position.X;
            float y = rightHand.Position.Y;
            float z = rightHand.Position.Z;
            
            //Console.WriteLine(
            //    $"Right hand: X={x:F2}, Y={y:F2}, Z={z:F2}"
            //);
            Console.WriteLine($"{y:F2}");
            arduino.WriteLine($"{y:F2}");
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

        //private static void GestureController_GestureRecognized(object sender, GestureEventArgs e)
        //{
        //    if (e.GestureType == GestureType.WaveRight ||
        //        e.GestureType == GestureType.WaveLeft)
        //    {
        //        Console.WriteLine("Hello World!");
        //    }
        //}


        //not very accurate
        private static void GestureController_GestureRecognized(object sender, GestureEventArgs e)
        {
            switch (e.GestureType)
            {
                case GestureType.WaveLeft:
                    Console.WriteLine("Wave Left");
                   // arduino.WriteLine("WL");
                    break;

                case GestureType.WaveRight:
                    Console.WriteLine("Wave Right");
                    break;

                case GestureType.SwipeLeft:
                    Console.WriteLine("Swipe Left");
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

    }
}

```


# Arduino 

```cpp

const int outPin = 9;


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

void rise_max() {
  analogWrite(outPin, 255);
  delay(5000);
  analogWrite(outPin, 0); 
  delay(5000);
}


void setup() {
  pinMode(outPin, OUTPUT);
  //test for full connection
  Serial.begin(9600);
  while (!Serial) {
    ;
  }
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

//read from Kinect
// void loop() {
//   if (Serial.available() > 0) {

//     String command;

//     // Read all complete commands currently in the buffer
//     while (Serial.available() > 0) {
//       command = Serial.readStringUntil('\n');
//     }

//     command.trim();

//     if (command.length() > 0) {
//       float value = command.toFloat();
//       value = constrain(value, 0.0, 1.0);

//       int pwm = (int)(value * 255.0);
//       analogWrite(outPin, pwm);
//     }
//   }
// }



```


## Kinect Code

There are three functions. In the main function we open the Kinect, accept the new incoming frames(BodyReader_FrameArrived), and register event handlers that trigger when an event occurs (GestureController_GestureRecognized()).

## Arduino Code

A comment on this code. You might have noticed that the Kinect prints out a lot of data very quickly. It's difficult for the Arduino/Ferrofluid to keep up with all this data.