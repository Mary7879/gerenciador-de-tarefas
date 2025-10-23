import tkinter as tk
from tkinter import messagebox
import json
import os

class TaskManagerApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Gerenciador de Tarefas")
        self.root.geometry("500x600")
        self.root.configure(bg="#f0f2f5")

        # Lista de tarefas
        self.tasks = self.load_tasks()

        # Título
        self.title_label = tk.Label(
            root, text="Gerenciador de Tarefas", font=("Arial", 16, "bold"), bg="#f0f2f5", fg="#333"
        )
        self.title_label.pack(pady=10)

        # Campo de entrada
        self.task_input = tk.Entry(root, font=("Arial", 12), width=40)
        self.task_input.pack(pady=10)

        # Botão para adicionar tarefa
        self.add_button = tk.Button(
            root, text="Adicionar Tarefa", font=("Arial", 12), bg="#667eea", fg="white", command=self.add_task
        )
        self.add_button.pack(pady=5)

        # Lista de tarefas (com scrollbar)
        self.task_frame = tk.Frame(root, bg="#f0f2f5")
        self.task_frame.pack(pady=10, padx=10, fill="both", expand=True)

        self.canvas = tk.Canvas(self.task_frame, bg="#f0f2f5")
        self.scrollbar = tk.Scrollbar(self.task_frame, orient="vertical", command=self.canvas.yview)
        self.scrollable_frame = tk.Frame(self.canvas, bg="#f0f2f5")

        self.scrollable_frame.bind(
            "<Configure>",
            lambda e: self.canvas.configure(scrollregion=self.canvas.bbox("all"))
        )

        self.canvas.create_window((0, 0), window=self.scrollable_frame, anchor="nw")
        self.canvas.configure(yscrollcommand=self.scrollbar.set)

        self.canvas.pack(side="left", fill="both", expand=True)
        self.scrollbar.pack(side="right", fill="y")

        # Renderizar tarefas iniciais
        self.render_tasks()

        # Vincular Enter ao adicionar tarefa
        self.task_input.bind("<Return>", lambda event: self.add_task())

    def load_tasks(self):
        """Carrega tarefas de um arquivo JSON, se existir."""
        try:
            if os.path.exists("tasks.json"):
                with open("tasks.json", "r") as file:
                    return json.load(file)
            return []
        except json.JSONDecodeError:
            return []

    def save_tasks(self):
        """Salva tarefas no arquivo JSON."""
        with open("tasks.json", "w") as file:
            json.dump(self.tasks, file, indent=4)

    def add_task(self):
        """Adiciona uma nova tarefa."""
        task_text = self.task_input.get().strip()
        if task_text:
            self.tasks.append({"text": task_text, "completed": False})
            self.task_input.delete(0, tk.END)
            self.save_tasks()
            self.render_tasks()
        else:
            messagebox.showwarning("Aviso", "Digite uma tarefa!")

    def toggle_task(self, index):
        """Marca/desmarca uma tarefa como concluída."""
        self.tasks[index]["completed"] = not self.tasks[index]["completed"]
        self.save_tasks()
        self.render_tasks()

    def delete_task(self, index):
        """Exclui uma tarefa."""
        if messagebox.askyesno("Confirmar", "Deseja excluir esta tarefa?"):
            self.tasks.pop(index)
            self.save_tasks()
            self.render_tasks()

    def render_tasks(self):
        """Renderiza a lista de tarefas na interface."""
        # Limpar frame atual
        for widget in self.scrollable_frame.winfo_children():
            widget.destroy()

        # Renderizar cada tarefa
        for i, task in enumerate(self.tasks):
            task_frame = tk.Frame(self.scrollable_frame, bg="#f9f9f9", bd=1, relief="solid")
            task_frame.pack(fill="x", padx=5, pady=5)

            # Checkbox para marcar como concluído
            chk_state = tk.BooleanVar(value=task["completed"])
            chk = tk.Checkbutton(
                task_frame,
                variable=chk_state,
                command=lambda idx=i: self.toggle_task(idx),
                bg="#f9f9f9",
                activebackground="#f9f9f9"
            )
            chk.pack(side="left", padx=5)

            # Texto da tarefa
            text_style = "Arial 12"
            if task["completed"]:
                text_style = "Arial 12 overstrike"
            task_label = tk.Label(
                task_frame, text=task["text"], font=text_style, bg="#f9f9f9", fg="#333"
            )
            task_label.pack(side="left", padx=5)

            # Botão de excluir
            delete_button = tk.Button(
                task_frame,
                text="🗑️",
                font=("Arial", 10),
                bg="#dc3545",
                fg="white",
                command=lambda idx=i: self.delete_task(idx)
            )
            delete_button.pack(side="right", padx=5)

if __name__ == "__main__":
    root = tk.Tk()
    app = TaskManagerApp(root)
    root.mainloop()
