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

# Logging setup
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)
logger = logging.getLogger(__name__)

TELEGRAM_API_TOKEN = config('TELEGRAM_API_TOKEN')
WEATHER_API_KEY = config('WEATHER_API_KEY')
BASE_WEATHER_URL = "http://api.openweathermap.org/data/2.5/weather?q={}&appid={}"
FORECAST_URL = "http://api.openweathermap.org/data/2.5/forecast?q={}&appid={}"

city = None

def weather_emoji(description):
    """Return an emoji based on the weather description."""
    description = description.lower()
    if "clear" in description:
        return "☀️"
    elif "cloud" in description:
        return "☁️"
    elif "rain" in description:
        return "🌧️"
    elif "thunder" in description:
        return "⛈️"
    elif "snow" in description:
        return "❄️"
    elif "mist" in description or "fog" in description:
        return "🌫️"
    else:
        return ""

def start(update: Update, context: CallbackContext) -> None:
    update.message.reply_text("In which city do you live?")

def menu(update: Update, context: CallbackContext) -> None:
    global city
    city = update.message.text
    keyboard = [
        [InlineKeyboardButton("Current Weather", callback_data='current_weather')],
        [InlineKeyboardButton("Weather Forecast", callback_data='forecast')],
    ]
    reply_markup = InlineKeyboardMarkup(keyboard)
    update.message.reply_text('Choose an option:', reply_markup=reply_markup)

def button(update: Update, context: CallbackContext) -> None:
    query = update.callback_query
    query.answer()

    if query.data == 'current_weather':
        response = requests.get(BASE_WEATHER_URL.format(city, WEATHER_API_KEY))
        if response.status_code == 200:
            data = response.json()
            main = data['main']
            weather_data = data['weather'][0]
            celsius_temp = main['temp'] - 273.15
            emoji = weather_emoji(weather_data['description'])
            message = f"Current weather in {city} {emoji}:\n"
            message += f"Temperature: {celsius_temp:.2f}°C\n"
            message += f"Description: {weather_data['description'].capitalize()}\n"
            message += f"Humidity: {main['humidity']}%\n"
            query.edit_message_text(text=message)
        else:
            query.edit_message_text(text="Can't find weather information for this city. Try again.")
    elif query.data == 'forecast':
        response = requests.get(FORECAST_URL.format(city, WEATHER_API_KEY))
        if response.status_code == 200:
            data = response.json()
            message = f"Weather forecast for {city}:\n"
            for item in data['list'][:5]:
                celsius_temp = item['main']['temp'] - 273.15
                emoji = weather_emoji(item['weather'][0]['description'])
                message += f"\nDate: {item['dt_txt']} {emoji}\n"
                message += f"Temperature: {celsius_temp:.2f}°C\n"
                message += f"Description: {item['weather'][0]['description'].capitalize()}\n"
            query.edit_message_text(text=message)
        else:
            query.edit_message_text(text="Can't find a forecast for this city. Try again.")

def error(update: Update, context: CallbackContext):
    """Log errors caused by updates."""
    logger.warning('Update "%s" caused error "%s"', update, context.error)

def main() -> None:
    logger.info("Starting bot...")
    updater = Updater(token=TELEGRAM_API_TOKEN, use_context=True)
    dp = updater.dispatcher
    dp.add_handler(CommandHandler("start", start))
    dp.add_handler(MessageHandler(Filters.text & ~Filters.command, menu))
    dp.add_handler(CallbackQueryHandler(button))
    
    # log all errors
    dp.add_error_handler(error)

    updater.start_polling()
    logger.info("Bot started and polling...")
    updater.idle()

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

