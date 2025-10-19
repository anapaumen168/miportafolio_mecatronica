# Actividad 1


## Parpadeo con Botón

De igual forma, se programó el parpadeo de un LED en función del estado de un botón, el cual, según su posición, permite o interrumpe el paso de corriente.

### Código: 

```codigo
//Revisar donde esta cableado el botón y el LED
#define LED 23
#define BUTTON 33

void setup() {
    pinMode(LED, OUTPUT);
    pinMode(BUTTON, INPUT);
}

void loop() {
    if (digitalRead(BUTTON) == HIGH) {
        digitalWrite(LED, HIGH);
    } else {
        digitalWrite(LED, LOW);
    }
}
```


### Resultados: