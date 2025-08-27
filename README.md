<h1 align="center">
    Projeto Vision AI
  <img src="https://cdn-icons-png.flaticon.com/512/10971/10971775.png" alt="Ideia" width="40" height="40" />

</h1>

## **Descrição**
O **Vision AI** é um projeto de **Inteligência Artificial e Visão Computacional** que processa vídeos em tempo real para identificar **pessoas, carros e bicicletas**. Utilizando o modelo **YOLOv8** e a biblioteca **OpenCV**, o sistema:

- Detecta objetos no vídeo.
- Desenha **caixas coloridas** ao redor de cada objeto.
- Adiciona **rótulos correspondentes** (Pessoa, Carro, Bicicleta).
- Mantém uma **contagem de cada tipo de objeto**.
- Calcula o **FPS (frames por segundo)**.
- Gera um **relatório CSV detalhado**.

---

## **Ideia do Projeto**
A ideia principal do Vision AI é demonstrar como modelos de IA podem ser aplicados para **monitoramento urbano, segurança, análise de tráfego e estudos de movimento em vídeos**. O projeto combina **aprendizado de máquina** com **processamento de imagens**, entregando resultados visuais claros e métricas quantitativas precisas.

---

## **Funcionalidades**
- ✅ Detecção em tempo real de **pessoas, bicicletas e carros**.  
- ✅ Desenho de **caixas coloridas e rótulos** por objeto.  
- ✅ Contagem dinâmica de objetos por frame.  
- ✅ Cálculo de **FPS real** durante o processamento.  
- ✅ Vídeo processado salvo com todas as marcações.  
- ✅ Relatório CSV com contagem detalhada.  
- ✅ Clareamento automático do vídeo para melhor visualização.  
- ✅ Possibilidade de adicionar novas classes e personalizar cores.

---

## **Benefícios**
- 📊 Visualização rápida do tráfego e movimentação de pessoas.  
- 📈 Geração de métricas úteis para **análise e monitoramento urbano**.  
- 🔒 Processamento **local**, garantindo privacidade dos dados.  
- ⚡ Flexível e escalável, permitindo inclusão de novas classes.  
- 🎓 Ideal para aprendizado e experimentação em **visão computacional e IA**.

---


## **Como Funciona**
1. O vídeo de entrada é carregado (`video2.mp4`).  
2. O modelo **YOLOv8** analisa cada frame para detectar **Pessoa, Bicicleta e Carro**.  
3. Cada objeto recebe uma **caixa colorida e rótulo** correspondente.  
4. É calculado o **FPS em tempo real**.  
5. A contagem de objetos é exibida na tela e salva em um arquivo **CSV**.  
6. O vídeo processado é salvo em `output/video_processado.mp4`.

---

## **Resultados**
- Vídeo final com **objetos destacados e contagem visível**.  
- Relatório CSV com **contagem detalhada por frame** e FPS.  
- Visualização em tempo real, permitindo **análise imediata**.

---



<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/a365e633-8beb-4ee7-a917-062753800e77" width="250" /></td>
    <td><img src="https://github.com/user-attachments/assets/f6eba08f-605f-4383-92f4-5ddedbd85e40" width="250" /></td>
    <td><img src="https://github.com/user-attachments/assets/c11879e7-c4eb-43c9-af20-a7a99b22349a" width="250" /></td>
  </tr>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/ff34b3ed-2f44-4f5a-b522-765b798d463b" width="250" /></td>
    <td><img src="https://github.com/user-attachments/assets/58ddae3d-f66c-4476-843a-07e33db4bfed" width="250" /></td>
    <td><img src="https://github.com/user-attachments/assets/82e3e4e2-dcc5-4354-93d6-e653abae8072" width="250" /></td>
  </tr>
</table>


## Descrição do Código: Vision AI - Detecção de Objetos em Vídeo
-  Importação de Bibliotecas
```bash
import cv2
from ultralytics import YOLO
import os
import csv
import time
```
- cv2 (OpenCV): Biblioteca para manipulação e processamento de imagens e vídeos.

- YOLO (Ultralytics): Modelo de detecção de objetos em tempo real.

- os: Permite criar pastas e manipular arquivos no sistema.

- csv: Para gerar relatórios em formato CSV.

- time: Para medir FPS e tempo de execução.

## Configuração Inicial
```bash 
VIDEO_ENTRADA = "video2.mp4"
VIDEO_SAIDA  = "output/video_processado.mp4"
CLASSES_ALVO = {0: "Pessoa", 1: "Bicicleta", 2: "Carro"}
os.makedirs("output", exist_ok=True)
```
- Define o vídeo de entrada e o arquivo de saída processado.

- Define quais classes serão detectadas pelo modelo (pessoa, bicicleta e carro).

- Cria a pasta output caso não exista.

###  Configuração de Vídeo
```bash
cap = cv2.VideoCapture(VIDEO_ENTRADA)
largura  = int(cap.get(cv2.CAP_PROP_FRAME_WIDTH))
altura   = int(cap.get(cv2.CAP_PROP_FRAME_HEIGHT))
fps      = cap.get(cv2.CAP_PROP_FPS)
fourcc = cv2.VideoWriter_fourcc(*"mp4v")
out = cv2.VideoWriter(VIDEO_SAIDA, fourcc, fps, (largura, altura))
``
Abre o vídeo de entrada.

Captura largura, altura e FPS do vídeo.

Configura o vídeo de saída (mp4) que será gerado após o processamento.
```

## Definição de Cores e CSV
```bash
CORES = {0: (0, 255, 0), 1: (255, 165, 0), 2: (0, 0, 255)}
csv_file = open("output/relatorio.csv", mode="w", newline="")
csv_writer = csv.writer(csv_file)
csv_writer.writerow(["Frame", "Pessoas", "Bicicletas", "Carros", "FPS"])
```
- Cada classe detectável recebe uma cor diferente para desenhar no vídeo.

- Cria um arquivo CSV para salvar contagem de objetos e FPS por frame.

## Detecção de Objetos
  ```bash
  resultados = modelo(frame_claro, verbose=False)[0]
```
- Aplica o modelo YOLO no frame.

- verbose=False evita mensagens extras no terminal
- 
## Desenhar Caixas e Contar Objetos

```bash
for box in resultados.boxes:
    cls = int(box.cls[0])
    if cls in CLASSES_ALVO:
        x1, y1, x2, y2 = map(int, box.xyxy[0])
        label = CLASSES_ALVO[cls]
        cor   = CORES[cls]

        cv2.rectangle(frame_claro, (x1, y1), (x2, y2), cor, 2)
        cv2.putText(frame_claro, label, (x1, y1-10),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.9, cor, 2)

        contagem[cls] += 1
```
- Para cada objeto detectado:

- Verifica se a classe é alvo.

- Desenha retângulo colorido ao redor do objeto.

- Adiciona rótulo (Pessoa, Bicicleta, Carro).

- Incrementa a contagem de cada objeto.

- Salvar Dados no CSV

```bash
csv_writer.writerow([frame_count, contagem[0], contagem[1], contagem[2], round(fps_real,1)])
```
### Exibir Contagem e FPS no Vídeo
```bash
contagem_texto = f"Pessoas: {contagem[0]}  |  Bicicletas: {contagem[1]}  |  Carros: {contagem[2]}"
fps_texto      = f"FPS: {fps_real:.1f}"

cv2.putText(frame_claro, contagem_texto, (10, altura-50),
            cv2.FONT_HERSHEY_SIMPLEX, 1.2, (255,255,255), 3)

cv2.putText(frame_claro, fps_texto, (10, altura-20),
            cv2.FONT_HERSHEY_SIMPLEX, 0.7, (200,200,200), 2)
```
- Mostra contagem organizada no canto inferior esquerdo.

- Exibe FPS de forma discreta.
## Finalização
```bash
cap.release()
out.release()
cv2.destroyAllWindows()
csv_file.close()

print(f"✅ Vídeo processado salvo em: {VIDEO_SAIDA}")
print("✅ Relatório detalhado salvo em: output/relatorio.csv")
``
