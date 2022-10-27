# Controlling your doorlock with a Telegram bot

## Introduction
In this Manual you will learn how to create a Telegram bot and connect it to your Arduino ESP8266 module to open and close a lock that is connected to a servo motor.


## Required hardware
1. Arduino ESP8266 board
2. Servomotor
3. 3 Jumpwires to connect to the LEDstrip *(Female > Male)*


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

The first stept to making a telegram bot is by making a Telegram account. After you have signed up, you can go ahead and find the user 'botFather' Start a conversation with him to make a bot by saying '/newbot' and follow the steps.

![image](https://user-images.githubusercontent.com/74150653/198282770-eca7b0c8-bc08-465b-9b79-f1b76fae7bed.png)

When you receive the HTTP API, save this for later.


After you've done this you need to find your own user ID. Search for IDBot and say 'getid' to receive your account ID

![image](https://user-images.githubusercontent.com/74150653/198282966-aab5880e-3378-42b8-993a-fe0137a2d962.png)


Now save the ID for later.



## Step 4: The code


Now that we have a bot to connect the Arduino with, we can go ahead and make a code! I've combined a ledstrip control code and a servo bot code for this manual, as shown below.

```C++
#include <ESP8266WiFi.h>
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h>
#include <Servo.h> 

Servo myservo;
int lockStatus = 0;

// Wifi network station credentials
#define WIFI_SSID "WIFI"
#define WIFI_PASSWORD "PASSWORD"
// Telegram BOT Token (Get from Botfather)
#define BOT_TOKEN "BOTTOKEN"

const unsigned long BOT_MTBS = 1000; // mean time between scan messages

X509List cert(TELEGRAM_CERTIFICATE_ROOT);
WiFiClientSecure secured_client;
UniversalTelegramBot bot(BOT_TOKEN, secured_client);
unsigned long bot_lasttime; // last time messages' scan has been done

void handleNewMessages(int numNewMessages)
{
  Serial.print("handleNewMessages ");
  Serial.println(numNewMessages);

  for (int i = 0; i < numNewMessages; i++)
  {
    String chat_id = bot.messages[i].chat_id;
    String text = bot.messages[i].text;

    String from_name = bot.messages[i].from_name;
    if (from_name == "")
      from_name = "Guest";

    if (text == "/opendoor")
    {
      myservo.write(180);
      lockStatus = 1;
      bot.sendMessage(chat_id, "Door is UNLOCKED", "");
    }

    if (text == "/closedoor")
    {
      lockStatus = 0;
      myservo.write(0);
      bot.sendMessage(chat_id, "Door is LOCKED", "");
    }

    if (text == "/status")
    {
      if (lockStatus)
      {
        bot.sendMessage(chat_id, "Door is UNLOCKED", "");
      }
      else
      {
        bot.sendMessage(chat_id, "Door is LOCKED", "");
      }
    }

    if (text == "/start")
    {
      String welcome = "Welcome to Universal Arduino Telegram Bot library, " + from_name + ".\n";
      welcome += "This is Flash Led Bot example.\n\n";
      welcome += "/opendoor : to switch the Led ON\n";
      welcome += "/closedoor : to switch the Led OFF\n";
      welcome += "/status : Returns current status of LED\n";
      bot.sendMessage(chat_id, welcome, "Markdown");
    }
  }
}


void setup()
{
  Serial.begin(115200);
  Serial.println();
  myservo.attach(D5);

  // attempt to connect to Wifi network:
  configTime(0, 0, "pool.ntp.org");      // get UTC time via NTP
  secured_client.setTrustAnchors(&cert); // Add root certificate for api.telegram.org
  Serial.print("Connecting to Wifi SSID ");
  Serial.print(WIFI_SSID);
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  while (WiFi.status() != WL_CONNECTED)
  {
    Serial.print(".");
    delay(500);
  }
  Serial.print("\nWiFi connected. IP address: ");
  Serial.println(WiFi.localIP());

  // Check NTP/Time, usually it is instantaneous and you can delete the code below.
  Serial.print("Retrieving time: ");
  time_t now = time(nullptr);
  while (now < 24 * 3600)
  {
    Serial.print(".");
    delay(100);
    now = time(nullptr);
  }
  Serial.println(now);
}

void loop()
{
  if (millis() - bot_lasttime > BOT_MTBS)
  {
    int numNewMessages = bot.getUpdates(bot.last_message_received + 1);

    while (numNewMessages)
    {
      Serial.println("got response");
      handleNewMessages(numNewMessages);
      numNewMessages = bot.getUpdates(bot.last_message_received + 1);
    }

    bot_lasttime = millis();
  }
}
```

Change the following in your code to make it work:

#define WIFI_SSID "WIFI_NAME"

#define WIFI_PASS "PASSWORD"


To your wifi name and password (Make sure not to use a 5hz connection).



#define BOT_TOKEN "BOTTOKEN"

To your BOT token, the HTTP API you saved for later.



## Step 5: Lets Lock!

Connect your board to your laptop of phone and watch the magic happen when you say /closedoor and /opendoor
You have now made your very own 'smart lock' prototype!



## Errors

TBA ...
