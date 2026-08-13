# Smart-Agri-Weather-Station-Automated-Irrigation-System



#define BLYNK_TEMPLATE_ID "TMPL3P2TwwE9j"
#define BLYNK_TEMPLATE_NAME "Smart Agriculture"
#define BLYNK_AUTH_TOKEN "yvmaOED933F4Ii0EhVFIgIhDM-Qm6KXV"

#include <WiFi.h>
#include <BlynkSimpleEsp32.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <DHT.h>
#include <OneWire.h>
#include <DallasTemperature.h>
#include <Adafruit_BMP085.h>

char ssid[] = "Wokwi-GUEST";
char pass[] = "";

#define DHTPIN 4
#define DHTTYPE DHT22
#define SOIL_PIN 35
#define RELAY_PIN 26
#define ONE_WIRE_BUS 18
#define GAS_PIN 34

OneWire oneWire(ONE_WIRE_BUS);
DallasTemperature soilTempSensor(&oneWire);

DHT dht(DHTPIN, DHTTYPE);

LiquidCrystal_I2C lcd(0x27, 20, 4);

Adafruit_BMP085 bmp;

BlynkTimer timer;

bool pumpState = false;


// ===============================
// SEND DATA TO BLYNK
// ===============================

void sendToBlynk() {

  float temp = dht.readTemperature();
  float hum = dht.readHumidity();

  soilTempSensor.requestTemperatures();
  float soilTemp = soilTempSensor.getTempCByIndex(0);

  int soilRaw = analogRead(SOIL_PIN);
  int soilPercent = map(soilRaw, 0, 4095, 100, 0);

  float pressure = bmp.readPressure() / 100.0;

  int gasRaw = analogRead(GAS_PIN);


  // Send sensor values to Blynk

  Blynk.virtualWrite(V0, temp);

  Blynk.virtualWrite(V1, hum);

  Blynk.virtualWrite(V2, soilPercent);

  Blynk.virtualWrite(V3, soilTemp);

  Blynk.virtualWrite(V4, pressure);

  Blynk.virtualWrite(V5, gasRaw);

  Blynk.virtualWrite(V6, pumpState ? 1 : 0);


  Serial.println("Data sent to Blynk");
}


// ===============================
// SETUP
// ===============================

void setup() {

 WiFi.begin(ssid, pass);

Serial.print("Connecting to WiFi");

while (WiFi.status() != WL_CONNECTED) {
  delay(500);
  Serial.print(".");
}

Serial.println();
Serial.println("WiFi connected!");
Serial.print("IP address: ");
Serial.println(WiFi.localIP());

Blynk.config(BLYNK_AUTH_TOKEN);

Serial.println("Connecting to Blynk...");

if (Blynk.connect(10000)) {
  Serial.println("Blynk connected!");
} else {
  Serial.println("Blynk connection FAILED!");
}
  // Start sensors
  dht.begin();
  soilTempSensor.begin();

  // Start LCD
  lcd.init();
  lcd.backlight();

  // Relay
  pinMode(RELAY_PIN, OUTPUT);
  digitalWrite(RELAY_PIN, LOW);

  // BMP180
  if (!bmp.begin()) {

    Serial.println("BMP180 NOT FOUND!");

    while (1);
  }


  // Start-up message

  lcd.setCursor(0, 0);
  lcd.print("Smart Agriculture");

  delay(2000);

  lcd.clear();


  // Send data every 2 seconds

  timer.setInterval(2000L, sendToBlynk);
}


// ===============================
// MAIN LOOP
// ===============================

void loop() {

  // Keep Blynk connection alive

  Blynk.run();

  // Run timer

  timer.run();


  // ===============================
  // READ DHT22
  // ===============================

  float temp = dht.readTemperature();

  float hum = dht.readHumidity();


  // ===============================
  // READ DS18B20
  // ===============================

  soilTempSensor.requestTemperatures();

  float soilTemp =
    soilTempSensor.getTempCByIndex(0);


  // ===============================
  // READ SOIL MOISTURE
  // ===============================

  int soilRaw = analogRead(SOIL_PIN);

  int soilPercent =
    map(soilRaw, 0, 4095, 100, 0);


  // ===============================
  // READ MQ-2
  // ===============================

  int gasRaw = analogRead(GAS_PIN);


  // ===============================
  // READ BMP180
  // ===============================

  float pressure =
    bmp.readPressure() / 100.0;


  // ===============================
  // AUTOMATIC IRRIGATION
  // ===============================

  if (soilPercent < 30) {

    pumpState = true;
  }

  else if (soilPercent > 60) {

    pumpState = false;
  }


  // Control relay

  digitalWrite(
    RELAY_PIN,
    pumpState ? HIGH : LOW
  );


  // ===============================
  // LCD DISPLAY
  // ===============================

  lcd.clear();


  lcd.setCursor(0, 0);
  lcd.print("Temp:");
  lcd.print(temp, 1);
  lcd.print(" C");


  lcd.setCursor(0, 1);
  lcd.print("Hum:");
  lcd.print(hum, 1);
  lcd.print("%");


  lcd.setCursor(0, 2);
  lcd.print("Soil:");
  lcd.print(soilPercent);
  lcd.print("%");


  lcd.setCursor(0, 3);
  lcd.print("ST:");
  lcd.print(soilTemp, 1);
  lcd.print(" P:");

  if (pumpState) {

    lcd.print("ON");
  }

  else {

    lcd.print("OFF");
  }


  // ===============================
  // SERIAL MONITOR
  // ===============================

  Serial.print("Temperature: ");
  Serial.print(temp, 1);
  Serial.print(" C");

  Serial.print(" | Humidity: ");
  Serial.print(hum, 1);
  Serial.print("%");

  Serial.print(" | Soil: ");
  Serial.print(soilPercent);
  Serial.print("%");

  Serial.print(" | SoilTemp: ");
  Serial.print(soilTemp, 1);
  Serial.print(" C");

  Serial.print(" | Pressure: ");
  Serial.print(pressure, 1);
  Serial.print(" hPa");

  Serial.print(" | Gas: ");
  Serial.print(gasRaw);

  Serial.print(" | Pump: ");

  if (pumpState) {

    Serial.println("ON");
  }

  else {

    Serial.println("OFF");
  }


  delay(2000);
}
