#include <Servo.h>
#include <Stepper.h>
#include <IRremote.hpp>

// servos
Servo myservo1;
Servo myservo2;
const int S2HOME = 170;        // define home for satellite dish servo
const int S2TARGET = 10;    // set target to move ~180° from home (not starting or stopping on servo limits to prolong servo life)

const int S1HOME = 5;        // define home for hatch servo
const int S1TARGET = 120;   // set target to move 115° from home to open hatch

const int STEP_DELAY = 25; // set stepper speed delay

const int stepsPerRevolution = 2048;  
Stepper myStepper(stepsPerRevolution, 8, 10, 9, 11); // assign stepper motor variable and its driver inputs

// millis vars for flashing light
unsigned long previousLightTime = 0;
const long lightInterval = 1000; // 1 second
bool lightState = false;

// millis vars for rotating dish
int pos = S2HOME;
unsigned long previousServoTime = 0;
int direction = -1;
const long servoInterval = 25;

// assign servos, LED, IR Rec inputs and stepper speed 
void setup() {
  pinMode(12,OUTPUT);
  myservo2.attach(2);
  myservo1.attach(1);
  myStepper.setSpeed(10);
  IrReceiver.begin(5);
}

void loop() {
  unsigned long currentMillis = millis();

  // flash light without delay using millis function
  if (currentMillis - previousLightTime >= lightInterval) {
    previousLightTime = currentMillis;
    lightState = !lightState;
    digitalWrite(12, lightState ? HIGH : LOW);  // write LED HIGH or LOW depending on lightState
  }

  // rotate dish without delay using millis funciton
  if (currentMillis - previousServoTime >= servoInterval) {
    previousServoTime = currentMillis;
    pos += direction;   
    myservo2.write(pos);  // rotate 1°

    // when target is reached direction is flipped 
    if (pos <= S2TARGET || pos >= S2HOME) {
      direction *= -1;
    }
  }

  if (IrReceiver.decode()) {
    unsigned long code = IrReceiver.decodedIRData.decodedRawData;   grab code from IR remote

    // open panels if 1 is pressed
    if (code == 0xF30CFF00) {
      myStepper.step(stepsPerRevolution * 1.75);
    }

    // retract panels if 2 is pressed
    else if (code == 0xE718FF00) {
      myStepper.step(-stepsPerRevolution * 1.75);
    }

    // open hatch if 4 is pressed
    else if (code == 0xF708FF00) {
      for (int pos = S1HOME; pos <= S1TARGET; pos++) {
        myservo1.write(pos);
        delay(STEP_DELAY);
      }
    }

    // close hatch if 5 is pressed
    else if (code == 0xE31CFF00) {
      for (int pos = S1TARGET; pos >= S1HOME; pos--) {
        myservo1.write(pos);
        delay(STEP_DELAY);
      }
    }

    else if (code == 0xC40BD817) {
      while(true);
    }

  IrReceiver.resume();  // wait for next signal

  }
} 
