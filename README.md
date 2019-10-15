# Getting data from a DHT11 Temperature and Humidity Sensor and using it to rotate a Servo Motor

By Ralph van Dodewaard<br>
Last updated 15 October 2019

## Introduction
Using this manual you'll be able to build a basic prototype that can automatically rotate a Servo Motor, using data gathered from a DHT11 Temperature and Humidity Sensor.

## Required hardware components
  - 1x Arduino Board (We'll be using the [ESP8266 Development Board](https://www.amazon.com/HiLetgo-Internet-Development-Wireless-Micropython/dp/B010O1G1ES))
  - 1x [DHT11 Temperature and Humidity Sensor](https://www.amazon.com/DHT11-Digital-Temperature-Humidity-Sensor/dp/B00V2DWL2E)
  - 1x Servo Motor (We'll be using the [MR.RC M-1502 Standard Servo Motor](https://www.amazon.com/DEH-M-1502-Standard-Helicopter-Airplane/dp/B07S3W7KNR))
  - 1x [Resistor](https://www.amazon.com/Projects-100EP51210K0-10k-Resistors-Pack/dp/B0185FIOTA) (5k-10k ohm)
  - 3x Jumper Wires
  
## Step 1: Connecting the DHT11 Sensor to the Arduino Board
The first thing we want to do is connect our DHT11 Sensor to the Arduino Board, using Jumber Wires. The image below shows which wires need to be connected to which pins on the board. Note that we also have to attach the resistor between the wires that are connected to `3v3` and `D2`.

Leftmost pin to `3v3` (red wire)<br>
Second pin to `D2` (green wire)<br>
Third pin empty<br>
fourth pin to `Gnd` (black wire)<br>
Resistor between red and green wire<br>
![Image of schematic](https://github.com/Ralphvandodewaard/iotManual/blob/develop/schematic.png)

## Step 2: Installing the required libraries in Arduino IDE
For the DHT11 Sensor to properly function, we will first need to install the required libraries in the [Arduino IDE](https://www.arduino.cc/en/main/software). We can do this by going to the 'Sketch' dropdown menu, selecting 'Include Library' and then clicking on 'Manage Libraries'.<br>
![Image of library manager](https://github.com/Ralphvandodewaard/manualiot/blob/develop/library1.png)

In the Library Manager, we first want to search for 'Adafruit Unified Sensor'. Scroll down to the correct library, made by Adafruit, and press 'Install'. If done correctly, the library will now say 'Installed'.<br>
![Image of library manager2](https://github.com/Ralphvandodewaard/manualiot/blob/develop/library2.png)

We now want to install one more library, called the 'DHT sensor library' by Adafruit. Search for the correct library in the Library Manager and again press 'Install'. If done correctly, the library will now say 'Installed'.<br>
![Image of library manager3](https://github.com/Ralphvandodewaard/manualiot/blob/develop/library3.png)


## Step 3: Writing and uploading the required code for the DHT11 Sensor
If the required libraries for the sensor have been properly installed, as done in Step 2, the code will now start uploading to your Arduino Board.
```
#include "DHT.h"

#define DHTPIN 2
#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);

  dht.begin();
}

void loop() {
  delay(5000);

  float h = dht.readHumidity();
  float t = dht.readTemperature();

  if (isnan(h) || isnan(t)) {
    Serial.println("Failed to read");
    return;
  }

  Serial.print("Humidity: ");
  Serial.print(h);
  Serial.print("%  Temperature: ");
  Serial.print(t);
  Serial.print("°C ");
}
```

### Customisation
- The feed is currently called `availability`. If you change this in the code, make sure to change it in Adafruit and the other way around.
- The delay inbetween measurements is currently set at `delay(10000)`, or 10 seconds, for the prototype. In reality, you may want to set this to a higher number to preserve energy. 

## Step 5: Adafruit
The final step is to setup a feed in Adafruit and create a new dashboard. [Click here](https://learn.adafruit.com/adafruit-io-basics-dashboards/overview) if you don't know how to. Add a Gauge to see if your chosen spot is currently available or taken, and add a line chart to see a history of the measured data.

![Image of schematic](https://github.com/Ralphvandodewaard/iotManual/blob/develop/dashboard.PNG)
