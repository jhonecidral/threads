import threading
import time 
import os

# Variáveis globais para contagem e peso total
counters = [0, 0, 0] 
total_weight = 0  s
mutex = threading.Lock()  
stop_flag = False  
paused = False

# Função para calcular o peso total a cada 1500 unidades
def update_total_weight():
    global total_weight
    while True:
        time.sleep(1)
        if sum(counters) >= 1500:
            with mutex:
                total_weight = sum(counters) * (5 + 2 + 0.5)  
            print("Peso total atualizado:", total_weight, "kg")
            for i in range(3):
                counters[i] = 0

# Função para simular a contagem de itens em cada esteira
def count_items(esteira_index, delay):
    global counters, stop_flag, paused
    while not stop_flag:
        if paused:
            time.sleep(0.1)
            continue
        time.sleep(delay)
        with mutex:
            counters[esteira_index] += 1
            print("Esteira", esteira_index + 1, "- Contagem atual:", counters[esteira_index])

# Função para atualizar o display a cada 2 segundos
def update_display():
    while not stop_flag:
        if paused:
            time.sleep(0.1)
            continue
        time.sleep(2)
        with mutex:
            print("Total de itens contados:", sum(counters))

# Função para aguardar entrada do operador e pausar/retomar a contagem
def wait_for_input():
    global stop_flag, paused
    while True:
        input("Pressione Enter para pausar/retomar a contagem...")
        paused = not paused

# Função principal
def main():
    # Iniciar threads para contagem de itens em cada esteira
    threads = []
    for i in range(3):
        if i == 0:
            t = threading.Thread(target=count_items, args=(i, 1))
        elif i == 1:
            t = threading.Thread(target=count_items, args=(i, 0.5))
        else:
            t = threading.Thread(target=count_items, args=(i, 0.1))
        threads.append(t)
        t.start()

    # Iniciar thread para atualizar o peso total
    total_weight_thread = threading.Thread(target=update_total_weight)
    total_weight_thread.start()

    # Iniciar thread para atualizar o display
    display_thread = threading.Thread(target=update_display)
    display_thread.start()

    # Thread para aguardar entrada do operador e pausar/retomar a contagem
    input_thread = threading.Thread(target=wait_for_input)
    input_thread.start()

    # Aguardar finalização de todas as threads
    input_thread.join()
    for t in threads:
        t.join()
    total_weight_thread.join()
    display_thread.join()

if _name_ == "_main_":
    main()
