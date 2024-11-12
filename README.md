# solarProject
#include <DHT.h>
#include <SoftwareSerial.h>

#define DHTPIN 7
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

#define SOUND_SENSOR A0
#define AIR_QUALITY_SENSOR A1
#define TEMP_SENSOR A2

// Initialize the SoftwareSerial to communicate with ESP8266
SoftwareSerial espSerial(10, 11); // RX, TX

const char* ssid = "sahnikita";
const char* password = "Khyati@23";
const char* apiKey = "ORM2S896L1KC7388";
const char* server = " https://api.thingspeak.com/update?api_key=ORM2S896L1KC7388&field1=0";

void setup()#include <SoftwareSerial.h>

#define TEMP_SENSOR_PIN A0  // TMP36 Temperature sensor
#define HUMIDITY_SENSOR_PIN A1  // Analog pin for simulated humidity sensor

// Variables for data transmission
String apiKey = "YOUR_THINGSPEAK_API_KEY"; // Replace with your actual API key
const char* server = "api.thingspeak.com";  // ThingSpeak server
SoftwareSerial espSerial(2, 3);  // RX, TX for ESP8266 (or similar module)

void setup() {
  Serial.begin(9600);
  espSerial.begin(9600); // Initialize serial communication to ESP8266
  
  // Connection setup for ESP8266
  connectToWiFi();
}

void loop() {
  float temperatureC = readTemperature();
  int humidityValue = analogRead(HUMIDITY_SENSOR_PIN);  // Simulated humidity

  // Print data to Serial Monitor for debugging
  Serial.print("Temperature (C): ");
  Serial.print(temperatureC);
  Serial.print("  Humidity: ");
  Serial.println(humidityValue);

  // Send data to ThingSpeak (simulated)
  sendToThingSpeak(temperatureC, humidityValue);

  delay(20000);  // ThingSpeak accepts data every 15 seconds, so a 20-second delay is used here
}

// Function to read temperature from TMP36 sensor
float readTemperature() {
  int tempValue = analogRead(TEMP_SENSOR_PIN);
  float voltage = tempValue * (5.0 / 1023.0); // Convert the analog reading to voltage
  return (voltage - 0.5) * 100;  // Convert voltage to temperature (Celsius)
}

// Function to simulate Wi-Fi connection setup
void connectToWiFi() {
  espSerial.println("AT+CWMODE=1");  // Set Wi-Fi mode to client
  delay(2000);
  espSerial.println("AT+CWJAP=\"YOUR_SSID\",\"YOUR_PASSWORD\"");  // Connect to Wi-Fi
  delay(5000);

  if (espSerial.find("OK")) {
    Serial.println("Connected to Wi-Fi");
  } else {
    Serial.println("Wi-Fi connection failed");
  }
}

// Function to send data to ThingSpeak
void sendToThingSpeak(float temperature, int humidity) {
  String sendData = "GET /update?api_key=" + apiKey +
                    "&field1=" + String(temperature) +
                    "&field2=" + String(humidity) + "\r\n";
  
  espSerial.println("AT+CIPSTART=\"TCP\",\"" + String(server) + "\",80"); // Connect to ThingSpeak
  delay(2000);

  if (espSerial.find("OK")) {
    espSerial.println("AT+CIPSEND=" + String(sendData.length() + 4));
    delay(2000);

    espSerial.println(sendData);  // Send data to ThingSpeak
    delay(2000);

    espSerial.println("AT+CIPCLOSE");  // Close the connection
    Serial.println("Data sent to ThingSpeak");
  } else {
    Serial.println("Connection to ThingSpeak failed");
  }
}
