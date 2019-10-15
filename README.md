# Getting data from a DHT11 Temperature and Humidity Sensor and using it to rotate a 

By Ralph van Dodewaard<br>
Last updated 15 October 2019

## Step 1: Introduction
Using this manual you'll be able to build a basic prototype that sends data to an Adafruit dashboard, which will display whether a certain spot is available or occupied. The prototype utilizes an Ultrasonic Sensor to measure distance once every few minutes and will be pointed at where people can sit at. If the measured distance goes below a certain threshold, it means that the chair is occupied by someone and the prototype will send this information to the webpage.

## Step 2: Required hardware components
  - 1x Arduino Board (We'll be using the [ESP8266 Development Board](https://www.amazon.com/HiLetgo-Internet-Development-Wireless-Micropython/dp/B010O1G1ES))
  - 1x [DHT11 Temperature and Humidity Sensor](https://www.amazon.com/DHT11-Digital-Temperature-Humidity-Sensor/dp/B00V2DWL2E)
  - 1x Servo Motor (We'll be using the [MR.RC M-1502 Standard Servo Motor](https://www.amazon.com/DEH-M-1502-Standard-Helicopter-Airplane/dp/B07S3W7KNR))
  - 4x Female-Female Jumper Wires
  
## Step 3: Connecting the DHT11 Sensor to the Arduino Board
The physical build is easy to make and consists of connecting an Ultrasonic Sensor to an ESP32 Development Board with Jumper Wires. Use the schematic below to make sure that the pins are connected correctly:<br>
`Vcc` to `Vin`<br>
`Trig` to `D1`<br>
`Echo` to `D0`<br>
`Gnd` to `Gnd`<br>
![Image of schematic](https://github.com/Ralphvandodewaard/iotManual/blob/develop/schematic.png)

## Step 4: Code
For the protoype to function, simply copy the code below and upload it to your ESP32 Development Board. Make sure to replace some of the code with your own data, like the Wifi Network and your Adafruit information.
```
#include <ESP8266WiFi.h>
#include "Adafruit_MQTT.h"
#include "Adafruit_MQTT_Client.h"

#define WLAN_SSID "[YOUR SSID]"
#define WLAN_PASS "[YOUR PASSWORD]"

#define AIO_SERVER "io.adafruit.com"
#define AIO_SERVERPORT 1883
#define AIO_USERNAME "[YOUR USERNAME]"
#define AIO_KEY "[YOUR KEY]"

WiFiClient client;
Adafruit_MQTT_Client mqtt(&client, AIO_SERVER, AIO_SERVERPORT, AIO_USERNAME, AIO_KEY);

Adafruit_MQTT_Publish availability = Adafruit_MQTT_Publish(&mqtt, AIO_USERNAME "/feeds/availability");

void MQTT_connect();

void setup() {
  pinMode(D0, INPUT);
  pinMode(D1, OUTPUT);
  Serial.begin(115200);
  delay(10);

  Serial.println(); Serial.println();
  Serial.print("Connecting to ");
  Serial.println(WLAN_SSID);

  WiFi.begin(WLAN_SSID, WLAN_PASS);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println();

  Serial.println("WiFi connected");
  Serial.println("IP address: "); Serial.println(WiFi.localIP());
}

uint32_t x=0;

void loop() {
  MQTT_connect();

  Adafruit_MQTT_Subscribe *subscription;

  delay(10);
  digitalWrite(D1, HIGH);
  delay(10);
  digitalWrite(D1, LOW);

  int distance = pulseIn(D0, HIGH);
  if (distance < 5820) {
    distance = 0;
  } else {
    distance = 1;
  }
  
  int sensorValue = distance;
  Serial.print(F("\nSending availability "));
  Serial.print(sensorValue);
  Serial.print("...");
  if (! availability.publish(sensorValue)) {
    Serial.println(F("Failed"));
  } else {
    Serial.println(F("Success"));
  }

  delay(10000);
}

void MQTT_connect() {
  int8_t ret;

  if (mqtt.connected()) {
    return;
  }

  Serial.print("Connecting to MQTT... ");

  uint8_t retries = 3;
  while ((ret = mqtt.connect()) != 0) {
       Serial.println(mqtt.connectErrorString(ret));
       Serial.println("Retrying MQTT connection in 5 seconds...");
       mqtt.disconnect();
       delay(5000);
       retries--;
       if (retries == 0) {
         while (1);
       }
  }
  Serial.println("MQTT Connected!");
}
```

### Customisation
- The feed is currently called `availability`. If you change this in the code, make sure to change it in Adafruit and the other way around.
- The delay inbetween measurements is currently set at `delay(10000)`, or 10 seconds, for the prototype. In reality, you may want to set this to a higher number to preserve energy. 

## Step 5: Adafruit
The final step is to setup a feed in Adafruit and create a new dashboard. [Click here](https://learn.adafruit.com/adafruit-io-basics-dashboards/overview) if you don't know how to. Add a Gauge to see if your chosen spot is currently available or taken, and add a line chart to see a history of the measured data.

![Image of schematic](https://github.com/Ralphvandodewaard/iotManual/blob/develop/dashboard.PNG)
