.
├── .env                     # Armazena variáveis de ambiente e segredos (chaves de API, senhas)
├── .gitignore               # Define quais arquivos e pastas o Git deve ignorar (ex: .env, __pycache__)
├── README.md                # Apresentação principal do projeto, com instruções de uso e instalação
├── manage.py                # Utilitário de linha de comando para interagir com o projeto Django
├── requirements.txt         # Lista todas as bibliotecas Python necessárias para o projeto funcionar
│
├── alertas/                 # Módulo responsável por todos os tipos de notificação
│   ├── __init__.py          # Transforma a pasta 'alertas' em um pacote Python importável
│   └── email/
│       ├── __init__.py      # Transforma a pasta 'email' em um sub-pacote Python
│       └── codigo.py        # Contém a função específica para enviar alertas por e-mail
│
├── banco_dados/             # Diretório para conter o arquivo do banco de dados
│   └── db.sqlite3           # Arquivo do banco de dados SQLite gerenciado pelo Django
│
├── core/                    # Diretório principal do projeto Django
│   ├── __init__.py          # Arquivo que inicializa o projeto como um pacote Python
│   ├── asgi.py              # Configuração para servidores web assíncronos
│   ├── settings.py          # Arquivo com todas as configurações do projeto Django
│   ├── urls.py              # Define as rotas de URL principais da aplicação
│   └── wsgi.py              # Configuração para servidores web síncronos
│
├── dados_externos/          # Módulo com dados de entrada para simulações
│   ├── cameras_urbanas/
│   │   └── exemplo_camera.jpg # Imagem de exemplo para ser usada na inferência
│   └── sensores/
│       └── simulador_nivel.py # Código que simula dados do nível da água
│
├── dashboard/               # Módulo que representa o frontend da aplicação
│   ├── css/
│   │   └── dashboard.css    # Folha de estilos para o painel de visualização
│   ├── html/
│   │   └── dashboard.html   # Estrutura HTML do painel de visualização
│   ├── images/
│   │   └── mapa_poa.jpg     # Imagem de fundo ou de apoio para o dashboard
│   └── js/
│       └── dashboard.js     # Código JavaScript para interatividade e lógica do frontend
│
├── documentacao/            # Diretório para a documentação detalhada do projeto
│   └── especificacao.md     # Arquivo com a especificação técnica e regras de negócio
│
├── gatilhos/                # Módulo que orquestra o fluxo de trabalho
│   ├── __init__.py          # Transforma a pasta 'gatilhos' em um pacote Python
│   └── orquestrador.py      # Lógica que decide se a inferência e os alertas devem ser acionados
│
└── rede_convolucional/      # Módulo da Inteligência Artificial
    ├── __init__.py          # Transforma a pasta em um pacote Python
    ├── inferencia.py        # Código que carrega o modelo e executa a predição na imagem
    └── modelo_treinado.h5   # O arquivo do modelo de CNN já treinado
