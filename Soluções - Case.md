# Soluções - Case


## Item 1 - Sobre Solutions Engineering

#### 1.1 Link do vídeo com a apresentação da arquitetura sugerida:

https://youtu.be/o0vZe84xz_c


## Item  2 - Sobre a Dadosfera

#### 2.1 Os dados foram coletados via Rest api:

![Imagem 1](imagens/item2/1.png)

![Imagem 1](imagens/item2/2.png)

#### 2.2 As configurações da Api:

![Imagem 3](imagens/item2/3.png)

#### 2.3 Fonte de dados importada com sucesso e documentada:

![Imagem 4](imagens/item2/4.png)

![Imagem 5](imagens/item2/5.png)

![Imagem 6](imagens/item2/6.png)

![Imagem 7](imagens/item2/7.png)

## Item 3 - Sobre GenAI e LLMs

#### 3.1 Dados Categorizados importados e documentados:

![Imagem 1](imagens/item3/1.png)

![Imagem 2](imagens/item3/2.png)

Foi necessário uma pequena manipulação para transformação da estrutura dos dados:

import requests

    ###### URL do arquivo a ser baixado
url = "https://datasets-server.huggingface.co/first-rows?dataset=spacemanidol%2Fproduct-search-corpus&config=default&split=train"

    ###### Enviar solicitação GET para o URL
response = requests.get(url)

    ###### Verificar se a solicitação foi bem-sucedida
if response.status_code == 200:
    # Escrever o conteúdo em um arquivo
    with open('dados.json', 'wb') as file:
        file.write(response.content)
else:
    print(f"Erro ao baixar o arquivo: Status Code {response.status_code}")

import os
os.listdir()


try:
    ##### Abrindo e lendo o arquivo JSON com o encoding ISO-8859-1
    with open('dados.json', 'r', encoding='ISO-8859-1') as file:
        data = json.load(file)

    ###### Verificando o tipo dos dados e exibindo uma parte do conteúdo
    if isinstance(data, list):
        # Se os dados são uma lista, imprime os primeiros 5 elementos
        print(json.dumps(data[:5], indent=4, ensure_ascii=False))
    elif isinstance(data, dict):
    ##### Se os dados são um dicionário, imprime as chaves e uma parte dos valores
        for key, value in list(data.items())[:5]:
            print(f"{key}: {value}")
    else:
        ##### Se os dados são de outro tipo, apenas imprime o tipo
        print(f"Tipo de dados desconhecido: {type(data)}")

except json.JSONDecodeError as e:
    print(f"Erro ao decodificar o JSON: {e}")
except Exception as e:
    print(f"Erro ao ler o arquivo: {e}")


    ##### Caminho do arquivo JSON
json_file_path = 'dados.json'

    ##### Carregar os dados do JSON
with open(json_file_path, 'r', encoding='ISO-8859-1') as file:
    data = json.load(file)

    ##### Extraindo as linhas dos dados
rows = [row['row'] for row in data['rows']]

    ##### Criando um DataFrame a partir das linhas
df = pd.DataFrame(rows)

    ##### Caminho para salvar o arquivo CSV
csv_file_path = 'produtos.csv'

    ##### Salvando o DataFrame em um arquivo CSV
df.to_csv(csv_file_path, index=False)

print(f"Arquivo CSV salvo com sucesso: {csv_file_path}")






![Imagem 3](item3/3.png)

![Imagem 4](item3/4.png)

## Item  4 - Sobre SQL e Python

### 4.1 Dashboard:

![Imagem 1](imagens/item4/1.png)

### 4.2 Consultas SQL  de origem dos gráficos:

#### 4.2.1 Consultas SQL  1:

-- Conta o número total de linhas na tabela

SELECT COUNT(*) AS "TOTAL_ROWS"

-- Especifica a tabela de onde os dados são extraídos

FROM "DADOSFERA_PRD_TREINAMENTOS"."PUBLIC"."TB__O5YFRG__PROD_CAT" AS "CAT"

![Imagem 2](imagens/item4/2.png)

#### 4.2.2 Consultas SQL  2:


-- Seleciona a coluna 'PRODUCT_TYPE' da tabela especificada, convertendo para maiúsculas para padronização

SELECT UPPER("CAT"."PRODUCT_TYPE"), 

-- Conta o número de registros para cada 'PRODUCT_TYPE'

COUNT(*) AS "COUNT" 

-- Especifica a tabela de onde os dados são extraídos

FROM "DADOSFERA_PRD_TREINAMENTOS"."PUBLIC"."TB__O5YFRG__PROD_CAT" AS "CAT" 

-- Filtra para excluir a categoria 'NOT SPECIFIED'

WHERE UPPER("CAT"."PRODUCT_TYPE") <> 'NOT SPECIFIED'

-- Agrupa os resultados pela coluna 'PRODUCT_TYPE'

GROUP BY UPPER("CAT"."PRODUCT_TYPE") 

-- Ordena os resultados pela contagem em ordem decrescente
ORDER BY "COUNT" DESC 







![Imagem 2](imagens/item4/3.png)

#### 4.2.3 Consultas SQL  3:

SELECT 

    -- A cláusula CASE é usada para tratar "Clothes" e "Clothing" como a mesma categoria
    
    CASE 
        -- Verifica se a categoria é "Clothes" ou "Clothing" (considera variações de maiúsculas/minúsculas)
        
        WHEN UPPER("CAT"."CATEGORY") IN ('CLOTHES', 'CLOTHING') THEN 'CLOTHES'
        
        -- Para outras categorias, apenas converte para maiúsculas para padronização
        
        ELSE UPPER("CAT"."CATEGORY")
        
    END AS "AGGREGATED_CATEGORY", -- A categoria resultante é renomeada como "AGGREGATED_CATEGORY"
    
    COUNT(*) AS "COUNT" -- Conta o número de registros para cada categoria agrupada
    
FROM 

    "DADOSFERA_PRD_TREINAMENTOS"."PUBLIC"."TB__O5YFRG__PROD_CAT" AS "CAT" 
    -- A tabela de onde os dados são extraídos
    
GROUP BY 

    "AGGREGATED_CATEGORY" 
    
    -- Agrupa os resultados pela categoria agregada
    
ORDER BY 

    "COUNT" DESC 
    
    -- Ordena os resultados pela contagem em ordem decrescente
    
LIMIT 5

-- Limita os resultados às 5 categorias mais frequentes


![Imagem 3](imagens/item4/4.png)

#### 4.2.4 Consultas SQL  4

-- Seleciona a coluna 'COLORS' da tabela especificada, convertendo para maiúsculas para padronização

SELECT UPPER("CAT"."COLORS"), 

-- Conta o número de registros para cada cor

COUNT(*) AS "COUNT" 

-- Especifica a tabela de onde os dados são extraídos

FROM "DADOSFERA_PRD_TREINAMENTOS"."PUBLIC"."TB__O5YFRG__PROD_CAT" AS "CAT" 

-- Filtra para excluir as cores 

WHERE UPPER("CAT"."COLORS") NOT IN ('NOT SPECIFIED', 'VARIOUS','UNINFORMED','COLORFUL','VARIED','STAINLESS STEEL','SEVERAL','HEATHER','TRANSPARENT')

-- Agrupa os resultados pela coluna 'COLORS'

GROUP BY UPPER("CAT"."COLORS") 

-- Ordena os resultados pela contagem em ordem decrescente

ORDER BY "COUNT" DESC 


![Imagem 4](imagens/item4/5.png)

## Item  5 - Sobre Data Apps

### 5.1 Configuração com recursos de bibliotecas necessárias:

![Imagem 1](imagens/item5/1.png)

### 5.2 Upload dos arquivos necessários:

![Imagem 2](imagens/item5/2.png)

### 5.3 Configuração do Service:

![Imagem 3](imagens/item5/3.png)

![Imagem 4](imagens/item5/4.png)

### 5.4 Data App rodando perfeitamente:

![Imagem 5](imagens/item5/5.png)

![Imagem 6](imagens/item5/6.png)

### 5.5 Link do DataApp:

https://app-intelligence-treinamentos.dadosfera.ai/pbp-service-streamlit-50d9c6e8-bcc9-4e6143da4674-ca2d-45d3_8501/

### Qualquer dúvida estou a disposição.

![Muito Obrigado!](https://gifs.eco.br/wp-content/uploads/2022/03/gifs-animados-de-obrigado-16.gif)

