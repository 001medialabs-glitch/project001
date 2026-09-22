### OS/Hardware Configuration

USB 3.0 (Kinect requires USB 3.X)
Windows 10/11

### Kinect 2.0 SDK

#### Install KINECT 2.0 SDK from Microsoft
https://www.microsoft.com/en-us/download/details.aspx?id=44561

Make sure that it is version 2

### Visual Studio


Set up C# project in visual studio

Select Console App (.NET Framework)

![alt text](<Screenshot 2026-09-20 144217.png>)


#### Install Lightbuzz kinnect sdk to the project
In visual studio, go to: 
Tools -> nuget packet manager -> packet manager console
https://github.com/LightBuzz/Vitruvius

Type in the package manager console (noted my “PM>” )
Install-Package lightbuzz-vitruvius


#### Add kinect extensions
On the right hand side of solution explorer 
Right Click on references -> add references -> extensions -> select all of the kinect related extensions

![alt text](<Screenshot 2026-09-20 144719.png>)

#### Select platform target
Any CPU drop down -> left click the drop down under active solution platform -> < new> -> select x64(x64 64 bit / x86 32 bit) or ARM depending on your laptop. 

![alt text](<Screenshot 2026-09-20 152332.png>)

Ready to code
