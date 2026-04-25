![1](https://github.com/sadeem058/stepper-with-esp32/blob/main/task3.png)

# Solve:

![com1](https://github.com/sadeem058/Electronics-Tasks/blob/main/components5-1.png)

## Components

ESP32
Stepper Motor
A4988 Stepper Driver
Jumper Wires

## Wiring & Pin Mapping

![comp2](https://github.com/sadeem058/stepper-with-esp32/blob/main/components.png)

A4988 Driver → ESP32:
STEP → GPIO 18
DIR → GPIO 19

Power Connections:
VMOT → External motor power (e.g., 9V–12V)
GND (driver) → GND (ESP32)
VDD → 3.3V (ESP32)

Motor Connections:
The 4 wires of the stepper motor → connected to A4988 outputs (1A, 1B, 2A, 2B)

Note:
Always connect GND of ESP32 and driver together.

## How It Works

In this project:

The ESP32 controls a stepper motor using the A4988 driver.
The motor performs:
One full rotation clockwise
![2](https://github.com/sadeem058/stepper-with-esp32/blob/main/-200.png)
One full rotation counterclockwise
![1](https://github.com/sadeem058/stepper-with-esp32/blob/main/0.png)
Gradual acceleration
Constant high-speed rotation for 2 seconds
[1](https://github.com/sadeem058/stepper-with-esp32/blob/main/2%20second.png)
Gradual deceleration
After completing all actions, the program stops.

## digram.json ( Components & Connection )
```
{
  "version": 1,
  "author": "Sadeem Jamal",
  "editor": "wokwi",
  "parts": [
    { "type": "board-esp32-devkit-c-v4", "id": "esp", "top": 105.6, "left": -14.36, "attrs": {} },
    { "type": "wokwi-uln2003", "id": "uln1", "top": 150, "left": 150, "attrs": {} },
    { "type": "wokwi-a4988", "id": "drv1", "top": 148.8, "left": -196.8, "attrs": {} },
    {
      "type": "wokwi-stepper-motor",
      "id": "stepper1",
      "top": -159.59,
      "left": -133.97,
      "attrs": { "size": "17" }
    },
    { "type": "wokwi-vcc", "id": "vcc1", "top": 29.56, "left": -144, "attrs": { "voltage": "9" } },
    { "type": "wokwi-gnd", "id": "gnd1", "top": 19.2, "left": 47.4, "attrs": {} }
  ],
  "connections": [
    [ "stepper1:A-", "drv1:1B", "cyan", [ "v105.6" ] ],
    [ "stepper1:A+", "drv1:1A", "cyan", [ "v96" ] ],
    [ "stepper1:B+", "drv1:2A", "cyan", [ "v86.4", "h-76.8" ] ],
    [ "stepper1:B-", "drv1:2B", "cyan", [ "v76.8", "h-0.01" ] ],
    [ "drv1:GND.2", "esp:GND.1", "black", [ "h124.95", "v96.08" ] ],
    [ "drv1:VMOT", "vcc1:VCC", "red", [ "h9.75", "v0.08" ] ],
    [ "gnd1:GND", "esp:GND.1", "black", [] ],
    [
      "drv1:ENABLE",
      "esp:GND.1",
      "black",
      [ "h-19.2", "v-9.6", "h115.2", "v0", "h67.2", "v115.2" ]
    ],
    [ "drv1:VDD", "esp:3V3", "red", [ "h105.75", "v-76.72" ] ],
    [ "drv1:GND.1", "esp:GND.1", "black", [ "v0.08", "h105.75", "v38.4" ] ],
    [ "drv1:DIR", "esp:19", "cyan", [ "h0", "v105.6", "h288", "v-124.8" ] ],
    [ "drv1:STEP", "esp:18", "cyan", [ "h-9.6", "v124.8", "h297.6", "v0", "h9.6", "v-124.8" ] ],
    [ "drv1:RESET", "esp:3V3", "red", [ "h-28.8", "v-57.6" ] ],
    [ "drv1:SLEEP", "esp:3V3", "red", [ "h-28.8", "v-67.2" ] ]
  ],
  "dependencies": {}
}
```
## Code 
```
#define STEP_PIN 18
#define DIR_PIN 19

#define STEPS_PER_REV 200

void stepMotor(int steps, int delayTime) {
  for (int i = 0; i < steps; i++) {
    digitalWrite(STEP_PIN, HIGH);
    delayMicroseconds(delayTime);
    digitalWrite(STEP_PIN, LOW);
    delayMicroseconds(delayTime);
  }
}

void setup() {
  pinMode(STEP_PIN, OUTPUT);
  pinMode(DIR_PIN, OUTPUT);
}

void loop() {

  // 1️⃣ لفة يمين
  digitalWrite(DIR_PIN, HIGH);
  stepMotor(STEPS_PER_REV, 800);

  delay(1000);

  // 2️⃣ لفة يسار
  digitalWrite(DIR_PIN, LOW);
  stepMotor(STEPS_PER_REV, 800);

  delay(1500);

  // 3️⃣ تسارع تدريجي
  digitalWrite(DIR_PIN, HIGH);

  for (int d = 1500; d > 400; d -= 50) {
    stepMotor(25, d);
  }

  // 4️⃣ سرعة ثابتة
  unsigned long startTime = millis();
  while (millis() - startTime < 2000) {
    stepMotor(50, 400);
  }

  // 5️⃣ تباطؤ
  for (int d = 400; d < 1500; d += 50) {
    stepMotor(25, d);
  }

  while (true);
}
```
## Explanation

#define STEP_PIN 18
#define DIR_PIN 19
Defines the pins used to control the stepper driver:

STEP → controls stepping
DIR → controls direction
#define STEPS_PER_REV 200
Number of steps for one full revolution (typical stepper motor).

void stepMotor(int steps, int delayTime) {
Function to rotate the motor a specific number of steps.

for (int i = 0; i < steps; i++) {
Loop for each step.

digitalWrite(STEP_PIN, HIGH);
delayMicroseconds(delayTime);
digitalWrite(STEP_PIN, LOW);
delayMicroseconds(delayTime);
Creates a pulse signal:

HIGH → step forward
LOW → complete the step
delay controls speed
void setup() {
Runs once at startup.

pinMode(STEP_PIN, OUTPUT);
pinMode(DIR_PIN, OUTPUT);
Sets control pins as outputs.

void loop() {
Main loop of the program.

1. Full Rotation Clockwise

digitalWrite(DIR_PIN, HIGH);
stepMotor(STEPS_PER_REV, 1000);
Sets direction to clockwise
Moves one full revolution
delay(500);
Short pause.

2. Full Rotation Counterclockwise

digitalWrite(DIR_PIN, LOW);
stepMotor(STEPS_PER_REV, 1000);
Reverses direction
Moves one full revolution
delay(1000);
Pause before next stage.

3. Gradual Acceleration
digitalWrite(DIR_PIN, HIGH);
Sets direction again.

for (int d = 2000; d > 500; d -= 100) {
  stepMotor(20, d);
}
Starts slow (delay = 2000 µs)
Gradually increases speed (delay decreases)

4. Constant High Speed (2 seconds)

unsigned long startTime = millis();
while (millis() - startTime < 2000) {
  stepMotor(20, 500);
}
Runs motor at maximum speed for 2 seconds

5. Gradual Deceleration
for (int d = 500; d < 2000; d += 100) {
  stepMotor(20, d);
}
Slowly reduces speed until it stops
while (true);
Stops the program completely.
