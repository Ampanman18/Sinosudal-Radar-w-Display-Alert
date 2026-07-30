# Sinosudal-Radar-w-Display-Alert
Utilizing an ultrasonic distance sensor, this project displays an objects distance away as the radar sweeps back and forth. Additionally, the LCD screen also displays the angle of the radar and includes an LED pin that flashes if an object is closer than 10cm, like an alarm.


/// pulling things out of arduino library
#include <LiquidCrystal.h>
#include <Servo.h>

/// initializing the LCD screen
LiquidCrystal lcd(10,11,7,6,5,4);

/// setting up variables
int servoPin = 3;
int trigPin = 8;
int echoPin = 9;
int alertPin = 13; 

int alertDistance = 20;
Servo radarServo;
int servoAngle = 0;
int stepDirection = 3;


/// screen and LED setup
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

/// ultrasonic distance sensor sends out signal to get distance that is then received to write on the LCD screen.
digitalWrite(trigPin, LOW);
delayMicroseconds(2);
digitalWrite(trigPin, HIGH);
delayMicroseconds(10);
digitalWrite(trigPin, LOW);


/// calculates the distance from how long the pulse took to send and recieve.
long duration = pulseIn(echoPin, HIGH);
long distance = duration * 0.034/2;

lcd.setCursor(0,0);
lcd.print("Angle: ");
lcd.print(servoAngle);
lcd.print(" deg   ");


lcd.setCursor(0,1);
lcd.print("Dist: ");


/// writes the distance on the LCD
if (distance > 0 && distance < 400) {
  lcd.print(distance);
  lcd.print(" cm   ");
} else {
  lcd.print("Out of Range");
}


///LED flashes when an object is closer than alert distance
if (distance > 0 && distance < alertDistance) {
  digitalWrite(alertPin, HIGH);
  lcd.print( "Danger");
} else {
  digitalWrite(alertPin, LOW);
}

/// sets up so that the radar moves as long as there is nothing in between distance of 0 and 20.
if (distance > 20 || distance <= 0 ) {
  servoAngle += stepDirection;
  if(servoAngle <= 0 || servoAngle>= 180) {
    stepDirection = -stepDirection;
  }
}

/// failsafe of the previous code

if (servoAngle >= 180) {
  servoAngle = 180;
  stepDirection = -3;
} else if(servoAngle <= 0) {
  servoAngle = 0;
  stepDirection = 3;
}

  delay(20);

}



