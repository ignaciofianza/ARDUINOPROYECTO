# Sistema de Control de Riego

Este proyecto tiene como objetivo automatizar el riego de un sistema de cultivos utilizando sensores de humedad y relés para controlar las bombas de agua. El sistema monitorea los niveles de humedad en el suelo y activa o desactiva las bombas en función de los valores medidos. Además, los datos de humedad se muestran en una pantalla LCD y se envían por el puerto serial para su monitoreo.

## Componentes

- **Arduino (o compatible)**: Para gestionar la lectura de sensores y controlar los relés.
- **Sensores de humedad**: Se utilizan 4 sensores para medir la humedad del suelo en diferentes áreas.
- **Relés**: Controlan las bombas de agua.
- **Pantalla LCD I2C**: Muestra el estado de las bombas.
- **Cables de conexión y otros accesorios**: Para conectar todos los componentes.

## Esquema de conexión

- **Sensores de humedad**: Conectados a los pines analógicos `A0`, `A1`, `A2` y `A3` del Arduino.
- **Relés**: Controlan las bombas y están conectados a los pines digitales `8`, `9`, `10` y `11`.
- **Pantalla LCD**: Conectada mediante la interfaz I2C a los pines `SDA` y `SCL` del Arduino.

## Funcionamiento

El sistema lee continuamente los valores de humedad de los sensores. Si la humedad es inferior al umbral definido (500), se activa la bomba correspondiente. Cada bomba está controlada por un relé y su estado (encendida o apagada) se muestra en la pantalla LCD. Los valores de humedad y los estados de las bombas también se envían por el puerto serial para su monitoreo.

### Pantalla LCD

- **B1, B2, B3, B4**: Indicadores del estado de las bombas. Si el valor está por debajo del umbral, la bomba se activa, mostrando "ON". Si está por encima, se apaga, mostrando "OFF".

### Puerto Serial

Los valores de humedad de los sensores se envían por el puerto serial para su visualización en el monitor serial de Arduino IDE.

## Código

```cpp
#include <Wire.h>                 // biblioteca para comunicación I2C
#include <LiquidCrystal_I2C.h>    // biblioteca para la pantalla LCD I2C

// pines de los sensores de humedad
const int sensorHumedad1 = A0;
const int sensorHumedad2 = A1;
const int sensorHumedad3 = A2;
const int sensorHumedad4 = A3;

// pines de los relés que controlan las bombas
const int rele1 = 8;
const int rele2 = 9;
const int rele3 = 10;
const int rele4 = 11;

int umbralHumedad = 500;  // umbral de humedad para activar las bombas

// crea el objeto para la pantalla LCD
LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup() {
  // inicializa la comunicación I2C
  Wire.begin();  
  
  // configura los pines de los relés como salidas
  pinMode(rele1, OUTPUT);
  pinMode(rele2, OUTPUT);
  pinMode(rele3, OUTPUT);
  pinMode(rele4, OUTPUT);

  // inicializa los relés apagados
  digitalWrite(rele1, LOW);
  digitalWrite(rele2, LOW);
  digitalWrite(rele3, LOW);
  digitalWrite(rele4, LOW);

  // inicializa la pantalla LCD
  lcd.init();
  lcd.backlight();
  
  // inicia la comunicación serial para monitorear los datos
  Serial.begin(9600);
}

void loop() {
  // leer humedad de los sensores
  int humedad1 = analogRead(sensorHumedad1);
  int humedad2 = analogRead(sensorHumedad2);
  int humedad3 = analogRead(sensorHumedad3);
  int humedad4 = analogRead(sensorHumedad4);

  // Limpia la pantalla antes de actualizar los valores
  lcd.clear();

  // controla la bomba 1
  if (humedad1 < umbralHumedad) {
    digitalWrite(rele1, HIGH);  // activa el relé 1
    lcd.setCursor(0, 0);
    lcd.print("B1: ON  ");
    Serial.println("Bomba 1: ENCIENDIDA");  // envía estado al puerto serial
  } else {
    digitalWrite(rele1, LOW);   // apaga el relé 1
    lcd.setCursor(0, 0);
    lcd.print("B1: OFF ");
    Serial.println("Bomba 1: APAGADA");  // envía estado al puerto serial
  }

  // controla la bomba 2
  if (humedad2 < umbralHumedad) {
    digitalWrite(rele2, HIGH);  // activa el relé 2
    lcd.setCursor(7, 0);
    lcd.print("B2: ON  ");
    Serial.println("Bomba 2: ENCIENDIDA");  // envía estado al puerto serial
  } else {
    digitalWrite(rele2, LOW);   // apaga el relé 2
    lcd.setCursor(7, 0);
    lcd.print("B2: OFF ");
    Serial.println("Bomba 2: APAGADA");  // envía estado al puerto serial
  }

  // controla la bomba 3
  if (humedad3 < umbralHumedad) {
    digitalWrite(rele3, HIGH);  // activa el relé 3
    lcd.setCursor(0, 1);
    lcd.print("B3: ON  ");
    Serial.println("Bomba 3: ENCIENDIDA");  // envía estado al puerto serial
  } else {
    digitalWrite(rele3, LOW);   // apaga el relé 3
    lcd.setCursor(0, 1);
    lcd.print("B3: OFF ");
    Serial.println("Bomba 3: APAGADA");  // envía estado al puerto serial
  }

  // controla la bomba 4
  if (humedad4 < umbralHumedad) {
    digitalWrite(rele4, HIGH);  // activa el relé 4
    lcd.setCursor(7, 1);
    lcd.print("B4: ON  ");
    Serial.println("Bomba 4: ENCIENDIDA");  // envía estado al puerto serial
  } else {
    digitalWrite(rele4, LOW);   // apaga el relé 4
    lcd.setCursor(7, 1);
    lcd.print("B4: OFF ");
    Serial.println("Bomba 4: APAGADA");  // envía estado al puerto serial
  }

  // envía los valores de humedad por el puerto serial para monitoreo
  Serial.print("Humedad 1: ");
  Serial.println(humedad1);
  Serial.print("Humedad 2: ");
  Serial.println(humedad2);
  Serial.print("Humedad 3: ");
  Serial.println(humedad3);
  Serial.print("Humedad 4: ");
  Serial.println(humedad4);
  
  delay(1000);  // espera 1 segundo antes de la próxima lectura
}
