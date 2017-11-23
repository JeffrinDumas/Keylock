# Keylock

include <Keypad.h>

const byte ROWS = 4; //four rows
const byte COLS = 4; //four columns

int idlePin = 11;
int goodPin = 10;
int badPin = 12; 

String curCode = "";
String correctCode = "0000";
bool correctCodeSet = true;

char keys[ROWS][COLS] = {
  {'1','2','3','A'},
  {'4','5','6','B'},
  {'7','8','9','C'},
  {'*','0','#','D'}
};

byte rowPins[ROWS] = {5, 4, 3, 2}; //connect to the row pinouts of the keypad
byte colPins[COLS] = {9, 8, 7, 6}; //connect to the column pinouts of the keypad

Keypad keypad = Keypad( makeKeymap(keys), rowPins, colPins, ROWS, COLS );

void setup(){
   Serial.begin(9600);
   pinMode(idlePin, OUTPUT);
   pinMode(goodPin, OUTPUT);
   pinMode(badPin, OUTPUT);

   digitalWrite(idlePin, HIGH);
   digitalWrite(goodPin, LOW);
   digitalWrite(badPin, LOW);

   Serial.println("We are currently using the default code. Please change by entering the code: A431B and end with #");
}
  
void loop(){
  char key = keypad.getKey();
  
  if(!correctCodeSet && key) {
    if(key == '#') {
      correctCodeSet = true;
      setGood();
      Serial.println("New code is " + correctCode);
      Serial.println("To set a new code enter the code A431B and end with #");
    } else {
      correctCode += key;
    }
  } else if(key) {

    if(key == '#') {
      if(curCode == "A431B") {
        setGood();
        curCode = "";
        correctCode = "";
        correctCodeSet = false;
        Serial.println("Set a new code and end with #!");
      } else if(curCode == correctCode) {
        Serial.println("Correct code entered");
        setGood();
        curCode = "";
      } else {
        Serial.println("Bad code entered");
        setBad();
        curCode = "";
      }
    } else {
      curCode += key;
    }
  }
}

void setGood() {
  digitalWrite(idlePin, LOW);
  digitalWrite(goodPin, HIGH);
  delay(1000);
  digitalWrite(goodPin, LOW);
  digitalWrite(idlePin, HIGH);
}


void setBad() {
  digitalWrite(idlePin, LOW);
  digitalWrite(badPin, HIGH);
  delay(1000);
  digitalWrite(badPin, LOW);
  digitalWrite(idlePin, HIGH);
}
