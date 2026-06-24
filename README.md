🎙️ Brave STT Voice Typing Anywhere (Linux & macOS)
---

A lightweight Python script to turn your voice into text in Brave, ChatGPT, or any text field on **Linux** and **macOS**.

---

##> ⚠️ **STOP! READ THIS FIRST** ⚠️  
> **Do not run the old installation commands** (`sudo apt install pyaudio`) on Ubuntu 22.04 or older hardware.  
> That step involves compiling C++ code that has **broken many users' systems**, causing:
> - Missing commands (`ls`, `apt`, `python3` vanish).
> - Lost desktop (just a black shell remains).
> - Full disk from failed builds.
>
> **If you want to stay safe:** Use the **Cloud Method** below. It requires **zero** system changes.


---

### 🚀 Quick Start (Safe Way)
### Option A: Run in the Cloud (100% Safe)
**Best for:** Anyone who doesn't want to risk breaking their computer.**
1. Go to [GitHub Codespaces](https://github.com/codespaces) or [Google Colab](https://colab.research.google.com).
2. Open a new terminal.
3. Run this single command:
   ```bash
   pip install SpeechRecognition
   ```

**4. Copy the code from **Section 2** below, save it as `voice_type.py`, and run it.  
   *(Note: Microphone access in the cloud requires browser permissions.)**

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

Create a file named `voice_type.py` (for Linux) or `voice_type_mac.py` (for macOS) and paste the code below.

### 🐧 For Linux (Ubuntu/Debian/Fedora)

```python
import speech_recognition as sr
import subprocess
import os

# --- Configuration ---
# Adjust device_index if your mic isn't detected (check with 'arecord -l')
MIC_DEVICE_INDEX = 0 

r = sr.Recognizer()
mic = sr.Microphone(device_index=MIC_DEVICE_INDEX)

print("🎙️  Voice Typing Started (Linux). Press Ctrl+C to stop.\n")

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

### 🍎 For macOS (Intel & Apple Silicon)

> ⚠️ **CRITICAL: You must grant Accessibility Permissions.**
> 1. Go to **System Settings** > **Privacy & Security** > **Accessibility**.
> 2. Click the **+** button and add **Terminal** (or your code editor).
> 3. Toggle the switch to **ON**.
> 4. **Restart your terminal**.

```python
import speech_recognition as sr
import subprocess
import platform

def type_text_macos(text):
    """
    Types text using macOS native AppleScript (no xdotool).
    Requires Accessibility Permissions.
    """
    # Escape quotes to prevent breaking the AppleScript
    safe_text = text.replace('"', '\\"').replace('\\', '\\\\')
    
    # AppleScript command to type the text
    script = f'''
    tell application "System Events"
        keystroke "{safe_text}"
        keystroke " "
    end tell
    '''
    try:
        subprocess.run(["osascript", "-e", script], check=True)
    except subprocess.CalledProcessError:
        print("❌ Error: Check Accessibility Permissions in System Settings.")

def main():
    r = sr.Recognizer()
    
    # Check for microphone access
    try:
        mic = sr.Microphone()
    except OSError:
        print("❌ Microphone access denied. Check System Preferences > Privacy.")
        return

    print("🎙️ macOS Voice Typing Started. Press Ctrl+C to stop.\n")

    while True:
        try:
            with mic as source:
                print("Listening... (Speak now)")
                r.adjust_for_ambient_noise(source, duration=0.5)
                audio = r.listen(source, timeout=5, phrase_time_limit=10)

            text = r.recognize_google(audio)
            print(f"✅ You said: {text}")
            type_text_macos(text)

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
            print(f"⚠️ Unexpected error: {e}")

if __name__ == "__main__":
    main()
```

---

## 🛠️ Prerequisites

### Linux
- **`xdotool`**: Required to type the text.
  ```bash
  # Safe to install
  sudo apt install xdotool
  ```
- **Microphone**: Must be working. Test with `arecord -l`.

### macOS
- **Accessibility Permissions**: Required for the script to type. (See instructions in the macOS code section above).
- **Microphone Permissions**: Required in **System Settings > Privacy & Security > Microphone**.
- **Dependencies**:
  ```bash
  pip install SpeechRecognition pyobjc-framework-ApplicationServices
  ```

---

## 🐛 Troubleshooting

### "Command 'ls' not found" or "Python3 not found" (Linux)
If this happens **after** you tried the "Danger Zone" installation:
1. Your system `PATH` is likely broken.
2. Run this **temporary fix**:
   ```bash
   export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
   ```
3. If that works, your system is salvageable. See [Issue #2](https://github.com/natasha0824inkf/brave-chatgpt-stt-type-anywhere-linux-web-script/issues/2) for the full recovery guide.

### "Microphone not found"
- **Linux**: Check your mic index: `arecord -l` and update `MIC_DEVICE_INDEX` in the script.
- **macOS**: Check **System Settings > Privacy & Security > Microphone**.

### "xdotool not found" (Linux)
Install it safely:
```bash
sudo apt install xdotool
```

### "AppleScript failed" (macOS)
- Ensure you added **Terminal** to **Accessibility Permissions**.
- Ensure the window you want to type in (e.g., Brave) is **active/focused**.

---

## 🛡️ Safety First

- **Don't** run `sudo apt install python3-pyaudio` blindly.
- **Do** use a Virtual Environment or Cloud IDE first.
- **Do** check [Issue #2](https://github.com/natasha0824inkf/brave-chatgpt-stt-type-anywhere-linux-web-script/issues/2) if you run into system issues.

*Happy typing! 🎤*
```
