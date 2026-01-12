# 10-Second-Stopwatch-Game
#include <LiquidCrystal.h>
void draw_bobby(int x, bool legs);
void update_bobby();
void print_time(int time);
void update_time();
int read_button();
void stop_time();

const int rs = 12, en = 11, d4 = 5, d5 = 4, d6 = 3, d7 = 2;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

const int joystickSWPin = 8;

byte eyes[8] = {0b01110, 
                0b10001, 
                0b00000, 
                0b00000,
                0b00000, 
                0b00000, 
                0b00000, 
                0b00000};

byte mouth[8] = {0b11111, 
                 0b10001, 
                 0b10001, 
                 0b10001,
                 0b01110, 
                 0b00000, 
                 0b00000, 
                 0b00000};

byte backslash[8] = {0b10000, 
                     0b01000, 
                     0b00100, 
                     0b00010,
                     0b00001, 
                     0b00000, 
                     0b00000, 
                     0b00000};
byte forwardslash[8] = {0b00001, 
                        0b00010, 
                        0b00100, 
                        0b01000,
                        0b10000, 
                        0b00000, 
                        0b00000, 
                        0b00000};
byte legl[8] = {0b00001, 
                0b00001, 
                0b00001, 
                0b00001,
                0b00001, 
                0b00000, 
                0b00000, 
                0b00000};         
byte legr[8] = {0b10000, 
                0b10000, 
                0b10000, 
                0b10000,
                0b10000, 
                0b00000, 
                0b00000, 
                0b00000};         

int bobbyX = 0;
int bobbyDirection = 1;
bool legs = false;
unsigned long prevTime = 0;
int countTime = 0;
int bobbyUpdateTime = 400;
unsigned long prevTimeBobby = 0;
int joystickPin = 8;

void setup() {
  lcd.begin (16,2);
  lcd.createChar(0, eyes);
  lcd.createChar(1, backslash);
  lcd.createChar(2, mouth);
  lcd.createChar(3, legl);
  lcd.createChar(4, legr);
  lcd.createChar(5, forwardslash);
  pinMode(joystickPin, INPUT_PULLUP);
    
  
}

void draw_bobby(int x, bool legs){
  
  if (legs == false){
    lcd.setCursor(x,0);
    lcd.print("(");
    lcd.write(byte(0));
    lcd.write(byte(2));
    lcd.write(byte(0));
    lcd.write(")");
    lcd.setCursor(x,1);
    lcd.write(byte(3));
    lcd.print(" ");
    lcd.print(" ");
    lcd.print(" ");
    lcd.write(byte(4));
  }
  else{
    lcd.setCursor(x,0);
    lcd.print("(");
    lcd.write(byte(0));
    lcd.write(byte(2));
    lcd.write(byte(0));
    lcd.write(")");
    lcd.setCursor(x,1);
    lcd.write(byte(5));
    lcd.print(" ");
    lcd.print(" ");
    lcd.print(" ");
    lcd.write(byte(1));
  }
}

void update_bobby(){
  unsigned long currentTimeBobby = millis();
  if (currentTimeBobby - prevTimeBobby < bobbyUpdateTime){
    return;
  }
  prevTimeBobby = currentTimeBobby;
  bobbyX = bobbyX + 1 * bobbyDirection;

  if (bobbyX > 5){
    bobbyDirection = -1;
  }
  if (bobbyX < 1){
    bobbyDirection = 1;
  }
  legs = !legs;

}

void print_time(int time){
  if (time == 1500){
    lcd.setCursor(11, 0);
    lcd.print("15.00");
  }
  else if (time == 1000){
    lcd.setCursor(11, 0);
    lcd.print("10.00");
  }
  else{
    if (time > 1000){
      lcd.setCursor(11, 0);
    }
    else{
      lcd.setCursor(12, 0);
    }
    lcd.print(time / 100);
    lcd.print(".");
    lcd.print(time % 100);
  }
}

void update_time(){
  unsigned long currentTime = millis();

  if (currentTime - prevTime >= 10){
    countTime += 1;
    prevTime = currentTime;
  }

  if (countTime == 1501){
    delay(1000);
    countTime = 0;
  }
}

int read_button(){
  int res = digitalRead(joystickPin);
  return res;
}

void stop_time(){
  lcd.setCursor(11, 1);
  if (countTime < 1000){
    lcd.print("Early");
  }
  else if (countTime == 1000){
    lcd.print("Win!");
  }
  else if (countTime > 1000){
    lcd.print("Late");
  }
  delay(3000);
  countTime = 0;
  return;
}


void loop() {
  update_time();
  update_bobby();
  lcd.clear();
  draw_bobby(bobbyX,legs);
  print_time(countTime);
  if (read_button() == LOW){
    stop_time();
  }
  delay(10);

}
