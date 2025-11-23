import speech_recognition as sr
import pyttsx3
import pywhatkit
import os
import pyautogui
import time
import threading

mic = sr.Recognizer()
speaker = pyttsx3.init()

def speak(message):
    speaker.say(message)
    speaker.runAndWait()

def listen_command():
    audio_data = []
    def stop_on_enter():
        input()
        mic.pause_threshold = 0.1
        audio_data.append("STOP")
    threading.Thread(target=stop_on_enter, daemon=True).start()
    with sr.Microphone() as source:
        print("Listening... (Press ENTER to stop)")
        audio = mic.listen(source, timeout=None, phrase_time_limit=None)
        if audio_data:
            print("Stopped by ENTER")
        else:
            print("Stopped by silence")
    try:
        text = mic.recognize_google(audio)
        print("You said:", text)
        return text
    except:
        print("Sorry, I could not understand.")
        return ""

def open_notepad():
    os.system("notepad")
    speak("Opening Notepad")

def send_whatsapp_message(message):
    speak("Sending WhatsApp message")
    pywhatkit.sendwhatmsg_instantly("+917878891296", message)

    time.sleep(5)     # wait for WhatsApp to load
    pyautogui.hotkey("alt", "tab")   # switch back
    speak("I'm back")

def play_youtube(full_text):
    speak("Searching on YouTube")
    song = full_text.replace("play", "").replace("youtube", "").strip()
    if song == "":
        song = "latest song"
    pywhatkit.playonyt(song)
    speak(f"Playing {song} on YouTube")
    time.sleep(8)
    pyautogui.hotkey("alt", "tab")
    speak("I'm back")

def handle(command, full_text):
    if "open notepad" in command:
        open_notepad()
    elif "whatsapp" in command:
        send_whatsapp_message(full_text)
    elif "youtube" in command:
        play_youtube(full_text)
    else:
        speak("I did not understand")

def main():
    speak("What can I do for you?")
    while True:
        text = listen_command()
        command = text.lower()

        handle(command, text)
        speak("What next?")   # continue Jarvis

        user_input = input("\nPress Enter to continue, type 'pause' to stop listening, 'stop' to exit: ")

        if user_input.lower() == "pause":
            speak("Listening paused. Press Enter to resume.")
            input()
            speak("Resuming listening...")
            continue

        if user_input.lower() == "stop":
            speak("Shutting down. Goodbye!")
            break


if __name__ == "__main__":
    main()
