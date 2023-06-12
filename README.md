# Misshapen Space
Collaborative project with Songyang Shao

## Original work link
As the Unity files are too large to upload to GitHub, we use Google Drive instead, please click on the link below to visit the original work.
https://drive.google.com/drive/folders/1goo6oy6cj3WwYuU3fMs6RjEmRKE3CdEC?usp=sharing

## Presentation video link
https://youtu.be/iz0lqonzwQQ

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
Building scene models using C4D
![UnityProject](https://github.com/wwdddq/UnityProject/blob/main/image/%E6%88%AA%E5%B1%8F2023-06-12%2001.38.11.png)

### Unity-Build, lighting, particle systems, animation
Build a scene in unity, add light sources and a particle system to create a cloud floating effect. There are also simple orbital rotations of planets and spaceships.
![UnityProject](https://github.com/wwdddq/UnityProject/blob/main/image/building.png)
![UnityProject](https://github.com/wwdddq/UnityProject/blob/main/image/animation.png)

### Unity-UI
Creation of the game's opening UI, and the UI screens for each level's LOSE and WIN.
![UnityProject](https://github.com/wwdddq/UnityProject/blob/main/image/UI.jpg)
![UnityProject](https://github.com/wwdddq/UnityProject/blob/main/image/ui.png)
```ruby
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;

public class StartUi : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }

    public void LoadScen(int value) {

        SceneManager.LoadScene(value);
    }

    public void QuitGame()
    {
        Application.Quit();
    }
}

// In this script, the LoadScene() method is used to load the scene. Depending on the value passed in, the corresponding scene is loaded.

// The QuitGame() method is used to exit the game, calling the Application.Quit() method to close the application.
```

### Unity-Sound
Add sound settings adjustment panel.
```ruby
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using ToolManager;
public class Sound : Singleton<Sound> {

  public   AudioSource m_Bg; // AudioSource component for background music
    public AudioSource m_effect; // AudioSource component for sound effects
    public string ResourcesDir = ""; // Path to the audio resource

    protected override void Awake()
    {
        base.Awake();

        DontDestroyOnLoad(this.gameObject); // The game object is not destroyed when the scene is switched
        m_Bg = gameObject.AddComponent<AudioSource>();
        m_Bg.playOnAwake = false;
        m_Bg.loop = true;
        
        m_effect = gameObject.AddComponent<AudioSource>();

       PlayBG("每个人都是白痴");
    }

    // Play background music
    public void PlayBG(string audioName) {
        string oldName;
        if (m_Bg.clip == null)
        {
            oldName = "";
        }
        else {
            oldName = m_Bg.clip.name;
        }

        if (oldName != audioName) {
            //loading clip
            string path = ResourcesDir + "/" + audioName;

            AudioClip clip = Resources.Load<AudioClip>(path);

            //play
            if (clip != null) {
                m_Bg.clip = clip;
                m_Bg.Play();
            }
        }
    }

    //sound effect
    public void PlayEffect(string audioName) {

        string path = ResourcesDir + "/" + audioName;

        AudioClip clip = Resources.Load<AudioClip>(path);

        //play
        m_effect.PlayOneShot(clip);
    }


}

// This is an audio management that implements the singleton pattern by inheriting from the Singleton<Sound> class.

// This script implements the singleton pattern by inheriting from the Singleton<Sound> class, ensuring that there is only one instance of Sound in the scene.

// In the Awake() method, the AudioSource component for the background music and the AudioSource component for the sound effects are set and the current game object is retained when the scene is switched. PlayBG("E
```
Setting management
```ruby
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;
using UnityEngine.UI;

// Volume adjustment for background music and sound effects and loading scenes

public class SettingManager : MonoBehaviour
{

    public Slider bgSlider; // Slider for background music volume adjustment
    public Slider effctSlider; // Slider for volume adjustment of sound effects


    // Start is called before the first frame update
    void Start()
    {
        if (Sound.Instance) {
            bgSlider.value = Sound.Instance.m_Bg.volume;  // Set the value of the background music slider to the current background music volume
            effctSlider.value = Sound.Instance.m_effect.volume; // Set the value of the sound slider to the current sound volume
        }
      
    }


    public void ChangedBg()
    {
        Sound.Instance.m_Bg.volume = bgSlider.value; // Adjust the background music volume according to the value of the background music slider
    }

    public void loadGame(int value)
    {

        SceneManager.LoadScene(value); // Loading scenes
    }

    public void ChangedEffct()
    {
        Sound.Instance.m_effect.volume = effctSlider.value; // Adjust the sound volume according to the value of the sound slider
    }
    
    // Update is called once per frame
    void Update()
    {
        
    }
}
```
  
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
    
}
```

## Conclusion and Reflection
I have learnt the basics of how to make games using Unity, but I think I am still lacking in scripting and overall game logic, so I will continue to learn more about this later. In terms of Arduino, it was a big regret that the device I wanted to make didn't work out in the end, and I would like to try again later on.

## References
https://answers.unity.com/questions/1902989/using-sound-with-the-3rd-person-starter-asset.html
https://www.bilibili.com/video/BV1uY4y1z73F/?spm_id_from=333.880.my_history.page.click&vd_source=4bf35e742c5f58014845d002599fdab0
https://learn.unity.com/tutorial/yin-pin-8?uv=2020.3&projectId=5dfa9a5dedbc2a26d1077ca8
