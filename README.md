# sense_hat
Raspberry PI SENSE Hat


AstroPI aka Sense PI Hat is a device that contains a 8x8 matrix LED display with sensors that can monitor temp, pressure, etc 

#Download the python pi sense package.#
*I used version 2.2.0
** sense-hat-2.2.0.tar.gz <- Direct file download
* Else you can use this link (https://pypi.python.org/pypi/sense-hat#downloads)
* Extract and then Install the pi-sense library within python.
* sudo python2 setup.py install --root=$pkgdir/ --optimize=1
* Requires Distributions
* sudo /usr/bin/pip2.7  install -U numpy
** This installation takes a while. 10+ minutes
* sudo /usr/bin/pip2.7 install -U pillow
* Install Path library
** sudo /usr/bin/pip2.7 install -U pathlib

#Now we need a library to talk with the Sense Hat motion sensors#
**RTIMULib** 
* clone the repo https://github.com/RPi-Distro/RTIMULib
** git clone https://github.com/RPi-Distro/RTIMULib
* To build RTIMULib for python
* The python build script is located here “PATH_DIR/RTIMULib/Linux/python”
* sudo /usr/bin/python2.7 setup.py build
* sudo /usr/bin/python2.7 setup.py install

#More sensor libraries needed I2C#
* Install i2c-tools and lm_sensors packages.
** sudo pacman -S i2c-tools
** sudo pacman -S lm_sensors
* Configure the bootloader to enable the i2c hardware by appending /boot/config.txt:
** dtparam=i2c_arm=on
* Configure the i2c-dev and i2c-bcm2708 (if not blacklisted for the camera) modules to be loaded at boot:
* /etc/modules-load.d/raspberrypi.conf
** i2c-dev 
** i2c-bcm2708

#Test the sensors#
* sudo i2cdetect -y 1
 
#Device Permissions#
* Change the permissions of the /dev/fb0 and fb1 so that the user alarm can communicate with it
* IOError: [Errno 13] Permission denied: '/dev/fb1'
* Add alarm to the video group so that you have access to /dev/fb0 and /dev/fb1
* **sudo usermod -a -G video alarm**

#Service (Startup and Shutdown)#
* Create a file called smileled.service
* Place it under "/usr/lib/systemd/system/"

```
[Unit]
Description=LED display Green Smile
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/python2.7 /scripts/smile.py
ExecStop=/usr/bin/python2.7 /scripts/sad.py
RemainAfterExit=yes
Restart=always
RestartSec=30

[Install]
WantedBy=multi-user.target
```

#Place a these files under /scripts#
**sad.py**

```python
#!/usr/bin/python
exit()
```

**smile.py**

```python
#!/usr/bin/python
from sense_hat import SenseHat
from time import sleep
import signal
import sys

#Code to handle system shutdown.
#If shutdown is detected, kill the script

def sigterm_handler(_signo, _stack_frame):

sense.show_message("Shutdown", scroll_speed=(0.05), text_colour=(255, 0, 0), back_colour=(0, 0,
0))
sense.show_letter("3")
sleep(1)
sense.show_letter("2")
sleep(1)
sense.show_letter("1")
sleep(1)
sense.set_pixels(sad)
sys.exit(0)
signal.signal(signal.SIGTERM, sigterm_handler)
if __name__ == "__main__":

sense = SenseHat()

# set up the colours (white, green, red, empty)

w = [150, 150, 150]
g = [0, 255, 0]
r = [255, 0, 0]
e = [0, 0, 0]

smile = [
e,e,e,e,e,e,e,e,
e,g,g,e,e,g,g,e,
e,g,g,e,e,g,g,e,
e,e,e,e,e,e,e,e,
e,e,e,e,e,e,e,e,
g,e,e,e,e,e,e,g,
g,g,g,g,g,g,g,g,
e,e,e,e,e,e,e,e
]

sad = [
e,e,e,e,e,e,e,e,
e,r,r,e,e,r,r,e,
e,r,r,e,e,r,r,e,
e,e,e,e,e,e,e,e,
e,e,e,e,e,e,e,e,
e,e,r,r,r,r,e,e,
e,r,e,e,e,e,r,e,
r,e,e,e,e,e,e,r
]

face1 = [
e,e,e,e,e,e,e,e,
e,g,w,e,e,g,g,e,
e,g,g,e,e,g,w,e,
e,e,e,e,e,e,e,e,
e,e,g,g,g,e,e,e,
e,e,g,e,g,e,e,e,
e,e,g,g,g,e,e,e,
e,e,e,e,e,e,e,e
]

face2 = [
e,e,e,e,e,e,e,e,
e,g,g,e,e,g,w,e,
e,r,w,e,e,r,g,e,
e,e,e,e,e,e,e,e,
e,e,e,e,e,e,e,e,
e,e,e,e,e,e,e,e,
e,g,g,g,g,g,g,e,
e,e,e,e,e,e,e,e
]

face3 = [
e,e,e,e,e,e,e,e,
e,g,g,e,e,w,g,e,
e,w,g,e,e,g,g,e,
e,e,e,e,e,e,e,e,
e,e,e,e,e,e,e,e,
e,e,e,e,e,e,e,e,
e,g,g,g,g,g,g,e,
e,e,e,e,e,e,e,e
]

face4 = [
e,e,e,e,e,e,e,e,
e,w,g,e,e,g,g,e,
e,g,g,e,e,w,g,e,
e,e,e,e,e,e,e,e,
e,e,e,e,e,e,e,e,
e,e,e,e,e,e,e,e,
e,g,g,g,g,g,g,e,
e,e,e,e,e,e,e,e
]

sense.set_pixels(smile)

while True:

x,y,z = sense.get_accelerometer_raw().values()

x = round(x, 0)
y = round(y, 0)

x_shake = abs(x)
y_shake = abs(y)
z_shake = abs(z)

if x_shake > 1.5 or y_shake > 1.5 or z_shake > 1.5:
for i in range(3):
sense.set_pixels(face1)
sleep(0.5)
sense.set_pixels(face2)
sleep(0.5)
sense.set_pixels(face3)
sleep(0.5)
sense.set_pixels(face4)
sleep(0.5)
sense.set_pixels(smile)
else:
if x == -1:
sense.set_rotation(180)
elif y == 1:
sense.set_rotation(270)
elif y == -1:
sense.set_rotation(90)
elif x == 1:
sense.set_rotation(0)
else:
sense.set_pixels(smile)
```
 
The files smile.png and sad.png are 8x8 bit graphics files. These files are used to display a sad or happy face on the sense pi hat led display.

 
**Test the service**
* sudo systemctl start smileled
* sudo systemctl status smileled

**If everything works, enable the service**
* sudo systemctl enable smileled
* sudo systemctl daemon-reload
