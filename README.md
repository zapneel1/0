import os
import subprocess
import sys

def run_command(command, description):
    print(f"[*] {description}...")
    subprocess.check_call(command, shell=True)

def create_and_run():
    # 1. Download the specialized 2B Vision model (fits in 8GB RAM)
    run_command("ollama pull qwen2-vl:2b", "Downloading Local AI (Qwen2-VL 2B)")

    # 2. Install Python requirements
    run_command("pip install customtkinter keyboard mss pillow ollama", "Installing Libraries")

    # 3. Create the actual Helper Script
    helper_code = '''
import warnings
warnings.filterwarnings("ignore")
import customtkinter as ctk
import keyboard
import mss
import threading
from PIL import Image
import ollama
import os

# CONFIG
MODEL = "qwen2-vl:2b"
HOTKEY = "ctrl+shift+s"

class LocalOverlay(ctk.CTk):
    def __init__(self):
        super().__init__()
        self.overrideredirect(True)
        self.attributes("-topmost", True)
        h = self.winfo_screenheight()
        # Small discreet box at bottom left
        self.geometry(f"200x35+10+{h-50}")
        self.configure(fg_color="#1a1a1a")

        self.label = ctk.CTkLabel(self, text="LOCAL AI READY", text_color="white", font=("Arial", 9, "bold"))
        self.label.pack(expand=True, fill="both")

        keyboard.add_hotkey(HOTKEY, self.start_task)

    def start_task(self):
        self.label.configure(text="Reading Screen...", text_color="#ffcc00")
        threading.Thread(target=self.solve).start()

    def solve(self):
        try:
            with mss.mss() as sct:
                sct.shot(output="snap.png")
            
            # Send screenshot to local Ollama model
            response = ollama.chat(
                model=MODEL,
                messages=[{
                    "role": "user",
                    "content": "Identify the machining question and give a very brief answer.",
                    "images": ["snap.png"]
                }]
            )
            ans = response["message"]["content"].strip()
            # Update UI with the result
            self.after(0, lambda: self.label.configure(text=ans[:60], text_color="#00ff00"))
            if os.path.exists("snap.png"): os.remove("snap.png")
        except Exception:
            self.after(0, lambda: self.label.configure(text="Local Error", text_color="red"))

if __name__ == "__main__":
    app = LocalOverlay()
    app.mainloop()
'''
    with open("local_helper.py", "w") as f:
        f.write(helper_code)

    print("[*] Everything is ready!")
    print("[*] Launching the helper in the background...")
    
    # Run the newly created helper without showing a console window
    subprocess.Popen([sys.executable, "local_helper.py"], 
                     creationflags=subprocess.CREATE_NO_WINDOW if os.name == 'nt' else 0)

if __name__ == "__main__":
    create_and_run()
