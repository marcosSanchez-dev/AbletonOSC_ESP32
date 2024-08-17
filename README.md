# Proyecto de Control de LEDs con WiFi y OSC

Este proyecto utiliza un microcontrolador para controlar una tira de LEDs NeoPixel, recibir datos de sensores a través de un puerto serial y enviar mensajes OSC (Open Sound Control) a un dispositivo remoto, como una computadora que ejecuta Ableton Live.

## Requisitos

- Microcontrolador compatible con WiFi (por ejemplo, ESP32)
- Tira de LEDs NeoPixel
- Sensores de distancia
- Conexión WiFi
- Software para recibir mensajes OSC (por ejemplo, Ableton Live)

## Librerías Utilizadas

- `Adafruit_NeoPixel`: Para controlar la tira de LEDs.
- `WiFi`: Para conectar el microcontrolador a una red WiFi.
- `WiFiUdp`: Para enviar mensajes UDP.
- `OSCMessage`: Para construir y enviar mensajes OSC.

## Configuración de Hardware

1. Conecta la tira de LEDs NeoPixel al pin GPIO 25 del microcontrolador.
2. Conecta los sensores de distancia a los pines RX2 (16) y TX2 (17) del microcontrolador.
3. Asegúrate de que el microcontrolador esté conectado a la misma red WiFi que la computadora que recibirá los mensajes OSC.

## Conexión a la Red WiFi

El microcontrolador se conecta a la red WiFi utilizando el SSID y la contraseña proporcionados:

```cpp
const char* ssid = "oceguedas";
const char* password = "amoryeppi";

WiFi.begin(ssid, password);
while (WiFi.status() != WL_CONNECTED) {
  delay(1000);
  Serial.println("Conectando a WiFi...");
}
Serial.println("Conectado a la WiFi");


### Parte 2

```markdown
## Recepción de Datos del Sensor

El microcontrolador lee los datos de los sensores de distancia a través del puerto serial 2. Los datos recibidos incluyen tres valores de distancia y un comando:

```cpp
if (Serial2.available() >= 4) {
  long distance1 = Serial2.read();
  long distance2 = Serial2.read();
  long distance3 = Serial2.read();
  char command = Serial2.read();
}


### Parte 3

```markdown
## Manejo de Comandos para Mezcla de Colores

El comando recibido del puerto serial se utiliza para controlar los colores de la tira de LEDs. Dependiendo del valor del comando, se pueden activar diferentes combinaciones de colores (rojo, verde y azul). El código maneja tres casos principales:

1. Un sensor activo: Se muestra un color sólido.
2. Dos sensores activos: Se realiza una interpolación de colores entre los dos sensores activos.
3. Tres sensores activos: Se realiza una mezcla de colores más compleja.

```cpp
if (command != lastCommand) {
  lastCommand = command;
  
  uint32_t color1 = 0, color2 = 0, color3 = 0;
  int activeSensors = 0;

  if (command & 0b001) {
    color1 = pixels.Color(255, 0, 0);
    activeSensors++;
  }
  if (command & 0b010) {
    color2 = pixels.Color(0, 255, 0);
    activeSensors++;
  }
  if (command & 0b100) {
    color3 = pixels.Color(0, 0, 255);
    activeSensors++;
  }

  // Lógica de mezcla de colores aquí...

  pixels.show();
}
