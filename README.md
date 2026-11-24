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

//distance variables
      int currentDistance1 = 0; //back ultrasonic sensor

//Servo Motor
    const int SERVO = 10; 
    Servo servo; 

//Back Ultrasonic Sensor 
    const int TRIGGER_PIN = 13; 
    const int ECHO_PIN = 12; 
    NewPing sonar(TRIGGER_PIN, ECHO_PIN, 60);

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
      currentDistance1 = sonar.ping_cm();
      while(currentDistance1 <= 15 && currentDistance1 > 0){ //aribitrary for calibration -> go to infront of loading dock
        currentDistance1 = sonar.ping_cm();
        forward(starterSpeed);
      }
      left(starterSpeed,starterSpeed); //align claws with the box
      delay(1500);
      currentDistance1 = sonar.ping_cm();
      while(currentDistance1 <= 10 && currentDistance1 > 0){ //aribitrary for calibration -> go to right in front of box
        currentDistance1 = sonar.ping_cm();
        forward(starterSpeed);
      }
    }
//DRIVING TO DOCK 2
    void dock2(){
      currentDistance1 = sonar.ping_cm();
      while(currentDistance1 <= 40 && currentDistance1 > 0){ //aribitrary for calibration -> go to infront of loading dock
        currentDistance1 = sonar.ping_cm();
        forward(starterSpeed);
      }
      left(starterSpeed,starterSpeed); //align claws with the box
      delay(1500);
      currentDistance1 = sonar.ping_cm();
      while(currentDistance1 <= 10 && currentDistance1 > 0){ //aribitrary for calibration -> go to right in front of box
        currentDistance1 = sonar.ping_cm();
        forward(starterSpeed);
      }
    }

//LIFTING FOR LOWER SHELF
    void lower(){
      servo.write(120);
      delay(500);
      forward(starterSpeed); //hook onto package
      delay(250);
      servo.write(40);
      delay(250);
      currentDistance1 = sonar.ping_cm();
      while(currentDistance1 <= 10 && currentDistance1 > 0){ //aribitrary for calibration -> go out of dock
        currentDistance1 = sonar.ping_cm();
        back(starterSpeed);
      }
      right(starterSpeed,starterSpeed); //face going towards loading
      delay(250);
    }

//LIFTING FOR UPPER SHELF
   void upper(){
      servo.write(180);
      delay(250);
      forward(starterSpeed); //hook onto package
      delay(250);
      servo.write(40);
      delay(250);
      currentDistance1 = sonar.ping_cm();
      while(currentDistance1 <= 10 && currentDistance1 > 0){ //aribitrary for calibration -> go out of dock
        currentDistance1 = sonar.ping_cm();
        back(50);
      }
      right(starterSpeed,starterSpeed); //face going towards loading
      delay(250);
    }

//DOCK 1/2 -> LOADING DOCK
  void load(){
      currentDistance1 = sonar.ping_cm();
      while(currentDistance1 <= 30 && currentDistance1 > 0){ //aribitrary for calibration -> go from either dock to aligned with loading dock
        currentDistance1 = sonar.ping_cm();
        forward(starterSpeed);
      }
      delay(250);
      left(starterSpeed,starterSpeed); //face the box
      delay(750); 
  }
//PUTTING BOX DOWN
    void unloading(){
      servo.write(0);
      delay(250);
      currentDistance1 = sonar.ping_cm();
      while(currentDistance1 <= 10 && currentDistance1 > 0){ //aribitrary for calibration -> go to infront of loading dock
        currentDistance1 = sonar.ping_cm();
        back(starterSpeed);
      }
      delay(250);
      left(starterSpeed,starterSpeed); //line up to drive back to starting area
      delay(250);
      currentDistance1 = sonar.ping_cm();
      while(currentDistance1 <= 30 && currentDistance1 > 0){ //aribitrary for calibration -> go back to starting area
        currentDistance1 = sonar.ping_cm();
        forward(starterSpeed);
      }
    }

void setup() {
  off();
  Serial.begin(9600);
  servo.attach(SERVO);
  servo.write(0);
}
void loop() {
  //debugger variables:
      currentDistance1 = sonar.ping_cm(); //back ultrasonic sensor
      //Serial.print("current distance is")
      //Serial.println(currentDistance1);

  //IMPORTANT FOR SWITCHING SHELVES
  int shelf = 1; //This will control what cases we call -> 1 for dock 1; 2 for dock 2
  //CONTROL HEIGHT OF PACKAGE -> 1 for lower; 2 for upper
    int height = 0;

  switch(shelf){
    case 1: //loading dock 1
      if(height == 0){
        dock1();
        lower();
        load();
        unloading();
        globalcounter++;
        while(globalcounter >= 1){
          off();
        }
      } else if(height == 1){
        dock1();
        upper();
        load();
        unloading();
        globalcounter++;
        while(globalcounter >= 1){
          off();
        }
      } else{
        Serial.println("Fix your code");
      }
      break;

    case 2: // loading dock 2
        if(height == 0){
          dock2();
          lower();
          load();
          unloading();
        globalcounter++;
        while(globalcounter >= 1){
          off();
        }
      } else if(height == 1){
          dock2();
          upper();
          load();
          unloading();
        globalcounter++;
        while(globalcounter >= 1){
          off();
        }
      } else{
          Serial.println("Fix your code");
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
