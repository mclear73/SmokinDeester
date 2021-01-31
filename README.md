## SmokinDeester  ##


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

### Verify I2C and SPI are enabled ###
1. Log back into your pi once it has rebooted and type the following into terminal:  

		ls /dev/i2c* /dev/spi*
2. You should get the following response: `/dev/i2c-1 /dev/spidev0.0 /dev/spidev0.1`

### Blinka Test ###
1. Create a new directory in your home directory titled "SmokinDeester":

		cd ~
		mkdir SmokinDeester
2. Navigate into that directory:

		cd SmokinDeester
3. create a new file called "BlinkaTest.py" we will use nano to do this:

		nano BlinkaTest.py
4. This will open a blank text document. Copy and paste the following code into the file:

		import board
		import digitalio
		import busio
		 
		print("Hello blinka!")
		 
		# Try to great a Digital input
		pin = digitalio.DigitalInOut(board.D4)
		print("Digital IO ok!")
		 
		# Try to create an I2C device
		i2c = busio.I2C(board.SCL, board.SDA)
		print("I2C ok!")
		 
		# Try to create an SPI device
		spi = busio.SPI(board.SCLK, board.MOSI, board.MISO)
		print("SPI ok!")
		 
		print("done!")
5. Close the file by pressing **Ctrl + x**
6. Press **Y** when asked "Save modified Buffer?"
7. Press **enter**
8. This will save the file. 
9. Open the file again to check the contents:

		nano BlinkaTest.py
10. If the file looks correct, press **Ctrl + x** again to close.
	- Note: if you did not modify the file, you will not be asked additional questions. 
11. Run the code to test your installation:

		python3 BlinkaTest.py

12. You should see the following:

		Hello blinka!
		Digital IO ok!
		I2C ok!
		SPI ok!
		done!
13. Install the max31865 library for python:

		sudo pip3 install adafruit-circuitpython-max31865
14. Create a new test file to test the max31865 installation:

		nano ~/SmokinDeester/MAXtest.py
15. Once again, you will get an empty text file. Copy and paste the following code into the file:

		# SPDX-FileCopyrightText: 2021 ladyada for Adafruit Industries
		# SPDX-License-Identifier: MIT
		
		# Simple demo of the MAX31865 thermocouple amplifier.
		# Will print the temperature every second.
		import time
		
		import board
		import busio
		import digitalio
		
		import adafruit_max31865
		
		
		# Initialize SPI bus and sensor.
		spi = busio.SPI(board.SCK, MOSI=board.MOSI, MISO=board.MISO)
		cs = digitalio.DigitalInOut(board.D5)  # Chip select of the MAX31865 board.
		sensor = adafruit_max31865.MAX31865(spi, cs, wires=3, rtd_nominal=100.0, ref_resistor=430.0)
		# Note you can optionally provide the thermocouple RTD nominal, the reference
		# resistance, and the number of wires for the sensor (2 the default, 3, or 4)
		# with keyword args:
		# sensor = adafruit_max31865.MAX31865(spi, cs, rtd_nominal=100, ref_resistor=430.0, wires=2)
		
		# Main loop to print the temperature every second.
		while True:
		    # Read temperature.
		    temp = sensor.temperature
		    # Print the value.
		    print("Temperature: {0:0.3f}C".format(temp))
		    # Delay for a second.
		    time.sleep(1.0)
16. Close the file by pressing **Ctrl + x**
17. Press **Y**
18. Press **Enter**
19. Run the test file:

		python3 ~/SmokinDeester/MAXtest.py
20. You should see the following continuously printed on your screen:

