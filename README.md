# Automatic-Crop-protection-System
Developed a system to protect crops from animals using an Arduino UNO R3. The system detects animal movement with PIR motion sensor, sounds an alarm, and sends SMS alerts to farmers via SIM900A GSM module.

Arduino Code:
#include <SoftwareSerial.h>
#include <LiquidCrystal.h>

// Define pins for PIR, LED, Buzzer, and SIM900A module
#define PIR_PIN 9
#define LED_PIN 13
#define BUZZER_PIN 12
#define SIM900A_TX_PIN 10
#define SIM900A_RX_PIN 11

// Define SIM900A communication object
SoftwareSerial sim900a(SIM900A_TX_PIN, SIM900A_RX_PIN);

// Define LCD pins
const int rs = 2, en = 3, d4 = 4, d5 = 5, d6 = 6, d7 = 7;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

void setup() {
  // Initialize PIR pin as input
  pinMode(PIR_PIN, INPUT);
  
  // Initialize LED and Buzzer pins as output
  pinMode(LED_PIN, OUTPUT);
  pinMode(BUZZER_PIN, OUTPUT);

  // Start serial communication
  Serial.begin(9600);

  // Initialize LCD
  lcd.begin(16, 2);

  // Initialize SIM900A module
  sim900a.begin(9600);
  delay(1000);
  sim900a.println("AT");
}

void loop() {
  // Read the PIR sensor input
  int motion = digitalRead(PIR_PIN);

  // If motion is detected
  if (motion == HIGH) {
    // Blink LED
    digitalWrite(LED_PIN, HIGH);
    delay(500);
    digitalWrite(LED_PIN, LOW);

    // Sound buzzer
    digitalWrite(BUZZER_PIN, HIGH);
    delay(500);
    digitalWrite(BUZZER_PIN, LOW);

    // Display message on LCD
    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Motion detected!");
    delay(500);
    lcd.clear();

    // Send SMS
    sendSMS("+918160017725", "An animal raided your farm!");
    
    // Wait for a while to avoid multiple detections
    delay(5000);
  }
}

void sendSMS(String phoneNumber, String message) {
  // Set SIM900A to SMS mode
  sim900a.println("AT+CMGF=1");
  delay(1000);

  // Set the recipient phone number
  sim900a.print("AT+CMGS=\"");
  sim900a.print(phoneNumber);
  sim900a.println("\"");
  delay(1000);

  // Send the SMS message
  sim900a.print(message);

  // End the SMS
  sim900a.write(26);
  delay(1000);
}
