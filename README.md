# Virtual-Assistant

import sounddevice as sd
import wavio
import pyttsx3
import speech_recognition as sr
import datetime
import webbrowser
import wikipedia
import os
import requests
import json
import smtplib

var1 = pyttsx3.init('sapi5')
voices = var1.getProperty('voices')
var1.setProperty(voices,voices[1].id)

def speak(audio):
    var1.say(audio)
    var1.runAndWait()
def takeCommand():
    fps = 44100
    duration = 5
    print("listening..")
    recording = sd.rec(duration * fps, samplerate=fps, channels=2)
    sd.wait()
    print("done")
    wavio.write('output.wav', recording, fps, sampwidth=2)
    rec = sr.Recognizer()
    audioF = 'output.wav'
    with sr.AudioFile(audioF) as sourceF:
        audio = rec.record(sourceF)
    try:
        text = rec.recognize_google(audio)
        print(text)
        return str(text)

    except Exception as e:
        print(" ")


def wishMe():
    speak("Your Name Plz")
    name = input()
    # name = takeCommand()
    hour = int(datetime.datetime.now().hour)
    if 0 <= hour < 12:
        speak(f"Good Morning{name}")
    elif 12 <= hour < 18:
        speak(f"Good Afternoon{name}")
    else:
        speak("Good evening")
    speak(f"how can I help you")

def sendEmail(to, content):
    server = smtplib.SMTP('sm tp.gmail.com', 587)
    server.ehlo()
    server.starttls()
    server.login('email address', 'password')
    server.sendmail('mail address', to, content)
    server.close()
if __name__ == '__main__':
    wishMe()  
    while True:
        query = takeCommand()
        # query = str(takeCommand())
        # executing commands
        if 'wikipedia' in query:  # if wikipedia found in the query then this block will be executed
            speak('Searching Wikipedia...')
            query = query.replace("wikipedia", "")
            results = wikipedia.summary(query, sentences=2)
            speak("According to Wikipedia")
            print(results)
            speak(results)
        elif 'open youtube' in query:
            webbrowser.open("youtube.com")
        elif 'open stackoverflow' in query:
            webbrowser.open("stackoverflow.com")
        elif 'open google' in query:
            webbrowser.open("google.com")
        elif 'play music' in query:
            music_dir = 'C:\\ Users\\Hp\\PycharmProjects\\PPandasandNumpy'
            songs = os.listdir(music_dir)
            os.startfile(os.path.join(music_dir, songs[0]))
        elif 'the time' in query:
            strt = datetime.datetime.now().strftime("%H:%M:%S")
            speak(f"the time is {strt}")
        elif 'open code' in query:
            codepath = "C:\\ProgramData\\Microsoft\\Windows\\Start Menu\\Programs"
            os.startfile(codepath)
        elif 'open pycharm' in query:
            pycharmpath = "C:\\ProgramData\\Microsoft\\Windows\\Start Menu\\Programs\\JetBrains"
            os.startfile(pycharmpath)
        elif f"send mail to" in query:
            try:
                speak("this mail is for testing")
                content = takeCommand()
                to = "pa583474834@gmail.com"
                sendEmail(to, content)
                speak("Email has been sent!")
            except Exception as e:
                print(e)
                speak("mail sent")
        elif "news" in query:
            speak("News for today.. Lets begin")
            url = "https://newsapi.org/v2/top-headlines?country=in&apiKey=fb8cc879647c4cdd9b4b9496d2144c3a"
            news = requests.get(url).text
            news_dict = json.loads(news)
            arts = news_dict['articles']
            for article in arts:
                speak(article['title'])
                print(article['title'])
                speak("Moving on to the next news")
