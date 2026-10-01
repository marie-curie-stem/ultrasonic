# Ultrasonic Distance Measurement

![GitHub License](https://img.shields.io/github/license/marie-curie-stem/ultrasonic)
![GitHub Release](https://img.shields.io/github/v/release/marie-curie-stem/ultrasonic)

Initially I started this project in January 2016 with an Arduino and a 1602 display.

## Update October 2019

I upgraded to a larger 2004 display and placed it on a desk in front of room 407B at AISVN, American School Vietnam in Nha Be. Documentation done on 18.10.2019.

![success](arduino/2019-10-18.jpg)

## New school September 2026

7 years later I handed over this project to the Marie Curie Stem department. The color of the 2004 display changed, it has an infrared proximity sensor and a uptime timer running at the same time:

![New version](arduino/2026-09-30.jpg)

Here is the current code, the HD library can probably be replaced by the `LiquidCrystal` library for both the I2C bridge and the LCD driver.

``` c
#include <Wire.h>
#include <hd44780.h>
#include <hd44780ioClass/hd44780_I2Cexp.h> // include i/o class header
#include <NewPing.h>

#define  TRIGGER_PIN  11
#define  ECHO_PIN     10
#define MAX_DISTANCE 350 // Maximum distance we want to ping for (in centimeters).
                         // Maximum sensor distance is rated at 400-500cm.

NewPing sonar(TRIGGER_PIN, ECHO_PIN, MAX_DISTANCE);

int DistanceIn;
int DistanceCm;

hd44780_I2Cexp lcd; // declare lcd object: auto locate & config display for hd44780 chip

void setup()
{
  Serial.begin(57600);  // start serial to PC
  Serial.println("Ultrasonic Distance Measurement");  
  pinMode(13, OUTPUT); // for status LED
  // initialize LCD with number of columns and rows:
  lcd.begin(20, 4);

  // Print a message to the LCD
  lcd.setCursor(1,0);  
  lcd.print("Ultrasonic Distance");
  lcd.setCursor(0,1); 
  lcd.print("The distance is:");
}

void loop()
{
   delay(100);   // Wartet 100 Milisekunden zwischen den Pings (ca. 10 Pings pro Sekunde). 29 Millisekunden ist der kürzest mögliche Delay zwischen zwei Pings.
   DistanceIn = sonar.ping_in();
   lcd.setCursor(0,2); 
   lcd.print("Ping: ");
   lcd.print(DistanceIn);  // Konvertiert die Ping Zeit in die Entfernung und gibt das Resultat über die Serielleschnittstelle zurück 
                            // (0 = outside set distance range, no ping echo)
   lcd.print(" in     ");
  
   delay(100);  // Wartet 100 Milisekunden zwischen den Pings (ca. 10 Pings pro Sekunde). 29 Millisekunden ist der kürzest mögliche Delay zwischen zwei Pings.
   DistanceCm = sonar.ping_cm();
   lcd.setCursor(0,3);
   lcd.print("Ping: ");
   lcd.print(DistanceCm); 
   lcd.print(" cm  ");  
}
```
