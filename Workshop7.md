---
layout: default
---


Comunicación I2C implementado en arduino.

## Materiales

*   Sensor de temperatura DHT11
*   2 placas de desarrollo arduino (UNO R3 y Mega 2560)
*   Un diodo LED
*   Un resistor (1kΩ)
*   Cables dupont

## Esquemático



## I2C

Es un protocolo que se enfoca principalmente en la comunicación de microcontroladores y sistemas embebidos; para esto utiliza dos líneas: SDA y SCL. SDA es la línea que se encarga de los datos y SCL es el reloj. Este protocolo permite una confirmación de los datos recibidos durante el mismo trayecto o trama. Cabe resaltar que para la utilización de este protocolo es necesario saber la cantidad de bytes que va a tener la información que se va a enviar desde el esclavo al maestro.

## Código

*   ### Maestro:

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

En el maestro se declara la librería Wire necesaria para la comunicación I2C, una variable que guardará el valor de la temperatura y una variable dedicada al pin del LED. 

En el setup se inicializa la comunicación I2C sin especificar canal porque así se declara el maestro según la librería, también inicializamos el pin dedicado al LED como salida e inicializamos el puerto serial para imprimir en consola información relevante. 

En el loop, el maestro solicita un byte en el canal 8 (el especificado para el esclavo) y antes de leer la información verifica que en efecto hay información disponible en el canal. Seguidamente hay una condición que se encarga de arreglar el formato para medidas de temperatura negativa (ya que si el sensor mide una temperatura de -1°C, envía un 128 lo que encendería el LED de manera errónea). Y finalmente hay un condicional que se encarga de verificar si la temperatura es menor a 30°C para saber si debe o no encender el LED

*   ### Esclavo:

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

Primero se declaran las librerías necesarias (Wire y DHT). La primera es para la comunicación I2C entre el esclavo y el maestro, y la segunda para el funcionamiento del sensor DHT11. Después se define el pin en donde va a estar conectado el sensor de temperatura y a través de la librería se asigna junto con el tipo de sensor. Finalmente se declara la variable de tipo float que se encargará de almacenar las lecturas de temperatura.

En el setup, se inicia la variable del sensor DHT, la comunicación I2C en el canal 8 y se especifica la función a través de la cual se van a realizar las peticiones. Esta función está encargada de enviar la temperatura como parámetro integer. Además también se inicia el serial para poder imprimir en la consola información relevante del código (como las lecturas de temperatura y un "send" cada que se activa el evento de la función "requestEvent()")

En el loop se actualiza cada 2 segundos la variable de la temperatura, esto porque la librería recomienda mínimo 2 segundos entre lecturas para los sensores DHT11 y DHT22.

## Video
https://youtu.be/e_LP6xS40O4
