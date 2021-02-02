# testedataengineer
#Tarefa Data Engineer Júnior:

Desafio: Realizar a normalização de um arquivo .txt contendo objetos json agrupados por linha.

Abordagem: Utilizei bibliotecas de expressões regulares (Regex) em python para formatar os dados de uma forma que possam ser consultados e trabalhados, dividindo-os em 2 tabelas: As relevantes para a tarefa e as estáticas que se repetem em todos os objetos JSON do arquivo.

Primeiro foi realizada a leitura crua do arquivo em .txt:

``` python
import pandas as pd
import re
import sqlite3

df= pd.read_csv('D:/dev/python/logs.txt', header=None)
df.head()
```

Após verificar que o pandas já abre o arquivo definindo relativamente bem os limites de colunas (são pontuadas 54), optei por utilizar regex ao formatar o arquivo. Comecei removendo os colchetes:

```python
df = dfcustom.replace('["{}]', '', regex=True)
```

Ainda sobraram caracteres desnecessários nas colunas. Utilizei então um Regex um pouco mais sofisticado para remover todos os caracteres que estivessem posicionados **antes** do caractere "**:**"

``` python
df = df.replace('^.*?:','', regex=True)
```
Feita a limpeza, separei os dados em 2 tabelas distintas usando o recurso *iloc* tanto para a tabela de dados estáticos (nomeada `request_table`) quanto para a tabela de dados dinâmicos (nomeada `response_table`):

```python
#tabela request
request_table = df.iloc[:,[0,1,2,3,4,5,7]]
request_table.head(3)

#tabela response
response_table= regexed.iloc[:,[9,10,11,18,19,34,39,40,45,46,47,48,49,50,51]]

```

Criadas as tabelas, utilizei o comando ```python to_csv()``` para exportar os dados em csv e processá-los em um servidor na nuvem, visto que minha máquina não teve RAM suficiente para rodar uma instância do `sqlite`na memória. Feito o carregamento, utilizei o software Aqua Data Studio para importar o CSV  `response_table` e realizar os comandos de Select abaixo:


``` SQL

--Query simples para trazer os números de requisições por consumidor

SELECT ID_REQUEST, COUNT(*) AS NUM_REQUISICOES FROM RESPONSE_TABLE
GROUP BY ID_REQUEST
ORDER BY 2 DESC

--Query para trazer todas requisições por id de serviço

SELECT ID_SERVICO, COUNT (*) as NUM_REQUISICOES FROM RESPONSE_TABLE
GROUP BY ID_SERVICO
ORDER BY 2 DESC

--Query para realizar as médias de requests, proxy e kong

SELECT ID_SERVICO, AVG(RETRIES) AS MEDIA_TENTATIVAS, AVG(PROXY) AS MEDIA_PROXY, AVG(KONG) AS MEDIA_KONG FROM RESPONSE_TABLE
GROUP BY ID_SERVICO
ORDER BY 3 DESC
```
Os dados agrupados das queries acima se encontram disponíveis nos arquivos CSV deste repositório.



