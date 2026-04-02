import tkinter as tk
from tkinter import scrolledtext, filedialog, messagebox
import subprocess


#Run command function
def run_command(command):
    try:
        output_box.delete(1.0, tk.END)
        output_box.insert(tk.END, f"Running: {' '.join(command)}\n\n")
        output_box.update()
        result = subprocess.run(command, capture_output=True, text=True)
        output_box.insert(tk.END, result.stdout)
        if result.stderr:
            output_box.insert(tk.END, "\nErrors:\n" + result.stderr)
    except Exception as e:
        output_box.insert(tk.END, f"Error: {e}\n")


# Network actions
def ping():
    domain = url_entry.get().strip()
    if domain:
        run_command(["ping", "-n", "4", domain])
    else:
        messagebox.showwarning("Input Error", "Please enter a domain or URL.")


def tracert():
    domain = url_entry.get().strip()
    if domain:
        run_command(["tracert", domain])
    else:
        messagebox.showwarning("Input Error", "Please enter a domain or URL.")


def nslookup():
    domain = url_entry.get().strip()
    if domain:
        run_command(["nslookup", domain])
    else:
        messagebox.showwarning("Input Error", "Please enter a domain or URL.")


# Save output
def save_results():
    file_path = filedialog.asksaveasfilename(
        defaultextension=".txt",
        filetypes=[("Text files", "*.txt"), ("All files", "*.*")]
    )
    if file_path:
        with open(file_path, "w") as file:
            file.write(output_box.get("1.0", tk.END))
        messagebox.showinfo("Saved", "Results saved successfully!")


# GUI setup
window = tk.Tk()
window.title("Network Toolkit")
window.geometry("900x500")
window.config(bg="white")


# URL entry
url_label = tk.Label(window, text="Enter a URL of interest:", font=("Comic Sans MS", 12), bg="white")
url_label.pack(pady=(10, 5))
url_entry = tk.Entry(window, width=50, font=("Comic Sans MS", 11))
url_entry.insert(0, "localhost")
url_entry.pack(pady=(0, 10))


# Buttons
button_frame = tk.Frame(window, bg="white")
button_frame.pack(pady=10)


btn_style = {
    "font": ("comic sans", 9),
    "bg": "skyblue",
    "fg": "white",
    "relief": "flat",
    "width": 25,
    "cursor": "heart",
    "bd": 0,
    "activebackground": "gray"
}


tk.Button(button_frame, text="Check if URL is up and active", command=ping, **btn_style).grid(row=0, column=0, padx=5, pady=5)
tk.Button(button_frame, text="Trace route to website", command=tracert, **btn_style).grid(row=0, column=1, padx=5, pady=5)
tk.Button(button_frame, text="Check the IP address of a website", command=nslookup, **btn_style).grid(row=1, column=0, padx=5, pady=5)
tk.Button(button_frame, text="Save Results", command=save_results, **btn_style).grid(row=1, column=1, padx=5, pady=5)


# Output Box
output_box = scrolledtext.ScrolledText(window, width=100, height=20, font=("Consolas", 10))
output_box.pack(padx=10, pady=10)


window.mainloop()


