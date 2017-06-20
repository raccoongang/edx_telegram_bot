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

Project has no opportunity to authorize new users as edX's instance student.

So you need to do this manually:
1. Go to admin panel, then to `Edx telegram users` model (table).
2. Make relationship between Telegram user and edX user in field `student`.
3. Add telegram `id` and telegram `username` (optional).

If you don't know telegram id and nickname, just go ahead and log it there
https://github.com/raccoongang/edx_telegram_bot/blob/master/edx_telegram_bot/edx_telegram_bot.py#L169:
```
def help_command(self, bot, update):
    print update.message
```
It works only when you start bot manually, not under supervisor.

`update.message` example of the output:
```
Update {'message': {'migrate_to_chat_id': 0, 'delete_chat_photo': False, 'new_chat_photo': [], 
'entities': [{'length': 5, 'type': u'bot_command', 'offset': 0}], 'text': u'/help', 
'migrate_from_chat_id': 0, 'channel_chat_created': False, 'from': {'username': u'racconganguser', 
'first_name': u'Raccon', 'last_name': '', 'type': '', 'id': 224623252}, 'supergroup_chat_created': 
False, 'chat': {'username': u'racconganguser', 'first_name': u'Raccon', 'last_name': '', 
'title': '', 'type': u'private', 'id': 224623252}, 'photo': [], 'date': 1497434033, 
'group_chat_created': False, 'caption': '', 'message_id': 6661, 'new_chat_title': ''}, 
'update_id': 757157549}
```
Pay attention to key `from`, here's all data you need:
```
'from': {
    'username': u'racconganguser', 
    'first_name': u'Raccon', 
    'last_name': '', 
    'type': '', 
    'id': 224623252
 }
```
