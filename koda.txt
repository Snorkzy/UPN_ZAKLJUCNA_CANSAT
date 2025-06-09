#include <SoftwareSerial.h>
#include <DHT.h>
#include <TinyGPSPlus.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_DPS310.h>
#include <SdFat.h>

// Definicija pinov
#define SD_CS 10
#define DHTPIN 8      
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);
 
SoftwareSerial apc220(6, 7); // APC220: RX, TX
 
SoftwareSerial gpsSerial(4, 3);  // GPS: RX, TX
TinyGPSPlus gps;
 
Adafruit_DPS310 dps;
Adafruit_Sensor *dps_temp = dps.getTemperatureSensor();
Adafruit_Sensor *dps_pressure = dps.getPressureSensor();
 
void setup() {
  Serial.begin(9600);    
  apc220.begin(9600);  
  gpsSerial.begin(9600);  
  dht.begin();    
 
  dps.begin_I2C();
  dps.configurePressure(DPS310_16HZ, DPS310_16SAMPLES);
  dps.configureTemperature(DPS310_16HZ, DPS310_16SAMPLES);

  if (!SD.begin(SD_CS)) {
  Serial.println("Napaka pri inicializaciji SD kartice!");
  return;
       
}
void loop() {
  // Preberi podatke iz GPS
  while (gpsSerial.available() > 0) {
    gps.encode(gpsSerial.read());
  }

  float temperature = dht.readTemperature();
  float humidity = dht.readHumidity();
 
  String data =String(temperature, 3) + ";" + String(humidity, 3);
 
  sensors_event_t temp_event, pressure_event;
 
  if (dps.temperatureAvailable()) {
    dps_temp->getEvent(&temp_event); // Read temperature
  }
  if (dps.pressureAvailable()) {
    dps_pressure->getEvent(&pressure_event); // Read pressure
  }
 
  float baroAltitude = 44330.0 * (1.0 - pow(pressure_event.pressure / 1020.8, 0.1903));  //potrebno spreminjat
 
  data += ";";
  data += String(temp_event.temperature, 3);
  data += ";";
  data += String(pressure_event.pressure);
  data += ";";
  data += String(baroAltitude);
 
  // Preveri, ali so GPS podatki veljavni
  if (gps.location.isValid()) {
    data += ";";
    data += String(gps.location.lat(), 6);
    data += ";";
    data += String(gps.location.lng(), 6);
    data += ";" ;
    data += String(gps.altitude.meters());
    data += ";";
    data += String(gps.speed.kmph(), 2);
    data += ";";
    data += String(gps.satellites.value());
  }else{
    data += ";0.000000;0.000000;0;0.00;0";
  }

    SdFile file;
  if (file.open("data.txt", O_WRITE | O_CREAT | O_APPEND)) {
    file.println(data);  // Write data to the file
    file.close();  // Close the file
 
    Serial.println("Podatki zapisani na SD: ");
  } else {
    Serial.println("Napaka pri odpiranju datoteke!");
  }
  data="";
   
  // Pošlji podatke prek APC220
  apc220.println(data);
  Serial.println(data);
  delay(1000);
}
