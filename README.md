# Tamrin

#include <Keypad.h>

const byte ROWS = 4;
const byte COLS = 4;

char keys[ROWS][COLS] = {
  {'1','2','3','+'},
  {'4','5','6','-'},
  {'7','8','9','*'},
  {'C','0','=','/'}
};

byte rowPins[ROWS] = {9, 8, 7, 6};
byte colPins[COLS] = {5, 4, 3, 2};

Keypad kpd = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

String num1 = "";
String num2 = "";
char op = 0;
bool secondNumber = false;

void setup() {
  Serial.begin(9600);
  Serial.println("Calculator Ready...");
}

void loop() {
  char key = kpd.getKey();

  if (key) {

    // پاک کردن همه چیز
    if (key == 'C') {
      num1 = "";
      num2 = "";
      op = 0;
      secondNumber = false;
      Serial.println("Clear");
      return;
    }

    // اگر کلید عملگر بود
    if (key == '+'  key == '-'  key == '*' || key == '/') {
      if (num1.length() > 0) {
        op = key;
        secondNumber = true;
        Serial.print("Operator: ");
        Serial.println(op);
      }
      return;
    }

    // اگر کلید = بود → محاسبه
    if (key == '=') {
      if (num1.length() > 0 && num2.length() > 0 && op != 0) {
        float a = num1.toFloat();
        float b = num2.toFloat();
        float result = 0;

        switch(op) {
          case '+': result = a + b; break;
          case '-': result = a - b; break;
          case '*': result = a * b; break;
          case '/': 
            if (b != 0) result = a / b;
            else {
              Serial.println("Error: Division by 0");
              return;
            }
            break;
        }

        Serial.print("Result: ");
        Serial.println(result);

        // بعد از محاسبه آماده ورودی جدید می‌شود
        num1 = String(result);
        num2 = "";
        op = 0;
        secondNumber = false;
      }
      return;
    }

    // اگر کلید عدد بود → به ورودی اضافه کن
    if (key >= '0' && key <= '9') {
      if (!secondNumber) {
        num1 += key;
        Serial.print("Num1: ");
        Serial.println(num1);
      } else {
        num2 += key;
        Serial.print("Num2: ");
        Serial.println(num2);
      }
    }
  }
}
