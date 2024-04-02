import threading
import time

# Esteiras
ESTEIRA_1 = 1
ESTEIRA_2 = 0.5
ESTEIRA_3 = 0.1

# Mutex para controle de acesso
mutex = threading.Lock()

# Contadores
contagem_esteira_1 = 0
contagem_esteira_2 = 0
contagem_esteira_3 = 0


def esteira(esteira_id, tempo):
  
  global contagem_esteira_1, contagem_esteira_2, contagem_esteira_3
  while True:
    time.sleep(tempo)
    mutex.acquire()
    if esteira_id == 1:
      contagem_esteira_1 += 1
    elif esteira_id == 2:
      contagem_esteira_2 += 1
    elif esteira_id == 3:
      contagem_esteira_3 += 1
    mutex.release()


def display():
  global contagem_esteira_1, contagem_esteira_2, contagem_esteira_3
  while True:
    time.sleep(2)
    mutex.acquire()
    print(
        f"Esteira 1: {contagem_esteira_1}, Esteira 2: {contagem_esteira_2}, Esteira 3: {contagem_esteira_3}"
    )
    mutex.release()


def main():
  # Criação das threads
  thread_1 = threading.Thread(target=esteira, args=(1, ESTEIRA_1))
  thread_2 = threading.Thread(target=esteira, args=(2, ESTEIRA_2))
  thread_3 = threading.Thread(target=esteira, args=(3, ESTEIRA_3))
  thread_display = threading.Thread(target=display)

  # Início das threads
  thread_1.start()
  thread_2.start()
  thread_3.start()
  thread_display.start()
 

if __name__ == "__main__":
  main()
  name = ["esteira 1",  "esteira 2", "esteira 3"]
