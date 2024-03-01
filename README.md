import tkinter as tk
from PIL import ImageTk, Image
import json

class program(tk.Tk):
    def __init__(self):
        super().__init__()
        self.title("Калькулятор")
        self.geometry("1000x500")
        self.configure(bg="gray")

        self.s1 = ImageTk.PhotoImage(Image.open("start.png"))
        self.s2 = ImageTk.PhotoImage(Image.open("start1.png"))
        self.s3 = ImageTk.PhotoImage(Image.open("start.png"))

        self.button = tk.Button(self, image=self.s1, relief=tk.FLAT, command=self.n_button)
        self.button.place(relx=0.5, rely=0.5, anchor=tk.CENTER)

        self.button.bind("<Enter>", self.peace)
        self.button.bind("<Leave>", self.look)

        self.history_list = []  

        self.load_history()

    def peace(self, event):
        self.button.config(image=self.s2)

    def look(self, event):
        self.button.config(image=self.s1)

    def clear_w(self):
        for widget in self.winfo_children():
            widget.destroy()

    def n_button(self):
        self.clear_w()

        c1 = ImageTk.PhotoImage(Image.open("calculate.png"))
        c2 = ImageTk.PhotoImage(Image.open("calculate1.png"))
        c3 = ImageTk.PhotoImage(Image.open("calculate.png"))
        h1 = ImageTk.PhotoImage(Image.open("history.png"))  
        h2 = ImageTk.PhotoImage(Image.open("history1.png"))  
        h3 = ImageTk.PhotoImage(Image.open("history.png"))  
        e1 = ImageTk.PhotoImage(Image.open("exit.png"))
        e2 = ImageTk.PhotoImage(Image.open("exit1.png"))
        e3 = ImageTk.PhotoImage(Image.open("exit.png"))

        button1 = tk.Button(self, image=c1, relief=tk.FLAT, command=self.calc)
        button1.place(relx=0.5, rely=0.2, anchor=tk.CENTER)

        button2 = tk.Button(self, image=h1, relief=tk.FLAT, command=self.s_history) 
        button2.place(relx=0.5, rely=0.5, anchor=tk.CENTER)

        button3 = tk.Button(self, image=e1, relief=tk.FLAT, command=self.quit)
        button3.place(relx=0.5, rely=0.8, anchor=tk.CENTER)

        button1.bind("<Enter>", lambda event: button1.config(image=c2))
        button1.bind("<Leave>", lambda event: button1.config(image=c1))
        button1.bind("<Button-1>", lambda event: button1.config(image=c3))

        button2.bind("<Enter>", lambda event: button2.config(image=h2))
        button2.bind("<Leave>", lambda event: button2.config(image=h1))
        button2.bind("<Button-1>", lambda event: button2.config(image=h3))

        button3.bind("<Enter>", lambda event: button3.config(image=e2))
        button3.bind("<Leave>", lambda event: button3.config(image=e1))
        button3.bind("<Button-1>", lambda event: button3.config(image=e3))

    def calc(self):
        self.clear_w()
        back_button = tk.Button(self, text="Back", command=self.n_button)
        back_button.place(relx=0.05, rely=0.05)

        entry = tk.Entry(self, font=('Arial', 20), justify='right')
        entry.place(relx=0.5, rely=0.1, anchor=tk.CENTER)

        button_texts = [
            ('7', 0.1, 0.4), ('8', 0.3, 0.4), ('9', 0.5, 0.4),
            ('4', 0.1, 0.5), ('5', 0.3, 0.5), ('6', 0.5, 0.5),
            ('1', 0.1, 0.6), ('2', 0.3, 0.6), ('3', 0.5, 0.6),
            ('0', 0.3, 0.7), ('+', 0.7, 0.4), ('-', 0.7, 0.5),
            ('*', 0.7, 0.6), ('/', 0.7, 0.7), ('C', 0.1, 0.7),
            ('←', 0.5, 0.7), ('=', 0.7, 0.8) 
        ]


        for (text, relx, rely) in button_texts:
            btn = tk.Button(self, text=text, font=('Arial', 15), command=lambda t=text: self.on_button_click(entry, t))
            btn.place(relx=relx, rely=rely, width=60, height=60)

    def on_button_click(self, entry, value):
        if value == '=':
            try:
                result = eval(entry.get())
                self.history_list.append(f"{entry.get()} = {result}")  
                entry.delete(0, tk.END)
                entry.insert(tk.END, str(result))
                
                self.save_history()
            except Exception as e:
                entry.delete(0, tk.END)
                entry.insert(tk.END, "Error")

            print(self.history_list)  
        elif value == 'C':
            entry.delete(0, tk.END)
        elif value == '←':
            entry.delete(len(entry.get()) - 1)
        else:
            entry.insert(tk.END, value)

    def s_history(self): 
        history_w = tk.Toplevel(self)
        history_w.title("History")

        history_t = tk.Text(history_w, height=20, width=50)
        history_t.pack()
        history_t.configure(bg="gray")

        for item in self.history_list:  
            history_t.insert(tk.END, f"{item}\n")

        history_t.config(state=tk.DISABLED)

        print(self.history_list) 

    def quit(self):
        self.save_history()
        self.destroy()

    def save_history(self):
        with open("history.json", "w") as f:
            json.dump(self.history_list, f)  

    def load_history(self):
        try:
            with open("history.json", "r") as f:
                self.history_list = json.load(f)  
        except (FileNotFoundError, json.decoder.JSONDecodeError):
            self.history_list = []

if __name__ == "__main__":
    app = program()
    app.mainloop()
