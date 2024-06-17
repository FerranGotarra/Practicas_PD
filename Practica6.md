# PRACTICA 6
## EJERCICIO 1: Lectura/escritura memoria SD
Consiste en conectar la ESP32 con un lector de tarjetas MicroSD. En esta tarjeta hemos añadido un archivo. 


Este programa utiliza el ESP32-S3 para leer datos de una tarjeta SD a través de la interfaz SPI, y mostrar el contenido de un archivo específico en el monitor serie. El código inicializa la comunicación SPI con pines personalizados y maneja la tarjeta SD para abrir y leer un archivo llamado "archivo.txt".

```
#include <SPI.h>
#include <SD.h>

#define SD_CS 4  
#define SD_CLK 6  
#define SD_MOSI 5  
#define SD_MISO 7  

File myFile;

void setup() {
  Serial.begin(115200);
  SPI.begin(SD_CLK, SD_MISO, SD_MOSI); // Iniciamos el Bus SPI con pines personalizados
  Serial.print("Iniciando SD ...");
  if (!SD.begin(SD_CS, SPI)) {
    Serial.println("No se pudo inicializar");
    return;
  }
  Serial.println("inicializacion exitosa");
  myFile = SD.open("/archivo.txt"); // Abrimos el archivo
  if (myFile) {
    Serial.println("archivo.txt:");
    while (myFile.available()) {
      Serial.write(myFile.read());
    }
    myFile.close(); // Cerramos el archivo
  } else {
    Serial.println("Error al abrir el archivo");
  }
}

void loop() {
  // ...
}
```

## Salida

La tarjeta SD trata de inicializarse, si no lo consigue, muestra por pantalla lo siguiente:
```
Iniciando SD ...
No se pudo inicializar
```

Si consigue inicializarse pero no consigue abrir el archivo se muestra lo siguiente:

```
Iniciando SD ...
No se pudo inicializar
```

Finalmente, si consigue hacer todo, se mostrará lo siguiente

```
Iniciando SD ...
inicializacion exitosa
archivo.txt:
*El contenido del archivo*
```


## EJERCICIO 2: Lectura RFID

El código inicializa la comunicación serie, el bus SPI con pines personalizados, y el módulo MFRC522. El código revisa continuamente si hay una nueva tarjeta RFID presente usando mfrc522.PICC_IsNewCardPresent(). Si una nueva tarjeta es detectada, el código lee el UID de la tarjeta con mfrc522.PICC_ReadCardSerial(). Una vez leído el UID, el código lo imprime en el monitor serie en formato hexadecimal.

```
#include <SPI.h>
#include "MFRC522.h"
#define RST_PIN 9 //Pin 9 para el reset del RC522
#define SS_PIN 10 //Pin 10 para el SS (SDA) del RC522
MFRC522 mfrc522(SS_PIN, RST_PIN); //Creamos el objeto para el RC522
void setup() {
Serial.begin(115200); //Iniciamos la comunicación serial
SPI.begin(18,35,37); //Iniciamos el Bus SPI
 // void begin(int8_t sck=-1, int8_t miso=-1, int8_t mosi=-1, int8_t ss=-1);
mfrc522.PCD_Init(); // Iniciamos el MFRC522
Serial.println("Lectura del UID");
}
void loop() {
// Revisamos si hay nuevas tarjetas presentes
if ( mfrc522.PICC_IsNewCardPresent()) 
 { 
 //Seleccionamos una tarjeta
 if ( mfrc522.PICC_ReadCardSerial()) 
 {
 // Enviamos serialemente su UID
 Serial.print("Card UID:");
 for (int i = 0; i < mfrc522.uid.size; i++) {
  Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0": " ");
  Serial.print(mfrc522.uid.uidByte[i], HEX); 
 } 
 Serial.println();
 // Terminamos la lectura de la tarjeta actual
 mfrc522.PICC_HaltA(); 
 } 
}
}

```


## Salida

Una posible salida puede ser:

```
Lectura del UID
Card UID: 04 D2 4F 1B
```