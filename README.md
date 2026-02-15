# OIBSIP_pythonprogramming_3
A beginner-level Python voice assistant that recognizes voice commands and performs basic tasks like telling time and date, searching the web, opening applications, and providing simple responses using speech recognition and text-to-speech
import sounddevice as sd
import numpy as np
import speech_recognition as sr
import wavio
import datetime
import webbrowser
from gtts import gTTS
from playsound import playsound
import os
import wikipedia


# ================= SPEAK FUNCTION =================
def speak(text):
    mp3_file = "speech.mp3"
    tts = gTTS(text=text, lang='en')
    tts.save(mp3_file)
    playsound(mp3_file)
    os.remove(mp3_file)


# ================= RECORD AUDIO =================
def record_audio(duration=5, fs=44100):
    print("Listening...")
    audio = sd.rec(int(duration * fs), samplerate=fs, channels=1)
    sd.wait()
    wav_file = "temp_audio.wav"
    wavio.write(wav_file, audio, fs, sampwidth=2)
    return wav_file


# ================= SPEECH TO TEXT =================
def recognize_speech(wav_file):
    recognizer = sr.Recognizer()
    with sr.AudioFile(wav_file) as source:
        audio_data = recognizer.record(source)
        try:
            command = recognizer.recognize_google(audio_data)
            return command.lower()
        except:
            return ""


# ================= COMMAND HANDLER =================
def handle_command(command):

    if "hello" in command:
        result = "Hello Mirunalini"

    elif "how are you" in command:
        result = "I am fine, ready to help you"

    elif "your name" in command or "who are you" in command:
        result = "I am your voice assistant"

    elif "time" in command:
        result = "The time is " + datetime.datetime.now().strftime("%H:%M")

    elif "date" in command:
        result = "Today is " + datetime.datetime.now().strftime("%Y-%m-%d")

    elif "open notepad" in command:
        result = "Opening Notepad"
        os.system("notepad")

    elif "open calculator" in command:
        result = "Opening Calculator"
        os.system("calc")

    elif "open youtube" in command or "youtube" in command:
        result = "Opening YouTube"
        webbrowser.open("https://www.youtube.com")

    elif "google" in command:
        result = "Opening Google"
        webbrowser.open("https://www.google.com")

    elif "search" in command:
        query = command.replace("search", "").strip()
        if query:
            result = f"Searching Google for {query}"
            webbrowser.open(f"https://www.google.com/search?q={query}")
        else:
            result = "Please say what to search"

    elif "wikipedia" in command:
        query = command.replace("wikipedia", "").strip()
        if query:
            try:
                summary = wikipedia.summary(query, sentences=2)
                result = summary
            except:
                result = f"Could not find information on {query}"
        else:
            result = "Please say what to search on Wikipedia"

    elif "joke" in command:
        result = "Why did the computer go to the doctor? Because it caught a virus"

    elif "stop" in command or "exit" in command:
        result = "Goodbye Mirunalini, exiting now"
        print(result)
        speak(result)
        return True

    else:
        result = "Sorry, command not recognized"

    print(result)
    speak(result)
    return False


# ================= MAIN PROGRAM =================
speak("Hello Mirunalini, I am listening. Speak now.")
print("Hello Mirunalini, I am listening. Speak now.")

while True:
    wav_file = record_audio()
    command = recognize_speech(wav_file)
    os.remove(wav_file)

    if not command:
        print("Sorry, I could not understand")
        speak("Sorry, I could not understand")
        continue

    stop = handle_command(command)
    if stop:
        break
