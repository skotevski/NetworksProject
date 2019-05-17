# Sauron
Sauron is a 2-part program, and is essentially a monitoring tool, whose main purpose is to track and log the Internet / MLB Network connectivity of various AWS Elemental Live Encoders.

Without going into too much detail, these Encoders are set up across all of the Major League and Minor League ballparks, and they are always 'encoding' video files, and moving them to AWS cloud servers which are handled by other other programs before being distributed to channels showing the actual live games. These encoders can sometimes lose their network connections, and require an engineer to log into it, to try and fix the issue.
With Sauron, the engineer would be able to know if the encoder is only having trouble getting through the VPN on the MLB Network, or if it can't even connect to the internet at all. This would allow the engineer to know how to begin resolving the issue and who to contact before even logging onto the device. Also, as Sauron is running, it is constantly logging the status of the connections, with timestamps. This will make it easier to determine a cause of the loss of connection, and track the problem.

The other main part of Sauron is the 'director' aspect, which essentially is a web server built on the CherryPy framework, that talks to all of the local servers of the encoders, and displays them in a nice graphical table. The table will have all of the information that is on the local web servers of the encoders (HostName, IP Address, Internet Status, etc.) The director is meant to be a central hub for anyone to be able to just quickly log onto it, and check on a certain encoder by looking up its IP or HostName, and then see all of the relevant information about it.

### Sauron:

![Image of Sauron](https://i.gyazo.com/46597d82217d9dff1962d2e401d04cd8.png)

### Director:

![Image of SauronDirector](https://i.gyazo.com/18a6deedbe1d86153fb4d7f5c6d15078.png)

## Requirements
In order for Sauron to work properly, you will need to run this on a unix-based machine (Mac/Linux) due to how the ping-script works. The commands that java is executing are in a unix syntax, and this is because this program was designed to work on unix based machines running CentOS.

Sauron also uses Google's Gson library

**All requrired files will be bundled with the jar of this project (Sauron.jar), so simply downloading and running the jar file (Sauron.jar) should be enough to have Sauron running on your machine, assuming you have the Java SDK installed**



## Files / Folders Explained
Since Sauron has a lot of different parts to it, I tried to separate the Director from the script, and all the work I did **for this project specifically** is going to be under 'Networks_Project'. Everything else would be related work to the overall project of Sauron, but not to my Networks Project.

- Everything in the 'Networks_Project' folder is a part of my actual final project for the class. (Essentially all of the java files)
- Everything in the 'Production' folder is the version of Sauron that's actually installed on the encoders in case if you were curious how I implemented that
- Everything in the 'Director' folder is the code that me and my mentor wrote in order to create the web-GUI, displaying info about all the encoders we installed Sauron onto.



## Networks Project
My original goal for the Networks Project was to create a Multi-Threaded Rock-Paper-Scissors tournament application. I was able to implement the rapid prototype of that application (A simple 2-player RPS game played through a server and client) without too much difficulty. However, when trying to allow for multithreading, as well as having the bracket being created based on the clients that joined, I had run into many unforseen problems. It ended up becoming very frustrating trying to solve all of the issues and bugs that I was having, and I got overwhelmed at some point and thought of changing my project entirely.  

I have been working on Sauron for the past few months at my job, and its gotten to the point where it is ready for deployment, and the program has went out and been installed on about 80 different encoders or so. When I first met with my mentor and discussed the goals of this project, we both agreed that I would write the program in Java, mainly due to my comfort in the language. However, over the course of the project we realized that it would be much simpler to move everything over to **Python**, since things like **Json support is built into the language** (avoiding the need of extra dependencies), its overall **lighter and more portable** due to the fact that python is usually preinstalled on CentOS (the operating system of the encoders), and there are **many existing web frameworks** (like flask and cherrypy) that allow for hosting content in a simple and straightforward manner.

Looking back on this project, I decided to challenge myself and tried to see if I can complete the java implementation, and I wanted to try and incorporate some of the complex topics we discussed in this course, such as **multi-threading**, **synchronization**, and **hosting an HTTP Server**. In the production version of Sauron, the web server displaying the JSON of all the values is built using a python framework called Flask, so for this version I decided to try and implement it myself in my **Web Server** class.

**To summarize**, the overall goals of my network project were the following:
- Utilize Multi-Threading somehow
- Create a Synchronized object for classes to communicate with each other
- Create an HTTP Server housing the json (using TCP)



## STATUS
The java port of Sauron was a success overall. I was able to implement Multi-Threading and the HTTP Server, however I did **not** include Synchronization in the way that I originally intended to. I was having trouble getting the Synchronization aspect for my project to work, so I decided to do more research on the topic and found another type of Java class, called the *Singleton*, which exists in only one instance of a running java application. All of the various threads and classes in Sauron communicate with the Singleton which has all of the values that we care about (internet/mlbam connectivity status, ip, and hostname).

Something strange about my program that I noticed was that whenever the HTTP Server recieved a GET Request (whenever I tried to look at the JSON from my browser), a whole bunch of exceptions would show up in Sauron's output. Nothing would crash or anything, but I just thought it was strange, and it's something that I would look into in the future.

### Summary
- MultiThreading implmented with **EncoderThread** which spins up a new thread and runs the Ping-Script, and **JSON WebThread** which spins up a new thread that hosts the HTTP Server all simultaneously.
- No Synchronization, but shared object was achieved using Singleton class '*Status*'.

### Future Improvements
Aside from figuring out what's causing the exceptions being thrown whenever the HTTP Server receives a GET Request, future improvements to Sauron include:
- Attempt to add synchronization
- Cleaning up the CSS on Director to make Online/Offline & Encoding/Idle look nicer
- Fixing the modal on Director to accurately display more information about a selected encoder
- Adding more information to the data payload


## Output
#### Successful execution of Sauron:
<img src="https://i.gyazo.com/38ce6e90ed0ea3fcd41fcd5199272304.png" width="40%">

#### Weird exceptions after hitting localhost:8001/payload.json:
<img src="https://i.gyazo.com/d34562431451fb462510fc05680bb7c1.png" width="40%">

#### Sample Output of Director
<img src="https://i.gyazo.com/31b8cfdd4c705b4866e1f82086e20809.png" width="40%">

