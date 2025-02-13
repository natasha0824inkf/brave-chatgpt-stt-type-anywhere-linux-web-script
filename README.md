# brave-chatgpt-stt-script (Linux web, Python)

This is a lightweight Python cloud script for voice typing and speech-to-text (STT) automation in Brave and ChatGPT, utilizing xdtool. 

While this script is primarily designed for desktop environments, similar functionality can be achieved on mobile devices (iOS and Android) using built-in voice dictation features or custom mobile app development


## Voice Typing Script Overview


1. ### Install Necessary Dependencies
Make sure you have these installed via the terminal:


```bash
sudo apt-get install python3 python3-pip xdotool portaudio19-dev python3-pyaudio
pip3 install SpeechRecognition
```

2. File instructions for code snippets: Python Script

#  Open the Python script and print its contents with open ('voice_type_py', 'r') as file:
print (file.read)

Create a file named voice_type.py and add the following code:

```
Python
import speech_recognition as sr
import subprocess

r = sr.Recognizer()
mic = sr.Microphone(device_index=5)  # Adjust this if needed

print("Starting voice typing. Press Ctrl+C to stop.\n")

while True:
    try:
        with mic as source:
            print("Please speak now...")
            r.adjust_for_ambient_noise(source, duration=1)
            audio_data = r.listen(source)

        recognized_text = r.recognize_google(audio_data)
        print("You said:", recognized_text)

        subprocess.run(["xdotool", "type", recognized_text])
        subprocess.run(["xdotool", "key", "space"])

    except sr.UnknownValueError:
        print("Didn't catch that. Try again.")
    except sr.RequestError as e:
        print("API unreachable or error:", e)
    except KeyboardInterrupt:
        print("\nExiting...")
        break```


Running the Script

Save the file as voice_type.py. Open the terminal and run the script:

```bash
#python3 voice_type.py

Testing

1. Open Brave or any text editor, and run the script.
2. Focus on the text field and speak into your microphone. The words should be typed automatically.

**Common Troubleshooting*
**
- If it's not working, try adjusting the device index in the script to match your active microphone (check with arecord -l).
- If you encounter errors with the microphone, ensure that PulseAudio is running correctly (pulseaudio --start).
Test the microphone in the terminal to ensure it's working using:

```
bash
arecord -D plughw:1,0 -f cd test-mic.wav```
Commit your changes: Enter a commit message like "Add Voice Typing Script Overview" and click "Commit changes".

