# Actividad 5

## Programación de motor

Un actuador es un dispositivo que convierte una señal eléctrica en movimiento o acción física. En otras palabras, es el elemento que ejecuta lo que el sistema le indica.

---

En las actividades anteriores, el actuador fue un LED; en esta actividad se utilizó un motor, que convierte la energía eléctrica en movimiento rotatorio. El PWM regula su velocidad controlando la cantidad de energía que recibe, mientras que el Puente H permite cambiar la dirección del giro al invertir la dirección de la corriente.

![]https://anapaumen168.github.io/miportafolio_mecatronica/Introducci%C3%B3n%20a%20la%20Mecatr%C3%B3nica/imagenes/puente_h_sinvel.png

*Código*

```codigo
/*Control de 1 solo motor*/
#define in1 27
#define in2 14
#define pwm 12 //Definicion de pin de Velocidad

void setup() {
  /*Declarar Pines Como salida*/
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  /*Configuracion de pin PWM 
    - Se conecta al pin 12(pwm)
    - Frecuencia de 1Khz
    - Resolucion de 8 bit (0-255)
    - Canal 0
  */
  ledcAttachChannel(pwm, 1000, 8, 0);
}

void loop() {
  /*ADELANTE*/
  digitalWrite(in1, 0);
  digitalWrite(in2, 1);
  ledcWrite(pwm, 0);//Velocidad al 0%
  delay(500);
  ledcWrite(pwm, 51);//Velocidad al 20%
  delay(500);
  ledcWrite(pwm, 102);//Velocidad al 40%
  delay(500);
  ledcWrite(pwm, 153);//Velocidad al 60%
  delay(500);
  ledcWrite(pwm, 204);//Velocidad al 80%
  delay(500);
  ledcWrite(pwm, 255);//Velocidad al 100%
  delay(500);
}
```

## Resultados
