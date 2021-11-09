---
lang: en
lang-niv: auto
lang-ref: indekso
layout: page
title: RF_gpio
---

# Presentation
 _rf_GPO_ enables to simulate Rflink with a transmitter and receiver connected directly to the ports _GPIO_ of A _raspberry pi_.


## Installation:

```
cd /home/pi
git clone https://github.com/jmichault/rf_gpio.git
cd rf_gpio  
sudo cp -p rf_gpio.sh /etc/init.d  
sudo update-rc.d rf_gpio.sh defaults  
sudo service rf_gpio.sh start  
```

Connect the receiver to GPO2 BCM27  ( PIN 13 )   
Connect the transmitter to GPO0 BCM17  ( PIN 11 )   

## Use with hosticz:
Add material of type  _«RFLink Gateway with LAN interface»_ 
 	 Remote Address: 127.0.0.1   
	 Port: 10000    
 If the hardware is recognized, the integration with Domomoticz is simple: it is enough to use the  " automatic detection button " on the " Switches " tab, or activate the option  " Allow 5 minutes "    

## Transceivers Tested:
* Chinese gear with reactor reactor  ( Asin = B00Z9SZNP0, MX-05V + MX-FS-03V ), viewed in Amazon at € 1.   


	The transmitter is fine, but the receiver is very low (range of about 4M), to use only for learning codes.  
* WL101-341 + WL102-341, Superherotedyne 433 MHz, seen on AliExpress at € 1.  


	 The receiver has a better sensitivity than the previous one.    
	
Note: For good results, the receiver must be protected from pests issued by  _raspberry-pi_. For testing, you can use an aluminum coat in a sheet of paper folded in half. 

## Recognized materials:
been successfully tested:   
* Kyg (Asin: B07DPMPW1, marked intercourse, points of view of Amazon)(recognized as "impulses")  


* Chinese Chinese anneng  (  Thermometer with LCD screen, viewed on AliExpress. )   


* Digoooo RG-8B Higrometer-Higrometer Chinese cost, without screen, viewed on Aliexpress.)  



Other sensors are predefined in sensors.ini, but were not tested.  

## To add a new sensor:
The data transfer protocol and the data format must be identified.  
### Option 1:
Follow the exit of RF_gpio, soit en le lançant dans une fenêtre shell, soit en s'y connectant avec la commande «telnet 127.0.0.1 10000».  
  
  
When the sensor sends data, and the transfer protocol is recognized, you will see something that looks like this:   
20; 00; P0102, Bits = 36, D0 = 529, D1 = 949, D2 = 1926, DS = 3865; binary = 011100110000100000, hex = 7300d8f20;  
  20; = Any RFLink emissions frame starts like this.  
    00; = First framework published.    
 P0102, Bits = 36, D0 = 529, D1 = 949, D2 = 1926, DS = 3865 = RF  _gpio a identifié un protocole du type :  
  
  
	bit 0 = D0 D1  
	bit 1 = D0 D2  
	36 data bits  
	Duration d0 = 520 μs  
	Duration d1 = 957 μs  
	Duration d2 = 1936 μs  
	 Synchronous duration DS = 3881 μs    
 Duuma = 0111001100000000100000, Hex = 7300D8F20; : Data received in Binary and Hexa.   

### Option 2:
Run in bark:   
./analyzi  
and press the button on the remote control, or wait for the sensor to send data. If the protocol is recognized, we see something similar to that:   
   73 Practical protocol:   " xxx; P0001, Bits = 36, D0 = 689, D1 = 1923, DS = 3890; ID: B1-B36  "    
 Binary Data: 01110011000000110110001100100000    
 Hexa Data: 7300d8f20    


You now need to analyze binary data to identify the meaning of each piece.  
You can then add a line in the Dentilos.ini file, each line consists of three elements separated by a semicolon:   
* First element: the name of the equipment. Attention, if it is a switch, it must be part of the list of elements recognized by Penoticz.  


* Second element: Protocol. Copy what is shown by RF_gpio ou analizo.  
    

    

	Example: P0102, bits = 36, d0 = 561, d1 = 1899, d2 = 3845, ds = 9158  
		means: protocol P0102 (bit 0 = D0 D1, bit 1 = D0 D2), 36 bits by frame, duration d0 = 561 μs, duration d1 = 1899 μs, duration d2 = 3845 μs, duration synchro ds = 9158 μs  
	 	 The protocol, bits, D0 and D1 parts are used to differentiate devices.   
* Third Element: Data description, each field separates from others by coma  


	Example for Multi-channel Remote Control, ID: ID: B1-20, CMD: B21-21, Swile: B22-24  
	 	 ID: B1-20 means that the ID of the remote control is in bits 1 to 20  ( bit 1 = first bit delivered 
 	  	 CMD: B21-21 means The command transmitted   (  ON / OFF  )   is located in ILO 21.    
 	  	 Change: B22-24 means that the number of the operated Jack is located at the bits 22 to 24.    
 	 Be acknowledged by Domomoticz, the name of the field must be in the renowned list  ( See sensilos.txt ). However, we can put what we want, simply the field will be ignored by Domomoticz.   
	There are several bit suites, for example: CMD: B17-17: B15-15: B16-16 Concatenate Bits 17 and 16 in that order.  
	The value of certain bits can be tested, for example: CST2: B43-48 = 1 will confirm that the bits 43 48 contain the value 1 (in reception, and influence These pieces in distribution, CST2: B43-48! 1 Confirm that the bits 438 do not contain the value 1 (hexadecimal) in reception.  
	 We can declare BCD-coded fields   (  Decimal coding binary  ) : Put B instead of b. Example: Temp: B12-15: B16-19: B20-23 declares a temperature field whose first number is to the bits 12-15, the second to the bits 16 to 20 and the third at the bits 21 to 23.    
 	 Field finishing with  " -Inv " is a special field that will take the inverse  ( complement to one )  of its homonyst field in the show.   
	You can assign a value to a field that is not found in the data per "=". Example: cmd = on  
	 Simple calculations can be made: Addition, multiplication and subtraction are possible   (  in this correct order  ), the constants are in hexadecimal.    
 	 	 Example to convert in tenths of ° C data that is in tenths of ° F + 900:   
	  	 Temp: B17-28 + -4c4  *  5/9    (   SO: DATA  _finale = (donnée_  Animal -1220  )   *   5/9   )    

In receipt all the lines that satisfy the condition will generate a line, if you want to avoid false positives, you can start or remove the lines that do not match your equipment.  
About question, only the first line with the correct name of equipment will be used.  
	
		
If the protocol is not recognized, you can use to analyze to study it by increasing its verb with the option -v, -vv or -vv. But RF_gpio ne pourra pas le reconnaitre sans développement supplémentaire.  
  
  


## Supported protocols:

Only protocols with at least the following features have a chance to be recognized:   
* Time of pulsed always > 100 μs  


* No more than three different durations to represent the data.  


* Each piece is coded with two pulses, always in the same way.  


* Each frame is surrounded by two synchronization signals of time >2500 μs.  


* Frame does not contain more than 200 pulses.  



In the show, the rotary codes and the confirmation amounts are not administered.  
