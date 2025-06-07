# Sistema de Monitoramento de Alagamentos com IA

## **1. VISÃO GERAL DO PROJETO**

Sistema web em tempo real para monitoramento de alagamentos urbanos, utilizando sensores simulados e inferência por CNN para detectar pessoas em risco. O sistema integra coleta de dados, análise por IA e dashboard para visualização, com sistema de alertas automatizado.

## **2. STACK TECNOLÓGICA**

- **Backend**: Python com Django
- **Frontend**: HTML, CSS, JavaScript, Bootstrap
- **Banco de Dados**: SQLite
- **IA**: CNN (Rede Neural Convolucional)
- **Estrutura**: Arquitetura modular com separação de responsabilidades

## **3. ARQUITETURA DO SISTEMA**

```
[Simulador Sensor] → [Orquestrador] ← [CNN Inferência]
                           ↓
                    [Banco de Dados]
                           ↓
                     [Dashboard Web] ← [Sistema Alertas]
```

## **4. ESTRUTURA DE DIRETÓRIOS COMPLETA**

```
projeto_alagamentos/
├── .env                     # Variáveis de ambiente e configurações sensíveis
├── .gitignore               # Arquivos/pastas ignorados pelo Git
├── README.md                # Documentação principal do projeto
├── manage.py                # Utilitário Django para gerenciamento
├── requirements.txt         # Dependências Python do projeto
│
├── alertas/                 # Módulo de notificações
│   ├── __init__.py          
│   └── email/
│       ├── __init__.py      
│       └── codigo.py        # Lógica de envio de emails
│
├── banco_dados/             # Armazenamento de dados
│   └── db.sqlite3           # Banco SQLite
│
├── core/                    # Configurações centrais Django
│   ├── __init__.py          
│   ├── asgi.py              # Configuração servidor assíncrono
│   ├── settings.py          # Configurações do projeto
│   ├── urls.py              # Roteamento principal
│   └── wsgi.py              # Configuração servidor síncrono
│
├── dados_externos/          # Dados de entrada/simulação
│   ├── cameras_urbanas/
│   │   └── exemplo_camera.jpg
│   └── sensores/
│       └── simulador_nivel.py
│
├── dashboard/               # Interface web
│   ├── css/
│   │   └── dashboard.css    
│   ├── html/
│   │   └── dashboard.html   
│   ├── images/
│   │   └── mapa_poa.jpg     
│   └── js/
│       └── dashboard.js     
│
├── documentacao/            
│   └── especificacao.md     
│
├── gatilhos/                # Orquestração do sistema
│   ├── __init__.py          
│   └── orquestrador.py      
│
└── rede_convolucional/      # Módulo de IA
    ├── __init__.py          
    ├── inferencia.py        
    └── modelo_treinado.h5   
```

## **5. REGRAS DE NEGÓCIO DETALHADAS**

### **5.1 Simulador do Sensor**
- **Frequência**: Execução a cada 15 segundos (representa 1 dia)
- **Lógica**: Gera valor pseudoaleatório entre 0-100
- **Classificação**:
  - `sensor < 50` = **NORMAL** (sem alagamento)
  - `sensor >= 50` = **CRÍTICO** (com alagamento)
- **Saída**: Envia resultado para o orquestrador

### **5.2 CNN (Rede Neural Convolucional)**
- **Função**: Inferência de imagens para detectar pessoas em risco
- **Entrada**: Imagem aleatória do banco de dados
- **Saída**:
  - `SEM_RISCO` = Não há pessoas em risco
  - `COM_RISCO` = Há pessoas em risco

### **5.3 Orquestrador (Lógica Central)**
- **Entrada**: Resultados do sensor e CNN
- **Fluxos de Decisão**:

  **Caso NORMAL (sensor < 50)**:
  - Alagamento: Não
  - CNN: Não acionada
  - Mapa: "normalidade"
  - Alertas: Nenhum
  
  **Caso CRÍTICO + SEM_RISCO**:
  - Alagamento: Sim
  - Pessoas em Risco: Não
  - Mapa: "alerta"
  - Alertas: Nenhum
  
  **Caso CRÍTICO + COM_RISCO**:
  - Alagamento: Sim
  - Pessoas em Risco: Sim
  - Mapa: "perigo"
  - Alertas: Enviados

## **6. ESPECIFICAÇÕES DO BANCO DE DADOS**

### **Tabela Principal**: `monitoramento_dados`
```sql
CREATE TABLE monitoramento_dados (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    data DATE,                    -- Formato DD/MM
    nivel_acumulado INTEGER,      -- 0-100 mm
    alagamento BOOLEAN,           -- True/False
    pessoas_em_risco BOOLEAN,     -- True/False
    classificacao VARCHAR(20),    -- 'Normal', 'Atenção', 'Perigo'
    imagem VARCHAR(255)           -- Link/caminho da foto
);
```

### **Mapeamento de Classificação**:
- **Normal**: sensor < 50, sem alagamento
- **Atenção**: sensor >= 50, alagamento sem pessoas em risco
- **Perigo**: sensor >= 50, alagamento com pessoas em risco

## **7. ESPECIFICAÇÕES DO DASHBOARD**

### **7.1 Seção de Alta Importância**
- **Mapa Principal**: 
  - Proporção: 1x1
  - Atualização: A cada loop (15s)
  - Estados: normalidade/alerta/perigo
- **Painel de Informações**:
  - Data (DD/MM)
  - Classificação (Perigo/Atenção/Normal)
  - Pessoas em Risco (Sim/Não)
  - Alagamento (Sim/Não)
  - Nível de Chuva (0-100 mm)

### **7.2 Seção de Média Importância**
- **Imagem de Inferência**: 
  - Proporção: 4x3
  - Atualização: A cada loop
  - Fonte: CNN
- **Gráfico de Linha**:
  - Eixo X: Tempo (dias)
  - Eixo Y: Nível de chuva (mm)
  - Atualização: A cada loop

### **7.3 Seção de Baixa Importância**
- **Tabela Histórica**:
  - Máximo: 14 entradas
  - Ordenação: Mais recente no topo
  - Colunas: Data, Nível de Chuva, Alagamento, Pessoas em Risco, Classificação, Foto

## **8. MÓDULOS E RESPONSABILIDADES**

### **8.1 dados_externos/sensores/simulador_nivel.py**
```python
# Responsabilidades:
- Gerar valores aleatórios 0-100 a cada 15s
- Classificar como NORMAL/CRÍTICO
- Enviar dados para orquestrador
```

### **8.2 rede_convolucional/inferencia.py**
```python
# Responsabilidades:
- Carregar modelo CNN treinado (.h5)
- Processar imagem de entrada
- Retornar SEM_RISCO/COM_RISCO
```

### **8.3 gatilhos/orquestrador.py**
```python
# Responsabilidades:
- Receber dados do sensor
- Decidir se aciona CNN
- Processar resultado da CNN
- Determinar estado do sistema
- Salvar dados no banco
- Acionar alertas se necessário
```

### **8.4 alertas/email/codigo.py**
```python
# Responsabilidades:
- Configurar cliente de email
- Definir templates de alerta
- Enviar notificações automáticas
```

### **8.5 dashboard/ (Frontend)**
```javascript
// dashboard.js responsabilidades:
- Polling para atualizações (15s)
- Atualizar mapa dinâmico
- Atualizar gráficos em tempo real
- Gerenciar tabela histórica
- Interface responsiva com Bootstrap
```

## **9. FLUXO DE FUNCIONAMENTO COMPLETO**

1. **Inicialização**: Simulador inicia ciclo de 15s
2. **Coleta**: Sensor gera valor aleatório
3. **Decisão**: Orquestrador avalia se é NORMAL/CRÍTICO
4. **Inferência**: Se CRÍTICO, aciona CNN
5. **Classificação**: Define estado final do sistema
6. **Armazenamento**: Salva dados no SQLite
7. **Visualização**: Dashboard atualiza em tempo real
8. **Alertas**: Envia emails se necessário
9. **Loop**: Reinicia ciclo

## **10. REQUISITOS TÉCNICOS**

### **10.1 Backend (Django)**
- Models para tabela de monitoramento
- Views para API de dados
- URLs para endpoints
- Admin interface
- Settings com configurações de banco

### **10.2 Frontend**
- Templates Django com Bootstrap
- JavaScript para atualizações automáticas
- CSS customizado para layout

Este documento fornece todas as especificações necessárias para implementação completa do sistema de monitoramento de alagamentos.
