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
  
![Image of requirements](https://github.com/Ralphvandodewaard/manualiot/blob/master/requirements.png)
  
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

In the Library Manager, we first want to search for 'Adafruit Unified Sensor'. Scroll down to the correct library, made by Adafruit, and press 'Install'. If done correctly, the library will now say 'Installed'.<br>
![Image of library manager2](https://github.com/Ralphvandodewaard/manualiot/blob/master/library2.png)

We now want to install one more library, called the 'DHT sensor library' by Adafruit. Search for the correct library in the Library Manager and again press 'Install'. If done correctly, the library will now say 'Installed'.<br>
![Image of library manager3](https://github.com/Ralphvandodewaard/manualiot/blob/master/library3.png)

## Step 3: Uploading the required code for the DHT11 Sensor to the Arduino Board
We can now start uploading code to our Arduino Board to get the DHT11 Sensor to work. Plug your Arduino Board into your computer using a USB to Micro USB cable and copy and paste the code shown below to the Arduino IDE. If the required libraries for the sensor have been properly installed, as done in step 2, you can now press 'Upload' in the top left of your screen. The code will start compiling and uploading to your Arduino Board.

When doing this, make sure that in 'Tools' the correct Board and Port is selected. In our case the Board is set to 'NodeMCU 1.0', while the Port depends on which USB input you're using.
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
## Step 4: Reading data from the DHT11 Sensor
If all steps so far have been succesfully executed, we will now be able to see the data gathered by our DHT11 Sensor. This can be done in the serial monitor in the Arduino IDE, by pressing the button in the top right of your screen. If everything functions properly, you will be able to read multiple measurements of the humidity and temperature in the serial monitor. When doing this, make sure that the selection dropdown in the bottom right is set to '9600 baud'. 

![Image of serial monitor](https://github.com/Ralphvandodewaard/manualiot/blob/master/serial.png)

## Step 5: Connecting the Servo Motor to the Arduino Board
Now that we've got our DHT11 Sensor working, we want to connect the Servo Motor to our Arduino Board. The Servo Motor that we use has three wires and needs to be connected to the Arduino Board as shown in the image below.

![Image of schematic](https://github.com/Ralphvandodewaard/iotManual/blob/develop/dashboard.PNG)

## Step 6: Uploading the required code for the Servo Motor to the Arduino Board
Unlike with the DHT11 Sensor, the required library for the Servo Motor comes pre-installed on the Arduino IDE. We can therefore start uploading the code for our Servo Motor to the Arduino Board straight away. Combining both the code for the DHT11 Sensor and the Servo Motor, the 

```
#include "DHT.h"
#include <Servo.h> 

#define DHTPIN 2
#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);

Servo servo;  
int servoPin = 5;
int angle = 0;

void setup() {
  Serial.begin(9600);

  dht.begin();
  servo.attach(servoPin); 
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
  
  float angle;
  float threshold = 65;
  
  if (h > threshold) {
    for(angle = 0; angle < 180; angle++) {                                  
      servo.write(angle);               
      delay(15); 
    }
  } else {
    for(angle = 180; angle > 0; angle--) {                                  
      servo.write(angle);               
      delay(15); 
  }
}
```

## Step 7: Test and customize
If the Servo Motor has been connected properly and the new code has been uploaded to the Arduino Board, you can now see that the Servo Motor starts to rotate as the measured humidity reaches a certain threshold. You can try this yourself by blowing into the DHT11 Sensor to increase the measured humidity.
