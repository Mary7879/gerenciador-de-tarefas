def mostrar_tarefas(tarefas):
    if not tarefas:
        print("Nenhuma tarefa na lista.")
    else:
        print("Lista de Tarefas:")
        for i, tarefa in enumerate(tarefas, 1):
            print(f"{i}. {tarefa}")

def adicionar_tarefa(tarefas):
    nova_tarefa = input("Digite a nova tarefa: ")
    tarefas.append(nova_tarefa)
    print(f"Tarefa '{nova_tarefa}' adicionada com sucesso!")

def remover_tarefa(tarefas):
    mostrar_tarefas(tarefas)
    indice = int(input("Digite o número da tarefa que deseja remover: ")) - 1
    if 0 <= indice < len(tarefas):
        tarefa_removida = tarefas.pop(indice)
        print(f"Tarefa '{tarefa_removida}' removida com sucesso!")
    else:
        print("Índice inválido.")

def main():
    tarefas = []
    while True:
        print("\nMenu:")
        print("1. Mostrar tarefas")
        print("2. Adicionar tarefa")
        print("3. Remover tarefa")
        print("4. Sair")
        escolha = input("Escolha uma opção: ")

        if escolha == '1':
            mostrar_tarefas(tarefas)
        elif escolha == '2':
            adicionar_tarefa(tarefas)
        elif escolha == '3':
            remover_tarefa(tarefas)
        elif escolha == '4':
            print("Saindo do programa...")
            break
        else:
            print("Opção inválida. Tente novamente.")

if __name__ == "__main__":
    main()
