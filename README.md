# Fabric Presence & Wrinkle Detection using Capacitive Sensing 🚀

## Overview
This project focuses on designing a **capacitive sensing circuit** to detect fabric properties, including **fabric presence and wrinkles**, for **robotic applications**. The system is designed to integrate with **robotic arms or textile automation systems** to identify fabric positioning, texture, and surface irregularities. The core challenge was to select the best sensor technology for **non-contact fabric detection**, ensuring **high precision, low power consumption, and minimal interference from environmental factors**.

## Features
✅ **Non-contact fabric detection**  
✅ **Wrinkle detection with high accuracy**  
✅ **OLED display for real-time status**  
✅ **I2C-based sensor integration**  
✅ **Low power consumption for embedded systems**  

## Components Used
- **MPR121 Capacitive Touch Sensor**
- **Custom-designed Copper Capacitive Plate**
- **Arduino Nano**
- **OLED Display**
- **Resistors, Capacitors, Wires**
- **Proteus Simulation for testing**

## How It Works
1. The **capacitive plate** detects changes in capacitance when fabric is present.
2. The **MPR121 sensor** processes the capacitance and sends data via **I2C** to the **Arduino Nano**.
3. The Arduino processes the data and classifies fabric conditions:
   - **Red LED ON** → No Fabric Detected
   - **Blue LED ON** → Smooth Fabric
   - **Green LED ON** → Wrinkled Fabric
4. Results are displayed on an **OLED screen**.

## Code
To upload the code to your Arduino Nano:
```cpp
/***************************
 * Fabric Detection with MPR121 & OLED Display (SSD1306)
 *
 * Hardware:
 *  - MPR121 (I2C address 0x5A) for capacitance measurement
 *  - SSD1306 OLED (I2C address 0x3C) for displaying results
 *  - Single electrode on ELE0 with a ~10×10 mm pad
 *
 * Author: Your Name
 ***************************/

#include <Wire.h>
#include <Adafruit_MPR121.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

// *Define OLED display width and height*
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

// *Create OLED display object*
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

// *Create MPR121 object*
Adafruit_MPR121 mpr = Adafruit_MPR121();

#define FABRIC_THRESHOLD   15   // Above this => fabric detected
#define WRINKLE_THRESHOLD  40   // Above this => wrinkles detected

void setup() {
  Serial.begin(115200);
  Wire.begin();   // Start I2C bus

  // *Initialize OLED Display*
  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println(F("SSD1306 OLED not found"));
    while (1); // Halt
  }
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(10, 10);
  display.println("Initializing...");
  display.display();
  delay(1000);
  
  // *Initialize MPR121*
  if (!mpr.begin(0x5A)) {
    Serial.println("MPR121 not found. Check wiring!");
    display.clearDisplay();
    display.setCursor(10, 10);
    display.println("MPR121 ERROR!");
    display.display();
    while (1); // Halt
  }
  Serial.println("MPR121 Initialized!");

  display.clearDisplay();
  display.setCursor(10, 10);
  display.println("MPR121 Ready...");
  display.display();
  delay(1000);
}

void loop() {
  // *Read MPR121 raw data & baseline for ELE0*
  uint16_t ele0Raw = mpr.filteredData(0);
  uint16_t ele0Base = mpr.baselineData(0) << 2;  // Shift left 2 bits for 10-bit value
  int diff = (int)ele0Base - (int)ele0Raw;  // Baseline - Raw => More capacitance = more fabric

  // *Determine fabric state*
  const char* fabricState = "No Fabric";
  if (diff > FABRIC_THRESHOLD && diff <= WRINKLE_THRESHOLD) {
    fabricState = "Fabric Present";
  } else if (diff > WRINKLE_THRESHOLD) {
    fabricState = "Wrinkled Fabric";
  }

  // *Print results to Serial*
  Serial.print("ELE0 | Raw: ");
  Serial.print(ele0Raw);
  Serial.print(" | Baseline: ");
  Serial.print(ele0Base);
  Serial.print(" | Diff: ");
  Serial.print(diff);
  Serial.print(" => ");
  Serial.println(fabricState);

  // *Display results on OLED*
  display.clearDisplay();
  
  display.setCursor(10, 10);
  display.setTextSize(2);
  display.println("Fabric:");
  
  display.setCursor(10, 30);
  display.setTextSize(2);
  display.println(fabricState);
  
  display.setTextSize(1);
  display.setCursor(10, 50);
  display.print("Raw: ");
  display.print(ele0Raw);
  display.print("  Diff: ");
  display.print(diff);
  
  display.display();

  delay(500);
}
```

## Installation & Usage
1. Clone the repository:  
   ```bash
   git clone https://github.com/yourusername/Fabric-Detection-Robotics.git
   cd Fabric-Detection-Robotics
   ```
2. Open the **Arduino Code** (`fabric_detection.ino`) and upload it to the **Arduino Nano**.
3. Connect the **MPR121** and **OLED display** according to the circuit diagram.
4. Observe the LED and OLED screen outputs.

## Applications
✔ **Textile industry automation**  
✔ **Smart sewing machines**  
✔ **Robotic fabric handling**  
✔ **Quality inspection for garments**  

## Future Enhancements
🔹 **Multi-sensor array for wider coverage**  
🔹 **Machine Learning for improved classification**  
🔹 **Wireless connectivity for IoT applications**  

## License
This project is licensed under the **MIT License**.

## Contributors
👤 **Dhaval Kalathiya** – [GitHub](https://github.com/DhavalKalathiya-Xenox)
