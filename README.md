import requests
import sqlite3
from datetime import datetime

def get_weather():
    url = "https://example.com/weather](https://www.accuweather.com/en/de/hamburg/20095/weather-forecast/178556"
    response = requests.get(url)

    if response.status_code == 200:
        weather_data = response.json()
        temperature = weather_data["temperature"]
        return temperature
    else:
        print(f"Failed to retrieve weather data. Status code: {response.status_code}")
        return None

def save_to_database(date_time, temperature):
    connection = sqlite3.connect("hamburg_weather.db")
    cursor = connection.cursor()
    cursor.execute('''CREATE TABLE IF NOT EXISTS weather_data
                      (id INTEGER PRIMARY KEY AUTOINCREMENT,
                       date_time TEXT,
                       temperature REAL)''')
    cursor.execute("INSERT INTO weather_data (date_time, temperature) VALUES (?, ?)", (date_time, temperature))
    connection.commit()
    connection.close()

if __name__ == "__main__":
    current_temperature = get_weather()

    if current_temperature is not None:
        current_date_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        save_to_database(current_date_time, current_temperature)
        print(f"Weather data saved to the database. Temperature: {current_temperature}°C at {current_date_time}")
