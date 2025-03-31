# Communicating With UART

### Prologue
Alright so I have to teach on UART for FITSEC (my school's CTF team) next week so I am making this in order to make sure that I somewhat know what I am talking about. I bought a router off of amazon for $20 on [this link](https://www.amazon.com/Setup-Wireless-Wi-Fi-Router-Internet/dp/B0CGLWPS5T?th=1) to do this. 
I will say, this is technically NOT hardware hacking since I am only going to talk about how to communicate with a device through a debug port (which is intended). The real hardware hacking comes from getting the device to dump its firmware but you need to be able to talk to the device in order to get it to dump the firmware.

### Looking at the device
When I was first trying to crack open the device I could not find any screws so I tried to yank it open, but there were these two plastic beams keeping it together, weird. What I did was drill holes on the box so that the beams would let out, and they did. Funny thing though, the screws were hiding behind a sticker so I didn't even have to do all that.

Now looking at the device on first glance you can see things like the CPU, the flash memory, etc. You can find sheets on these components by looking up the names written on top of the component. The main thing that I found interesting was these four labeled pin holes.
![Image of the pin holes](./images/IMG_4681.jpeg)

If you look at the labels you will see a GND, VCC, Tx, Rx. This is a great sign for it to be UART because UART also has 4 pins with a transmit, recieve, ground, and VCC. To double check I will use a multimeter to make sure that this is all correct:
 - GND: All I had to do was run continuity on it with a metal part in the device and it should beep
 - VCC: you can check the component sheet on the CPU/flash memory to do continuity on it or you can just get the voltage on it when you turn it on, which will either be 3.3V or 5V
 - Rx: The recieve should just have a constant voltage since it is waiting for incoming signals
 - Tx: The voltage should fluctuate when you turn the device on because it should be sending signals

### Using UART
Cool now that we found UART we can then solder some pins in to be able to hook it up.
![Image of the soldered pins](./images/IMG_4733.jpeg)

Okay cool we have the pins soldered in, but what goes where? Well luckily UART is pretty straight forward, GND to GND, VCC to VCC, Tx to Rx, and Rx to Tx. The reason why there is a crossover in transmit and recieve is because that is how communication works. The way that I view it is like when you speak (Tx) you want someone to be listening to you (Rx) compared to trying to speak over you (Tx) and vice versa. Here is a little model I found online:
![Image of uart](./images/uart.png)

### Finding baud rate
Now that I know how this works, I then can hook it up to a logic analyzer and use pulseview to figure out the baud rate so I can actually communicate with the device. All I need to connect with the logic analyzer is to the Tx and GND since I am not trying to speak to it just yet. 
Okay now that I hooked it up, I set pulseview to read 100 million samples at 1MHz and then ran it and turned on the device. 
Now that I got some signals I zoomed in to one part and discerned the smallest peak so I could assume that it is one bit. Then I just tried different popular baud rates until the bits lined up correctly. When I took the image I was sending it to a friend over the phone so sorry it is like this but I don't feel like redoing this again.
![Image of pulseview](./images/IMG_4735.jpeg)

Now that you have the stuff right you should see text:
![Text](./images/IMG_4681.jpeg)

### Communicating with the Bus Pirate
Now when doing this you can hook it up to the bus pirate, MISO (master in slave out) counts as Rx and MOSI (master out slave in) counts as Tx. Then I used tio to talk to the device.
One problem though was that I was setting tio to have the baud rate of the device but in reality tio is talking to the bus pirate, so keep the baud rate as default and then set the bus pirate configs by sending the `m` command and doing whatever you need. When done, just send `(0)` to see the communication menu, select the one you need and BOOM you are talking to the device.


