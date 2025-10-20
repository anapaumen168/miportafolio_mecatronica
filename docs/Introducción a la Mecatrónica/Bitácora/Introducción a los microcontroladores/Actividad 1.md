# Actividad 1

## Parpadeo de led. 

Para poner en práctica la programación de los microcontroladores se realizaron distintas actividades. La primera fue programar el encendido y apagado de un led repetidamente.

```codigo
//Revisar en que pin está el LED
#define LED 2

void setup() {
    pinMode(LED, OUTPUT); // Configurar el pin del LED como salida
}

void loop() {
    digitalWrite(LED, HIGH); // Enciende el LED
    delay(1000); // Espera 1 segundo
    digitalWrite(LED, LOW); // Apaga el LED
    delay(2000); // Espera 2 segundos
        digitalWrite(LED, HIGH); // Enciende el LED
    delay(500); // Espera 0.5 segundos
    digitalWrite(LED, LOW); // Apaga el LED
    delay(100); // Espera 0.1 segundos
        digitalWrite(LED, HIGH); // Enciende el LED
    delay(1000); // Espera 1 segundo
    digitalWrite(LED, LOW); // Apaga el LED
    delay(2000); // Espera 2 segundos

    
}
```

## Resultados