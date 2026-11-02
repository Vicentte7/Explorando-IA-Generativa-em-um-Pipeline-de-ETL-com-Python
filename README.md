Pipeline ETL com Python, MongoDB e Google Gemini
Este projeto foi desenvolvido como parte do desafio de Engenharia de Dados da DIO (Digital Innovation One), tendo como objetivo a implementação de um pipeline de ETL (Extract, Transform, Load) completo em Python, integrando fontes de dados locais, banco de dados NoSQL na nuvem e Inteligência Artificial generativa.

Objetivo do Projeto
Simular um fluxo bancário automatizado onde IDs de correntistas são extraídos de uma planilha CSV local, cruzados com dados estruturados armazenados no MongoDB Atlas, enriquecidos com mensagens personalizadas de incentivo a investimentos geradas pelo Google Gemini, e consolidados de volta na base de dados.

Tecnologias Utilizadas
Linguagem: Python 3

Manipulação de Dados: pandas

Banco de Dados NoSQL: pymongo (MongoDB Atlas)

Inteligência Artificial: google-genai (Google Gemini API)

Arquitetura do Pipeline (ETL)
1. Extract (Extração)
Planilha Local (UserID.csv): O script lê uma lista de identificadores (UserID) utilizando a biblioteca pandas.

MongoDB: Conecta ao cluster do MongoDB Atlas e busca os documentos dos clientes correspondentes na coleção clientes, filtrando apenas os usuários válidos e ignorando IDs inexistentes.

2. Transform (Transformação)
Os nomes dos correntistas resgatados são enviados como prompt para o modelo de linguagem Gemini (gemini-3.8-flash).

O prompt instrui a IA a atuar como um consultor bancário, criando uma frase curta (até 100 caracteres) e personalizada sobre a relevância de investir.

O script conta com tratamento de exceções (try/except) e tempo limite de requisição (timeout), além de uma mensagem padrão de contingência para garantir a resiliência do pipeline em casos de indisponibilidade momentânea ou instabilidade de rede.

O texto gerado é formatado e anexado à lista news do perfil do usuário em memória.

3. Load (Carregamento)
Utiliza o método update_one do pymongo referenciando o identificador nativo _id de cada documento.

Com o operador $set, o pipeline grava a lista news atualizada diretamente no MongoDB sem sobrescrever as informações de contas e cartões já existentes.

Como Executar o Projeto
Pré-requisitos
Python instalado na máquina.

Uma conta ativa no MongoDB Atlas com cluster configurado.

Uma chave de API válida do Google Gemini.

Instalação de Dependências
Bash
pip install pandas pymongo google-genai
Configuração de Variáveis de Ambiente
Recomenda-se exportar as credenciais como variáveis de ambiente no terminal antes de executar o script:

No Windows (PowerShell):

PowerShell
$env:MONGODB_URI="sua_string_de_conexao_mongodb"
$env:GEMINI_API_KEY="sua_chave_de_api_gemini"
No Linux/macOS:

Bash
export MONGODB_URI="sua_string_de_conexao_mongodb"
export GEMINI_API_KEY="sua_chave_de_api_gemini"
Execução
Certifique-se de que o arquivo UserID.csv está no mesmo diretório do script e execute:

Bash
python 8_Pipeline_ETL_com_Python.py
Estrutura de Dados (Exemplo no MongoDB)
JSON
{
  "_id": { "$oid": "..." },
  "id": 4,
  "name": "Pyterson",
  "account": { "number": "00001-1", "agency": "0001", "balance": 0, "limit": 500 },
  "card": { "number": "**** **** **** 1111", "limit": 1000 },
  "news": [
    {
      "icon": "https://digitalinnovationone.github.io/santander-dev-week-2023-api/icons/credit.svg",
      "description": "Pyterson, invista no seu futuro com rentabilidade e segurança."
    }
  ]
}
