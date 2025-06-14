# MC536: Projeto 2 de Banco de Dados

## Visão Geral do Projeto
Projeto desenvolvido por:  
- Gabriel dos Santos Souza - 269844  
- Pedro Gomes Ascef - 257086 
- Rafael Attilio Agricola - 249245

Este repositório contém o trabalho 2 da disciplina MC536: Bancos de Dados. O projeto envolve o projeto, implementação e consulta a um banco de dados DuckDB para analisar relações entre:

- Unidades de conservação ambiental e ações de limpeza
- Volume e tipos de resíduos coletados em mutirões
- Eficiência de instituições organizadoras
- Distribuição geográfica (municípios e biomas) dos resíduos
- Evolução temporal das coletas

## Visão Geral do Esquema do Banco de Dados
Este projeto utiliza o DuckDB por ser uma solução moderna e otimizada para análise de dados.

Forma de Armazenamento de Ficheiros:
    O DuckDB opera num modelo serverless, onde toda a base de dados está contida num único ficheiro (ex: banco_projeto2.duckdb), o que contrasta fortemente com o modelo cliente-servidor do PostgreSQL, que exige um processo de servidor a ser executado constantemente. Para um projeto de análise de dados em que a principal interação é local, a simplicidade de gerir um único ficheiro é uma vantagem imensa, eliminando a necessidade de instalação, configuração e manutenção de um servidor, tornando a partilha da base de dados tão simples como enviar um ficheiro.

Linguagem e Processamento de Consultas:
    O DuckDB utiliza SQL padrão, o que tornou a migração das consultas do PostgreSQL direta e intuitiva. A sua principal vantagem, no entanto, é ser uma base de dados analítica (OLAP) orientada a colunas, diferentemente do PostgreSQL, o DuckDB armazena todos os valores de uma mesma coluna juntos, permitindo um desempenho muito superior em consultas analíticas, que tipicamente agregam dados de poucas colunas, por exemplo SUM(lixo_total) ou GROUP BY ano. A sua capacidade de processamento de consultas vetorizado executa operações em lotes de dados de uma vez, em vez de linha por linha, acelerando drasticamente as análises.

Processamento e Controlo de Transações:
    O DuckDB oferece suporte completo a transações ACID, garantindo a integridade dos dados. Ele utiliza um modelo de concorrência de escritor único / múltiplos leitores. Isso significa que, a qualquer momento, apenas um processo pode escrever na base de dados, enquanto vários podem ler. Para o nosso trabalho, onde a escrita é um único processo de carga de dados e a leitura são as várias consultas de análise, este modelo é perfeitamente adequado e muito menos complexo do que a gestão de múltiplos utilizadores e permissões do PostgreSQL.

Mecanismos de Recuperação e Segurança:
    A durabilidade e recuperação dos dados no DuckDB são asseguradas por um mecanismo de Write-Ahead Log (WAL). Este sistema garante que todas as modificações sejam primeiramente registadas num ficheiro de log antes de serem efetivadas no ficheiro principal da base de dados e em caso de uma falha inesperada, este log permite a restauração da base de dados ao seu último estado consistente, preservando a integridade das transações. O modelo de segurança é inerente ao sistema de ficheiros, ou seja, o controle de acesso é determinado diretamente pelas permissões do utilizador sobre o ficheiro .duckdb. Esta abordagem simplifica a gestão de segurança para projetos de análise local, em contraste com a administração de utilizadores e permissões exigida por sistemas como o PostgreSQL.

A seguir, é apresentada a visualização do modelo relacional:

**Figura 2:** Modelo Lógico  
![Modelo Lógico do Banco de Dados](modelos/modelo_logico.png)  

(O script SQL do Modelo Físico pode ser encontrado [aqui](modelos/modelo_fisico.sql)).

## Conjuntos de Dados
### Dados Originais
Os dados brutos foram obtidos do Portal de Dados Abertos do Governo, em específico do Ministério do Meio Ambiente e Mudança do Clima. Os arquivos originais usados estão no diretório `/datasets`:
- `cnuc_2022_01sem.csv`: Unidades de Conservação Ambiental.
- `dados_abertos_rios___limpos(1).csv`: Mutirões de Limpeza em Corpos Hídricos.

## Esquema do Banco de Dados

**Tabelas Normalizadas:**
- `unidade_de_conservacao`: Cadastro de áreas protegidas.
- `mutirao_limpeza`: Registro de ações coletivas.

Essas duas tabelas são resultados da desnormalização de outras 9 tabelas, para otimizar o DuckDB.


## Tecnologias Utilizadas
- **Banco de Dados:** DuckDB.
- **Linguagem:** Python 3.
- **Bibliotecas:**
  - `duckdb`: O próprio banco de dados.
  - `pandas`: Manipulação e análise de dados.
  - `jupyterlab`/`notebook`: Para executar arquivos `.ipynb`.
- **Ferramentas:** pgAdmin 4 (geração de DER).

## Estrutura do Projeto
```plaintext
Projeto-MC536/
│
├── datasets/                  # Dados originais
│   ├── cnuc_2022_01sem.csv
│   ├── dados_abertos_rios___limpos(1).csv
│
├── modelos/                    # Modelos do banco
│   ├── modelo_fisico.sql
│   ├── modelo_logico.pgerd
│   └── modelo_logico.png
│
├── resultados/                  # Resultados das consultas
│   ├── query_1.csv
│   ├── query_2.csv
│   ├── query_3.csv
│   ├── query_4.csv
│   └── query_5.csv
│
├── tratamento_e_analise_de_dados/                
│   ├── codigo_completo.ipynb
│   ├── criacao_populacao_e_queries.ipynb
│   ├── manipulacao_e_tratamento_dados.ipynb
│            
└── README.md                            
```

## Configuração e Uso
### Pré-requisitos
- **Python 3.x**: Certifique-se de que o Python 3 está instalado.
- **pip**: Instalador de pacotes do Python.
- **Bibliotecas Python necessárias**: Instale as bibliotecas necessárias com o seguinte comando:
 ```bash
 pip install jupyterlab duckdb pandas 
 ```
- **Clonar o Repositório**: Clone este repositório para sua máquina local:
 ```bash
 git clone <repository-url>
 cd Projeto2-MC536
 ```
- **Arquivos CSV**: Verifique se os arquivos CSV estão presentes no diretório `/datasets`.

---
## Execução do Código
### 1. Iniciar o Jupyter
Navegue até o diretório do repositório no terminal e execute:
```bash
jupyter lab
# ou
jupyter notebook
```
### 2. Abrir o Notebook
Abra o notebook `codigo_completo.ipynb` no Jupyter.
### 3. Executar as Células
Execute as células do notebook sequencialmente. O notebook irá:

1. Criar as tabelas com o auxílio do pandas, preparando-as para inserção no banco de dados.
2. Estabelecer conexão com o banco de dados.
3. Executar o script SQL responsável pela criação das tabelas.
4. Carregar os dados dos DataFrames gerados no código para as respectivas tabelas do banco.
5. Executar 5 consultas SQL predefinidas e exibir os resultados no notebook (utilizando pandas DataFrames).

---
## Pré-processamento dos Dados

Os notebooks `criacao_populacao_e_queries.ipynb` e `manipulacao_e_tratamento_dados.ipynb` separam as etapas de pré-processamento dos dados e a parte de criação/população do banco de dados. A execução desses notebooks é opcional, pois todo o processo está unificado em `codigo_completo.ipynb`.

---
## Análises e Consultas
As análises principais são realizadas via consultas SQL incorporadas no notebook
`database_creation_and_queries.ipynb`. As cinco consultas investigam relações entre os dados, incluindo:

1. **Proporção de Unidades de Conservação vs. Lixo Extraído por Cidade**  
   Analisa a relação entre áreas protegidas e volume de resíduos coletados em mutirões, identificando padrões de geração de lixo por município.

2. **Proporção de Lixo Extraído vs. Unidades de Conservação por Estado**  
   Compara o volume de resíduos coletados com a quantidade de áreas protegidas em cada estado, revelando padrões regionais.

3. **Análise de Resíduos por Bioma**  
   Segmenta os tipos e quantidades de lixo coletado por bioma (Amazônia, Cerrado, etc.), destacando impactos ambientais específicos.

4. **Eficiência das Instituições Organizadoras (Público vs. Privado)**  
   Avalia o desempenho de organizações (ONGs, órgãos públicos) baseado no volume total de resíduos coletados nos mutirões.

5. **Eficiência Temporal dos Mutirões de Limpeza**  
   Mede a produtividade das ações por período (lixo/minuto, resíduos/participante) e compara sazonalidades.
---
## Resultados
Os resultados gerados pelas 5 consultas analíticas são salvos como arquivos CSV no diretório `/resultados`:
- `query_1.csv`
- `query_2.csv`
- `query_3.csv`
- `query_4.csv`
- `query_5.csv`
