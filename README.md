# Github Actions bot on Telegram

## Outcome
The ***CI-CD Bot*** will send a message in the ***Github updates*** group chat each time someone commits a change in this repository.

Group chat link: https://t.me/+t1Q4XOTSV6xlNmY1

## Steps

### 1.1 Creating a new bot
[Following documentation](https://core.telegram.org/bots#6-botfather):
* Go to [BotFather](https://t.me/botfather) and select 'SEND MESSAGE'
* Start a chat and use `/newbot` to create a new bot
* Send the ***name*** of your bot
* Send the ***username*** of your bot, it must end with 'bot'/'Bot'
* A ***token*** will be generated e.g. `110201543:AAHdqTcvCH1vGWJxfSeofSAs0K5PALDsaw`

### 1.2 Creating a chat with the new bot
* Search for your bot's ***name*** and select it
* Send the message `/start`
* Send any message e.g. `hello`

### 1.3 Obtaining chat id
* Open terminal
* Execute the following code, replacing `<TOKEN>` with the ***token*** obtained in ***1.1***:
  
  `curl https://api.telegram.org/bot<TOKEN>/getUpdates`
* Obtain ***chat id*** from API response below (XXXXXXXXX in this case):
```json
  {
   "ok":true,
   "result":[
      {
         "update_id":777368030,
         "message":{
            "message_id":1,
            "from":{
               "id":1234567890,
               "is_bot":false,
               "first_name":"YourUserName",
               "username":"YourTelegramUser",
               "language_code":"pt-br"
            },
            "chat":{
               "id":XXXXXXXXX,
               "first_name":"YourUserName",
               "username":"YourTelegramUser",
               "type":"private"
            },
            "date":1234567890,
            "text":"/start",
            "entities":[
               {
                  "offset":0,
                  "length":6,
                  "type":"bot_command"
               }
            ]
         }
      }
   ]
}
```

### 1.4 Configuring action
* In repo, go to 'Settings' => 'Actions' => 'New repository secret'
  * Create secret `secrets.TELEGRAM_TO` = ***chat id***, this secret tells the bot which chat to send the message to
  * Create secret `secrets.TELEGRAM_TOKEN` = ***token***
* In repo, go to 'Actions' => 'set up a workflow yourself'
* Paste in the following code:
```yml
name: Notifier

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
    
jobs:
  notifyTelegram:
    runs-on: ubuntu-latest
    steps:
      - name: send custom message
        uses: appleboy/telegram-action@master
        with:
          to: ${{ secrets.TELEGRAM_TO }}
          token: ${{ secrets.TELEGRAM_TOKEN }}
          message: |
            The ${{ github.event_name }} event triggered final step.
```
### 1.5 Commit a change
* Commit any change and wait for your bot to notify you!

### 2.1 Adding bot to a chat group
* Add your bot into a chat group
* Repeat ***1.3*** and obtain the latest ***chat id*** from API response
* Set secret `secrets.TELEGRAM_TO` = latest ***chat id***
* Done! Commit any change and wait for your bot to notify the group chat!
