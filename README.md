# Telegram Weather Bot 🌦️

A Telegram bot developed in Python to provide users with real-time weather updates and forecasts using the OpenWeatherMap API.

## Features

- Ask users for their city.
- Provide current weather updates.
- Provide a forecast for the next few days.
- Use relevant emojis based on the weather description.
- Integrated error logging.

## Code Overview

```python
import os
import logging
from telegram import Bot, Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import Updater, CommandHandler, MessageHandler, Filters, CallbackContext, CallbackQueryHandler
import requests
from decouple import config

# ... [rest of the code]

if __name__ == '__main__':
    main()
```

## Setup & Installation

1. Clone this repository.
2. Install required dependencies:
   ```
   pip install python-telegram-bot requests python-decouple
   ```
3. Set up environment variables for `TELEGRAM_API_TOKEN` and `WEATHER_API_KEY`.
4. Run the bot using:
   ```
   python bot.py
   ```

## How to Use

1. Start the bot with the `/start` command.
2. Enter your city.
3. Choose between "Current Weather" or "Weather Forecast".
4. Get the weather details displayed.

## Contributions

Feel free to fork this project, open an issue, or submit pull requests. All contributions are welcome!

