# Sistema-domotico
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT11 // Cambia a DHT22 si usas ese modelo
DHT dht(DHTPIN, DHTTYPE);

// Pines de los LEDs
const int led1 = 8;
const int led2 = 9;
const int led3 = 10;

// Variables para millis()
unsigned long tiempoAnterior = 0;
const long intervalo = 2000; // 2 segundos

void setup() {
  Serial.begin(9600);
  dht.begin();
  
  pinMode(led1, OUTPUT);
  pinMode(led2, OUTPUT);
  pinMode(led3, OUTPUT);
}

void loop() {
  unsigned long tiempoActual = millis();

  // 1. Tarea: Leer sensor cada 2 segundos sin detener el loop
  if (tiempoActual - tiempoAnterior >= intervalo) {
    tiempoAnterior = tiempoActual;
    float h = dht.readHumidity();
    float t = dht.readTemperature();

    if (!isnan(h) && !isnan(t)) {
      // Formato: T:25.50,H:60.00
      Serial.print("T:");
      Serial.print(t);
      Serial.print(",H:");
      Serial.println(h);
    }
  }

  // 2. Tarea: Escuchar comandos constantemente
  if (Serial.available() > 0) {
    char comando = Serial.read();
    
    // Asignación de comandos:
    // LED 1: 'A' (encender), 'a' (apagar)
    // LED 2: 'B' (encender), 'b' (apagar)
    // LED 3: 'C' (encender), 'c' (apagar)
    switch(comando) {
      case 'A': digitalWrite(led1, HIGH); break;
      case 'a': digitalWrite(led1, LOW); break;
      case 'B': digitalWrite(led2, HIGH); break;
      case 'b': digitalWrite(led2, LOW); break;
      case 'C': digitalWrite(led3, HIGH); break;
      case 'c': digitalWrite(led3, LOW); break;
    }
  }
}
