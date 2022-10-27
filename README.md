# Controlling your doorlock with a Telegram bot

## Introduction
In this Manual you will learn how to create a Telegram bot and connect it to your Arduino ESP8266 module to open and close a lock that is connected to a servo motor.


## Required hardware
1. Arduino ESP8266 board
2. Servomotor
3. 3 Jumpwires to connect to the LEDstrip *Female > Male*


### Step 1: Connect your board with the LEDstrip

On the Servo Motor you see 3 jumpwire connections. These connections will have a certain color representing the right value to connect them to.

- RED = 3v3
- ORANGE = Din
- BROWN = GROUND

Connect these 3 with the board accordingly for this Manual

- RED to 3V3 (ideally 5V)
- Din -> D5
- GND -> GND


### Step 2 Installing the required Library

First you [have to download this zip](https://github.com/witnessmenow/Universal-Arduino-Telegram-Bot/archive/master.zip) containing the library for Telegram
Go to **Sketch -> Include Library -> Manage Libraries -> Add.ZIP Library...** and add the downloaded library


### Step 3: Telegram Bot

... TBA


## Step 4: The code

... TBA

```C++
... TBA
```

#define WIFI_SSID "WIFI_NAME"
#define WIFI_PASS "PASSWORD"

To your wifi name and password (Make sure not to use a 5hz connection).


## Step 5: Lets Lock!



## Errors

TBA ...
