# memory_game.ino
const int leds[5] = {2, 3, 4, 5, 6};
const int buttons[5] = {7, 8, 9, 10, 11};
const int buzzer = 12;

int sequence[8];

void setup() {
  for (int i = 0; i < 5; i++) {
    pinMode(leds[i], OUTPUT);
    pinMode(buttons[i], INPUT_PULLUP);
  }

  pinMode(buzzer, OUTPUT);

  randomSeed(analogRead(A0));
}

void loop() {
  playRound(3, 700);  // الراوند 1

  successSignal();

  playRound(5, 500);  // الراوند 2

  successSignal();

  playRound(8, 300);  // الراوند 3

  winnerSignal();

  delay(2000);
}

void playRound(int length, int speedDelay) {

  for (int i = 0; i < length; i++) {
    sequence[i] = random(0, 5);
  }

  delay(1000);

  // عرض التسلسل
  for (int i = 0; i < length; i++) {
    digitalWrite(leds[sequence[i]], HIGH);
    tone(buzzer, 1000);
    delay(speedDelay);

    digitalWrite(leds[sequence[i]], LOW);
    noTone(buzzer);
    delay(200);
  }

  // انتظار إدخال اللاعب
  for (int i = 0; i < length; i++) {

    int pressed = -1;

    while (pressed == -1) {
      for (int j = 0; j < 5; j++) {
        if (digitalRead(buttons[j]) == LOW) {
          pressed = j;

          digitalWrite(leds[j], HIGH);
          tone(buzzer, 1200);
          delay(200);
          digitalWrite(leds[j], LOW);
          noTone(buzzer);

          while (digitalRead(buttons[j]) == LOW);
        }
      }
    }

    if (pressed != sequence[i]) {
      failSignal();
      return;
    }
  }
}

void successSignal() {
  for (int i = 0; i < 5; i++) {
    digitalWrite(leds[i], HIGH);
  }

  tone(buzzer, 1500);
  delay(1000);

  for (int i = 0; i < 5; i++) {
    digitalWrite(leds[i], LOW);
  }

  noTone(buzzer);
}

void failSignal() {
  for (int k = 0; k < 2; k++) {
    for (int i = 0; i < 5; i++) {
      digitalWrite(leds[i], HIGH);
    }

    tone(buzzer, 500);
    delay(500);

    for (int i = 0; i < 5; i++) {
      digitalWrite(leds[i], LOW);
    }

    noTone(buzzer);
    delay(500);
  }
}

void winnerSignal() {
  for (int k = 0; k < 3; k++) {
    for (int i = 0; i < 5; i++) {
      digitalWrite(leds[i], HIGH);
    }

    tone(buzzer, 1800);
    delay(300);

    for (int i = 0; i < 5; i++) {
      digitalWrite(leds[i], LOW);
    }

    noTone(buzzer);
    delay(300);
  }
}