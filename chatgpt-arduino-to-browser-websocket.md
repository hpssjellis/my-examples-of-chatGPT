## Write two code examples one for the arduino microcontroller and the other for javascript in the browser that lets the two devices communicate using websockets to turn an LED on and off on the micrcontroller.
Here is an example of code that can be used to control an LED using websockets on an Arduino microcontroller:

Copy code

```
#include <SPI.h>
#include <Ethernet.h>
#include <WebSocketsServer.h>

const int ledPin = 13;

byte mac[] = { 0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED };
IPAddress ip(192, 168, 1, 177);
WebSocketsServer webSocket = WebSocketsServer(81);

void setup() {
  Serial.begin(9600);
  pinMode(ledPin, OUTPUT);
  Ethernet.begin(mac, ip);
  webSocket.begin();
  webSocket.onEvent(webSocketEvent);
}

void loop() {
  webSocket.loop();
}

void webSocketEvent(uint8_t num, WStype_t type, uint8_t * payload, size_t length) {
  switch(type) {
    case WStype_DISCONNECTED:
      Serial.println("Client disconnected");
      break;
    case WStype_CONNECTED:
      Serial.println("Client connected");
      break;
    case WStype_TEXT:
      if (strcmp((char*)payload, "on") == 0) {
        digitalWrite(ledPin, HIGH);
        webSocket.sendTXT(num, "LED turned on");
      } else if (strcmp((char*)payload, "off") == 0) {
        digitalWrite(ledPin, LOW);
        webSocket.sendTXT(num, "LED turned off");
      }
      break;
  }
}

```
And here is an example of code that can be used in a JavaScript in a web browser to connect to the Arduino microcontroller and send commands to turn the LED on and off:

Copy code

```
const socket = new WebSocket('ws://192.168.1.177:81/');

socket.onopen = function() {
  console.log('Connected to server');
  socket.send('on');
};

socket.onmessage = function(event) {
  console.log(event.data);
};

function turnOff() {
  socket.send('off');
}

```
To use these examples, you will need to install the appropriate libraries for Ethernet and websockets on the Arduino microcontroller. You will also need to modify the IP address in both examples to match the IP address of your Arduino microcontroller.
