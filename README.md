# JarvisAi
Jarvis AI is an advanced virtual assistant designed to enhance productivity, automation, and user interaction through the power of Artificial Intelligence (AI) and Machine Learning (ML). Inspired by Marvel’s J.A.R.V.I.S.
import pyttsx3
import datetime
import pywhatkit
import wikipedia
import sympy as sp
import requests
import os
from email.message import EmailMessage

# Initialize text-to-speech engine
engine = pyttsx3.init()

def speak(text):
    print(f"Jarvis: {text}")
    engine.say(text)
    engine.runAndWait()

def calculate_expression(expression):
    try:
        result = sp.sympify(expression)
        speak(f"The result is {result}")
    except Exception:
        speak("Sorry, I couldn't understand the expression.")

def get_weather(city):
    api_key = "e46876cdca03430ca6104101252404"  # Replace with your real API key
    url = f"http://api.weatherapi.com/v1/current.json?key={api_key}&q={city}"
    try:
        response = requests.get(url)
        data = response.json()
        if "current" in data:
            temp = data["current"]["temp_c"]
            desc = data["current"]["condition"]["text"]
            speak(f"The temperature in {city.title()} is {temp}°C with {desc.lower()}.")
        else:
            speak("City not found.")
    except Exception as e:
        speak("Failed to retrieve weather information.")
        print("Error:", e)

def open_app(app_name):
    paths = {
        "chrome": "C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe",
        "notepad": "C:\\Windows\\system32\\notepad.exe",
        "vscode": "C:\\Users\\HP\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe",
        "calculator": "C:\\Windows\\System32\\calc.exe"
    }
    app_name = app_name.lower()
    if app_name in paths:
        os.startfile(paths[app_name])
        speak(f"Opening {app_name}")
    else:
        speak("I don't know that app. Please add it to my list.")

def draft_email():
    recipient = input("Enter recipient email: ")
    subject = input("Enter email subject: ")
    body = input("Enter email body: ")

    email = EmailMessage()
    email['To'] = recipient
    email['Subject'] = subject
    email.set_content(body)

    filename = "email_draft.txt"
    with open(filename, "w", encoding="utf-8") as f:
        f.write(f"To: {recipient}\nSubject: {subject}\n\n{body}")

    speak(f"Draft saved as {filename}. You can review or send it later.")

def run_assistant():
    speak("Hello! I'm Jarvis. Type your command below.")

    while True:
        command = input("You: ").lower()

        if not command:
            continue

        if 'play' in command:
            song = command.replace('play', '').strip()
            speak(f"Playing {song}")
            pywhatkit.playonyt(song)

        elif 'time' in command:
            time = datetime.datetime.now().strftime('%I:%M %p')
            speak(f"The time is {time}")

        elif 'who is' in command:
            person = command.replace('who is', '').strip()
            try:
                info = wikipedia.summary(person, sentences=2)
                speak(info)
            except wikipedia.exceptions.DisambiguationError:
                speak("There are multiple options. Can you be more specific?")
            except wikipedia.exceptions.PageError:
                speak("Sorry, I couldn't find that on Wikipedia.")
                pywhatkit.search(person)
            except Exception:
                speak("Something went wrong, let me search online.")
                pywhatkit.search(person)

        elif 'calculate' in command:
            expression = command.replace('calculate', '').strip()
            calculate_expression(expression)

        elif 'search' in command:
            query = command.replace('search', '').strip()
            speak(f"Searching for {query}")
            pywhatkit.search(query)

        elif 'weather' in command:
            city = command.replace('weather', '').strip()
            if city:
                get_weather(city)
            else:
                speak("Please specify a city name.")

        elif 'open' in command:
            app = command.replace('open', '').strip()
            open_app(app)

        elif 'draft email' in command or 'write mail' in command:
            draft_email()

        elif 'stop' in command or 'exit' in command or 'bye' in command:
            speak("Goodbye!")
            break

        else:
            speak("Searching the web...")
            pywhatkit.search(command)

# Run the assistant
run_assistant()
