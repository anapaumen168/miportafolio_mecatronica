### Circuitos integrados


### *Definición*:
Un circuito integrado (IC) es un pequeño chip que contiene componentes electrónicos (como transistores y resistencias) interconectados para realizar una función específica.
Se usa para simplificar la implementación de los circuitos electrónicos en dispositivos como computadoras, celulares y controladores.

### *Funcionamiento*:
El primer acercamiento que se tuvo a los circuitos integrados fue a través del temporizador 555: Un IC que permite crear temporizadores y osciladores con dos comparadores, un flip-flop SR y un transistor de descarga. 

![](https://anapaumen168.github.io/miportafolio_mecatronica/Introducci%C3%B3n%20a%20la%20Mecatr%C3%B3nica/diagrama555.jpg)


Para entender el funcionamiento de este temporizador se construyó un oscilador que permitiera el encendido y apagado automático de un led cada 4 segundos.

El IC 555 opera en tres modos que permiten distintas funciones: 

- #### Astable:
El 555 genera un ciclo continuo de encendido y apagado. No necesita señal externa para dispararse; siempre cambia de estado solo.

- #### Monostable (pulso único):
El 555 genera un solo pulso de duración fija al recibir un disparo externo. Después del pulso, vuelve a su estado estable.

- #### Biestable (flip-flop):
El 555 actúa como interruptor: cambia de estado con cada señal de disparo. Permanece en ese estado hasta recibir otra señal.

### Actividad:
Se empleó el modo astable del temporizador 555 para generar el parpadeo cíclico de un LED. Con ayuda de una calculadora en línea se determinaron los valores de las resistencias y capacitores para lograr el encendido y apagado automático de un led aproximadamente cada 4 segundos.

[Calculadora para temporizador 555](https://www.digikey.com.mx/es/resources/conversion-calculators/conversion-calculator-555-timer?srsltid=AfmBOopbM2F4kBKWD8n8-fVGb5gEoQxKXo3YCXbVUPw4arBwxIQpEXOX)


Materiales utilizados:

- 1 × NE555 (DIP-8)
- 1 × LED
- 1 × Resistor para LED (330 Ω)
- 2 × Resistores temporizadores 
- 1 × Capacitor de temporización 
- 1 × Capacitor 10 nF 

Después de armar el cricuito, se conectó al osciloscopio para observar el parpadeo del led de manera gráfica a través de una señal cuadrada. Con esto se comprobó el funcionamiento correcto del circuito. 

<video width="600" controls>
  <source src="https://anapaumen168.github.io/miportafolio_mecatronica/Introducci%C3%B3n%20a%20la%20Mecatr%C3%B3nica/Video%20de%20WhatsApp%202025-10-16%20a%20las%2019.17.37_16eaf117.mp4" type="video/mp4">
  Tu navegador no soporta videos HTML5.
</video>

