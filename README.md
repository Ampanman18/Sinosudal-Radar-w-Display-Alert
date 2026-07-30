# Sinosudal-Radar-w-Display-Alert
Utilizing an ultrasonic distance sensor, this project displays an objects distance away as the radar sweeps back and forth. Additionally, the LCD screen also displays the angle of the radar and includes an LED pin that flashes if an object is closer than 10cm, like an alarm.





#include <LiquidCrystal.h>
#include <Servo.h>
LiquidCrystal lcd(10,11,7,6,5,4);
 
int servoPin = 3;
int trigPin = 8;
int echoPin = 9;
int alertPin = 13; 

int alertDistance = 10;
Servo radarServo;
int servoAngle = 0;
int stepDirection = 2;

void setup() {
  // put your setup code here, to run once:
  lcd.begin(16,2);
  radarServo.attach(3);

  pinMode(trigPin,OUTPUT);
  pinMode(echoPin,INPUT);
  pinMode(alertPin, OUTPUT);

  lcd.setCursor(0,0);
  lcd.print("Scanner Ready");
  delay(1000);
  lcd.clear();
}

void loop() {
  // put your main code here, to run repeatedly:
radarServo.write(servoAngle);
delay(15);

digitalWrite(trigPin, LOW);
delayMicroseconds(2);
digitalWrite(trigPin, HIGH);
delayMicroseconds(10);
digitalWrite(trigPin, LOW);

long duration = pulseIn(echoPin, HIGH);
long distance = duration * 0.034/2;

lcd.setCursor(0,0);
lcd.print("Angle: ");
lcd.print(servoAngle);
lcd.print(" deg   ");


lcd.setCursor(0,1);
lcd.print("Dist: ");

if (distance > 0 && distance < 400) {
  lcd.print(distance);
  lcd.print(" cm   ");
} else {
  lcd.print("Out of Range");
}

if (distance > 0 && distance < alertDistance) {
  digitalWrite(alertPin, HIGH);
  lcd.print( "Danger");
} else {
  digitalWrite(alertPin, LOW);
}

servoAngle += stepDirection;

if (servoAngle >= 180) {
  servoAngle = 180;
  stepDirection = -2;
} else if(servoAngle <= 0) {
  servoAngle = 0;
  stepDirection = 2;
}

  delay(20);

}
