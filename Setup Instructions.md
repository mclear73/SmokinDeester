## Setup Instructions ##

### Getting started with the raspberrypi ###
1. Get the pi on to the wifi
2. Open up your terminal and type the following:  
 `ifconfig`
3. Write down the wlan ip address: it should be something that starts with 192.168.*.***
4. We also need to enable SSH on your raspberrypi so that you can remotely access your pi over the wifi network
5. To do so, type the following into your terminal window:  
	`sudo raspi-config`
6. A configuration scree should then pop-up. 
7. Use your arrow keys to go down to "**Interfacing Options**" and press enter
8. Select and enable "**SSH**"
9. Select and enable "**SPI**"
10. Select and enable "**I2C**"
11. Navigate down to "**Finish**" and press enter
12. This should reboot your raspberry pi
13. Note: you may get a warning when you log back in that you have enabled SSH, but you are still using the default pi password. 
4. Log onto your router admin page through your preferred browser (ex: chrome). Refer to the manufacturer of your router to determine how to do this
5. You should be able to see all of the connected devices on the wifi network, their device name, ip address, and MAC address
5. Locate the raspberry pi either through the device name or the ip adress you wrote down previously
6. Using your router's menu, assign a static ip address to the raspberry pi. It is best just to use the same ip address that it already has (aka. the one that was assigned to it manually)
7. You should now save the ip address that you assigned to your raspberry pi. The easiest way to do this is to save the information into an SSH client like [MobaXterm](https://mobaxterm.mobatek.net/)

### Remote login to raspberrypi (SSH) ###
1. Once you have downloaded your favorite SSH client such as[ PuTTY](https://www.putty.org/) or [MobaXterm](https://mobaxterm.mobatek.net/) (recommended), you will need to start an SSH client. The following instructions are for how to do this on MobaXterm
2. In the top left corner, press the button labeled "**Session**"
3. A window will pop-up asking you to choose session type. 
4. Press "**SSH**" in the top-left corner
5. In the box labeled "**Remote host**" enter in the static ip address that you assigned previously (ex: 192.168.1.14)
6. Make sure that the **port** is set to "**22**". It should already be set to 22 by default
7. Press ok in the bottom of the screen
8. If a connection is made, you should be prompted to enter in a username. The defualt username for all raspberrypis is:   
	`pi`
9. You will then be prompted for a password. The default password to all raspberrypis is:  
	`raspberry`
10. When you enter the password into the terminal, you will not see any characters appear. This is a security feature of all linux systems. Once you have entered the password press enter
11. You should now be logged into the terminal of your raspberrypi. This is where we will do all of the configuring for the application
12. If this process did not work, here are some possible causes and solutions:  
	13. If you were not prompted for a password:  
		- Your pi is not on the internet. Check by connecting a screen directly into your pi to see if you are connected to wifi
		- You were unsuccessful in setting your static ip address and your pi's ip address has changed. Check your router's connected devices and check your raspberry pi's ip address
		- You did not enable "SSH" make sure that you followed the steps for enabeling SSH above
	14. If you were prompted for a username and password, but it did not work:
		- You may have changed your username and password. Connect a screen back into your pi and using the GUI to change your password

### Update your pi ###
1. Log into your raspberry pi through MobaXterm. See steps above for instructions. 
2. **To update your pi**: type in the following:  
	`sudo apt update`  
	`sudo apt full-upgrade`  
	`sudo pip3 install --upgrade setuptools`
3. To install the [adafruit circuit pi packages](https://learn.adafruit.com/circuitpython-on-raspberrypi-linux/installing-circuitpython-on-raspberry-pi): type the following into your terminal  

		cd ~
		sudo pip3 install --upgrade adafruit-python-shell
		wget https://raw.githubusercontent.com/adafruit/Raspberry-Pi-Installer-Scripts/master/raspi-blinka.py
		sudo python3 raspi-blinka.py  
4. If your default Python package is Python2, you will be asked if you want to change to Python3. You want to indicate yes and follow the prompts to update and restart your pi

### Install VirtualHub for Yocto-PT100 and Test Connectivity ###
1. Navigate to the yoctopuce page for the [Yocto-PT100](https://www.yoctopuce.com/EN/products/yocto-pt100). (Link current as of February, 15 2021)
2. Right-click to the copy the URL from the "Virtual Hub for Linux, Intel + ARM" and run the `wget` command to download the Virtual Hub tool for all Yocto products
3. You can run the following code below IF THE LINK hasn't changed. Change the url if not working:

		cd ~
		wget https://www.yoctopuce.com/FR/downloads/VirtualHub.linux.42982.zip
4. Unzip the file by running (note: filename will differ if the version has changed):

		unzip VirtualHub.linux.42982.zip
5. Run the following to start the RCP-bind:

		/etc/init.d/rpcbind start
		/etc/init.d/rpcbind enable
6. Plugin the Yocto-PT100 into the Raspberry pi using a micro-usb, make sure the Yocto-PT100 is lighting up
7. Navigate to the armhf directory by: `cd ~/armhf`
8. Run the Virtual Hub with admin privelages by running the following:

		sudo ./VirtualHub
9. You should see the server start. To view the configuration webpage created by Virtual Hub. Open up a browser on your computer and type the IP address of your raspberry pi into your url followed by `:4444`
	- This will make it so that you are locally looking at port 4444 of your raspberry pi. If this port doesn't work for some reason, you can also try port 4445
10. You should be able to see your Virtual Hub and your Yocto-Pt100. If you cannot see your Yocto-Pt100 connected, try unplugging and plugging it back in. If that does not work, try a different USB cable. 
11. Follow the instructions on the Yocto-PT100 Webpage to configure your PT-100 for your particular PT-100 probe
12. Press Ctrl + c to kill the Yocto server and continue installs

### Install Yocto-PT100 Python Library ###
1. From the same [Yocto-PT100 page](https://www.yoctopuce.com/EN/products/yocto-pt100) as above: (Link current as of February, 15 2021)
2. Right-click to copy the URL from the "Library for Python" file.
3. You can run the following code below IF THE LINK hasn't changed. Change the url if not working:

		cd ~/SmokinDeester
		wget https://www.yoctopuce.com/FR/downloads/YoctoLib.python.43781.zip
4. Unzip the file by running (note: filename will differ if the version has changed):

		unzip YoctoLib.python.43781.zip

### Install MongoDB ###
1. Type in the following into terminal:

		sudo apt install mongodb
		sudo apt-get install mongodb-server
		sudo systemctl enable mongodb
		sudo systemctl start mongodb
2. Check to see if MongoDB installed correctly. Type the following into terminal:

		mongo
3. You should see mongo startup and may have a warning saying that a 32 bit installation is limited to 2GB of data. This is not a problem. 
4. To exit the application type:

		quit()
### Install PyMongo for interacting with MongoDB with python ###
1. Type the following into terminal:

		sudo pip3 install pymongo

### Yocto PT100 Test Script ###
1. Create a new python file. I will call mine "yoctoPT100.py". To name yours the same thing. Type the following into the terminal window:

		cd ~/SmokinDeester
		nano yoctoPT100.py
2. This will open a blank document. Copy and and paste the following code into the document:

		#!/usr/bin/python
		# -*- coding: utf-8 -*-
		import os, sys
		# add ../../Sources to the PYTHONPATH
		#CHANGE ME BELOW!!!!!!
		sys.path.append(os.path.join("/home/pi/SmokinDeester/Sources"))
		
		from yocto_api import *
		from yocto_temperature import *
		
		
		def usage():
		    scriptname = os.path.basename(sys.argv[0])
		    print("Usage:")
			#CHANGE ME BELOW!!!!!!
		    print(scriptname + ' PT100MK1-10E420')
			#CHANGE ME BELOW!!!!!!
		    print(scriptname + ' temp_sensor')
		    print(scriptname + ' any  ')
		    sys.exit()
		
		
		def die(msg):
		    sys.exit(msg + ' (check USB cable)')
		
		
		errmsg = YRefParam()
		
		if len(sys.argv) < 2:
		    usage()
		
		target = sys.argv[1]
		
		# Setup the API to use local USB devices
		if YAPI.RegisterHub("usb", errmsg) != YAPI.SUCCESS:
		    sys.exit("init error" + errmsg.value)
		
		if target == 'any':
		    # retrieve any temperature sensor
		    sensor = YTemperature.FirstTemperature()
		    if sensor is None:
		        die('No module connected')
		else:
		    sensor = YTemperature.FindTemperature(target + '.temperature')
		
		if not (sensor.isOnline()):
		    die('device not connected')
		
		while sensor.isOnline():
		    print("Temp :  " + "%2.1f" % sensor.get_currentValue() + "°F (Ctrl-C to stop)")
		    YAPI.Sleep(1000)
		YAPI.FreeAPI()

3. Note: You will need to change the lines underneath the comments labeled "CHANGE ME BELOW!!"

- Change this so that the path to the "Source" directory downloaded from the Yocto webpage is in the apostrophes. Your path may be the same if you followed the instructions above. 

		sys.path.append(os.path.join("</home/pi/SmokinDeester/Sources>"))
- Change this so that the serial number of your specific PT-100 sensor is displayed. To view the serial number, follow the steps listed under the  "Install Yocto-PT100 Python Library" section starting with Step 7. This will run the Yocto server and allow you to configure your Yocto-PT100 sensor, record the serial number, and give the sensor a nickname.
	- Please Note: you need to specify the number of wires used for your PT100 probe. 
	- I nicknamed the sensor: `temp_sensor`

			print(scriptname + ' <PT100MK1-10E420>')
- Change this line so that the nickname you specified is in the apostrophes 

		print(scriptname + ' <temp_sensor>')
4. Press CTRL + x to close the file
5. Press y
6. Press Enter
7. Once your PT-100 is connected and you have modified the python script, test the script by running:

		sudo python yoctoPT100.py any
8. You should see the temperature printed on the screen. It will appear like the following: 
		1. Note: this script is set to report F. Make sure that you configure the sensor to report the correct output temperature by using the Yocto server as described in Step 5 of this section

		pi@raspberrypi:~/SmokinDeester $ sudo python yoctoPT100.py any
		Temp :  61.9°F (Ctrl-C to stop)
		Temp :  61.9°F (Ctrl-C to stop)
		Temp :  61.9°F (Ctrl-C to stop)
		Temp :  62.0°F (Ctrl-C to stop)
		Temp :  61.9°F (Ctrl-C to stop)
		Temp :  61.9°F (Ctrl-C to stop)
		Temp :  62.0°F (Ctrl-C to stop)
		Temp :  61.9°F (Ctrl-C to stop)

### Setup Flask Server ###
1. Install Flask

		pip3 install flask
2. Install nginx

		sudo apt-get install nginx
3. Start nginx service

		sudo /etc/init.d/nginx start
4. Initialize mongodb

		service mongodb start
5. Follow any steps to initialize
6. Modify the MongoDB config file:

		sudo nano /etc/mongodb.conf
7. Comment out the line that says: `bind_ip = 127.0.0.1` and add a line that says `bind_ip = 0.0.0.0`. So it will appear as follows:

		#bind_ip = 127.0.0.1
		bind_ip = 0.0.0.0
8. Do not change any other part of the config file 

### Install a few more dependencies ###
1. We may not use this but it is nice to have pandas installed

		pip3 install pandas
2. angular-charts.js and Chart.js will be used to display the plots. npm will be used to install these libraries:

		sudo apt-get install npm
3. Within SmokinDeester, create a directory called "static"

		mkdir static
4. move to the folder and install the following

		cd ~/SmokinDeester/static
		npm install angular-chart.js --save
		npm install angular --save
		npm install chart.js --save

