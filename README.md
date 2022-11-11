
//DS= [D]ata [S]torage - data
//STCP= [ST]orage [C]lock [P]in latch
//SHCP= [SH]ift register [C]lock [P]in clock

const int latchPin = 11; // STCP to 12 on Shift Register
const int clockPin = 10; // SHCP to 11 on Shift Register
const int dataPin = 12; // DS to 14 on Shift Register

const int segD1 = 7;
const int segD2 = 6;
const int segD3 = 5;
const int segD4 = 4;

const byte regSize = 8; // 1 byte aka 8 bits

int displayDigits[] = {
  segD1, segD2, segD3, segD4
};

const int encodingsNumber = 16;

int byteEncodings[encodingsNumber] = {
//A B C D E F G DP 
  B11111100, // 0 
  B01100000, // 1
  B11011010, // 2
  B11110010, // 3
  B01100110, // 4
  B10110110, // 5
  B10111110, // 6
  B11100000, // 7
  B11111110, // 8
  B11110110, // 9
  B11101110, // A
  B00111110, // b
  B10011100, // C
  B01111010, // d
  B10011110, // E
  B10001110  // F
};

int encodingArray[encodingsNumber][regSize] = {
  {1, 1, 1, 1, 1, 1, 0, 0},  // 0 B11111100
  {0, 1, 1, 0, 0, 0, 0, 0},  // 1
  {1, 1, 0, 1, 1, 0, 1, 0},  // 2
  {1, 1, 1, 1, 0, 0, 1, 0},  // 3
  {0, 1, 1, 0, 0, 1, 1, 0},  // 4
  {1, 0, 1, 1, 0, 1, 1, 0},  // 5
  {1, 0, 1, 1, 1, 1, 1, 0},  // 6
  {1, 1, 1, 0, 0, 0, 0, 0},  // 7
  {1, 1, 1, 1, 1, 1, 1, 0},  // 8
  {1, 1, 1, 1, 0, 1, 1, 0},  // 9
  {1, 1, 1, 0, 1, 1, 1, 0},  // A
  {0, 0, 1, 1, 1, 1, 1, 0},  // b
  {1, 0, 0, 1, 1, 1, 0, 0},  // C
  {0, 1, 1, 1, 1, 0, 1, 0},  // d
  {1, 0, 0, 1, 1, 1, 1, 0},  // E
  {1, 0, 0, 0, 1, 1, 1, 0},  // F
};

const int displayCount = 4;

int registers[regSize];

void setup() {
  // put your setup code here, to run once:
  pinMode(latchPin, OUTPUT);
  pinMode(clockPin, OUTPUT);
  pinMode(dataPin, OUTPUT);

  for (int i = 0; i < displayCount; i++) {
    pinMode(displayDigits[i], OUTPUT);
    digitalWrite(displayDigits[i], LOW);
  }
  Serial.begin(9600);
}

int index = 0;

void loop() {
  // activateDisplay(1);
  // writeReg(byteEncodings[index]);
  // index++;
  // if (index == 16) {
  //   index = 0;
  // }
  
  // delay(300);
  // for (int i = regSize - 1; i >= 0; i--) {
  //   registers[i] = LOW;
  //   writeReg(registers);
  //   delay(100);
  //   Serial.print(registers[i]);
  // }
  // Serial.println();
  writeNumber(1234);
  // for (int i = 0; i < regSize; i++) {
  //   registers[i] = HIGH;
  //   writeReg(registers);
  //   delay(100);
  //   Serial.print(registers[i]);
  // }
  // Serial.println();
}
void activateDisplay(int digit) {
  for(int i=0; i<4; i++) {
    digitalWrite(displayDigits[i], HIGH);
  }

  digitalWrite(displayDigits[digit], LOW);
}
void writeReg(int encoding) {
  digitalWrite(latchPin, LOW);
  shiftOut(dataPin, clockPin, MSBFIRST, encoding);
  digitalWrite(latchPin, HIGH);
  
}

void writeNumber(int number) {
  int currentNumber = number % 10000;
  int displayIndex = 0;
  int currentDigit = 0;
  while (currentNumber > 0) {
    currentDigit = currentNumber % 10;
    currentNumber /= 10;
    writeReg(byteEncodings[currentDigit]);
    activateDisplay(displayIndex);
    displayIndex ++;
    delay(5);
  }
}
