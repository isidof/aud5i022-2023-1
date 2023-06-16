
PROYECTO FINAL

Estoy trabajando con [LinaHerrera01](https://github.com/linaherrera01)

Viernes 2 de Junio de 2023
EXPERIENCIA MUSICAL INTERACTIVA

PRIMEROS PASOS 

¿Es posible participar e influir en una proyección de sonido a tiempo real mediante un flex sensor?
En primera instancia, debemos ver qué softwares tenemos disponibles para realizar la 

SOFTWARES DISPONIBLES
ARDUINO IDE
PROCESSING

Efectos de sonido disponibles en que pueden ser útiles en este proyecto [Processing](https://processing.org/reference/libraries/sound/index.html):
[BandPass](https://processing.org/reference/libraries/sound/BandPass.html)
[Delay](https://processing.org/reference/libraries/sound/Delay.html)
[HighPass](https://processing.org/reference/libraries/sound/HighPass.html)
[LowPass](https://processing.org/reference/libraries/sound/LowPass.html)
[Reverb](https://processing.org/reference/libraries/sound/Reverb.html)


HERRAMIENTAS DISPONIBLES
2 kits de ARDUINO UNO
potenciómetro flex sensor
joystick PS4
micrófono acústico (conector de entrada Jack 6.3mm Mono)

Hicimos una pequeña investigación a modo de introducción para conocer más sobre el potenciómetro flex sensor, sobre su composición y posibles usos ya que es una herramienta que nunca habíamos utilizado antes. 

Potenciómetro flex sensor
Es un sensor de flexión o sensor de curvatura es un sensor de bajo coste diseñado específicamente para medir la cantidad de desviación o flexión gracias a una resistencia variable que fluctúa su resistencia al doblarse.

Los ejemplos de uso más comunes del potenciómetro lineal flexible serían en el área de la robótica, los videojuegos (movimiento virtual), dispositivos médicos, componentes externos de PC, instrumentos musicales, terapia física, etc.

([DigiKey](https://www.digikey.com/en/product-highlight/s/spectra-symbol/flex-sensor))
([MCIcapacitación](https://cursos.mcielectronics.cl/2022/12/27/interfaz-del-sensor-flex-con-arduino/))



Viernes 9 de Junio de 2023
Paralización de actividades

Viernes 16 de Junio de 2023
DESARROLLO DEL PROYECTO

En la página [MCI capacitación](https://cursos.mcielectronics.cl/2019/06/11/conectando-arduino-a-processing/) existe un tutorial que nos enseña cómo conectar ARDUINO con Processing de varias formas. Debido a que el flex sensor va conectado al ARDUINO mediante el protoboard, asumimos que el apartado de “Como enviar datos desde Arduino a Processing mediante un puerto serial” debería ser suficiente para realizar la primera parte del proyecto.

Enviar datos desde Arduino a Processing

A través del tutorial publicado, llegamos al siguiente código para conectar el ARDUINO a Processing, además de reconocer el sensor flex en el software.

Código ARDUINO 
const int flexPin = A0; // Pin analógico utilizado para el sensor flex
int flexValue; // Valor leído del sensor flex

void setup() {
  Serial.begin(9600); // Iniciar comunicación serie a 9600 baudios
}

void loop() {
  flexValue = analogRead(flexPin); // Leer el valor del sensor flex
  Serial.println(flexValue); // Enviar el valor por la comunicación serie
  delay(100); // Esperar un breve período antes de leer nuevamente
}


Recibir datos en Processing desde Arduino

En este caso, generamos un código para recibir los datos desde arduino para además utilizar los códigos predeterminados de processing para influencias en el audio emitido.

import processing.serial.*;
import processing.sound.*;

Serial arduino; // Objeto Serial para la comunicación con Arduino
AudioIn input; // Objeto de entrada de audio
Delay delay; // Objeto de efecto de retardo
Reverb reverb; // Objeto de efecto de reverberación

void setup() {
  size(400, 200);
  arduino = new Serial(this, Serial.list()[0], 9600); // Inicializar la comunicación con Arduino

  // Configuración de efectos de audio
  input = new AudioIn(this, 0);
  delay = new Delay(this);
  reverb = new Reverb(this);

  input.start();
  delay.process(input);
  reverb.process(input);

  // Establecer valores iniciales de los efectos
  delay.delayTime(0.5);
  reverb.amount(0.5);
}

void draw() {
  if (arduino.available() > 0) {
    String data = arduino.readStringUntil('\n'); // Leer datos de Arduino hasta el salto de línea
    if (data != null) {
      data = data.trim(); // Eliminar espacios en blanco al inicio y al final
      int flexValue = Integer.parseInt(data); // Convertir el valor a entero
      println("Flex value: " + flexValue);

      // Mapear el valor del sensor flex al rango de efectos de audio
      float effectValue = map(flexValue, 0, 1023, 0, 1); // Ajustar según tus necesidades

      // Aplicar los efectos de audio en función del valor del sensor flex
      audioEffect(effectValue);
    }
  }
}

void audioEffect(float value) {
  // Cambiar el efecto de retardo en función del valor del sensor flex
  float delayTime = map(value, 0, 1, 0.1, 1); // Mapear el valor del sensor flex al rango de tiempo de retardo
  delay.delayTime(delayTime);

  // Cambiar el efecto de reverberación en función del valor del sensor flex
  float reverbAmount = map(value, 0, 1, 0, 1); // Mapear el valor del sensor flex al rango de cantidad de reverberación
  reverb.amount(reverbAmount);
}



*
Paso a Paso
	1.- Antes de empezar el armado, debes contar con Arduino compatible para tu sistema operativo, también necesitarás la board de Arduino y una protoboard para realizar las conexiones.
	2.- Realizar conexión de 

//
TESTEO DE EMISIÓN DE SONIDO EN ARDUINO MEDIANTE UN PARLANTE
 En nuestra búsqueda para buscar las notas y melodías indicadas encontramos dos códigos de melodías de las cuales nos gustaría sacar los tonos para generar nuestra propia melodía.

Melodía 1: Mario Bros

// -------------------------------------------------
// Copyright (c) 2022 HiBit <https://www.hibit.dev>
// -------------------------------------------------

#include "pitches.h"

#define BUZZER_PIN 9

int melody[] = {
  NOTE_E5, NOTE_E5, REST, NOTE_E5, REST, NOTE_C5, NOTE_E5,
  NOTE_G5, REST, NOTE_G4, REST, 
  NOTE_C5, NOTE_G4, REST, NOTE_E4,
  NOTE_A4, NOTE_B4, NOTE_AS4, NOTE_A4,
  NOTE_G4, NOTE_E5, NOTE_G5, NOTE_A5, NOTE_F5, NOTE_G5,
  REST, NOTE_E5,NOTE_C5, NOTE_D5, NOTE_B4,
  NOTE_C5, NOTE_G4, REST, NOTE_E4,
  NOTE_A4, NOTE_B4, NOTE_AS4, NOTE_A4,
  NOTE_G4, NOTE_E5, NOTE_G5, NOTE_A5, NOTE_F5, NOTE_G5,
  REST, NOTE_E5,NOTE_C5, NOTE_D5, NOTE_B4,
  
  REST, NOTE_G5, NOTE_FS5, NOTE_F5, NOTE_DS5, NOTE_E5,
  REST, NOTE_GS4, NOTE_A4, NOTE_C4, REST, NOTE_A4, NOTE_C5, NOTE_D5,
  REST, NOTE_DS5, REST, NOTE_D5,
  NOTE_C5, REST,
  
  REST, NOTE_G5, NOTE_FS5, NOTE_F5, NOTE_DS5, NOTE_E5,
  REST, NOTE_GS4, NOTE_A4, NOTE_C4, REST, NOTE_A4, NOTE_C5, NOTE_D5,
  REST, NOTE_DS5, REST, NOTE_D5,
  NOTE_C5, REST,
  
  NOTE_C5, NOTE_C5, NOTE_C5, REST, NOTE_C5, NOTE_D5,
  NOTE_E5, NOTE_C5, NOTE_A4, NOTE_G4,
  
  NOTE_C5, NOTE_C5, NOTE_C5, REST, NOTE_C5, NOTE_D5, NOTE_E5,
  REST, 
  NOTE_C5, NOTE_C5, NOTE_C5, REST, NOTE_C5, NOTE_D5,
  NOTE_E5, NOTE_C5, NOTE_A4, NOTE_G4,
  NOTE_E5, NOTE_E5, REST, NOTE_E5, REST, NOTE_C5, NOTE_E5,
  NOTE_G5, REST, NOTE_G4, REST, 
  NOTE_C5, NOTE_G4, REST, NOTE_E4,
  
  NOTE_A4, NOTE_B4, NOTE_AS4, NOTE_A4,
  NOTE_G4, NOTE_E5, NOTE_G5, NOTE_A5, NOTE_F5, NOTE_G5,
  REST, NOTE_E5, NOTE_C5, NOTE_D5, NOTE_B4,
  
  NOTE_C5, NOTE_G4, REST, NOTE_E4,
  NOTE_A4, NOTE_B4, NOTE_AS4, NOTE_A4,
  NOTE_G4, NOTE_E5, NOTE_G5, NOTE_A5, NOTE_F5, NOTE_G5,
  REST, NOTE_E5, NOTE_C5, NOTE_D5, NOTE_B4,
  
  NOTE_E5, NOTE_C5, NOTE_G4, REST, NOTE_GS4,
  NOTE_A4, NOTE_F5, NOTE_F5, NOTE_A4,
  NOTE_D5, NOTE_A5, NOTE_A5, NOTE_A5, NOTE_G5, NOTE_F5,
  
  NOTE_E5, NOTE_C5, NOTE_A4, NOTE_G4,
  NOTE_E5, NOTE_C5, NOTE_G4, REST, NOTE_GS4,
  NOTE_A4, NOTE_F5, NOTE_F5, NOTE_A4,
  NOTE_B4, NOTE_F5, NOTE_F5, NOTE_F5, NOTE_E5, NOTE_D5,
  NOTE_C5, NOTE_E4, NOTE_E4, NOTE_C4,
  
  NOTE_E5, NOTE_C5, NOTE_G4, REST, NOTE_GS4,
  NOTE_A4, NOTE_F5, NOTE_F5, NOTE_A4,
  NOTE_D5, NOTE_A5, NOTE_A5, NOTE_A5, NOTE_G5, NOTE_F5,
  
  NOTE_E5, NOTE_C5, NOTE_A4, NOTE_G4,
  NOTE_E5, NOTE_C5, NOTE_G4, REST, NOTE_GS4,
  NOTE_A4, NOTE_F5, NOTE_F5, NOTE_A4,
  NOTE_B4, NOTE_F5, NOTE_F5, NOTE_F5, NOTE_E5, NOTE_D5,
  NOTE_C5, NOTE_E4, NOTE_E4, NOTE_C4,
  NOTE_C5, NOTE_C5, NOTE_C5, REST, NOTE_C5, NOTE_D5, NOTE_E5,
  REST,
  
  NOTE_C5, NOTE_C5, NOTE_C5, REST, NOTE_C5, NOTE_D5,
  NOTE_E5, NOTE_C5, NOTE_A4, NOTE_G4,
  NOTE_E5, NOTE_E5, REST, NOTE_E5, REST, NOTE_C5, NOTE_E5,
  NOTE_G5, REST, NOTE_G4, REST, 
  NOTE_E5, NOTE_C5, NOTE_G4, REST, NOTE_GS4,
  NOTE_A4, NOTE_F5, NOTE_F5, NOTE_A4,
  NOTE_D5, NOTE_A5, NOTE_A5, NOTE_A5, NOTE_G5, NOTE_F5,
  
  NOTE_E5, NOTE_C5, NOTE_A4, NOTE_G4,
  NOTE_E5, NOTE_C5, NOTE_G4, REST, NOTE_GS4,
  NOTE_A4, NOTE_F5, NOTE_F5, NOTE_A4,
  NOTE_B4, NOTE_F5, NOTE_F5, NOTE_F5, NOTE_E5, NOTE_D5,
  NOTE_C5, NOTE_E4, NOTE_E4, NOTE_C4,
  
  // Game over sound
  NOTE_C5, NOTE_G4, NOTE_E4,
  NOTE_A4, NOTE_B4, NOTE_A4, NOTE_GS4, NOTE_AS4, NOTE_GS4,
  NOTE_G4, NOTE_D4, NOTE_E4
};

int durations[] = {
  8, 8, 8, 8, 8, 8, 8,
  4, 4, 8, 4, 
  4, 8, 4, 4,
  4, 4, 8, 4,
  8, 8, 8, 4, 8, 8,
  8, 4,8, 8, 4,
  4, 8, 4, 4,
  4, 4, 8, 4,
  8, 8, 8, 4, 8, 8,
  8, 4,8, 8, 4,
  
  
  4, 8, 8, 8, 4, 8,
  8, 8, 8, 8, 8, 8, 8, 8,
  4, 4, 8, 4,
  2, 2,
  
  4, 8, 8, 8, 4, 8,
  8, 8, 8, 8, 8, 8, 8, 8,
  4, 4, 8, 4,
  2, 2,
  
  8, 4, 8, 8, 8, 4,
  8, 4, 8, 2,
  
  8, 4, 8, 8, 8, 8, 8,
  1, 
  8, 4, 8, 8, 8, 4,
  8, 4, 8, 2,
  8, 8, 8, 8, 8, 8, 4,
  4, 4, 4, 4, 
  4, 8, 4, 4,
  
  4, 4, 8, 4,
  8, 8, 8, 4, 8, 8,
  8, 4, 8, 8, 4,
  
  4, 8, 4, 4,
  4, 4, 8, 4,
  8, 8, 8, 4, 8, 8,
  8, 4, 8, 8, 4,
  
  8, 4, 8, 4, 4,
  8, 4, 8, 2,
  8, 8, 8, 8, 8, 8,
  
  8, 4, 8, 2,
  8, 4, 8, 4, 4,
  8, 4, 8, 2,
  8, 4, 8, 8, 8, 8,
  8, 4, 8, 2,
  
  8, 4, 8, 4, 4,
  8, 4, 8, 2,
  8, 8, 8, 8, 8, 8,
  
  8, 4, 8, 2,
  8, 4, 8, 4, 4,
  8, 4, 8, 2,
  8, 4, 8, 8, 8, 8,
  8, 4, 8, 2,
  8, 4, 8, 8, 8, 8, 8,
  1,
  
  8, 4, 8, 8, 8, 4,
  8, 4, 8, 2,
  8, 8, 8, 8, 8, 8, 4,
  4, 4, 4, 4, 
  8, 4, 8, 4, 4,
  8, 4, 8, 2,
  8, 8, 8, 8, 8, 8,
  
  8, 4, 8, 2,
  8, 4, 8, 4, 4,
  8, 4, 8, 2,
  8, 4, 8, 8, 8, 8,
  8, 4, 8, 2,
  
  //game over sound
  4, 4, 4,
  8, 8, 8, 8, 8, 8,
  8, 8, 2
};

void setup()
{
  pinMode(BUZZER_PIN, OUTPUT);
}

void loop()
{
  int size = sizeof(durations) / sizeof(int);

  for (int note = 0; note < size; note++) {
    //to calculate the note duration, take one second divided by the note type.
    //e.g. quarter note = 1000 / 4, eighth note = 1000/8, etc.
    int duration = 1000 / durations[note];
    tone(BUZZER_PIN, melody[note], duration);

    //to distinguish the notes, set a minimum time between them.
    //the note's duration + 30% seems to work well:
    int pauseBetweenNotes = duration * 1.30;
    delay(pauseBetweenNotes);
    
    //stop the tone playing:
    noTone(BUZZER_PIN);
  }
}





Melodía 2 : Pacman
// -------------------------------------------------
// Copyright (c) 2022 HiBit <https://www.hibit.dev>
// -------------------------------------------------

#include "pitches.h"

#define BUZZER_PIN 9

int melody[] = {
  NOTE_B4, NOTE_B5, NOTE_FS5, NOTE_DS5,
  NOTE_B5, NOTE_FS5, NOTE_DS5, NOTE_C5,
  NOTE_C6, NOTE_G6, NOTE_E6, NOTE_C6, NOTE_G6, NOTE_E6,
  
  NOTE_B4, NOTE_B5, NOTE_FS5, NOTE_DS5, NOTE_B5,
  NOTE_FS5, NOTE_DS5, NOTE_DS5, NOTE_E5, NOTE_F5,
  NOTE_F5, NOTE_FS5, NOTE_G5, NOTE_G5, NOTE_GS5, NOTE_A5, NOTE_B5
};

int durations[] = {
  16, 16, 16, 16,
  32, 16, 8, 16,
  16, 16, 16, 32, 16, 8,
  
  16, 16, 16, 16, 32,
  16, 8, 32, 32, 32,
  32, 32, 32, 32, 32, 16, 8
};

void setup()
{
  pinMode(BUZZER_PIN, OUTPUT);
}

void loop()
{
  int size = sizeof(durations) / sizeof(int);

  for (int note = 0; note < size; note++) {
    //to calculate the note duration, take one second divided by the note type.
    //e.g. quarter note = 1000 / 4, eighth note = 1000/8, etc.
    int duration = 1000 / durations[note];
    tone(BUZZER_PIN, melody[note], duration);

    //to distinguish the notes, set a minimum time between them.
    //the note's duration + 30% seems to work well:
    int pauseBetweenNotes = duration * 1.30;
    delay(pauseBetweenNotes);
    
    //stop the tone playing:
    noTone(BUZZER_PIN);
  }
}
//






REFERENCIAS

[ITP_NYC](https://vimeo.com/itpred)
[SumoZade](https://www.sumozade.com/es/product/el-potenciometro-lineal-flexible)
[ProcessingSound](https://processing.org/reference/libraries/sound/index.html)
[MCIcapacitación](https://cursos.mcielectronics.cl/2022/12/27/interfaz-del-sensor-flex-con-arduino/)
[MCIcapacitacion](https://cursos.mcielectronics.cl/2019/06/11/conectando-arduino-a-processing/)




