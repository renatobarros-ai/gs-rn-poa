# Sistema de Monitoramento Inteligente de Alagamentos

## Visão Geral

Sistema Django que monitora alagamentos urbanos através de sensores simulados e rede neural convolucional (CNN), acionando alertas automatizados quando detecta pessoas em risco.

## Arquitetura do Sistema

```
Simulador Sensor → Orquestrador → CNN (condicional) → Dashboard + Alertas
```

## Estrutura de Arquivos

```
├── .env                     # Variáveis de ambiente e chaves de API
├── .gitignore               # Arquivos ignorados pelo Git
├── README.md                # Documentação principal
├── manage.py                # Utilitário Django
├── requirements.txt         # Dependências Python
│
├── alertas/                 # Sistema de notificações
│   ├── email/codigo.py      # Alertas por e-mail
│   └── whatsapp/codigo.py   # Alertas via WhatsApp
│
├── banco_dados/
│   └── db.sqlite3           # Banco SQLite
│
├── core/                    # Configurações Django
│   ├── settings.py          # Configurações do projeto
│   ├── urls.py              # Rotas principais
│   ├── asgi.py              # Servidor assíncrono
│   └── wsgi.py              # Servidor síncrono
│
├── dados_externos/          # Dados de entrada
│   ├── cameras_urbanas/exemplo_camera.jpg
│   └── sensores/simulador_nivel.py
│
├── dashboard/               # Interface web
│   ├── css/dashboard.css    # Estilos
│   ├── html/dashboard.html  # Estrutura HTML
│   ├── images/mapa_poa.jpg  # Mapa base
│   └── js/dashboard.js      # JavaScript
│
├── gatilhos/
│   └── orquestrador.py      # Lógica central de decisões
│
└── rede_convolucional/      # IA
    ├── inferencia.py        # Execução da CNN
    └── modelo_treinado.h5   # Modelo treinado
```

## Regras de Funcionamento

### Simulador do Sensor
- **Frequência**: A cada 15 segundos
- **Valores**: Sorteio pseudoaleatório entre 0 e 100
- **Classificação**:
  - `< 50` = NORMAL (menos de 50mm de chuva, sem alagamentos)
  - `≥ 50` = CRÍTICO (mais de 50mm de chuva, com alagamentos)

### CNN (Rede Neural Convolucional)
- **Entrada**: Imagem aleatória do banco de dados
- **Saída**: 
  - `SEM_RISCO` = Não há pessoas em risco
  - `COM_RISCO` = Há pessoas em risco

### Orquestrador - Lógica de Decisão

#### Cenário 1: Sensor NORMAL (< 50)
- CNN: **Não acionada**
- Dashboard: Mapa "normalidade"
- Alertas: **Nenhum**

#### Cenário 2: Sensor CRÍTICO (≥ 50) + CNN SEM_RISCO
- CNN: **Acionada**
- Dashboard: Mapa "alerta" (filtro amarelo)
- Alertas: **Nenhum**

#### Cenário 3: Sensor CRÍTICO (≥ 50) + CNN COM_RISCO
- CNN: **Acionada**
- Dashboard: Mapa "perigo" (filtro vermelho)
- Alertas: **E-mail + WhatsApp enviados**

## Dashboard - Interface Web

### Componentes da Página Única

1. **Mapa da Cidade**
   - 3 imagens idênticas com filtros diferentes
   - Normal: sem filtro
   - Alerta: filtro amarelo
   - Perigo: filtro vermelho
   - Funcionalidade: zoom habilitado

2. **Área de Inferência**
   - **CNN acionada**: Exibe imagem analisada
   - **CNN não acionada**: Mostra "não há alagamentos"

3. **Gráfico de Chuva**
   - Atualização: a cada loop de aferição
   - Frequência: 1 dados por dia
   - Conteúdo: histórico do nível de chuva

## Fluxo de Execução

```
1. Simulador gera valor (0-100) a cada 15s
2. Orquestrador recebe valor do sensor
3. SE valor < 50:
   - Dashboard mostra mapa normal
   - Sistema em standby
4. SE valor ≥ 50:
   - CNN é acionada com imagem aleatória
   - SE CNN = SEM_RISCO:
     - Dashboard mostra mapa amarelo (alerta)
   - SE CNN = COM_RISCO:
     - Dashboard mostra mapa vermelho (perigo)
     - Dispara alertas (e-mail + WhatsApp)
5. Dashboard atualiza gráfico de chuva
```

## Módulos Principais

- **alertas/**: Gerencia notificações por diferentes canais
- **gatilhos/**: Centraliza lógica de decisão do sistema
- **rede_convolucional/**: Processa inferência de IA
- **dashboard/**: Interface web responsiva
- **dados_externos/**: Simula fontes de dados reais
