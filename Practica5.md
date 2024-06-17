
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



El código utiliza el ESP32-S3 para leer datos de temperatura y humedad del sensor y los muestra en una pantalla LCD I2C. En la función setup(), el código inicializa la comunicación serie, el LCD y el sensor AHT. Si el sensor no se encuentra, el programa se detiene con un mensaje de error.

En la función loop(), se obtienen los datos de temperatura y humedad del sensor, que se actualizan en la pantalla LCD cada segundo.

```
#include <Arduino.h>
//YWROBOT
//Compatible with the Arduino IDE 1.0
//Library version:1.1
#include <LiquidCrystal_I2C.h>

#include <Adafruit_AHTX0.h>

Adafruit_AHTX0 aht;
LiquidCrystal_I2C lcd(0x27,20,4);  // set the LCD address to 0x27 for a 16 chars and 2 line display

void setup()
{
  Serial.begin(115200);
  lcd.init();                      // initialize the lcd 
  Serial.println("Adafruit AHT10/AHT20 demo!");

  if (! aht.begin()) {
    Serial.println("Could not find AHT? Check wiring");
    while (1) delay(10);
  }
  Serial.println("AHT10 or AHT20 found");
}





void loop() {
  sensors_event_t humidity, temp;
  aht.getEvent(&humidity, &temp);// populate temp and humidity objects with fresh data
    // Print a message to the LCD.
  lcd.backlight();
  lcd.setCursor(1,0);
  lcd.print("Temperatura: "); 
  lcd.setCursor(3,1);
  lcd.print(temp.temperature); 
  lcd.println(" grados");
  lcd.setCursor(1,2);
  lcd.print("Humedad: "); 
  lcd.setCursor(3,3);
  lcd.print(humidity.relative_humidity); 
  lcd.println("%");
  delay (1000);
}
```

