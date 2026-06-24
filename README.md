# 🎙️ Brave STT Voice Typing Anywhere (Linux)

A lightweight Python script to turn your voice into text in Brave, ChatGPT, or any text field on Linux.

> ⚠️ **STOP! READ THIS FIRST** ⚠️  
> **Do not run the old installation commands** (`sudo apt install pyaudio`) on Ubuntu 22.04 or older hardware.  
> That step involves compiling C++ code that has **broken many users' systems**, causing:
> - Missing commands (`ls`, `apt`, `python3` vanish).
> - Lost desktop (just a black shell remains).
> - Full disk from failed builds.
>
> **If you want to stay safe:** Use the **Cloud Method** below. It requires **zero** system changes.

---

## 🚀 Quick Start (The Safe Way)

### Option A: Run in the Cloud (100% Safe)
**Best for:** Anyone who doesn't want to risk breaking their computer.
1. Go to [GitHub Codespaces](https://github.com/codespaces) or [Google Colab](https://colab.research.google.com).
2. Open a new terminal.
3. Run this single command:
   ```bash
   pip install SpeechRecognition
   ```
4. Copy the code from **Section 2** below, save it as `voice_type.py`, and run it.  
   *(Note: Microphone access in the cloud requires browser permissions.)*

### Option B: Local Virtual Environment (Safe-ish)
**Best for:** Testing locally without touching system files.
```bash
# 1. Create a safe "bubble" for your project
python3 -m venv stt_env

# 2. Step inside the bubble
source stt_env/bin/activate

# 3. Install the Python parts
pip install SpeechRecognition

# 4. If this fails, your system is missing audio libs. 
#    Stop here and use Option A (Cloud) instead of trying to fix your OS.
```

### ⚠️ Option C: Full System Install (DANGER ZONE)
**Only do this if you have a full system backup.**  
This step forces your computer to compile C++ code, which is where the breakage happens.
```bash
# 1. Install the safe parts first
sudo apt-get install xdotool python3-pip

# 2. Try to install pyaudio WITHOUT compiling (Safe)
pip install --only-binary :all: pyaudio SpeechRecognition

# 3. IF that fails, you MUST compile (RISKY)
#    Only run these if you know how to fix a broken Linux install.
sudo apt-get install portaudio19-dev
pip install pyaudio
```

---

## 📝 The Script Code

Create a file named `voice_type.py` and paste this code inside:

```python
import speech_recognition as sr
import subprocess
import os

# --- Configuration ---
# Adjust device_index if your mic isn't detected (check with 'arecord -l')
MIC_DEVICE_INDEX = 0 

r = sr.Recognizer()
mic = sr.Microphone(device_index=MIC_DEVICE_INDEX)

print("🎙️  Voice Typing Started. Press Ctrl+C to stop.\n")

while True:
    try:
        with mic as source:
            print("Listening... (Speak now)")
            r.adjust_for_ambient_noise(source, duration=0.5)
            audio = r.listen(source, timeout=5, phrase_time_limit=10)

        text = r.recognize_google(audio)
        print(f"✅ You said: {text}")

        # Type the text using xdotool (Linux only)
        # Escape special characters to prevent breaking the command
        safe_text = text.replace('"', '\\"').replace("'", "\\'")
        subprocess.run(["xdotool", "type", safe_text])
        subprocess.run(["xdotool", "key", "space"])

    except sr.WaitTimeoutError:
        continue
    except sr.UnknownValueError:
        print("❌ Couldn't understand. Try again.")
    except sr.RequestError as e:
        print(f"🌐 Google API Error: {e}")
    except KeyboardInterrupt:
        print("\n👋 Stopping...")
        break
    except Exception as e:
        print(f"⚠️  Unexpected error: {e}")
```

---

## 🛠️ How It Works

1. **Listens** to your microphone.
2. **Converts** speech to text using Google's free API.
3. **Types** the text into whatever window you have focused (Brave, ChatGPT, Notepad, etc.) using `xdotool`.

### Prerequisites (Linux Only)
- **`xdotool`**: Required to type the text.
  ```bash
  # Safe to install
  sudo apt install xdotool
  ```
- **Microphone**: Must be working. Test with `arecord -l`.

---

## 🐛 Troubleshooting

### "Command 'ls' not found" or "Python3 not found"
If this happens **after** you tried the "Danger Zone" installation:
1. Your system `PATH` is likely broken.
2. Run this **temporary fix**:
   ```bash
   export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
   ```
3. If that works, your system is salvageable. See [Issue #2](https://github.com/natasha0824inkf/brave-chatgpt-stt-type-anywhere-linux-web-script/issues/2) for the full recovery guide.

### "Microphone not found"
1. Check your mic index: `arecord -l`
2. Update `MIC_DEVICE_INDEX` in the script to match your device number.

### "xdotool not found"
Install it safely:
```bash
sudo apt install xdotool
```

---

## 🛡️ Safety First

- **Don't** run `sudo apt install python3-pyaudio` blindly.
- **Do** use a Virtual Environment or Cloud IDE first.
- **Do** check [Issue #2](https://github.com/natasha0824inkf/brave-chatgpt-stt-type-anywhere-linux-web-script/issues/2) if you run into system issues.

*Happy typing! 🎤*
