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

 ![](https://anapaumen168.github.io/miportafolio_mecatronica/Introducci%C3%B3n%20a%20la%20Mecatr%C3%B3nica/blink_wiring.png)

## Resultados

 ![]
 <video width="600" controls>
  <source src="https://anapaumen168.github.io/miportafolio_mecatronica/Introducci%C3%B3n%20a%20la%20Mecatr%C3%B3nica/imagenes/Video%20de%20WhatsApp%202025-10-19%20a%20las%2018.14.20_1a95dd57.mp4" type="video/mp4">
  Tu navegador no soporta videos HTML5.
</video>

