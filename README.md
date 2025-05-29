import tkinter as tk
from tkinter import messagebox, simpledialog
import os

TASKS_FILE = "tasks.txt"

def load_tasks():
    if os.path.exists(TASKS_FILE):
        with open(TASKS_FILE, "r") as file:
            for line in file:
                task_listbox.insert(tk.END, line.strip())

def save_tasks():
    with open(TASKS_FILE, "w") as file:
        for task in task_listbox.get(0, tk.END):
            file.write(task + "\n")

def add_task():
    task = task_entry.get()
    if task:
        task_listbox.insert(tk.END, task)
        task_entry.delete(0, tk.END)
        save_tasks()
    else:
        messagebox.showwarning("Input Error", "Please enter a task.")

def delete_task():
    try:
        index = task_listbox.curselection()
        task_listbox.delete(index)
        save_tasks()
    except:
        messagebox.showwarning("Selection Error", "Please select a task to delete.")

def mark_done():
    try:
        index = task_listbox.curselection()[0]
        task = task_listbox.get(index)
        if not task.startswith("✅ "):
            task_listbox.delete(index)
            task_listbox.insert(index, "✅ " + task)
            save_tasks()
    except:
        messagebox.showwarning("Selection Error", "Please select a task to mark as done.")

def edit_task():
    try:
        index = task_listbox.curselection()[0]
        current_task = task_listbox.get(index)
        new_task = simpledialog.askstring("Edit Task", "Modify task:", initialvalue=current_task.replace("✅ ", ""))
        if new_task:
            # Keep ✅ if task was marked done
            if current_task.startswith("✅ "):
                new_task = "✅ " + new_task
            task_listbox.delete(index)
            task_listbox.insert(index, new_task)
            save_tasks()
    except:
        messagebox.showwarning("Selection Error", "Please select a task to edit.")

# GUI Setup
root = tk.Tk()
root.title("To-Do List")
root.geometry("450x500")
root.configure(bg="#e9f5f9")

# Title
title = tk.Label(root, text="To-Do List", font=("Helvetica", 20, "bold"), bg="#e9f5f9", fg="#333")
title.pack(pady=20)

# Input Frame
input_frame = tk.Frame(root, bg="#e9f5f9")
input_frame.pack()

task_entry = tk.Entry(input_frame, font=("Helvetica", 14), width=30, bd=2)
task_entry.grid(row=0, column=0, padx=5)

add_btn = tk.Button(input_frame, text="Add", font=("Helvetica", 12), bg="#5cb85c", fg="white", command=add_task)
add_btn.grid(row=0, column=1)

# Listbox
task_listbox = tk.Listbox(root, font=("Helvetica", 14), width=40, height=12, selectbackground="#a3c2f2", bd=0)
task_listbox.pack(pady=15)

# Buttons Frame
btn_frame = tk.Frame(root, bg="#e9f5f9")
btn_frame.pack()

mark_btn = tk.Button(btn_frame, text="Mark as Done", font=("Helvetica", 12), bg="#0275d8", fg="white", width=14, command=mark_done)
mark_btn.grid(row=0, column=0, padx=10)

edit_btn = tk.Button(btn_frame, text="Edit Task", font=("Helvetica", 12), bg="#f0ad4e", fg="white", width=14, command=edit_task)
edit_btn.grid(row=0, column=1, padx=10)

del_btn = tk.Button(btn_frame, text="Delete Task", font=("Helvetica", 12), bg="#d9534f", fg="white", width=14, command=delete_task)
del_btn.grid(row=0, column=2, padx=10)

# Load tasks on start
load_tasks()

root.mainloop()

