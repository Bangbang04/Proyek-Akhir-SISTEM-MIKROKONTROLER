# Proyek-Akhir-SISTEM-MIKROKONTROLER
<P>Source Code</P>
#define BLYNK_TEMPLATE_ID "TMPL67jGWndRb"
#define BLYNK_TEMPLATE_NAME "Smart Irrigation"
#define BLYNK_AUTH_TOKEN "Ret4CBK9Zj8P-zuAeUwHkDrvtQ-sToEK"

#include <WiFi.h>
#include <WiFiClient.h>
#include <BlynkSimpleEsp32.h>
#include <ESP32Servo.h>

char ssid[] = "Wokwi-GUEST";
char pass[] = "";

#define SOIL_PIN 34
#define RELAY_PIN 26
#define SERVO_PIN 25
#define LED_GREEN 18
#define LED_RED 19
#define LED_BLUE 23

Servo valveServo;
BlynkTimer timer;

bool manualMode = false;

void sendSensor() {

  int adc = analogRead(SOIL_PIN);

  int moisture = map(adc,4095,0,0,100);

  Blynk.virtualWrite(V0, moisture);

  Serial.print("ADC : ");
  Serial.print(adc);
  Serial.print(" Moisture : ");
  Serial.println(moisture);

  if(!manualMode){

    if(moisture < 40){

      digitalWrite(RELAY_PIN,HIGH);

      digitalWrite(LED_RED,HIGH);

      digitalWrite(LED_GREEN,LOW);

      digitalWrite(LED_BLUE,HIGH);

      valveServo.write(90);

      Blynk.virtualWrite(V1,"POMPA ON");

      Blynk.virtualWrite(V3,255);

    }

    else{

      digitalWrite(RELAY_PIN,LOW);

      digitalWrite(LED_RED,LOW);

      digitalWrite(LED_GREEN,HIGH);

      digitalWrite(LED_BLUE,LOW);

      valveServo.write(0);

      Blynk.virtualWrite(V1,"POMPA OFF");

      Blynk.virtualWrite(V3,0);

    }

  }

}

BLYNK_WRITE(V2){

  int value=param.asInt();

  if(value==1){

    manualMode=true;

    digitalWrite(RELAY_PIN,HIGH);

    digitalWrite(LED_RED,HIGH);

    digitalWrite(LED_GREEN,LOW);

    digitalWrite(LED_BLUE,HIGH);

    valveServo.write(90);

    Blynk.virtualWrite(V1,"MANUAL ON");

    Blynk.virtualWrite(V3,255);

  }

  else{

    manualMode=false;

    digitalWrite(RELAY_PIN,LOW);

    digitalWrite(LED_RED,LOW);

    digitalWrite(LED_GREEN,HIGH);

    digitalWrite(LED_BLUE,LOW);

    valveServo.write(0);

    Blynk.virtualWrite(V1,"AUTO MODE");

    Blynk.virtualWrite(V3,0);

  }

}

void setup(){

  Serial.begin(115200);

  pinMode(RELAY_PIN,OUTPUT);

  pinMode(LED_GREEN,OUTPUT);

  pinMode(LED_RED,OUTPUT);

  pinMode(LED_BLUE,OUTPUT);

  digitalWrite(RELAY_PIN,LOW);

  digitalWrite(LED_GREEN,HIGH);

  digitalWrite(LED_RED,LOW);

  digitalWrite(LED_BLUE,LOW);

  valveServo.setPeriodHertz(50);

  valveServo.attach(SERVO_PIN,500,2400);

  valveServo.write(0);

  Blynk.begin(BLYNK_AUTH_TOKEN,ssid,pass);

  timer.setInterval(1000L,sendSensor);

}

void loop(){

  Blynk.run();

  timer.run();

}

