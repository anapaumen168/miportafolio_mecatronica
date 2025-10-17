# Introduccion a los microcontroladores 

### Definición

Un microcontrolador procesa los datos que recibe a través de sus periféricos de entrada y salida para llevar a cabo una función específica de un dispositivo. Usa el código archivado en su memoria y procesador interno para interpretar la información y ejecutar la acción correspondiente.

### Actividad

El primer acercamiento a los microcontroladores fue a través de la tarjeta ESP32: Un microcontrolador potente con conectividad Wi-Fi y Bluetooth.

#### Programación

Para escribir, compilar y cargar programas al microcontrolador, se utiliza un IDE (Integrated Development Environment). Es un software que reúne en un solo lugar todas las herramientas necesarias para programar.

Para programar el parpadeo de un led con una ESP32 se uitilizó este código en Arduino IDE.

```codigo
//Revisar donde esta cableado el LED
#define LED 23

void setup() {
    pinMode(LED, OUTPUT); // Configura el pin del LED como salida
}

void loop() {
    digitalWrite(LED, HIGH); // Enciende el LED
    delay(1000); // Espera 1 segundo
    digitalWrite(LED, LOW); // Apaga el LED
    delay(1000); // Espera 1 segundo
}
```

### Evidencia