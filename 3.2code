#include <SPI.h>
#include <WiFiNINA.h>
#include <ArduinoHttpClient.h>
#include <hp_BH1750.h>
hp_BH1750 sensor;

int LED = 2;

// Replace with your Wi-Fi credentials
char ssid[] = "FTTH-C997";
char pass[] = "rrrrrrrr";

// IFTTT Webhook details
const char* IFTTT_HOST = "maker.ifttt.com";
const int IFTTT_PORT = 80;
const char* IFTTT_EVENT = "light_detected";  
const char* IFTTT_KEY = "kgyWcQftGBlYHU7wfCjtwx3sFeixWhr2kH7CCFx6E1b";

// Light level threshold (adjust as needed)
const int threshold = 500;

WiFiClient wifiClient;
HttpClient client = HttpClient(wifiClient, IFTTT_HOST, IFTTT_PORT);

void setup() {
  Serial.begin(9600);
  delay(2000);
  
  pinMode(LED, OUTPUT);
  sensor.begin(BH1750_TO_GROUND);
  sensor.start();

  // Connect to Wi-Fi
  while (WiFi.begin(ssid, pass) != WL_CONNECTED) {
    Serial.println("Connecting to WiFi...");
    delay(1000);
  }
  Serial.println("Connected to WiFi");
}

void loop() {
  onLightSensorChange();
  delay(5000); // Wait for a minute before checking again

}

void onlightChange(){
  digitalWrite(LED, HIGH);
  delay(2000);

  digitalWrite(LED, LOW);
  delay(2000);
}

void sendToIFTTT(String value) {
  String url = "/trigger/Light_detected/with/key/kgyWcQftGBlYHU7wfCjtwx3sFeixWhr2kH7CCFx6E1b"; 
  String json = "{\"value1\":\"" + value + "\"}";

  client.beginRequest();
  client.post(url);
  client.sendHeader("Content-Type", "application/json");
  client.sendHeader("Content-Length", json.length());
  client.beginBody();
  client.print(json);
  client.endRequest();

  int statusCode = client.responseStatusCode();
  String response = client.responseBody();
  
  Serial.print("IFTTT Response Code: ");
  Serial.println(statusCode);
  Serial.print("Response: ");
  Serial.println(response);
}

void onLightSensorChange(){
  if (sensor.hasValue())
  {
    float lightSensor = sensor.getLux();
    bool light;
    if (lightSensor > 150)
    {
      light = true;
      onlightChange();
      sendToIFTTT("lightDetected");

      Serial.print("Light Intensity: ");
      Serial.print(lightSensor);
      Serial.println(", light: ON");
    }
    else
    {
      light = false;
      digitalWrite(LED, LOW);
      Serial.print("Light Intensity: ");
      Serial.print(lightSensor);
      Serial.println(", light: OFF");
    }

    delay(5000);
    sensor.start();
}
}
