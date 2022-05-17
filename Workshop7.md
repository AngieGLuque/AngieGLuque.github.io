---
layout: default
---


Comunicación I2C implementado en arduino.

## Materiales

Sensor de temperatura, presión y humedad BME280

## I2C

*   #### Hardware:

    Placa de desarrollo Raspberry pi 3
    
*   #### Software:

    Las diferentes librerías que se usaron son:
    
    - Wiring Pi
    - Azure IoT Device
    - Azure IoT Device MQTT
    - BME280 sensor

## Código

```
#include <Wire.h>  //Se usa la librería Wire para la comunicación I2C
#include <DHT.h>    //Se usa la librería DHT para el sensor de temperatura

#define DHTPIN 2     // Pin donde está conectado el sensor

#define DHTTYPE DHT11   // Se usa el DHT 11


DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);           // Iniciar serial
  Wire.begin(8);                // Iniciar la comunicación I2C en el canal #8
  Wire.onRequest(requestEvent); // Se especifica la función para peticiones
  dht.begin();                  // Se inicia la variable del sensor
}

void loop() {
  delay(100);
}

// Esta función se ejecuta cuando el master pide infomación en el canal #8
// y se especifica como un evento en el setup()
void requestEvent() {
  float temperatura = dht.readTemperature(); //Leemos la temperatura en grados Celsius
  Serial.println(temperatura);
  Wire.write(int(temperatura)); //Se envía la temperatura en forma de integer            
}
```
## Video
https://youtu.be/e_LP6xS40O4
