
# PRACTICA 5 :  Buses de comunicación I (introducción y I2c)  

El objetivo de la practica es comprender el funcionamiento de los buses
  sistemas de comunicación entre periféricos;  estos elementos pueden 
  ser internos o externos  al procesador.

Esta es la primera practica de  4 donde se verán los buses i2c, spi,
 i2s , usart.

Para lo cual realizaremos una practica en cada caso donde controlaremos 
 un periférico de ejemplo

## Ejercicio Practico 1  ESCÁNER I2C

Programar el siguiente codigo,  colocar varios dispositivos  I2C 

```
En el caso de ESP32 por defecto:

| I2C Device | ESP32                    |
| ---------- | ------------------------ |
| SDA        | SDA (default is GPIO 21) |
| SCL        | SCL (default is GPIO 22) |
| GND        | GND                      |
| VCC        | usually 3.3V or 5V       |
```

## **Material**
* ESP32-Wroom-32.
* AHT10 Sensor de Temperatura y Humedad I2C.
* Sensor I2C BMP280.


# Código del programa
***
Las librerías añadidas son las siguientes:
```
#include <Arduino.h>
#include <Wire.h>

```
Se inicializa el bus I2C, la comunicación serie e imprime un mensaje indicando el inicio del escaneo I2C.

```
void setup()
{
  Wire.begin();
  Serial.begin(115200);
  while (!Serial);             // Leonardo: wait for serial monitor
  Serial.println("\nI2C Scanner");
}
```
Imprime un mensaje indicando que se está escaneando e Inicializa el contador de dispositivos encontrados a 0.
```
void loop() {
  byte error, address;  
  int nDevices;  
  Serial.println("Scanning...");  

  nDevices = 0;   
```
Itera a través de todas las direcciones I2C posibles e Inicia una transmisión I2C a la dirección especificada.
```
 
  for(address = 1; address < 127; address++ ) {   
    Wire.beginTransmission(address);  
    error = Wire.endTransmission(); 
```
Si encuentra un dispositivo, manda un mensaje donde imprime la dirección del dispositivo en formato hexadecimal. Si no, imprime un mensaje de error o la dirección del error en formato hexadecimal
```
    if (error == 0) { 
      Serial.print("I2C device found at address 0x");  
      if (address < 16) 
        Serial.print("0");  
      Serial.print(address, HEX);  
      Serial.println(" !");  
      nDevices++; 
    } else if (error == 4) { 
      Serial.print("Unknown error at address 0x");  
      if (address < 16) 
        Serial.print("0"); 
      Serial.println(address, HEX);  
    }
  }
```
Si no se encontraron dispositivos I2C  imprime un mensaje indicando que no se encontraron dispositivos. Si se encontraron dispositivos, imprime "done"
```

  if (nDevices == 0) 
    Serial.println("No I2C devices found\n");  
  else 
    Serial.println("done\n");  
  
  delay(5000);
}

```

# Salida
En caso de encontrar dispositivos la salida puede ser la siguiente:
```
I2C Scanner
Scanning...
I2C device found at address 0x3C  !
I2C device found at address 0x68  !
done
```
En caso de no encontrar, sería distinta:
```
No I2C devices found
done
```