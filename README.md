This is telegram bot for edx clients support
======================================================================
Connect to vagrant via ssh. By user **edxapp** run following commands:

```
cd /edx/app/edxapp/edx-platform/
git clone https://github.com/vz10/edx_telegram_bot.git
pip install -e edx_telegram_bot
```

In `lms/envs/common.py` :

- override default url with new endpoints
    ```
    ...

    ROOT_URLCONF = 'edx_telegram_bot.urls'
    ...
    ```
- add telegram bot to installed application list
    ```
    ...
    INSTALLED_APPS = {
        ...
        'edx_telegram_bot',
        ...
    }
    ...
    ```
- override default student profile template
    ```
    ...

    MAKO_TEMPLATES['main'] = ['/edx/app/edxapp/edx-platform/edx-telegram-bot/edx_telegram_bot/templates/lms'] + \
                             MAKO_TEMPLATES['main']
    ...
    ```

- Add bot settings (https://core.telegram.org/bots#3-how-do-i-create-a-bot)
    - token - bot token recieved by BotFather bot.
    - bot_name - your bot username
    ```
    ...
    TELEGRAM_BOT = {
        'token': '<TELEGRAM_BOT_TOKEN>',
        'bot_name': "<TELEGRAM_BOT_USERNAME>"
    }
    ...
    ```

Use this command to start bot  in parallel terminal after starting LMS
```
./manage.py lms start_bot --settings=devstack
```

### Users registration

Project has no opportunity to authorize new users as edX`s instance student.

So you need to do this manually:
1) Go to admin panel, then to `Edx telegram users` model (table).
2) Make relationship between Telegram user and edX user in field `student`.
3) Add telegram `id` and telegram `nickname`.

If you don`t know telegram id and nickname, just go ahead and log this, i.e:
```
def help_command(self, bot, update):
    print update.message
```

`update.message` will show you all keys you need like `id`, `nick`, `chat id`, `message id`, etc.
