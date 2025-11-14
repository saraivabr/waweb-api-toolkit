# 📚 CLAUDE.md - Guia Completo do WhatsApp Web API Toolkit

> **Por Prof. Fellipe Saraiva**
> **Saraiva.AI** - A Maior Livraria Grátis de Conhecimento sobre IA
> *Transformando conhecimento técnico em aprendizado acessível*

---

## 📖 Índice de Conteúdos

1. [Bem-vindo ao Projeto](#bem-vindo-ao-projeto)
2. [Entendendo o Projeto](#entendendo-o-projeto)
3. [Arquitetura do Sistema](#arquitetura-do-sistema)
4. [Estrutura de Diretórios](#estrutura-de-diretórios)
5. [Guia de Configuração](#guia-de-configuração)
6. [Fluxos de Desenvolvimento](#fluxos-de-desenvolvimento)
7. [Convenções de Código](#convenções-de-código)
8. [Protocolo WhatsApp Web](#protocolo-whatsapp-web)
9. [Testando e Depurando](#testando-e-depurando)
10. [Referências e Recursos](#referências-e-recursos)

---

## 🎓 Bem-vindo ao Projeto

### Sobre Este Documento

Olá! Sou o **Professor Fellipe Saraiva**, da **Saraiva.AI**, e preparei este guia completo para você que está começando a trabalhar com este projeto fascinante de engenharia reversa do WhatsApp Web.

Este documento foi criado especialmente para assistentes de IA e desenvolvedores que precisam entender rapidamente como o projeto funciona, suas convenções e como contribuir de forma efetiva.

### O Que Você Vai Aprender

✅ Como o WhatsApp Web funciona "por baixo dos panos"
✅ A arquitetura completa de três camadas do sistema
✅ Protocolos de criptografia e segurança implementados
✅ Como adicionar novos comandos e funcionalidades
✅ Boas práticas e convenções do código
✅ Como debugar e solucionar problemas comuns

---

## 🔍 Entendendo o Projeto

### Visão Geral

**Nome do Projeto:** whatsapp-web-reveng (WhatsApp Web API Toolkit)
**Objetivo:** Implementação completa via engenharia reversa da API do WhatsApp Web
**Licença:** MIT
**Autor Original:** sigalor
**Tecnologias:** Python 2.7, Node.js, JavaScript (ES6+), WebSockets

### O Que Este Projeto Faz?

Este projeto fornece um **cliente web completo para o WhatsApp Web** através da engenharia reversa do protocolo proprietário. Ele implementa toda a pilha de comunicação, incluindo:

🔐 **Criptografia de ponta a ponta:**
- AES 256 CBC para criptografia simétrica
- Curve25519 para acordo de chave Diffie-Hellman
- HKDF (HMAC-based Key Derivation Function) para derivação de chaves
- HMAC com SHA256 para autenticação de mensagens

📦 **Processamento de dados:**
- Codificação/decodificação de formato binário customizado
- Manipulação de Protocol Buffers (protobuf)
- Gestão de WebSockets bidirecionais
- Sistema de filas de mensagens

### Por Que Este Projeto é Importante?

Este projeto é uma **verdadeira aula de engenharia reversa** e demonstra:

1. **Análise de Protocolos:** Como entender e reimplementar protocolos proprietários
2. **Criptografia Aplicada:** Implementação real de algoritmos criptográficos modernos
3. **Arquitetura de Sistemas:** Design de três camadas com comunicação assíncrona
4. **Interoperabilidade:** Integração entre Python, Node.js e JavaScript no navegador

---

## 🏗️ Arquitetura do Sistema

### Visão em Três Camadas

O sistema utiliza uma arquitetura em **três camadas** que separa responsabilidades de forma clara e elegante:

```
┌─────────────────────────────────────────────────────────────────────┐
│                         CAMADA 1: Frontend                          │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Navegador Web (Chrome, Firefox, Safari, etc.)              │   │
│  │  • Interface HTML/CSS (SCSS)                                │   │
│  │  • Lógica JavaScript (ES6+)                                 │   │
│  │  • Bibliotecas: jQuery, Bootstrap, Crypto-JS                │   │
│  │  • WebSocket Client para comunicação                        │   │
│  └─────────────────────────────────────────────────────────────┘   │
└────────────────────────────────┬────────────────────────────────────┘
                                 │ WebSocket (porta 2019)
                                 │ HTTP (porta 2018)
                                 ▼
┌─────────────────────────────────────────────────────────────────────┐
│                      CAMADA 2: API Node.js                          │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Servidor Node.js (index.js)                                │   │
│  │  • Servidor HTTP Express (arquivos estáticos)               │   │
│  │  • Servidor WebSocket (comunicação cliente-backend)         │   │
│  │  • Roteamento de comandos                                   │   │
│  │  • Gerenciamento de instâncias WhatsApp                     │   │
│  │  • Orquestração de mensagens                                │   │
│  └─────────────────────────────────────────────────────────────┘   │
└────────────────────────────────┬────────────────────────────────────┘
                                 │ WebSocket (porta 2020)
                                 ▼
┌─────────────────────────────────────────────────────────────────────┐
│                     CAMADA 3: Backend Python                        │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Servidor Python 2.7 (whatsapp_web_backend.py)             │   │
│  │  • Cliente WhatsApp Web                                     │   │
│  │  • Criptografia/Descriptografia (AES, Curve25519)          │   │
│  │  • Codificador/Decodificador binário                        │   │
│  │  • Geração de QR Code                                       │   │
│  │  • Gestão de sessões                                        │   │
│  └─────────────────────────────────────────────────────────────┘   │
└────────────────────────────────┬────────────────────────────────────┘
                                 │ WebSocket Seguro (wss://)
                                 ▼
                    ┌──────────────────────────┐
                    │   Servidores WhatsApp    │
                    │  wss://w[1-8].web.       │
                    │   whatsapp.com/ws        │
                    └──────────────────────────┘
```

### Configuração de Portas

| Porta | Protocolo | Finalidade | Comunicação |
|-------|-----------|------------|-------------|
| **2018** | HTTP | Servidor Express | Serve arquivos estáticos (HTML, CSS, JS) |
| **2019** | WebSocket | API ↔ Cliente | Comunicação bidirecional navegador-API |
| **2020** | WebSocket | Backend ↔ API | Comunicação bidirecional API-Python |

### Fluxo de Dados Completo

Vamos entender o fluxo de uma requisição completa, do início ao fim:

```
1. USUÁRIO CLICA EM "Gerar QR Code"
   ↓
2. [NAVEGADOR] JavaScript envia comando via WebSocket
   {
     "from": "client",
     "type": "call",
     "command": "backend-generateQRCode"
   }
   ↓
3. [NODE.JS API] Recebe, valida e roteia para Python
   • Adiciona resource_instance_id
   • Encaminha para backend Python
   ↓
4. [PYTHON BACKEND] Processa requisição
   • Conecta ao WhatsApp Web (se necessário)
   • Gera chaves Curve25519
   • Cria QR Code com ref + publicKey + clientId
   • Retorna imagem base64
   ↓
5. [NODE.JS API] Recebe resposta e encaminha
   • Valida resposta do backend
   • Encaminha para cliente correto
   ↓
6. [NAVEGADOR] Exibe QR Code na tela
   • Renderiza imagem
   • Aguarda scan do celular
   ↓
7. USUÁRIO ESCANEIA COM WHATSAPP
   ↓
8. [PYTHON BACKEND] Recebe mensagem "Conn"
   • Extrai secret (144 bytes)
   • Deriva chaves de criptografia
   • Autentica conexão
   ↓
9. [NAVEGADOR] Recebe confirmação de login
   • Atualiza UI
   • Passa a receber mensagens
```

### Componentes Principais

#### 1️⃣ Frontend Client (`client/`)

**Localização:** `/client/`
**Tecnologias:** HTML5, JavaScript ES6+, SCSS, jQuery

**Arquivos Principais:**

| Arquivo | Descrição | Responsabilidade |
|---------|-----------|------------------|
| `index.html` | Interface principal | UI do usuário, formulários, botões |
| `login-via-js-demo.html` | Demo standalone | Implementação puramente JavaScript |
| `js/main.js` | Lógica principal | Coordenação geral, inicialização |
| `js/WebSocketClient.js` | Abstração WebSocket | Cliente WebSocket reutilizável |
| `js/BootstrapStep.js` | Orquestração | Gerencia requisições/respostas |
| `js/UpdaterPromise.js` | Promises | Atualização baseada em Promises |

**Bibliotecas Utilizadas:**
- jQuery 3.2.1 - Manipulação DOM
- Bootstrap 3.3.7 - Framework CSS
- Curve25519-js - Criptografia de curva elíptica
- SJCL 1.0.7 - Stanford JavaScript Crypto Library
- QRCode.js - Geração de QR codes
- Moment.js 2.20.1 - Manipulação de datas

#### 2️⃣ Camada API Node.js (`index.js`)

**Localização:** `/index.js` (raiz do projeto)
**Tecnologias:** Node.js, Express, WebSocket (ws)

**Responsabilidades:**
1. **Servidor HTTP (Express):** Serve arquivos estáticos na porta 2018
2. **WebSocket Server:** Gerencia conexões na porta 2019
3. **Roteador de Mensagens:** Encaminha comandos entre cliente e backend
4. **Gerenciador de Ciclo de Vida:** Controla instâncias WhatsApp ativas
5. **Middleware:** Validação e transformação de mensagens

**Padrão de Fluxo de Comandos:**

```javascript
// O cliente envia comandos com este padrão:
Cliente → API: "api-connectBackend"          // Conectar ao backend Python
Cliente → API: "backend-connectWhatsApp"     // Iniciar instância WhatsApp
Cliente → API: "backend-generateQRCode"      // Gerar QR Code
Cliente → API: "backend-restoreSession"      // Restaurar sessão anterior
Cliente → API: "backend-disconnectWhatsApp"  // Desconectar WhatsApp
```

#### 3️⃣ Backend Python (`backend/`)

**Localização:** `/backend/`
**Tecnologias:** Python 2.7, SimpleWebSocketServer
**Ponto de Entrada:** `whatsapp_web_backend.py`

**Módulos Principais:**

| Módulo | Arquivo | Função |
|--------|---------|--------|
| **Backend Server** | `whatsapp_web_backend.py` | Servidor WebSocket principal (porta 2020) |
| **Cliente WhatsApp** | `whatsapp.py` | Implementação do protocolo WhatsApp Web |
| **Decodificador** | `whatsapp_binary_reader.py` | Decodifica mensagens binárias |
| **Codificador** | `whatsapp_binary_writer.py` | Codifica mensagens para binário |
| **Definições** | `whatsapp_defines.py` | Constantes, tokens, tags do protocolo |
| **Protobuf** | `whatsapp_protobuf_pb2.py` | Definições Protocol Buffer geradas |
| **Utilitários** | `utilities.py` | Funções auxiliares (timestamps, merge, etc) |

**Operações Criptográficas:**
- Geração de chaves Curve25519
- Acordo de chave Diffie-Hellman
- Derivação HKDF
- Criptografia/descriptografia AES-CBC
- HMAC-SHA256 para autenticação

---

## 📁 Estrutura de Diretórios

### Mapa Completo do Projeto

```
waweb-api-toolkit/                    # Raiz do projeto
│
├── 📂 backend/                        # Implementação Python do backend
│   ├── whatsapp_web_backend.py       # ⭐ Servidor WebSocket (porta 2020)
│   ├── whatsapp.py                   # ⭐ Cliente WhatsApp Web principal
│   ├── whatsapp_binary_reader.py     # Decodificador de mensagens binárias
│   ├── whatsapp_binary_writer.py     # Codificador de mensagens binárias
│   ├── whatsapp_defines.py           # Constantes e definições do protocolo
│   ├── whatsapp_protobuf_pb2.py      # Código gerado do protobuf
│   └── utilities.py                  # Funções utilitárias
│
├── 📂 client/                         # Frontend web
│   ├── 📂 css/                        # Estilos
│   │   ├── main.scss                 # Fonte SCSS (Sass)
│   │   └── main.css                  # CSS compilado (gerado automaticamente)
│   │
│   ├── 📂 js/                         # JavaScript do cliente
│   │   ├── main.js                   # ⭐ Lógica principal do cliente
│   │   ├── WebSocketClient.js        # Classe abstrata para WebSocket
│   │   ├── BootstrapStep.js          # Orquestração de requisições
│   │   └── UpdaterPromise.js         # Promises para atualizações
│   │
│   ├── 📂 lib/                        # Bibliotecas de terceiros
│   │   ├── bootstrap/                # Framework CSS/JS
│   │   ├── jquery/                   # jQuery 3.2.1
│   │   ├── curve25519-js/            # Criptografia de curva elíptica
│   │   ├── sjcl/                     # Stanford JavaScript Crypto Library
│   │   ├── qrcode/                   # Gerador de QR Code
│   │   ├── moment/                   # Manipulação de datas
│   │   ├── lodash/                   # Utilitários JavaScript
│   │   ├── jsonTree/                 # Visualizador de JSON
│   │   └── jquery-colResizable/      # Redimensionamento de colunas
│   │
│   ├── index.html                    # ⭐ Interface principal
│   └── login-via-js-demo.html        # Demo puramente JavaScript
│
├── 📂 doc/                            # Documentação
│   ├── 📂 img/                        # Diagramas e imagens
│   │   └── app-architecture1000.png  # Diagrama de arquitetura
│   │
│   └── 📂 spec/                       # Especificações do protocolo
│       ├── def.proto                 # ⭐ Definições Protocol Buffer
│       └── 📂 protobuf-extractor/     # Ferramenta de extração
│           ├── index.js
│           ├── package.json
│           └── package-lock.json
│
├── 📂 windows/                        # Dependências específicas Windows
│   └── stdint.h                      # Header C++ para Windows
│
├── 📂 .git/                           # Repositório Git
│
├── index.js                          # ⭐ Servidor Node.js API (principal)
├── index_jsdemo.js                   # Servidor para demo JavaScript
│
├── 📄 package.json                    # Dependências Node.js
├── 📄 package-lock.json               # Lock de versões Node.js
├── 📄 requirements.txt                # Dependências Python
│
├── 📄 Dockerfile                      # Configuração Docker
├── 📄 shell.nix                       # Ambiente Nix
├── 📄 .envrc                          # Configuração direnv
├── 📄 .gitignore                      # Arquivos ignorados pelo Git
│
├── 📄 session.json                    # Persistência de sessão (gitignored)
├── 📄 README.md                       # Documentação do usuário
└── 📄 CLAUDE.md                       # ⭐ Este arquivo (guia para IA)
```

### Arquivos Ignorados pelo Git (`.gitignore`)

Estes arquivos **não** devem ser commitados:

```
node_modules/              # Dependências Node.js (instalar com npm)
.sass-cache/               # Cache de compilação SCSS
backend/decodable_msgs/    # Mensagens decodificadas com sucesso
backend/undecodable_msgs/  # Mensagens que falham na decodificação
log.txt                    # Logs de execução
misc/                      # Arquivos diversos
.vscode/                   # Configurações VS Code
.idea/                     # Configurações IntelliJ/PyCharm
session.json               # Dados de sessão (contém tokens sensíveis)
```

---

## ⚙️ Guia de Configuração

### Pré-requisitos

Antes de começar, certifique-se de ter instalado:

- **Node.js** versão 8+ (recomendado: 14 ou superior)
- **Python** 2.7 (⚠️ importante: não funciona com Python 3)
- **npm** (geralmente vem com Node.js)
- **pip** (gerenciador de pacotes Python)
- **Git** (para versionamento)

### Opção 1: Instalação com Nix (Recomendado)

**Nix** é um gerenciador de pacotes que cria ambientes isolados e reproduzíveis.

```bash
# 1. Instale Nix (se ainda não tiver)
curl -L https://nixos.org/nix/install | sh

# 2. (Opcional) Instale direnv para carregamento automático
# No Ubuntu/Debian:
sudo apt-get install direnv

# 3. Entre no diretório do projeto
cd waweb-api-toolkit

# 4. Entre no shell Nix (carrega todas dependências)
nix-shell

# 5. Instale dependências Node.js
npm install -f

# 6. Inicie o projeto
npm start
```

**Com direnv configurado:**
O ambiente é carregado automaticamente ao entrar no diretório!

```bash
cd waweb-api-toolkit
# direnv detecta .envrc e carrega ambiente automaticamente
# Mensagem: "Installing node modules..."
npm start
```

### Opção 2: Instalação Bare Metal (Linux/Mac)

```bash
# 1. Clone o repositório
git clone https://github.com/sigalor/whatsapp-web-reveng.git
cd whatsapp-web-reveng

# 2. Instale dependências Node.js
npm install -f

# 3. Instale dependências Python
pip install -r requirements.txt

# 4. Inicie o desenvolvimento
npm start

# Ou alternativamente:
npm run dev
```

### Opção 3: Instalação no Windows

⚠️ **Atenção:** Windows requer passos adicionais!

```powershell
# 1. Instale Microsoft Visual C++ 9.0
# Baixe de: http://aka.ms/vcpython27

# 2. Copie stdint.h para o diretório do Visual C++
# De: windows/stdint.h (neste projeto)
# Para: C:\Users\SEU_USUARIO\AppData\Local\Programs\Common\Microsoft\Visual C++ for Python\9.0\VC\include\

# 3. Instale dependências Node.js
npm install -f

# 4. Instale dependências Python
pip install -r requirements.txt

# 5. Inicie o desenvolvimento (comando específico Windows)
npm run win
```

### Opção 4: Docker (Mais Simples)

```bash
# 1. Build da imagem Docker
docker build . -t whatsapp-web-reveng

# 2. Execute o container
docker run -p 2019:2019 -p 2018:2018 whatsapp-web-reveng

# 3. Acesse no navegador
# http://localhost:2018/
```

**Para uso em servidor (deployment):**

Se você quer disponibilizar em um servidor:

1. Edite `client/js/main.js`:

```javascript
let backendInfo = {
    url: "wss://SEU-SERVIDOR.com:2020",  // Use wss:// para conexão segura em produção
    timeout: 10000
};
```

> ⚠️ **Importante:** Para produção, sempre use `wss://` (WebSocket Seguro) ao invés de `ws://`. Você precisará configurar certificados SSL/TLS no servidor.

2. Rebuild e execute:

```bash
docker build . -t whatsapp-web-reveng
docker run -p 2019:2019 -p 2018:2018 whatsapp-web-reveng
```

3. Acesse: `http://SEU-SERVIDOR.com:2018/`

### Scripts NPM Disponíveis

| Comando | Descrição | Quando Usar |
|---------|-----------|-------------|
| `npm start` | Inicia desenvolvimento | Dia a dia, desenvolvimento ativo |
| `npm run dev` | Alias para `npm start` | Mesma coisa que acima |
| `npm run win` | Desenvolvimento Windows | Somente no Windows |
| `npm test` | Executa testes | Testar funcionalidades |
| `npm run __run_in_docker` | Execução em Docker | Chamado automaticamente no container |

**O que `npm start` faz:**

Executa **três processos simultaneamente** usando `concurrently`:

1. **Node.js API** (`nodemon index.js`)
   - Auto-reinicia ao detectar mudanças em `.js`
   - Ignora mudanças em `client/`

2. **Python Backend** (`nodemon --exec python ./backend/whatsapp_web_backend.py`)
   - Auto-reinicia ao detectar mudanças em `.py`
   - Ignora mudanças em `client/`

3. **Compilador SCSS** (`sass --watch client/css/main.scss:client/css/main.css`)
   - Compila SCSS → CSS automaticamente
   - Monitora mudanças em `main.scss`

---

## 🔄 Fluxos de Desenvolvimento

### Workflow Típico de Desenvolvimento

```
1. [INÍCIO] Faça alterações no código
   ↓
2. [AUTO] Nodemon detecta mudanças
   ↓
3. [AUTO] Servidor reinicia automaticamente
   ↓
4. [MANUAL] Recarregue a página no navegador (F5)
   ↓
5. [TESTE] Verifique se funciona
   ↓
6. [COMMIT] Se OK, faça commit das alterações
```

### Adicionando Novos Comandos

Vamos aprender como adicionar um novo comando ao sistema. Exemplo: **"backend-getUserStatus"** para obter status de um usuário.

#### Passo 1: Client-Side (`client/js/main.js`)

Adicione a requisição no JavaScript do cliente:

```javascript
// Adicione esta função no arquivo client/js/main.js

function getUserStatus(phoneNumber) {
    new BootstrapStep({
        websocket: apiWebsocket,
        request: {
            type: "call",
            callArgs: {
                command: "backend-getUserStatus",
                phone: phoneNumber
            },
            successCondition: obj => obj.type == "user_status_received",
            successActor: response => {
                console.log("Status do usuário:", response.status);
                // Atualize a UI com o status
                document.getElementById("userStatus").textContent = response.status;
            }
        }
    }).run(10000).catch(error => {
        console.error("Erro ao obter status:", error);
    });
}
```

#### Passo 2: API Layer (`index.js`)

Adicione o handler na camada Node.js:

```javascript
// Adicione este bloco no arquivo index.js

clientWebsocket.waitForMessage({
    condition: obj => obj.from == "client" &&
                     obj.type == "call" &&
                     obj.command == "backend-getUserStatus",
    keepWhenHit: true  // Mantém o listener ativo para múltiplas requisições
}).then(clientCallRequest => {
    // Validação: backend deve estar conectado
    if(!backendWebsocket.isOpen) {
        clientCallRequest.respond({
            type: "error",
            reason: "Backend não está conectado."
        });
        return;
    }

    // Encaminha para o backend Python
    new BootstrapStep({
        websocket: backendWebsocket,
        request: {
            type: "call",
            callArgs: {
                command: "backend-getUserStatus",
                phone: clientCallRequest.data.phone,
                whatsapp_instance_id: backendWebsocket.activeWhatsAppInstanceId
            },
            successCondition: obj => obj.type == "user_status_received"
        }
    }).run(backendInfo.timeout).then(backendResponse => {
        // Encaminha resposta do backend para o cliente
        clientCallRequest.respond({
            type: "user_status_received",
            status: backendResponse.data.status,
            phone: backendResponse.data.phone
        });
    }).catch(reason => {
        clientCallRequest.respond({ type: "error", reason: reason });
    });
}).run();
```

#### Passo 3: Backend Handler (`backend/whatsapp_web_backend.py`)

Adicione o case no switch de comandos:

```python
# No arquivo backend/whatsapp_web_backend.py
# Dentro do método handleMessage, adicione:

cmd = obj["command"]

# ... outros comandos existentes ...

elif cmd == "backend-getUserStatus":
    # Obtém o telefone do usuário
    phone = obj.get("phone")

    if not phone:
        self.sendError("Telefone não fornecido", tag)
        return

    # Chama o método no cliente WhatsApp
    currWhatsAppInstance.getUserStatus(phone, callback)
```

#### Passo 4: WhatsApp Client (`backend/whatsapp.py`)

Implemente o método no cliente WhatsApp:

```python
# No arquivo backend/whatsapp.py
# Adicione este método na classe WhatsAppWebClient:

def getUserStatus(self, phone, callback):
    """
    Obtém o status de um usuário do WhatsApp

    Args:
        phone (str): Número de telefone no formato JID (ex: "5511999999999@c.us")
        callback (dict): Callback para retornar o resultado
    """
    # Gera tag única para esta requisição
    tag = str(getTimestampMs())

    # Adiciona na fila de mensagens aguardando resposta
    self.messageQueue[tag] = {
        "callback": callback,
        "phone": phone,
        "request_type": "getUserStatus"
    }

    # Envia mensagem para o servidor WhatsApp
    # Formato: ["action", "query", "status", "PHONE_JID"]
    self.sendMessage(tag, ["query", "Status", phone])

    # A resposta será processada em handleMessage quando chegar
```

#### Passo 5: Processar Resposta (`backend/whatsapp.py`)

No método que processa respostas do WhatsApp:

```python
# No método que processa mensagens recebidas
# Geralmente dentro de um loop que lê do WebSocket

def handleWhatsAppResponse(self, tag, response):
    """Processa respostas do servidor WhatsApp"""

    if tag in self.messageQueue:
        request_info = self.messageQueue[tag]
        callback = request_info["callback"]

        if request_info.get("request_type") == "getUserStatus":
            # Extrai o status da resposta
            status_text = response.get("status", "Indisponível")
            phone = request_info["phone"]

            # Chama o callback com o resultado
            callback["func"]({
                "type": "user_status_received",
                "status": status_text,
                "phone": phone
            }, callback)

            # Remove da fila
            del self.messageQueue[tag]
```

### Fluxo Completo do Novo Comando

```
1. Usuário clica em "Obter Status"
   ↓
2. [CLIENT JS] getUserStatus("5511999999999@c.us")
   ↓
3. [CLIENT→API] WebSocket envia comando
   ↓
4. [API NODE.JS] Valida e encaminha
   ↓
5. [API→BACKEND] WebSocket envia para Python
   ↓
6. [BACKEND] whatsapp_web_backend.py recebe
   ↓
7. [WHATSAPP CLIENT] getUserStatus() envia para servidor WhatsApp
   ↓
8. [SERVIDOR WHATSAPP] Processa e responde
   ↓
9. [WHATSAPP CLIENT] Recebe resposta, chama callback
   ↓
10. [BACKEND→API] Envia resultado
    ↓
11. [API→CLIENT] Encaminha para navegador
    ↓
12. [CLIENT JS] successActor atualiza UI
```

---

## 📝 Convenções de Código

### Estilo Python

#### Características Específicas deste Projeto

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

# Este projeto usa Python 2.7 (legacy)
from __future__ import print_function

# Padrão: ponto-e-vírgula no final (incomum, mas consistente neste código)
import sys;
import json;

# Função de logging para stderr
def eprint(*args, **kwargs):
    print(*args, file=sys.stderr, **kwargs);
```

#### Convenções de Nomenclatura

| Tipo | Convenção | Exemplo |
|------|-----------|---------|
| **Classes** | PascalCase | `WhatsAppWebClient`, `MessageParser` |
| **Funções** | snake_case | `generate_qr_code()`, `send_message()` |
| **Variáveis** | snake_case | `client_id`, `message_queue` |
| **Constantes** | UPPER_SNAKE_CASE | `LIST_EMPTY`, `BINARY_8` |
| **Privadas** | _leading_underscore | `_internal_method()` |

#### Exemplo Completo

```python
class WhatsAppWebClient:
    """Cliente para comunicação com WhatsApp Web"""

    # Constantes da classe
    TIMEOUT_MS = 10000
    MAX_RETRIES = 3

    def __init__(self, onOpen, onMessage, onClose):
        """
        Inicializa o cliente WhatsApp

        Args:
            onOpen: Callback chamado ao conectar
            onMessage: Callback para mensagens recebidas
            onClose: Callback ao desconectar
        """
        self.messageQueue = {};
        self.activeWs = None;
        self._callbacks = {
            "open": onOpen,
            "message": onMessage,
            "close": onClose
        };

    def connect_to_server(self, server_url):
        """
        Conecta ao servidor WhatsApp

        Args:
            server_url (str): URL do servidor (ex: wss://w1.web.whatsapp.com/ws)

        Returns:
            bool: True se conectou com sucesso
        """
        try:
            self.activeWs = self._create_websocket(server_url);
            return True;
        except Exception as e:
            eprint("Erro ao conectar:", str(e));
            return False;
```

### Estilo JavaScript

#### Características ES6+

```javascript
// Usar const/let ao invés de var
const API_PORT = 2019;
let currentConnection = null;

// Arrow functions para callbacks
const handleMessage = (msg) => {
    console.log("Mensagem recebida:", msg);
};

// Async/await para operações assíncronas
async function connectToBackend() {
    try {
        const response = await backendWebsocket.connect();
        console.log("Conectado:", response);
    } catch (error) {
        console.error("Erro:", error);
    }
}

// Promises para fluxos assíncronos
new Promise((resolve, reject) => {
    if (condition) resolve(data);
    else reject(error);
});

// Template literals
const message = `Conectado na porta ${API_PORT}`;

// Destructuring
const { from, type, command } = messageData;
```

#### Convenções de Nomenclatura

| Tipo | Convenção | Exemplo |
|------|-----------|---------|
| **Classes** | PascalCase | `WebSocketClient`, `BootstrapStep` |
| **Funções** | camelCase | `connectToBackend()`, `sendMessage()` |
| **Variáveis** | camelCase | `clientWebsocket`, `messageTag` |
| **Constantes** | UPPER_SNAKE_CASE ou camelCase | `API_PORT`, `backendInfo` |
| **Privadas** | _leadingUnderscore | `_internalMethod()` |

#### Exemplo Completo

```javascript
/**
 * Cliente WebSocket reutilizável
 * Fornece abstração sobre WebSocket nativo
 */
class WebSocketClient {
    constructor() {
        this.ws = null;
        this.messageHandlers = [];
        this.isOpen = false;
    }

    /**
     * Inicializa conexão WebSocket
     * @param {string} url - URL do servidor WebSocket
     * @param {string} identifier - Identificador desta conexão
     * @param {Object} options - Opções de configuração
     * @returns {WebSocketClient} Esta instância (para chaining)
     */
    initialize(url, identifier, options = {}) {
        this.ws = new WebSocket(url);
        this.identifier = identifier;

        this.ws.onopen = () => {
            this.isOpen = true;
            console.log(`[${identifier}] Conectado a ${url}`);
        };

        this.ws.onmessage = (msg) => {
            const data = JSON.parse(msg.data);
            this._handleMessage(data);
        };

        this.ws.onclose = () => {
            this.isOpen = false;
            console.log(`[${identifier}] Desconectado`);
        };

        return this; // Permite chaining
    }

    /**
     * Envia mensagem pelo WebSocket
     * @param {Object} data - Dados a enviar (será convertido para JSON)
     */
    send(data) {
        if (!this.isOpen) {
            throw new Error("WebSocket não está conectado");
        }

        const message = JSON.stringify(data);
        this.ws.send(message);
    }

    /**
     * Aguarda por mensagem que satisfaça condição
     * @param {Object} config - Configuração do listener
     * @returns {Promise} Promise que resolve quando mensagem chegar
     */
    waitForMessage({ condition, keepWhenHit = false }) {
        return new Promise((resolve, reject) => {
            const handler = {
                condition,
                keepWhenHit,
                resolve,
                reject
            };
            this.messageHandlers.push(handler);
        });
    }

    /**
     * Processa mensagem recebida (privado)
     * @private
     */
    _handleMessage(data) {
        // Procura handler que corresponda
        this.messageHandlers = this.messageHandlers.filter(handler => {
            if (handler.condition(data)) {
                handler.resolve({ data });
                return handler.keepWhenHit; // Remove se não deve manter
            }
            return true; // Mantém se não correspondeu
        });
    }
}
```

### Formato de Mensagens WebSocket

#### Padrão Universal

**Todas** as mensagens WebSocket neste projeto seguem este formato:

```
messageTag,{JSON_OBJECT}
```

#### Componentes

1. **messageTag:** Identificador único da mensagem
   - Geralmente timestamp em milissegundos
   - Usado para correlacionar requisição/resposta
   - **Não pode conter vírgulas**

2. **vírgula:** Separador
   - Separa tag do payload JSON

3. **JSON_OBJECT:** Payload da mensagem
   - Sempre um objeto JSON válido
   - Contém campos `from`, `type`, `command`, etc.

#### Exemplos Reais

```javascript
// Exemplo 1: Gerar QR Code
"1637849234567,{\"from\":\"client\",\"type\":\"call\",\"command\":\"backend-generateQRCode\"}"

// Exemplo 2: Resposta com sucesso
"1637849235123,{\"from\":\"backend\",\"type\":\"generated_qr_code\",\"image\":\"data:image/png;base64,...\"}"

// Exemplo 3: Mensagem de erro
"1637849235890,{\"from\":\"api\",\"type\":\"error\",\"reason\":\"Backend não conectado\"}"

// Exemplo 4: Notificação de mensagem recebida
"1637849236445,{\"type\":\"whatsapp_message_received\",\"message\":{...},\"timestamp\":1637849236445}"
```

#### Campos Padrão do JSON

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `from` | string | Sim | Origem da mensagem |
| `type` | string | Sim | Tipo da mensagem |
| `command` | string | Condicional | Comando a executar (para type="call") |
| `resource` | string | Condicional | Recurso afetado (backend, whatsapp) |
| `resource_instance_id` | string | Condicional | ID da instância WhatsApp |
| `reason` | string | Condicional | Razão do erro (para type="error") |

#### Valores Comuns

**Origem (from):**
- `"client"` - Navegador web
- `"api"` - Servidor Node.js
- `"api2backend"` - API comunicando com backend
- `"backend"` - Servidor Python

**Tipo (type):**
- `"call"` - Requisição de comando
- `"connected"` - Confirmação de conexão
- `"resource_connected"` - Recurso conectado
- `"resource_gone"` - Recurso desconectado
- `"error"` - Mensagem de erro
- `"whatsapp_message_received"` - Mensagem do WhatsApp

### Gerenciamento de Instâncias

#### Ciclo de Vida de uma Instância WhatsApp

```python
# Python: Criação de instância
clientInstanceId = uuid.uuid4().hex  # Ex: "a3f2c1d4e5b6..."

# Armazenada no dicionário
self.clientInstances[clientInstanceId] = WhatsAppWebClient(
    onOpenCallback,
    onMessageCallback,
    onCloseCallback
)
```

```javascript
// Node.js: Rastreamento de instância
backendWebsocket.activeWhatsAppInstanceId = clientInstanceId;

// Uso em requisições
callArgs: {
    command: "backend-generateQRCode",
    whatsapp_instance_id: backendWebsocket.activeWhatsAppInstanceId
}
```

#### Callbacks no Backend Python

Padrão de callback usado em Python:

```python
callback = {
    "func": lambda obj, cbSelf: self.sendJSON(obj, cbSelf["tag"]),
    "tag": messageTag,
    "args": {
        "resource_instance_id": clientInstanceId,
        "additional": "data"
    }
}
```

**Invocação do callback:**

```python
# Quando a resposta chega
callback["func"](
    responseData,      # obj
    callback           # cbSelf
)

# A função acessa:
# - responseData: dados da resposta
# - callback["tag"]: tag original da mensagem
# - callback["args"]: argumentos adicionais
```

---

## 🔐 Protocolo WhatsApp Web

### Fluxo de Conexão Completo

#### 1. Inicialização da Conexão

**Objetivo:** Estabelecer conexão WebSocket com servidor WhatsApp

```python
# Conectar ao servidor
ws_url = "wss://w{}.web.whatsapp.com/ws".format(random.randint(1, 8))

# Headers obrigatórios
headers = {
    "Origin": "https://web.whatsapp.com"
}

# Gerar Client ID (16 bytes aleatórios em base64)
client_id = base64.b64encode(os.urandom(16))
# Resultado: algo como "xK8dP2mN5qR9tY3vZ7wA1B=="
```

#### 2. Mensagem de Inicialização

**Enviar comando "admin init":**

```python
tag = str(int(time.time() * 1000))  # Timestamp em ms
version = [0, 3, 2390]  # Versão do WhatsApp Web

message = [
    "admin",
    "init",
    version,
    ["Long browser description", "ShortDesc"],  # Descrição do cliente
    client_id,
    True
]

ws.send(f"{tag},{json.dumps(message)}")
```

**Formato da mensagem:**
```
1637849234567,["admin","init",[0,3,2390],["Chrome Linux x64","Chrome"],"xK8dP2mN5qR9tY3vZ7wA1B==",true]
```

#### 3. Resposta do Servidor

**O servidor responde com:**

```json
{
    "status": 200,
    "ref": "rE3f7X9yZ2Qp...",
    "ttl": 20000,
    "update": false,
    "curr": "0.2.7314",
    "time": 1637849234567.0
}
```

**Campos importantes:**
- `ref`: Server ID (necessário para QR code)
- `ttl`: Tempo de validade do QR (20 segundos)
- `curr`: Versão atual do WhatsApp Web

#### 4. Geração do QR Code

**Criar chave privada Curve25519:**

```python
import curve25519

# Gerar par de chaves
private_key = curve25519.Private()
public_key = private_key.get_public()

# Serializar chave pública
public_key_b64 = base64.b64encode(public_key.serialize())
```

**Montar string do QR code:**

```python
qr_content = f"{ref},{public_key_b64},{client_id}"
# Exemplo: "rE3f7X9yZ2Qp...,yT8kL3nP9qM...,xK8dP2mN5qR9tY3vZ7wA1B=="
```

**Gerar imagem QR:**

```python
import pyqrcode

qr = pyqrcode.create(qr_content)
qr_base64 = qr.png_as_base64_str(scale=8)
```

#### 5. Após Escanear QR Code

O servidor envia múltiplas mensagens:

**a) Mensagem "Conn" (Connection):**

> **Nota:** O exemplo abaixo contém valores fictícios para fins de documentação. O campo "secret" é um exemplo de dados criptografados retornados pelo servidor WhatsApp, não uma chave de API real.

```json
{
    "type": "Conn",
    "data": {
        "battery": 85,
        "browserToken": "1//0gXxY...",
        "clientToken": "kL9mN2pQ3rS...",
        "phone": {
            "device_manufacturer": "Samsung",
            "device_model": "SM-G973F",
            "os_build_number": "QP1A.190711.020",
            "os_version": "10"
        },
        "platform": "android",
        "pushname": "João Silva",
        "secret": "vW8xY1zA2bC3dE4fG5hI6jK7lM8nO9pQ0rS1tU2vW3xY4zA5bC6dE7fG8hI9jK0lM1nO2pQ3rS4tU5vW6xY7zA8bC9dE0fG1hI2jK3lM4nO5pQ6rS7tU8vW9xY0zA1bC2dE3fG4hI5jK6lM7nO8pQ9rS0tU1vW2xY3zA4bC5dE6fG7hI8jK9lM0nO1pQ2rS3tU4vW5xY6zA7bC8dE9fG0hI1jK2lM3nO4pQ5rS6tU7vW8xY9zA0bC1dE2fG3hI4jK5lM6nO7pQ8rS9tU0vW1xY2zA3bC4dE5fG6hI7jK8lM9nO0pQ1rS2tU3vW4==",
        "serverToken": "1.mN9pQ0rS1tU...",
        "wid": "5511999999999@c.us"
    }
}
```

**b) Mensagem "Stream":**

```json
["Stream", "update", false, "0.2.7314"]
```

**c) Mensagem "Props" (Properties):**

```json
{
    "type": "Props",
    "data": {
        "imageMaxKBytes": 1024,
        "maxParticipants": 257,
        "videoMaxEdge": 960,
        "mediaMaxSize": 16777216
    }
}
```

### Derivação de Chaves de Criptografia

Este é o **coração do sistema de segurança**. Vamos detalhar cada passo:

#### Passo 1: Extrair Secret

```python
# Secret vem codificado em base64 (144 bytes quando decodificado)
secret_b64 = conn_message["data"]["secret"]
secret = base64.b64decode(secret_b64)

# secret tem exatamente 144 bytes
assert len(secret) == 144
```

**Estrutura do secret:**

```
secret (144 bytes)
├─ [0:32]   → Chave pública do servidor (32 bytes)
├─ [32:64]  → HMAC para validação (32 bytes)
└─ [64:144] → Parte das chaves criptografadas (80 bytes)
```

#### Passo 2: Acordo Diffie-Hellman

```python
import curve25519

# Chave pública do servidor
server_public_key = curve25519.Public(secret[0:32])

# Gerar shared secret usando nossa chave privada
shared_secret = private_key.get_shared_key(
    server_public_key,
    lambda x: x  # Função identidade, sem hashing
)

# shared_secret tem 32 bytes
assert len(shared_secret) == 32
```

**O que acontece aqui:**
- Usamos nossa chave privada (gerada no passo 4)
- Combinamos com a chave pública do servidor
- Resultado: segredo compartilhado que só nós e o servidor conhecemos

#### Passo 3: Expandir com HKDF

```python
import hmac
import hashlib

def hkdf(key, length, app_info=""):
    """
    HMAC-based Key Derivation Function

    Args:
        key: Chave de entrada
        length: Número de bytes desejados na saída
        app_info: Informação específica da aplicação

    Returns:
        bytes: Chave derivada expandida
    """
    # Extract phase (HKDF-Extract)
    prk = hmac.new(b"\x00" * 32, key, hashlib.sha256).digest()

    # Expand phase (HKDF-Expand)
    okm = b""
    prev = b""

    for i in range((length + 31) // 32):
        prev = hmac.new(
            prk,
            prev + app_info.encode() + bytes([i + 1]),
            hashlib.sha256
        ).digest()
        okm += prev

    return okm[:length]

# Expandir shared_secret de 32 → 80 bytes
shared_secret_expanded = hkdf(shared_secret, 80)

assert len(shared_secret_expanded) == 80
```

#### Passo 4: Validar com HMAC

```python
# Calcular HMAC para validação
validation_data = secret[0:32] + secret[64:]  # 32 + 80 = 112 bytes

calculated_hmac = hmac.new(
    shared_secret_expanded[32:64],  # Chave HMAC (32 bytes)
    validation_data,                 # Dados a validar
    hashlib.sha256
).digest()

# Comparar com HMAC fornecido pelo servidor
server_hmac = secret[32:64]

if calculated_hmac != server_hmac:
    raise ValueError("Validação HMAC falhou! Possível ataque MITM!")

print("✓ Validação HMAC bem-sucedida - conexão autêntica")
```

**Por que isso é importante:**
- Garante que estamos falando com o servidor real
- Detecta tentativas de man-in-the-middle
- Valida integridade dos dados

#### Passo 5: Descriptografar Chaves

```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad

# Montar chaves criptografadas (80 bytes)
keys_encrypted = shared_secret_expanded[64:] + secret[64:]
#                 └─ 16 bytes ─┘            └─ 64 bytes ─┘
# Total: 16 + 64 = 80 bytes... Ops! Deveria ser 16 + 80 = 96!
# Correção:
keys_encrypted = shared_secret_expanded[64:] + secret[64:]
assert len(keys_encrypted) == 96  # Confirmar tamanho

# Chave AES (primeiros 32 bytes do expanded)
aes_key = shared_secret_expanded[0:32]

# Descriptografar com AES-256-CBC
# IV (Initialization Vector) é zeros por padrão
iv = b"\x00" * 16

cipher = AES.new(aes_key, AES.MODE_CBC, iv)
keys_decrypted = unpad(cipher.decrypt(keys_encrypted), AES.block_size)

assert len(keys_decrypted) == 64
```

#### Passo 6: Extrair Chaves Finais

```python
# As duas chaves principais
enc_key = keys_decrypted[0:32]   # Chave de criptografia (32 bytes)
mac_key = keys_decrypted[32:64]  # Chave de autenticação (32 bytes)

print("✓ Chaves derivadas com sucesso!")
print(f"  enc_key: {enc_key.hex()[:16]}...")
print(f"  mac_key: {mac_key.hex()[:16]}...")

# Armazenar para uso futuro
session_keys = {
    "enc_key": base64.b64encode(enc_key).decode(),
    "mac_key": base64.b64encode(mac_key).decode(),
    "client_id": client_id,
    "server_token": conn_message["data"]["serverToken"],
    "client_token": conn_message["data"]["clientToken"]
}

# Salvar em arquivo para restaurar sessão depois
with open("session.json", "w") as f:
    json.dump(session_keys, f, indent=2)
```

### Formato de Mensagens Binárias

#### Estrutura da Mensagem

Após estabelecer a conexão, as mensagens vêm em formato binário:

```
┌────────────────┬──────────────────────────────┐
│  32 bytes HMAC │   Conteúdo Criptografado    │
└────────────────┴──────────────────────────────┘
```

#### Validação de Mensagem

```python
def validate_message(message_content, mac_key):
    """
    Valida integridade da mensagem usando HMAC

    Args:
        message_content (bytes): Mensagem completa (HMAC + dados)
        mac_key (bytes): Chave MAC (32 bytes)

    Returns:
        bool: True se válida

    Raises:
        ValueError: Se inválida
    """
    # Separar HMAC e dados
    received_hmac = message_content[0:32]
    data = message_content[32:]

    # Calcular HMAC dos dados
    calculated_hmac = hmac.new(
        mac_key,
        data,
        hashlib.sha256
    ).digest()

    # Comparar
    if calculated_hmac != received_hmac:
        raise ValueError("HMAC inválido - mensagem corrompida ou adulterada!")

    return True
```

#### Descriptografia de Mensagem

```python
def decrypt_message(message_content, enc_key, mac_key):
    """
    Descriptografa mensagem binária do WhatsApp

    Args:
        message_content (bytes): Mensagem completa
        enc_key (bytes): Chave de criptografia (32 bytes)
        mac_key (bytes): Chave de autenticação (32 bytes)

    Returns:
        bytes: Dados descriptografados
    """
    # 1. Validar
    validate_message(message_content, mac_key)

    # 2. Extrair dados criptografados
    encrypted_data = message_content[32:]

    # 3. Descriptografar com AES-256-CBC
    iv = b"\x00" * 16  # IV é zeros
    cipher = AES.new(enc_key, AES.MODE_CBC, iv)
    decrypted = cipher.decrypt(encrypted_data)

    # 4. Remover padding
    decrypted = unpad(decrypted, AES.block_size)

    return decrypted
```

#### Decodificação Binária

O conteúdo descriptografado ainda está em formato binário proprietário. Veja `backend/whatsapp_binary_reader.py` para detalhes completos.

**Tags principais:**

```python
# Tags do protocolo (backend/whatsapp_defines.py)
LIST_EMPTY = 0
STREAM_8 = 2
DICTIONARY_0 = 236
DICTIONARY_1 = 237
DICTIONARY_2 = 238
DICTIONARY_3 = 239
LIST_8 = 248
LIST_16 = 249
JID_PAIR = 250
HEX_8 = 251
BINARY_8 = 252
BINARY_20 = 253
BINARY_32 = 254
NIBBLE_8 = 255
```

**Tokens (primeiros 20 de 151):**

```python
TOKENS = [
    None, None, None,
    "200", "400", "404", "500", "501", "502",
    "action", "add", "after", "archive", "author",
    "available", "battery", "before", "body",
    "broadcast", "chat"
    # ... total de 151 tokens
]
```

### Identificação de Chats (Formato JID)

#### Tipos de JID

**1. Chats Individuais:**

```
[código_país][número]@c.us
```

Exemplos:
```
5511999999999@c.us    # Brasil (55) + SP (11) + número
5521988888888@c.us    # Brasil (55) + RJ (21) + número
49123456789@c.us      # Alemanha (49) + número
```

**2. Grupos:**

```
[número_criador]-[timestamp_criação]@g.us
```

Exemplos:
```
5511999999999-1509911919@g.us    # Grupo criado em 05/11/2017
49123456789-1637849234@g.us      # Grupo criado em 25/11/2021
```

**3. Canais de Transmissão:**

```
[timestamp_criação]@broadcast
```

Exemplos:
```
1509911919@broadcast    # Canal criado em 05/11/2017
1637849234@broadcast    # Canal criado em 25/11/2021
```

#### Funções Utilitárias

```python
def parse_jid(jid):
    """
    Analisa um JID e retorna informações

    Args:
        jid (str): JID a analisar

    Returns:
        dict: Informações do JID
    """
    if "@c.us" in jid:
        number = jid.replace("@c.us", "")
        return {
            "type": "individual",
            "number": number,
            "country_code": number[:2],
            "area_code": number[2:4] if len(number) > 2 else None,
            "phone": number[4:] if len(number) > 4 else number[2:]
        }

    elif "@g.us" in jid:
        parts = jid.replace("@g.us", "").split("-")
        return {
            "type": "group",
            "creator": parts[0],
            "created_at": int(parts[1]) if len(parts) > 1 else None
        }

    elif "@broadcast" in jid:
        timestamp = int(jid.replace("@broadcast", ""))
        return {
            "type": "broadcast",
            "created_at": timestamp
        }

    return {"type": "unknown", "jid": jid}

# Uso
info = parse_jid("5511999999999@c.us")
print(info)
# {'type': 'individual', 'number': '5511999999999',
#  'country_code': '55', 'area_code': '11', 'phone': '999999999'}
```

### Protocol Buffers (Protobuf)

#### Definições Principais

O arquivo `doc/spec/def.proto` define as estruturas de mensagens:

```protobuf
// Informação completa da mensagem
message WebMessageInfo {
    required MessageKey key = 1;
    optional Message message = 2;
    optional uint64 messageTimestamp = 3;
    optional MessageStatus status = 4;
    optional string participant = 5;
    // ... mais campos
}

// Conteúdo da mensagem
message Message {
    optional string conversation = 1;
    optional ImageMessage imageMessage = 2;
    optional VideoMessage videoMessage = 3;
    optional AudioMessage audioMessage = 4;
    optional DocumentMessage documentMessage = 5;
    // ... mais tipos
}

// Mensagem de imagem
message ImageMessage {
    optional string url = 1;
    optional string mimetype = 2;
    optional string caption = 3;
    optional bytes fileSha256 = 4;
    optional uint64 fileLength = 5;
    optional uint32 height = 6;
    optional uint32 width = 7;
    optional bytes mediaKey = 8;
    optional bytes fileEncSha256 = 9;
    // ... mais campos
}
```

#### Gerar Código Python

```bash
cd doc/spec
protoc --python_out=../../backend/ def.proto
```

Isso gera `backend/whatsapp_protobuf_pb2.py`.

#### Uso no Código

```python
from whatsapp_protobuf_pb2 import WebMessageInfo, Message

# Decodificar mensagem
def decode_protobuf_message(binary_data):
    """
    Decodifica mensagem protobuf

    Args:
        binary_data (bytes): Dados binários da mensagem

    Returns:
        WebMessageInfo: Mensagem decodificada
    """
    msg_info = WebMessageInfo()
    msg_info.ParseFromString(binary_data)
    return msg_info

# Criar mensagem
def create_text_message(text, to_jid):
    """
    Cria mensagem de texto

    Args:
        text (str): Texto da mensagem
        to_jid (str): Destinatário (JID)

    Returns:
        bytes: Mensagem serializada
    """
    msg = Message()
    msg.conversation = text

    msg_info = WebMessageInfo()
    msg_info.key.remoteJid = to_jid
    msg_info.message.CopyFrom(msg)
    msg_info.messageTimestamp = int(time.time())

    return msg_info.SerializeToString()
```

---

## 🧪 Testando e Depurando

### Executando a Aplicação

#### Passo a Passo

**1. Iniciar os serviços:**

```bash
cd waweb-api-toolkit
npm start
```

Você verá três processos iniciando:

```
[0] whatsapp-web-reveng API server listening on port 2019
[0] whatsapp-web-reveng HTTP server listening on port 2018
[1] whatsapp-web-backend listening on port 2020
[2] Compiled client/css/main.scss successfully
```

**2. Abrir no navegador:**

```
http://localhost:2018/
```

**3. Conectar ao backend:**

Clique no botão **"Connect to Backend"**

Você verá no console:
```
✓ Backend connected
```

**4. Conectar ao WhatsApp:**

Clique no botão **"Connect to WhatsApp"**

Você verá:
```
✓ WhatsApp connected
```

**5. Gerar QR Code:**

Clique no botão **"Generate QR Code"**

Um QR code aparecerá na tela.

**6. Escanear com celular:**

1. Abra WhatsApp no celular
2. Vá em **Configurações** → **WhatsApp Web**
3. Toque em **"Escanear QR code"**
4. Aponte para o QR code na tela

**7. Aguardar login:**

Após escanear:
```
✓ Login successful
✓ Connected as: Seu Nome
✓ Phone: 5511999999999@c.us
```

### Pontos de Debug

#### No Navegador (Chrome DevTools)

**Console JavaScript:**

```javascript
// Ligar debug verboso
localStorage.setItem("debug", "true");

// Ver todas as mensagens WebSocket
// (já logadas automaticamente se debug = true)

// Inspecionar cliente WebSocket
console.log(apiWebsocket);
console.log(apiWebsocket.isOpen);
console.log(apiWebsocket.messageHandlers);
```

**Network → WS (WebSocket):**

1. Abra **DevTools** (F12)
2. Vá em **Network**
3. Filtre por **WS** (WebSocket)
4. Clique na conexão para ver:
   - Messages: todas mensagens trocadas
   - Frames: frames individuais
   - Timing: informações de timing

#### No Node.js (Terminal)

O console mostra todas as mensagens:

```
[API] Received from client: {"from":"client","type":"call","command":"backend-generateQRCode"}
[API] Forwarding to backend...
[API] Received from backend: {"from":"backend","type":"generated_qr_code","image":"data:image..."}
[API] Forwarding to client...
```

**Debug adicional:**

Edite `index.js` e adicione:

```javascript
// No topo do arquivo
const DEBUG = true;

// Função de debug
function debug(...args) {
    if (DEBUG) console.log("[DEBUG]", ...args);
}

// Use em pontos estratégicos
clientWebsocket.on("message", msg => {
    debug("Client message received:", msg);
    // ... resto do código
});
```

#### No Python (Terminal)

O backend usa `eprint()` para stderr:

```python
# Todas as mensagens são logadas automaticamente
eprint("sending", json.dumps(obj));
eprint("received", self.data);
```

**Debug adicional:**

Edite `backend/whatsapp_web_backend.py`:

```python
# No topo
DEBUG = True

def debug(*args):
    if DEBUG:
        eprint("[DEBUG]", *args)

# Use em pontos estratégicos
def handleMessage(self):
    debug("Message received:", self.data)
    # ... resto do código
```

### Problemas Comuns e Soluções

#### 1. Portas em Uso

**Sintoma:**
```
Error: listen EADDRINUSE: address already in use :::2018
```

**Solução:**

```bash
# Verificar o que está usando a porta
lsof -i :2018
lsof -i :2019
lsof -i :2020

# Matar processo (substitua PID)
kill -9 PID

# Ou liberar todas as portas do projeto
killall node
killall python
```

#### 2. Python 2.7 não encontrado

**Sintoma:**
```
python: command not found
```

**Solução:**

```bash
# Ubuntu/Debian
sudo apt-get install python2.7

# Criar alias (adicione ao ~/.bashrc)
alias python=python2.7

# Ou use python2 explicitamente
python2 backend/whatsapp_web_backend.py
```

#### 3. Bibliotecas Python faltando

**Sintoma:**
```
ImportError: No module named 'curve25519'
```

**Solução:**

```bash
# Reinstalar todas as dependências
pip install -r requirements.txt

# Ou instalar individualmente
pip install curve25519-donna
pip install pycryptodome
pip install pyqrcode
pip install protobuf
pip install websocket-client
pip install git+https://github.com/dpallot/simple-websocket-server.git
```

#### 4. QR Code expira

**Sintoma:**
QR code não funciona após 20 segundos

**Causa:**
O `ttl` (time to live) é 20000ms = 20 segundos

**Solução:**
1. Gere um novo QR code
2. Escaneie rapidamente
3. (Futuro) Implementar comando "reref" para renovar

#### 5. Erro de descriptografia

**Sintoma:**
```
ValueError: HMAC validation failed
```

**Causas possíveis:**
- Chaves derivadas incorretamente
- Mensagem corrompida
- Ataque man-in-the-middle

**Debug:**

```python
# Adicione logs no processo de derivação
print("Secret length:", len(secret))
print("Secret (hex):", secret.hex()[:32], "...")
print("Shared secret:", shared_secret.hex()[:32], "...")
print("Expanded:", shared_secret_expanded.hex()[:32], "...")
print("Calculated HMAC:", calculated_hmac.hex())
print("Server HMAC:", server_hmac.hex())
```

#### 6. WebSocket desconecta

**Sintoma:**
Conexão cai após alguns minutos

**Causas:**
- Timeout do servidor
- Rede instável
- Não há heartbeat/ping-pong

**Solução (futura):**

Implementar ping/pong:

```javascript
// Em WebSocketClient.js
setInterval(() => {
    if (this.isOpen) {
        this.ws.ping();
    }
}, 30000); // A cada 30 segundos
```

### Ferramentas de Debug

#### Visualizar Mensagens Binarias

```python
# Salvar mensagem para análise
def save_binary_message(data, filename):
    """Salva mensagem binária para análise posterior"""
    with open(f"backend/decodable_msgs/{filename}.bin", "wb") as f:
        f.write(data)

    # Também salvar como hex
    with open(f"backend/decodable_msgs/{filename}.hex", "w") as f:
        f.write(data.hex())

# Uso
save_binary_message(encrypted_message, f"msg_{timestamp}")
```

#### Visualizar JSON Tree

No navegador, o projeto usa `jsonTree.js`:

```javascript
// Renderizar JSON como árvore
const tree = jsonTree.create(jsonData, document.getElementById("output"));
```

Isso cria uma visualização expansível do JSON.

#### Monitorar Performance

```javascript
// Medir tempo de operações
console.time("generateQRCode");
await generateQRCode();
console.timeEnd("generateQRCode");
// Output: generateQRCode: 1234.56ms
```

```python
# Python
import time

start = time.time()
result = expensive_operation()
elapsed = time.time() - start
print(f"Operation took {elapsed:.2f} seconds")
```

---

## 📚 Referências e Recursos

### Documentação Interna

| Arquivo | Conteúdo | Finalidade |
|---------|----------|------------|
| `README.md` | Documentação do usuário | Instruções de uso, detalhes do protocolo |
| `CLAUDE.md` | Este arquivo | Guia completo para IA e desenvolvedores |
| `doc/spec/def.proto` | Definições Protobuf | Estrutura das mensagens |
| `doc/img/app-architecture1000.png` | Diagrama de arquitetura | Visualização do sistema |

### Projetos Relacionados

Reimplementações do WhatsApp Web em outras linguagens:

| Projeto | Linguagem | Link | Status |
|---------|-----------|------|--------|
| **Baileys** | Node.js/TypeScript | [GitHub](https://github.com/WhiskeySockets/Baileys) | ✅ Ativo |
| **WaJs** | TypeScript | [GitHub](https://github.com/ndunks/WaJs) | ✅ Ativo |
| **kyros** | Python 3 | [GitHub](https://github.com/p4kl0nc4t/kyros) | ⚠️ Manutenção |
| **whatsappweb-rs** | Rust | [GitHub](https://github.com/wiomoc/whatsappweb-rs) | ⚠️ Descontinuado |
| **go-whatsapp** | Go | [GitHub](https://github.com/Rhymen/go-whatsapp) | ⚠️ Descontinuado |
| **whatsappweb-clj** | Clojure | [GitHub](https://github.com/vzaramel/whatsappweb-clj) | ⚠️ Experimental |

### Recursos Externos

#### Protocolos e RFCs

- **RFC 6455** - The WebSocket Protocol
  https://datatracker.ietf.org/doc/html/rfc6455

- **RFC 5869** - HKDF (HMAC-based Extract-and-Expand Key Derivation Function)
  https://datatracker.ietf.org/doc/html/rfc5869

#### Criptografia

- **Curve25519** - Wikipedia
  https://en.wikipedia.org/wiki/Curve25519

- **AES** - Advanced Encryption Standard
  https://en.wikipedia.org/wiki/Advanced_Encryption_Standard

- **HMAC** - Hash-based Message Authentication Code
  https://en.wikipedia.org/wiki/HMAC

- **Protocol Buffers** - Google
  https://developers.google.com/protocol-buffers

#### Bibliotecas Usadas

**Python:**
- `curve25519-donna` - https://github.com/agl/curve25519-donna
- `pycryptodome` - https://pycryptodome.readthedocs.io/
- `pyqrcode` - https://github.com/mnooner256/pyqrcode

**Node.js:**
- `express` - https://expressjs.com/
- `ws` - https://github.com/websockets/ws
- `nodemon` - https://nodemon.io/

**JavaScript (Browser):**
- `jQuery` - https://jquery.com/
- `Bootstrap` - https://getbootstrap.com/docs/3.3/
- `SJCL` - Stanford JavaScript Crypto Library

### Comunidade e Suporte

#### Repositório Original

- **URL:** https://github.com/sigalor/whatsapp-web-reveng
- **Issues:** Para reportar bugs ou pedir features
- **Wiki:** Documentação adicional

#### Saraiva.AI

Para mais conteúdos educacionais sobre IA e engenharia reversa:

- **Site:** [saraiva.ai](https://saraiva.ai) (exemplo fictício)
- **Cursos:** Engenharia reversa, Criptografia aplicada, Protocolos de rede
- **Professor:** Fellipe Saraiva

---

## ⚖️ Avisos Legais e de Segurança

### Aviso Legal

⚠️ **IMPORTANTE:**

Este código **NÃO é afiliado, autorizado, mantido, patrocinado ou endossado** pelo WhatsApp ou qualquer de suas afiliadas ou subsidiárias. Este é um software **independente e não oficial**.

**Use por sua própria conta e risco.**

O WhatsApp pode:
- Bloquear contas que usam clientes não oficiais
- Modificar o protocolo a qualquer momento
- Tomar ações legais contra uso não autorizado

### Aviso de Criptografia

🔒 **NOTICE:**

Este software contém tecnologia de criptografia. O país em que você reside pode ter restrições sobre:

- **Importação** de software de criptografia
- **Posse** de software de criptografia
- **Uso** de software de criptografia
- **Reexportação** para outro país de software de criptografia

**ANTES** de usar qualquer software de criptografia, verifique as leis, regulamentos e políticas do seu país sobre importação, posse, uso e reexportação de software de criptografia.

Veja http://www.wassenaar.org/ para mais informações.

### Classificação dos EUA

O Departamento de Comércio dos EUA, Bureau of Industry and Security (BIS), classificou este software como:

**ECCN (Export Control Classification Number): 5D002.C.1**

Inclui software de segurança da informação usando ou executando funções criptográficas com algoritmos assimétricos.

A forma e maneira desta distribuição o tornam elegível para exportação sob a **License Exception ENC Technology Software Unrestricted (TSU)** (veja o BIS Export Administration Regulations, Seção 740.13) para código objeto e código fonte.

### Uso Educacional

📖 **RECOMENDAÇÃO:**

Este projeto deve ser usado para fins:

- **Educacionais** - Aprender sobre protocolos e criptografia
- **Pesquisa** - Estudar engenharia reversa
- **Acadêmicos** - Análise de segurança

**NÃO recomendamos** uso em produção ou para:
- Spam ou mensagens em massa
- Violação de termos de serviço
- Atividades ilegais ou maliciosas
- Coleta não autorizada de dados

### Responsabilidade

Os autores e mantenedores deste projeto:

- **NÃO se responsabilizam** por qualquer uso indevido
- **NÃO fornecem garantias** de funcionamento
- **NÃO oferecem suporte** para uso comercial
- **NÃO assumem responsabilidade** por bans ou bloqueios

---

## 📝 Metadados do Documento

**Última Atualização:** 2025-11-14
**Versão:** 2.0.0 (Edição Saraiva.AI)
**Idioma:** Português Brasileiro (pt-BR)
**Autor:** Prof. Fellipe Saraiva
**Organização:** Saraiva.AI
**Licença do Documento:** CC BY-SA 4.0

### Histórico de Versões

| Versão | Data | Alterações |
|--------|------|------------|
| 1.0.0 | 2025-11-14 | Versão inicial em inglês |
| 2.0.0 | 2025-11-14 | Tradução completa para pt-BR, reestruturação didática |

### Contribuindo

Este documento é vivo e pode ser melhorado! Sugestões:

1. **Correções:** Erros de português, técnicos ou conceituais
2. **Adições:** Novos exemplos, seções ou explicações
3. **Melhorias:** Clareza, organização, diagramas

Para contribuir, abra uma issue ou pull request no repositório.

---

## 🎓 Mensagem Final do Professor

Chegamos ao fim deste guia extenso! Espero que tenha sido uma jornada educativa e esclarecedora.

Este projeto é um **excelente exemplo** de como a engenharia reversa pode ser usada de forma construtiva para:

1. **Aprender** sobre protocolos de comunicação modernos
2. **Entender** criptografia aplicada em sistemas reais
3. **Desenvolver** habilidades de análise e debugging
4. **Criar** software interoperável

### Próximos Passos

Agora que você domina o básico, sugiro:

1. **Experimente:** Rode o projeto, gere um QR code, veja funcionando
2. **Explore:** Leia o código fonte, entenda cada módulo
3. **Modifique:** Adicione um novo comando, implemente uma feature
4. **Compartilhe:** Documente seu aprendizado, ensine outros

### Continue Aprendendo

Na **Saraiva.AI**, acreditamos que o conhecimento deve ser livre e acessível. Este documento faz parte da nossa missão de democratizar o aprendizado sobre IA, segurança e engenharia de software.

Visite nossos outros recursos:
- Cursos de criptografia aplicada
- Workshops de engenharia reversa
- Tutoriais de análise de protocolos
- Comunidade de desenvolvedores

**Bons estudos e boa codificação!**

*Prof. Fellipe Saraiva*
*Fundador - Saraiva.AI*
*"Transformando conhecimento técnico em aprendizado acessível"*

---

📚 **Saraiva.AI** - A Maior Livraria Grátis de Conhecimento sobre IA
