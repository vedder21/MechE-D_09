#include <Servo.h>
#include <NewPing.h>

//Wheel Motors
    const int LEFT_MOTORS_SP = 5;
    const int RIGHT_MOTORS_SP = 6;
    const int LEFT_MOTOR_DIR = 8;
    const int RIGHT_MOTOR_DIR = 7;
    const int MOTOR_OUTPUT = 3;

    int starterSpeed = 50; //starting value for speed of motors is between 0-255

//Global counter to turn off mobot for the rest of the demonstration
    int globalcounter = 0;
//height variable to control what height to bring the motor -> 1 for lower; 2 for upper
    int height = 0;


//Servo Motor
    const int SERVO = 10; 
    Servo servo; 

//Ultrasonic Sensor
    const int TRIGGER_PIN = 13; 
    const int ECHO_PIN = 12; 
    NewPing sonar(TRIGGER_PIN, ECHO_PIN, 30);

//STOP WHEEL MOTORS
    void off (){
      digitalWrite(MOTOR_OUTPUT, LOW); //LOW turns off output to motors
    }

//STRAIGHT FORWARD - WHEEL MOTORS
    void forward (int forwardSpeed) {
      digitalWrite(MOTOR_OUTPUT, HIGH); // HIGH turns on output to motors
      digitalWrite(LEFT_MOTOR_DIR, HIGH); // HIGH sets direction to forward (LOW sets direction to reverse)
      digitalWrite(RIGHT_MOTOR_DIR, HIGH);
      analogWrite(LEFT_MOTORS_SP, forwardSpeed);  // speed should be more than zero (range is 0-255)
      analogWrite(RIGHT_MOTORS_SP, forwardSpeed+11);
    }

//FRONT PIVOTS TO THE RIGHT - WHEEL MOTORS
    void right (int leftSpeed, int rightSpeed) {  // front of mobot turns right and back turns left
      digitalWrite(MOTOR_OUTPUT, HIGH); //HIGH turns on output to motors
      digitalWrite(LEFT_MOTOR_DIR, HIGH); //HIGH sets direction to forward (LOW sets direction to reverse)
      digitalWrite(RIGHT_MOTOR_DIR, LOW);
      analogWrite(LEFT_MOTORS_SP, leftSpeed);  //speed should be more than zero (range is 0-255)
      analogWrite(RIGHT_MOTORS_SP, rightSpeed);
    }

//FRONT PIVOTS TO THE LEFT - WHEEL MOTORS
    void left (int leftSpeed, int rightSpeed) { //front of mobot turns left and back turns right
      digitalWrite(MOTOR_OUTPUT, HIGH); // HIGH turns on output to motors
      digitalWrite(LEFT_MOTOR_DIR, LOW); // HIGH sets direction to forward (LOW sets direction to reverse)
      digitalWrite(RIGHT_MOTOR_DIR, HIGH);
      analogWrite(LEFT_MOTORS_SP, leftSpeed);  // speed should be more than zero (range is 0-255)
      analogWrite(RIGHT_MOTORS_SP, rightSpeed);
    }

//STRAIGHT BACKWARD - WHEEL MOTORS
    void back (int backSpeed) {
      digitalWrite(MOTOR_OUTPUT, HIGH); // HIGH turns on output to motors
      digitalWrite(LEFT_MOTOR_DIR, LOW); // HIGH sets direction to forward (LOW sets direction to reverse)
      digitalWrite(RIGHT_MOTOR_DIR, LOW);
      analogWrite(LEFT_MOTORS_SP, backSpeed);  // speed should be more than zero (range is 0-255)
      analogWrite(RIGHT_MOTORS_SP, backSpeed);
    }

//DRIVING TO DOCK 1
    void dock1(){
      forward(50); //aribitrary for calibration -> go to infront of loading dock
      delay(3000);
      left(starterSpeed,startSpeed); //align claws with the box
      delay(1500);
      forward(50); //get close to box
      delay(1000)
    }
//DRIVING TO DOCK 2
    void dock2(){
      forward(50); //aribitrary for calibration -> go to infront of loading dock
      delay(6000);
      left(starterSpeed,startSpeed); //align claws with the box
      delay(1500);
      forward(50); //get close to box
      delay(1000)
    }
//DOCK 1 -> LOADING DOCK
  void load1(){
        forward(50);
        delay(250);
        left(50);
        delay(250);
  }
//DOCK 2 -> LOADING DOCK
  void load2(){
          forward(50);
          delay(250);
          left(50);
          delay(250);
  }
//LIFTING FOR LOWER SHELF
    void lower(){
      servo.write(120);
      delay(500);
      servo.write(40);
      delay(250);
      forward(50);
      delay(250);
      back(50);
      delay(250);
      right(50);
      delay(250);
    }

//LIFTING FOR UPPER SHELF
   void upper(){
      servo.write(180);
      delay(1500);
      servo.write(40);
      delay(250);
      forward(50);
      delay(250);
      back(50);
      delay(250);
      right(50);
      delay(250);
    }

//PUTTING BOX DOWN
    void unloading(){
      servo.write(180); //pretend this is for lowering the elevator
      delay(250);
      back(50); //back out of loading zone
      delay(250);
      left(50); //line up to drive back to starting area
      delay(250);
    }

void setup() {
  Serial.begin(9600);
  servo.attach(SERVO);
  servo.write(0);
}
void loop() {
  //debugger variables:
      int currentDistance = sonar.ping_cm();
      //Serial.print("current distance is")
      //Serial.println(currentDistance);

  //Idea for the loop:
  int shelf = 1; //This will control what cases we call

  switch(shelf){
    case = 1: //loading dock 1
      if(height = 0){
        dock1();
        lower();
        load1();
        unloading();
        forward(50);
        delay(5000);
        globalcounter++;
        while(globalcounter >= 1){
          off();
        }
      } else if(height == 1){
        dock1();
        upper();
        load1();
        unloading();
        forward(50);
        delay(5000);
        globalcounter++;
        while(globalcounter >= 1){
          off();
        }
      } else{
        Serial.println("Fix your code")
      }
      }
      break;

    case = 2: // loading dock 2
        if(height == 1){
          dock2();
          lower();
          load2();
          unloading();
          forward(50);
          delay(5000);
        globalcounter++;
        while(globalcounter >= 1){
          off();
        }
      } else if(height == 1){
          dock2();
          upper();
          load2();
          unloading();
          forward(50);
          delay(5000);
        globalcounter++;
        while(globalcounter >= 1){
          off();
        }
      } else{
          Serial.println("Fix your code")
      }
      break;
  }
  //-Initialize starting position
  //-2 Cases: Set each case for height and if it's being performed
  //  -2 different variables for each case (if it's the one in interest)
  //  -Height variable is something aribritary to each case
  //  -Each case:
  //    Hard code driving to the dock based on initialized starting position
  //    Lifting motor up to level with divot
  //    -How far up it goes is determined by the height variable (height is either 2in or 9 in)
  //    -Two if statements for each height dependent on value of height
  //    Driving forward to be in the divot
  //    Lifting motor up to hook onto package
  //    Drive back
  //    Drive to loading
  //    Drive back
    
      
}
