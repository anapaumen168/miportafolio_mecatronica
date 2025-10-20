# Actividad 3

## Led con Bluetooth

De igual forma, se programó el encendido y apagado de un LED mediante comunicación Bluetooth, permitiendo controlar su estado de forma inalámbrica desde un dispositivo externo.


```codigo
#include "BluetoothSerial.h"
BluetoothSerial SerialBT;

#define LED 23

void setup() {
    Serial.begin(115200);
    SerialBT.begin("ESP32");
    pinMode(LED, OUTPUT);
}

void loop() {
    if (SerialBT.available()) {
        String mensaje = SerialBT.readString();
        Serial.println("Recibido: " + mensaje);
        if (mensaje == "ON") {
            digitalWrite(LED, HIGH);
        } else if (mensaje == "OFF") {
            digitalWrite(LED, LOW);
        }
    }
    delay(1000);
}
```

### Resultados: 

<video width="600" controls>
  <source src="https://anapaumen168.github.io/miportafolio_mecatronica/Introducci%C3%B3n%20a%20la%20Mecatr%C3%B3nica/imagenes/Video%20de%20WhatsApp%202025-10-19%20a%20las%2018.27.54_b188989b.mp4" type="video/mp4">
  Tu navegador no soporta videos HTML5.
</video>
