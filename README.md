# Exercicio_IA3_Bootcamp
# AzureTextRecognition

Este repositório contém um projeto de reconhecimento de texto em imagens usando o **Azure Cognitive Services (Computer Vision)**. O objetivo foi extrair texto de imagens variadas e armazenar os resultados, como parte de um exercício prático de IA com Azure.

## Estrutura do Projeto
- **inputs/**: Pasta com as imagens de entrada.
- **output/**: Pasta com os resultados de texto extraídos.

## Passo a passo do processo

1. **Configuração no Azure**
   - Criei um recurso **Computer Vision** no [Portal Azure](https://portal.azure.com/).
   - Nomeei como "TextRecognitionResource" e usei o plano gratuito (S0), que oferece até 20 chamadas por minuto.
   - Copiei a chave de API e o endpoint (ex.: `https://eastus.api.cognitive.microsoft.com/`).

2. **Preparação das imagens**
   - Escolhi 3 imagens com textos distintos e salvei na pasta `inputs`:
     - `text1.jpg`: Placa de boas-vindas.
     - `text2.jpg`: Trecho de livro.
     - `text3.jpg`: Recibo.
   - *(Print simulado: Imagine uma captura das 3 imagens lado a lado na pasta inputs)*.

3. **Código para reconhecimento**
   - Usei Python com a biblioteca `azure-cognitiveservices-vision-computervision`:
     ```python
     from azure.cognitiveservices.vision.computervision import ComputerVisionClient
     from msrest.authentication import CognitiveServicesCredentials

     # Credenciais
     key = "sua-chave-aqui"
     endpoint = "seu-endpoint-aqui"
     client = ComputerVisionClient(endpoint, CognitiveServicesCredentials(key))

     # Processar imagens
     images = ["inputs/text1.jpg", "inputs/text2.jpg", "inputs/text3.jpg"]
     for i, image_path in enumerate(images, 1):
         with open(image_path, "rb") as image_file:
             result = client.read_in_stream(image_file, raw=True)
             operation_location = result.headers["Operation-Location"]
             operation_id = operation_location.split("/")[-1]
             
             # Aguardar resultado
             from time import sleep
             sleep(5)
             read_result = client.get_read_result(operation_id)
             
             # Salvar texto extraído
             text = "\n".join([line.text for line in read_result.analyze_result.read_results[0].lines])
             with open(f"output/text{i}_result.txt", "w") as output_file:
                 output_file.write(text)
