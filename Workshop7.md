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

```js
#include <Wire.h> // Se usa la librería Wire para la comunicación I2C

int t;  // Variable donde se recibe la temperatura

int LED = 7; // Se especifica el pin del LED

void setup() {
  Wire.begin();        // Iniciar la comunicación I2C sin canal para el master
  Serial.begin(9600);  // Iniciar el serial
  pinMode(LED, OUTPUT); // Inicializar el pin del LED
}

void loop() {
  delay(2000);
  Wire.requestFrom(8, 1);    // Solicitar 1 Byte del canal 8
  
  while (Wire.available()) { // Verificar que el esclavo esté envíando información
    t = Wire.read();         // Leer la información dispoinible en el canal
  }

   if (t > 127) {       // Ajuste en caso que la temperatura sea negativa
    t = 256 - t;
    t *= -1;
  }
   
  Serial.print(F("Temperature: ")); // Imprimir la temperatura leída en el serial
  Serial.print(t);
  Serial.println(F("°C "));
  
  if(t < 30){              // Condición para encender el LED
    digitalWrite(LED, LOW);   // Si es menor a 30 no enciende el LED
    Serial.println("No se enciende");
  }else{
    digitalWrite(LED, HIGH);  // Si es mayor o igual a 30 enciende el LED
    Serial.println("Se enciende");
  }
}
```


```js
#include <Wire.h> //Se usa la librería Wire para la comunicación I2C
#include <DHT.h> //Se usa la librería DHT para el sensor de temperatura

#define DHTPIN A0     // Pin donde está conectado el sensor

#define DHTTYPE DHT11  // Se usa el DHT 11

float t; // Variable para almacenar la temperatura

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);           // Iniciar serial
  Wire.begin(8);                // Iniciar la comunicación I2C en el canal #8
  Wire.onRequest(requestEvent); // Se especifica la función para peticiones
  dht.begin();                  // Se inicia la variable del sensor
}

void loop() {
  delay(2000);
  t = dht.readTemperature();          //Leemos la temperatura en grados Celsius
  Serial.print(F("Temperature: "));
  Serial.print(t);
  Serial.print(F("°C "));
}

// Esta función se ejecuta cuando el master pide infomación en el canal #8
// y se especifica como un evento en el setup()
void requestEvent() {  
  Wire.write(int(t)); //Se envía la temperatura en forma de integer   
  Serial.println("send");
}
```
## Video
https://youtu.be/e_LP6xS40O4
