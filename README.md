# Relatória Estrutura de Dados - Simulador de Autômatos

- ### Importação dos Módulos: As primeiras linhas importam os módulos necessários para o funcionamento do código: json, csv, sys e time.
~~~
Importação dos módulos necessários
import json
import csv
import sys
import time

 Definição de uma função para carregar os dados do autômato a partir de um arquivo JSON
def carregar_automato(caminho_arquivo):
    # Abertura do arquivo no modo de leitura ('r') usando o gerenciador de contexto 'with'
    with open(caminho_arquivo, 'r') as arquivo:
        # A função json.load() é usada para carregar os dados do arquivo JSON no formato de dicionário
        dados_automato = json.load(arquivo)
~~~
No geral a função acima, é responsável por carregar os dados do autômato de um arquivo JSON, que são usados posteriormente no restante do código para executar as operações necessárias, como a conversão de AFND para AFD e o processamento dos casos de teste.

- ### Essa função é responsável por carregar os casos de teste a partir de um arquivo CSV, criando uma lista de listas onde cada sublista representa um caso de teste com seus valores correspondentes. Esses casos de teste serão usados posteriormente para processamento no código.
~~~
# Definição de uma função para carregar casos de teste a partir de um arquivo CSV
def carregar_casos_teste(caminho_arquivo):
    # Criação de uma lista vazia para armazenar os casos de teste
    casos_teste = []
    
    # Abertura do arquivo no modo de leitura ('r') usando o gerenciador de contexto 'with'
    with open(caminho_arquivo, 'r') as arquivo:
        # O módulo csv é usado para criar um objeto leitor (csv.reader) que lê linhas do arquivo CSV
        leitor = csv.reader(arquivo)
        
        # Loop for que percorre cada linha no arquivo CSV
        for linha in leitor:
            # Cada linha lida é uma lista de strings (campos do CSV)
            # Essa lista de strings (linha) é adicionada à lista de casos_teste
            casos_teste.append(linha)
    
    # Retorna a lista de casos_teste, onde cada caso é representado como uma lista de strings
    return casos_teste
~~~

- ### No restante do código, essas estruturas serão usadas para converter o autômato AFND em um autômato AFD, aplicando o algoritmo de conversão por subconjunto.
~~~
# Definição de uma função para converter um AFND em um AFD
def converter_afnd_para_afd(dados_afnd):
    # Extração dos dados do autômato AFND
    estados_afnd = dados_afnd['estados']
    alfabeto_afnd = dados_afnd['alfabeto']
    transicoes_afnd = dados_afnd['transicoes']
    estado_inicial_afnd = dados_afnd['estado_inicial']
    estados_aceitacao_afnd = dados_afnd['estados_aceitacao']

    # Inicialização de estruturas para o autômato AFD
    estados_afd = []  # Lista de conjuntos de estados do AFD
    transicoes_afd = {}  # Dicionário de transições do AFD
    fila_novos_estados = []  # Fila para processar novos estados do AFD
~~~

- ### No final deste processo, os dados do autômato AFD serão armazenados nas estruturas estados_afd, transicoes_afd e estados_aceitacao_afd, que são usadas posteriormente para o processamento dos casos de teste e a escrita dos resultados.
~~~
# Criar o conjunto inicial de estados do AFD com o estado inicial do AFND
    estado_inicial_afd = frozenset([estado_inicial_afnd])
    estados_afd.append(estado_inicial_afd)
    fila_novos_estados.append(estado_inicial_afd)

    # Enquanto houver novos estados a serem processados na fila
    while fila_novos_estados:
        # Retira o próximo estado da fila para processamento
        estado_atual_afd = fila_novos_estados.pop(0)

        # Inicializa as transições para o estado atual do AFD
        transicoes_afd[estado_atual_afd] = {}

        # Itera por cada símbolo do alfabeto do AFND
        for caractere in alfabeto_afnd:
            novo_estado_afd = set()

            # Itera por cada estado do conjunto atual no AFD
            for estado_afnd in estado_atual_afd:
                # Verifica se há uma transição no AFND para o caractere atual
                if caractere in transicoes_afnd[estado_afnd]:
                    # Atualiza o novo conjunto de estados do AFD
                    novo_estado_afd.update(transicoes_afnd[estado_afnd][caractere])

            # Cria um frozenset a partir do novo conjunto de estados
            novo_estado_afd = frozenset(novo_estado_afd)

            # Se o novo estado não é vazio e não foi adicionado anteriormente
            if novo_estado_afd and novo_estado_afd not in estados_afd:
                estados_afd.append(novo_estado_afd)
                fila_novos_estados.append(novo_estado_afd)

            # Adiciona a transição ao dicionário de transições do AFD
            transicoes_afd[estado_atual_afd][caractere] = novo_estado_afd

    # Calcula os estados de aceitação do AFD
    estados_aceitacao_afd = [estado_afd for estado_afd in estados_afd if estado_afd.intersection(estados_aceitacao_afnd)]
~~~

- ### Essa parte do código é responsável por consolidar os resultados da conversão do AFND para AFD em um dicionário dados_afd, que é retornado como saída da função. Os dados contidos nesse dicionário representam o autômato AFD resultante e são usados mais tarde para o processamento dos casos de teste e a escrita dos resultados.
~~~
# Montar os dados do AFD resultante
    dados_afd = {
        'estados': estados_afd,
        'alfabeto': alfabeto_afnd,
        'transicoes': transicoes_afd,
        'estado_inicial': estado_inicial_afd,
        'estados_aceitacao': estados_aceitacao_afd
    }

    return dados_afd
~~~

- ### Essa função é responsável por processar os casos de teste no autômato fornecido e comparar os resultados obtidos com os resultados esperados, armazenando-os em uma lista de resultados. Essa lista de resultados será usada posteriormente para escrever os resultados em um arquivo de saída.
~~~
# Definição de uma função para processar os casos de teste no autômato fornecido
def processar_testes(automato, casos_teste):
    resultados = []
    for caso in casos_teste:
        entrada, resultado_esperado = caso
        estado_atual = automato['estado_inicial']
        for caractere in entrada:
            if caractere in automato['alfabeto']:
                estado_atual = automato['transicoes'][estado_atual][caractere]

        resultado = 'aceita' if estado_atual in automato['estados_aceitacao'] else 'rejeita'
        resultados.append([entrada, resultado_esperado, resultado])
    return resultados
~~~

- ### Essa parte do código trata da função para escrever os resultados em um arquivo de saída e inclui um trecho condicional que verifica se o script está sendo executado diretamente e se os argumentos da linha de comando estão corretos. Se não estiverem, uma mensagem de uso é exibida, e o script é encerrado com um código de saída de 1 para indicar uma saída anormal.
~~~
# Definição de uma função para escrever os resultados em um arquivo de saída
def escrever_resultados(caminho_arquivo, resultados):
    with open(caminho_arquivo, 'w') as arquivo:
        for resultado in resultados:
            arquivo.write(';'.join(map(str, resultado)) + '\n')

# Trecho condicional que é executado quando este script é executado diretamente
if __name__ == "__main__":
    if len(sys.argv) != 4:
        print("Uso: python ferramenta.py arquivo_do_automato.aut arquivo_de_testes.in arquivo_de_saida.out")
        sys.exit(1)
~~~

- ### Essa parte do código realiza a sequência completa de operações, desde a obtenção dos nomes dos arquivos de entrada e saída até o carregamento dos dados, conversão do autômato AFND para AFD, processamento dos casos de teste, gravação dos resultados e exibição de uma mensagem de conclusão.
~~~
# Obter os nomes dos arquivos a partir dos argumentos da linha de comando
    arquivo_automato = sys.argv[1]
    arquivo_casos_teste = sys.argv[2]
    arquivo_saida = sys.argv[3]

    # Carregar os dados do autômato e os casos de teste
    dados_afnd = carregar_automato(arquivo_automato)
    casos_teste = carregar_casos_teste(arquivo_casos_teste)

    # Converter AFND para AFD
    dados_afd = converter_afnd_para_afd(dados_afnd)

    # Processar os casos de teste no autômato convertido
    resultados = processar_testes(dados_afd, casos_teste)

    # Escrever os resultados no arquivo de saída
    escrever_resultados(arquivo_saida, resultados)

    print("Processamento concluído. Resultados gravados em", arquivo_saida)
~~~











