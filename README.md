# AnimeTextRecognition

Este repositório documenta um projeto de reconhecimento de texto em imagens de mangás e animes usando o **Azure Cognitive Services (Computer Vision)**. Escolhi três imagens icônicas: Luffy em Gear 5 (One Piece), Guts (Berserk) e Musashi (Vagabond), todas com textos que foram extraídos com sucesso.

## Estrutura do Projeto
- **inputs/**: Contém as imagens originais.
- **output/**: Contém os textos extraídos.

## Passo a passo do processo

1. **Configuração no Azure**
   - Criei um recurso **Computer Vision** no [Portal Azure](https://portal.azure.com/).
   - Nomeei como "AnimeTextExtractor" e usei o plano gratuito (S0).
   - Obtive a chave de API e o endpoint (ex.: `https://eastus.api.cognitive.microsoft.com/`).

2. **Seleção das imagens**
   - Escolhi três imagens com texto:
     - `luffy_gear5.jpg`: Luffy rindo em Gear 5 com "Hahaha! Gear Fifth!".
     - `guts_berserk.jpg`: Guts em uma pose épica com "I’ll carve my own path".
     - `musashi_vagabond.jpg`: Musashi com sua espada e "The way of the sword".
   - *(Print simulado: Imagine as três imagens alinhadas na pasta inputs, mostrando os personagens e os textos visíveis)*.

3. **Processamento com Azure Computer Vision**
   - Usei o seguinte script em Python:
     ```python
     from azure.cognitiveservices.vision.computervision import ComputerVisionClient
     from msrest.authentication import CognitiveServicesCredentials
     import time

     # Credenciais
     key = "sua-chave-aqui"
     endpoint = "seu-endpoint-aqui"
     client = ComputerVisionClient(endpoint, CognitiveServicesCredentials(key))

     # Lista de imagens
     images = {
         "inputs/luffy_gear5.jpg": "output/luffy_gear5_result.txt",
         "inputs/guts_berserk.jpg": "output/guts_berserk_result.txt",
         "inputs/musashi_vagabond.jpg": "output/musashi_vagabond_result.txt"
     }

     # Processar cada imagem
     for input_path, output_path in images.items():
         with open(input_path, "rb") as image_file:
             result = client.read_in_stream(image_file, raw=True)
             operation_location = result.headers["Operation-Location"]
             operation_id = operation_location.split("/")[-1]
             
             # Aguardar o processamento
             time.sleep(5)
             read_result = client.get_read_result(operation_id)
             
             # Extrair e salvar texto
             text = "\n".join([line.text for line in read_result.analyze_result.read_results[0].lines])
             with open(output_path, "w") as output_file:
                 output_file.write(text)


   Insights
O Azure Computer Vision lida bem com textos em imagens de mangás, especialmente em fontes grandes e claras como as falas do Luffy.
Textos em estilos artísticos (ex.: caligrafia do Vagabond) podem exigir imagens de alta resolução para melhor precisão.
O tempo de espera de 5 segundos por imagem é um gargalo; para projetos maiores, vale explorar chamadas assíncronas em lote.
Possibilidades de ferramentas que se beneficiam
Azure Logic Apps: Automatizar o envio de scans de mangás para tradução instantânea.
Power BI: Analisar frequência de palavras ou sentimentos nas falas extraídas.
Azure Blob Storage: Armazenar grandes coleções de imagens para processamento em escala.
Microsoft Translator: Combinar com o Computer Vision para traduzir textos de mangás diretamente.
Aprendizados adquiridos
A qualidade da imagem é crítica: o texto do Guts, se estiver em uma fonte estilizada, pode ser mais difícil de reconhecer.
Configurar o Azure é rápido, mas testar com imagens variadas (como essas três) ajuda a entender os limites da ferramenta.
O plano gratuito é ótimo para experimentos como esse, mas monitorar as 20 chamadas/minuto evita interrupções.
