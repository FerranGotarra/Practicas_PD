# Informe del Código Arduino

## Descripción del Código
El siguiente código Arduino lee un valor analógico de un pin, lo imprime por el puerto serie y enciende un LED durante un segundo. Luego repite el proceso.

## Código

```cpp
#include <Arduino.h>

#define ANALOG_PIN 34 // Definir el pin analógico que se va a leer
#define LED_FLASH 23

void setup() {
  Serial.begin(115200); // Iniciar la comunicación serie a 115200 baudios
  pinMode(ANALOG_PIN, INPUT); // Configurar el pin analógico como entrada
  pinMode(LED_FLASH, OUTPUT);
}

void loop() {
  int sensorValue = analogRead(ANALOG_PIN); // Leer el valor del pin analógico
  Serial.println(sensorValue); // Enviar el valor leído al puerto serie
  digitalWrite(LED_FLASH,HIGH);
  delay(1000); // Esperar un segundo antes de la próxima lectura
  Serial.println(sensorValue); // Enviar el valor leído al puerto serie
  digitalWrite(LED_FLASH,LOW);
  delay(1000);

```

mermaid ```
graph TD;
    A[Inicio] --> B[Lectura de Valor Analógico];
    B --> C[Impresión por Puerto Serie];
    C --> D[Encendido de LED];
    D --> E[Esperar 1 segundo];
    E --> B;

```
mermaid ```
sequenceDiagram
    participant Arduino
    participant Sensor
    participant LED
    participant Serial

    Note over Arduino, Sensor: Setup
    Arduino->>Sensor: analogRead(ANALOG_PIN)
    Sensor-->>Arduino: sensorValue
    Arduino->>Serial: Serial.println(sensorValue)
    Note over Arduino, LED: Encendido
    Arduino->>LED: digitalWrite(LED_FLASH,HIGH)
    LED-->>Arduino: Encendido
    Arduino->>Serial: Serial.println(sensorValue)
    Arduino-->>Arduino: Esperar 1 segundo
    Note over Arduino, LED: Apagado
    Arduino->>LED: digitalWrite(LED_FLASH,LOW)
    LED-->>Arduino: Apagado
    
    ```