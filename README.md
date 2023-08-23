# Relatória Estrutura de Dados - Simulador de Autômatos

- ### Importando as bibliotecas necessárias:
~~~
import json
import time
import os
~~~~
Importa as bibliotecas necessárias para lidar com JSON, medir o tempo e realizar operações no sistema.


- ### Definindo o diretório:
~~~
  diretorio = 'C://Users//menega//Documents//TrabalhoTC/'
~~~
Define o diretório onde os arquivos serão criados.

- ### Verificando e criando o diretório:
~~~
if not os.path.exists(diretorio):
    os.makedirs(diretorio)
    print(f"Diretório '{diretorio}' criado com sucesso.")
~~~
Verifica se o diretório existe e, caso não exista, cria o diretório e exibe uma mensagem.

- ### Definindo o caminho completo do arquivo de resultado:
~~~
caminho_arquivo = os.path.join(diretorio, 'resultado.txt')
~~~
Combina o diretório e o nome do arquivo para obter o caminho completo do arquivo de resultado.

- ### Função para carregar definições de autômato a partir de um arquivo JSON:
~~~
def carregar_definicoes_automato(transition):
    if not os.path.exists(transition):
        print(f"O arquivo '{transition}' não foi encontrado.")
        return None

    try:
        with open(transition, 'r') as transition_file:
            automato_definicoes = json.load(transition_file)
            return automato_definicoes
    except json.JSONDecodeError:
        print(f"Erro ao decodificar o arquivo '{transition}'. Verifique se o arquivo está em formato JSON válido.")
        
~~~
Verifica se o arquivo de definições existe. Se sim, carrega as definições de autômato a partir do arquivo. Se não, exibe mensagens de erro.

- ### Definindo o nome do arquivo de definições:
~~~
nome_arquivo = 'transition.json'
~~~
Define o nome do arquivo onde as definições do autômato serão armazenadas.

- ### Verificando e criando o arquivo de definições se ele não existir:
~~~
if not os.path.exists(nome_arquivo):
    entrada_json = {
        # ... (definições de estado inicial, estados finais e transições)
    }

    with open(nome_arquivo, 'w') as transition_file:
        json.dump(entrada_json, transition_file, indent=4)
        print(f"Arquivo '{nome_arquivo}' criado com sucesso com a entrada JSON.")
else:
    print(f"O arquivo '{nome_arquivo}' já existe. Você pode editar manualmente sua entrada JSON nele.
~~~
Verifica se o arquivo de definições já existe. Se não existir, cria o arquivo e insere as definições em formato JSON.

- ### Carregando definições de autômato e exibindo informações:
~~~
automato_definicoes = carregar_definicoes_automato(nome_arquivo)
if automato_definicoes is not None:
    # ... (processamento das definições e exibição)
~~~
Chama a função carregar_definicoes_automato para carregar as definições de autômato e, se carregadas com sucesso, exibe informações sobre o autômato.

- ### Processando as entradas do autômato:
~~~
def processar_entradas(automato, entradas):
    resultados = []
    for entrada in entradas:
        # ... (processamento de cada entrada)
    return resultados
~~~
Define uma função que processa as entradas do autômato, executa as transições e calcula os resultados.

- ### Carregando entradas do arquivo CSV e processando:
~~~
entradas = []
with open('entrada.csv', 'r') as entradas_file:
    linhas = entradas_file.read().strip().split('\n')

for linha in linhas:
    simbolos = linha.strip().split(',')
    entradas.append(simbolos)

resultado = processar_entradas(automato, entradas)
~~~
Lê as entradas do arquivo CSV, as processa usando a função processar_entradas e armazena os resultados.

- ### Criando um arquivo de saída com os resultados:
~~~
resultado_txt = '\n'.join([f"{r['entrada']}, {r['resultadoEsperado']}, {r['resultadoObtido']}, {r['tempoExecucao']}" for r in resultado])

with open(caminho_arquivo, 'w') as resultado_file:
    resultado_file.write(resultado_txt)
~~~
Formata os resultados e escreve-os em um arquivo de saída.

- ### Exibindo mensagem de conclusão:
~~~
print(f"Arquivo '{caminho_arquivo}' criado com sucesso.")
~~~
Exibe uma mensagem indicando que o arquivo de resultados foi criado com sucesso.

