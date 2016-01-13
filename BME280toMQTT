#include <ESP8266WiFi.h>
#include "Adafruit_MQTT.h"
#include "Adafruit_MQTT_Client.h"
#include <Wire.h>
#include <SPI.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BME280.h>

 
/************************* WiFi setup *********************************/
 
#define WLAN_SSID       ""
#define WLAN_PASS       ""
#define WLAN_SECURITY   WLAN_SEC_WPA2

// Create an ESP8266 WiFiClient class to connect to the MQTT server.
WiFiClient client;
 
/************************* MQTT setup *********************************/
 
const int MQTT_PORT = 1883;
const char MQTT_SERVER[] PROGMEM    = "mypi2.home.ip";
const char MQTT_CLIENTID[] PROGMEM  = "Wifidino";
const char MQTT_USERNAME[] PROGMEM  = "";
const char MQTT_PASSWORD[] PROGMEM  = "";
 
Adafruit_MQTT_Client mqtt(&client, MQTT_SERVER, MQTT_PORT, MQTT_CLIENTID, MQTT_USERNAME, MQTT_PASSWORD);
 
const char TEMPERATURE_FEED[] PROGMEM = "/node/wifidino/temperature";
Adafruit_MQTT_Publish temperature_topic = Adafruit_MQTT_Publish(&mqtt, TEMPERATURE_FEED);
 
const char PRESSURE_FEED[] PROGMEM = "/node/wifidino/pressure";
Adafruit_MQTT_Publish pressure_topic = Adafruit_MQTT_Publish(&mqtt, PRESSURE_FEED);

const char ALTITUDE_FEED[] PROGMEM = "/node/wifidino/altitude";
Adafruit_MQTT_Publish altitude_topic = Adafruit_MQTT_Publish(&mqtt, ALTITUDE_FEED);

const char HUMIDITY_FEED[] PROGMEM = "/node/wifidino/humidity";
Adafruit_MQTT_Publish humidity_topic = Adafruit_MQTT_Publish(&mqtt, HUMIDITY_FEED);


/****************************** BME280 setup ***************************************/

#define SEALEVELPRESSURE_HPA (1013.25) 
Adafruit_BME280 bme; // I2C
 
/*************************** Sketch Code ************************************/
 
void setup() {
    Serial.begin(115200);
    delay(10);
   
  // Check the BME280 sensor
    Serial.print("BME280 test....");
    if (!bme.begin())
       {
        Serial.println("could not find a valid BME280 sensor, check wiring!");
        while (1);
      
  }
    else {
        Serial.println("BME280 ready.");
      
  }
   
   
    // Connect to WiFi access point.
    Serial.println();
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
 


void loop() {
    MQTT_connect();
   
    /* Get a sensor data */
  Serial.print("Temperature = ");
  Serial.print(bme.readTemperature());
  Serial.println(" *C");
  temperature_topic.publish(bme.readTemperature());

  Serial.print("Pressure = ");
  Serial.print(bme.readPressure() / 100.0F);
  Serial.println(" hPa");
  pressure_topic.publish(bme.readPressure() / 100.0F);

  Serial.print("Approx. Altitude = ");
  Serial.print(bme.readAltitude(SEALEVELPRESSURE_HPA));
  Serial.println(" m");
  altitude_topic.publish(bme.readAltitude(SEALEVELPRESSURE_HPA));

  Serial.print("Humidity = ");
  Serial.print(bme.readHumidity());
  Serial.println(" %");
  humidity_topic.publish(bme.readHumidity());

  Serial.println();
    delay(60000);                                                     //update each minute
}
 
 

void MQTT_connect() {
    int8_t ret;
   
    if (mqtt.connected()) {                                           // Stop if already connected.
        return;
      
  }
   
    Serial.print("Connecting to MQTT... ");
   
    while ((ret = mqtt.connect()) != 0) {                             // connect will return 0 for connected
        switch (ret) {
            case 1: Serial.println("Wrong protocol"); break;
            case 2: Serial.println("ID rejected"); break;
            case 3: Serial.println("Server unavailable"); break;
            case 4: Serial.println("Bad user/password"); break;
            case 5: Serial.println("Not authenticated"); break;
            case 6: Serial.println("Failed to subscribe"); break;
            default: Serial.print("Couldn't connect to server, code: ");
                Serial.println(ret);
                break;
            
    }
        Serial.println("Retrying MQTT connection in 5 seconds...");
        mqtt.disconnect();
        delay(5000);                                                    // wait 5 seconds
      
  }
    Serial.println("MQTT Connected!");
}
