

### Introduction


Full code at the bottom. 

Let's start with the main function. 


```cpp
        ...
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
        ...
```







### Full Code


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