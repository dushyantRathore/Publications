---
layout:     post
title:      Getting started with Python programming on Raspberry Pi
date:       2017-09-27 12:32:18
summary:    Basic steps to get started with python programming on Raspberry Pi
categories: 
---

## What is a Raspberry Pi ?
The Raspberry Pi is a low cost, credit-card sized computer that plugs into a computer monitor or TV, and uses a standard keyboard and mouse. It is a capable little device that enables people of all ages to explore computing, and to learn how to program in languages like Scratch and Python.

## Setting up the Raspberry Pi

1. Get your Raspberry Pi 2 or 3, an SD memory card with 8Gb or more memory (class 10 or more), a micro USB cable (for power), an ethernet cable and the Wifi USB dongle.
2. Download Raspbian from [here](https://www.raspberrypi.org/downloads/raspbian/). Raspbian is the Raspberry Pi foundation’s official supported operating system. It comes pre-installed with plenty of software for education, programming and general use. It has Python, Scratch, Sonic Pi, Java, Mathematica and more.
3. After downloading the image, you have to flash the image on the SD card. There is and app called [Etcher.io](https://etcher.io/), that make the process incredibly simple, fast and safe. Download Etcher app (for all platform), then select the Raspbian image, the SD card (which you have to insert in your computer) and flash it. Few minutes and it will be done and verified.
4. Put the SD card into the RPi, you’re ready to rock!

## Connecting to the Raspberry Pi using SSH

Before connecting to your Raspberry Pi using SSH, you will need to know the IP address of the Pi. Some of the ways to get the IP address are as follows : 

* If you are running the Pi with a screen, type ```hostname -I``` from your Raspberry Pi terminal.
* If you are running the Pi without a screen (headless), you can also look at the device list on your router or use a tool like [nmap](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

Once we get the IP address of the Pi, we can connect to it from another computer by using the following command :

```python
ssh pi@<IP>
``` 

Here `<IP>` is the IP adress of the Pi. 

If you receive a connection timed out error it is likely that you have entered the wrong IP address for the Raspberry Pi.

When the connection works you will see a security/authenticity warning. Type  `yes` to continue. You will only see this warning the first time you connect.

Next you will be prompted for the password for the `pi login:` on Raspbian. The default password is `raspberry`. You should now be able to see the Raspberry Pi prompt, which will be identical to the one found on the Raspberry Pi itself.

![SSH-with-X11-Forwarding-Linux.png](https://cdn.filestackcontent.com/6Ur66kW4TNmzTKp2cnHA)

## Remote access to the Raspberry Pi graphic desktop environment using VNC

VNC is a desktop sharing system that allows remote access to the graphical interface of one computer (in our case the Raspberry Pi) from another (your desktop / laptop). It will transmit keyboard and mouse events from the laptop / desktop to the Raspberry Pi and will provide a view of the Raspberry Pi graphical desktop on your laptop / desktop. For this to work you need a VNC server set-up and running on the Raspberry Pi and a VNC client on your desktop / laptop.

To install tightvncserver, open the Raspberry Pi console (either directly on the Pi, or remotely, using SSH) and type:

```python
sudo apt-get install tightvncserver
```

Once VNC server is installed, simply start the server on the Raspberry Pi using the following command : 

```python
vncserver :1
```

Now to access the Raspberry Pi GUI from your linux machine, open the Remote Desktop Viewer and enter the IP address along with the screen number assigned by the current session of the tightvncserver in the form `<IP>:<Screen Number>` in the `Host:` line and click the `Connect` button in the lower right corner.

You should be prompted for a password. Enter the default Raspberry Pi password here to complete the process. After the entire process is complete, you should be able to see the following GUI on your laptop/desktop :

![tightvnc-raspberrypi2.png](https://cdn.filestackcontent.com/RW0KwRyaQfS4Y1xhMIuj)

## Programming on the Raspberry Pi

Here we will take the basic example of printing "Hello World" using python on the Raspberry Pi. Now, create a file called `Helloworld.py` with the following code and save it on the desktop.

```python
print "Hello world!"
```

To run the file, `cd` into `Desktop` using the terminal and finally enter the following command to execute the python script :

```python
python Helloworld.py
```

#### You should be able to see the following output in the terminal : 

![FQ7LMMHIC42DNG4.jpg](https://cdn.filestackcontent.com/jmuhojlfTpygEwUwLPUb)


For getting started with the GPIO setup and usage along with hardware programming on Raspberry Pi, kindly refer to this [link](https://pythonprogramming.net/introduction-raspberry-pi-tutorials/).

Hope you found this helpful 😄