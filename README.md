# Misshapen Space
Collaborative project with Songyang Shao

## Original work link
As the Unity files are too large to upload to GitHub, we use Google Drive instead, please click on the link below to visit the original work.
https://drive.google.com/drive/folders/1goo6oy6cj3WwYuU3fMs6RjEmRKE3CdEC?usp=sharing

## Background
In a mysterious space station, scientists have conducted a ground-breaking experiment in memory. They developed a technology called the "Memory Fusion Device", which can fuse human memories directly into the physical environment. However, the experiment exploded and the device's energy stones were split into four parts and scattered in different spaces. Only by collecting the scattered pieces of energy stones by walking through the four spaces following the routes remembered in the fusion device, can people restore the space station to its normal state. Now you are the hero of this mission and only your memory and skill can save this mysterious space station.

## Design & Development
### Game flow
![UnityProject](https://github.com/wwdddq/UnityProject/blob/main/image/gameflow.png)

### Interactive approach
- Using an Arduino to create a custom physics button, the player needs to press the button to control the main character's movement during the game. Once the game starts, the player must remember the route and press the button when needed to indicate the direction of the main character's movement. (Implemented)
- Use the Arduino to create a physical energy stone interactive device that lights up with LED lights under the energy stone pieces when the player passes the level. The player collects all the energy stone pieces to light up the physical energy stone device. (Not implemented yet)

## What I did
### Modelling in Cinema4D
![UnityProject](https://github.com/wwdddq/UnityProject/blob/main/image/%E6%88%AA%E5%B1%8F2023-06-12%2001.38.11.png)

### Unity-Build, lighting, particle systems, animation

### Unity-UI

### Unity-Sound



### Arduino-Energy stone devices
Use the Arduino to create a physical energy stone interactive device that lights up with LED lights under the energy stone pieces when the player passes the level. The player collects all the energy stone pieces to light up the physical energy stone device.

![UnityProject](https://github.com/wwdddq/UnityProject/blob/main/image/led.png)
![UnityProject](https://github.com/wwdddq/UnityProject/blob/main/image/ledmap.png)
```ruby
// Defining the bulb pins
const int bulb1Pin = 13;
const int bulb2Pin = 12;
const int bulb3Pin = 11;
const int bulb4Pin = 10;

void setup() {
  // Set the lamp pin to output mode
  pinMode(bulb1Pin, OUTPUT);
  pinMode(bulb2Pin, OUTPUT);
  pinMode(bulb3Pin, OUTPUT);
  pinMode(bulb4Pin, OUTPUT);
}

void loop() {
  // Listen for signals from the Unity game here and perform the appropriate actions

  // Example: Receiving signals via serial communication
  if (Serial.available()) {
    char signal = Serial.read();
    // Control the corresponding light bulb according to the signal
    if (signal == '1') {
      digitalWrite(bulb1Pin, HIGH);  
    } else if (signal == '2') {
      digitalWrite(bulb2Pin, HIGH); 
    } else if (signal == '3') {
      digitalWrite(bulb3Pin, HIGH); 
    } else if (signal == '4') {
      digitalWrite(bulb4Pin, HIGH); 
    }
  }
}
```

![UnityProject](https://github.com/wwdddq/UnityProject/blob/main/image/ring.png)
![UnityProject](https://github.com/wwdddq/UnityProject/blob/main/image/ringmap.png)
```ruby
#include "FastLED.h"
#define NUM_LEDS 12
#define DATA_PIN 5
#define LED_TYPE WS2812
#define COLOR_ORDER GRB

uint8_t max_bright = 128;
CRGB leds[NUM_LEDS];
const int sensorPin = A0;  // Pressure sensor connected to analogue pin A0

void setup() {
  Serial.begin(9600); 
  LEDS.addLeds<LED_TYPE, DATA_PIN, COLOR_ORDER>(leds, NUM_LEDS);
  FastLED.setBrightness(max_bright);
  pinMode(sensorPin, INPUT); // Setting the pressure sensor pin to input mode
}

void loop() {
  int sensorValue = analogRead(sensorPin); // Reading the analogue values of the sensors
  Serial.print("Sensor Value: ");
  Serial.println(sensorValue); // Output sensor readings to serial monitor
  
  if (sensorValue < 200) {
    // Light ring illuminates when the sensor reading is less than 200
     fill_gradient_RGB(leds, 0,CRGB::Blue, 11, CRGB::Green);
 // Light ring all lit up in white
  } else {
    // When the sensor reading is greater than or equal to 800, the light ring goes out
    fill_solid(leds, NUM_LEDS, CRGB::Black); // All light rings are extinguished
  }

  FastLED.show();
  delay(100); 
}
```
I wanted to make the LEDs light up when each level of the game was passed. I tried adding a script to the interface of each level of win to make unity communicate with Arduino, but in the end it didn't work.
```ruby
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using System.IO.Ports;

public class WinObject : MonoBehaviour
{
    SerialPort serialPort;

    void Start()
    {
        // Create serial port object, COM4 is the serial port number, 9600 is the baud rate
        serialPort = new SerialPort("COM4", 9600);
        // Open the serial port
        serialPort.Open();
    }

    void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Player"))
        {
            // Sending signals to the serial port
            serialPort.Write("1");
            Debug.Log("传输串口信号");
        }
    }

    void OnDestroy()
    {
        // Close the serial port
        serialPort.Close();
    }
    ```
}
