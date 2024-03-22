## PRACTICA A
<div align="justify">

Usamos una función llamada attachInterrupt() para establecer una interrupción en base a un pin por pin. "button1.PIN" es la patilla de interrupción, isr es el nombre de la función a la que se llama y FALLING es el modo epscificado que es cuando el pin va de HIGH a LOW.
```cpp
attachInterrupt(button1.PIN, isr, FALLING);
```
La función que llamamos cuenta los golpes que ha sido apretado el botón. En el loop tenemos un condicional que muestra por pantalla el número de veces que ha sido apretado el botón.
```cpp
if (button1.pressed) {
Serial.printf("Button 1 has been pressed %u times\n", button1.numberKeyPresses);
button1.pressed = false;
}
```

En ese mismo código también hay una interrupción por Timer (evento programado). Es decir, la interrupción tiene lugar después de un tiempo programado.

En ese caso después de un minuto se desactivan las interrupciones del pin. Es decir nos aparece por pantalla "Interrupt Detached!".
```cpp
//Detach Interrupt after 1 Minute
static uint32_t lastMillis = 0;
if (millis() - lastMillis > 60000) {
lastMillis = millis();
detachInterrupt(button1.PIN);
Serial.println("Interrupt Detached!");
}
```


<div>

## PRACTICA B

Ahora tenemos un bucle que detecta cada vez quién ha habido una interrupción y va mostrando por pantalla cuántas interrupciones lleva. Las interrupciones ocurren periódicamente sin fin según un timer previamente programado. El código es el siguiente:

```cpp
volatile int interruptCounter;
int totalInterruptCounter;
hw_timer_t * timer = NULL;
portMUX_TYPE timerMux = portMUX_INITIALIZER_UNLOCKED;
void IRAM_ATTR onTimer() {

portENTER_CRITICAL_ISR(&timerMux);
interruptCounter++;
portEXIT_CRITICAL_ISR(&timerMux);
}
void setup() {
Serial.begin(115200);
timer = timerBegin(0, 80, true);
timerAttachInterrupt(timer, &onTimer, true);
timerAlarmWrite(timer, 1000000, true);
timerAlarmEnable(timer);
}
void loop() {
if (interruptCounter > 0) {
portENTER_CRITICAL(&timerMux);
interruptCounter--;
portEXIT_CRITICAL(&timerMux);
totalInterruptCounter++;
Serial.print("An interrupt as occurred. Total number: ");
Serial.println(totalInterruptCounter);
}
}
```
