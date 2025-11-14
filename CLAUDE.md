# CLAUDE.md - WhatsApp Web API Toolkit

This document provides comprehensive guidance for AI assistants working with the WhatsApp Web API Toolkit codebase.

## Project Overview

**Name:** whatsapp-web-reveng (WhatsApp Web API Toolkit)
**Purpose:** Reverse-engineered implementation of the WhatsApp Web API providing a complete description and re-implementation of WhatsApp Web's internal communication protocols.
**License:** MIT
**Author:** sigalor

### What This Project Does

This project provides a web-based client for WhatsApp Web by reverse-engineering the protocol. It operates using WebSockets and implements the complete encryption/decryption pipeline including:
- AES 256 CBC encryption
- Curve25519 for Diffie-Hellman key agreement
- HKDF for extended shared secret generation
- HMAC with SHA256 for message authentication
- Binary message format encoding/decoding
- Protocol Buffer message handling

## Architecture

### Three-Layer Architecture

```
┌─────────────┐         ┌─────────────┐         ┌──────────────┐
│   Browser   │ ◄─────► │   Node.js   │ ◄─────► │    Python    │
│   Client    │  :2018  │     API     │  :2020  │   Backend    │
│ (HTML/JS)   │         │  (index.js) │         │  (whatsapp)  │
└─────────────┘         └─────────────┘         └──────────────┘
                                                        │
                                                        ▼
                                                 ┌──────────────┐
                                                 │  WhatsApp    │
                                                 │  Web Server  │
                                                 │ (wss://w*..) │
                                                 └──────────────┘
```

**Port Configuration:**
- **2018**: HTTP server serving static client files (Express)
- **2019**: WebSocket server for client-to-API communication
- **2020**: WebSocket server for API-to-backend communication

### Component Breakdown

#### 1. Frontend Client (`client/`)
- **Location:** `/client/`
- **Technology:** HTML, JavaScript, CSS (SCSS), jQuery
- **Key Files:**
  - `index.html` - Main web interface
  - `login-via-js-demo.html` - JavaScript-only demo implementation
  - `js/main.js` - Primary client logic and WebSocket communication
  - `js/WebSocketClient.js` - WebSocket abstraction layer
  - `js/BootstrapStep.js` - Request/response orchestration
  - `js/UpdaterPromise.js` - Promise-based update handling

#### 2. Node.js API Layer (`index.js`)
- **Location:** `/index.js`
- **Technology:** Node.js, Express, WebSocket (ws)
- **Purpose:** Message routing and orchestration between browser client and Python backend
- **Key Functions:**
  - Serves static client files via Express
  - Manages WebSocket connections on port 2019
  - Routes commands between client and backend
  - Handles connection lifecycle for WhatsApp instances

**Command Flow Pattern:**
```
Client → API (command: "api-connectBackend")
API → Backend (command: "backend-connectWhatsApp")
API → Backend (command: "backend-generateQRCode")
API → Backend (command: "backend-restoreSession")
API → Backend (command: "backend-disconnectWhatsApp")
```

#### 3. Python Backend (`backend/`)
- **Location:** `/backend/`
- **Technology:** Python 2.7
- **Entry Point:** `whatsapp_web_backend.py`
- **Key Modules:**
  - `whatsapp.py` - Core WhatsApp Web client implementation
  - `whatsapp_binary_reader.py` - Binary message decoder
  - `whatsapp_binary_writer.py` - Binary message encoder
  - `whatsapp_defines.py` - Protocol constants and tokens
  - `whatsapp_protobuf_pb2.py` - Generated protobuf message definitions
  - `utilities.py` - Helper functions

## Directory Structure

```
waweb-api-toolkit/
├── backend/                     # Python backend implementation
│   ├── whatsapp_web_backend.py # WebSocket server (port 2020)
│   ├── whatsapp.py             # Main WhatsApp client logic
│   ├── whatsapp_binary_reader.py
│   ├── whatsapp_binary_writer.py
│   ├── whatsapp_defines.py
│   ├── whatsapp_protobuf_pb2.py
│   └── utilities.py
├── client/                      # Frontend web client
│   ├── css/                    # Stylesheets (SCSS)
│   ├── js/                     # JavaScript client logic
│   │   ├── main.js
│   │   ├── WebSocketClient.js
│   │   ├── BootstrapStep.js
│   │   └── UpdaterPromise.js
│   ├── lib/                    # Third-party libraries
│   ├── index.html
│   └── login-via-js-demo.html
├── doc/                        # Documentation
│   ├── img/                    # Architecture diagrams
│   └── spec/                   # Protocol specifications
│       ├── def.proto           # Protobuf definitions
│       └── protobuf-extractor/ # Protobuf extraction tool
├── windows/                    # Windows-specific dependencies
├── index.js                    # Node.js API server
├── index_jsdemo.js            # JavaScript demo server
├── package.json               # Node.js dependencies
├── requirements.txt           # Python dependencies
├── Dockerfile                 # Docker configuration
├── shell.nix                  # Nix environment
├── session.json               # Session persistence
└── README.md                  # Project documentation
```

## Development Workflows

### Setting Up the Environment

#### Option 1: Using Nix (Recommended)
```bash
nix-shell  # Enter Nix environment with all dependencies
npm start  # Start development servers
```

With `direnv` installed, dependencies auto-load when entering the directory.

#### Option 2: Bare Metal Installation
```bash
# Install Node.js dependencies
npm install -f

# Install Python dependencies
pip install -r requirements.txt

# Start development (Linux/Mac)
npm start

# Start development (Windows)
npm run win
```

#### Option 3: Docker
```bash
docker build . -t whatsapp-web-reveng
docker run -p 2019:2019 -p 2018:2018 whatsapp-web-reveng
```
Access at: http://localhost:2018/

### Development Scripts

**package.json scripts:**
- `npm start` / `npm run dev` - Development mode with auto-reload
  - Starts Node.js API with nodemon (auto-restart on .js changes)
  - Starts Python backend with nodemon (auto-restart on .py changes)
  - Watches and compiles SCSS to CSS
- `npm run win` - Windows development mode
- `npm run __run_in_docker` - Docker container mode

### Making Changes

#### Adding New Commands

Follow this pattern when adding new API commands:

**1. Client Side (`client/js/main.js`)**
```javascript
new BootstrapStep({
    websocket: apiWebsocket,
    request: {
        type: "call",
        callArgs: { command: "backend-yourCommand" },
        successCondition: obj => obj.type == "expected_response",
        successActor: response => {
            // Handle response
        }
    }
}).run(timeout);
```

**2. API Layer (`index.js`)**
```javascript
clientWebsocket.waitForMessage({
    condition: obj => obj.from == "client" &&
                     obj.type == "call" &&
                     obj.command == "backend-yourCommand",
    keepWhenHit: true
}).then(clientCallRequest => {
    new BootstrapStep({
        websocket: backendWebsocket,
        request: {
            type: "call",
            callArgs: {
                command: "backend-yourCommand",
                whatsapp_instance_id: backendWebsocket.activeWhatsAppInstanceId
            },
            successCondition: obj => obj.type == "your_response_type"
        }
    }).run(backendInfo.timeout).then(backendResponse => {
        clientCallRequest.respond(backendResponse.data);
    });
}).run();
```

**3. Backend (`backend/whatsapp_web_backend.py`)**
```python
cmd = obj["command"]
if cmd == "backend-yourCommand":
    currWhatsAppInstance.yourMethod(callback)
```

**4. WhatsApp Client (`backend/whatsapp.py`)**
```python
def yourMethod(self, callback):
    tag = str(getTimestampMs())
    self.messageQueue[tag] = {
        "callback": callback,
        # additional context
    }
    self.sendMessage(tag, ["action", "your_action", "params"])
```

## Key Conventions

### Code Style

#### Python
- Python 2.7 syntax (uses `print_function` from `__future__`)
- UTF-8 encoding with `# -*- coding: utf-8 -*-` header
- Semicolons used at end of statements (unusual but consistent in this codebase)
- `eprint()` function for stderr logging
- CamelCase for classes, snake_case for functions/variables

#### JavaScript
- ES6+ features (async/await, arrow functions, let/const)
- CamelCase for classes and constructors
- camelCase for functions and variables
- Promises used extensively for async operations
- WebSocket message format: `"messageTag,JSON_PAYLOAD"`

### Message Format Convention

**All WebSocket messages follow this format:**
```
messageTag,{JSON_OBJECT}
```

Example:
```
1234567890,{"from":"client","type":"call","command":"backend-generateQRCode"}
```

**Message tags:**
- Usually timestamp in milliseconds: `str(getTimestampMs())`
- Can be arbitrary but must not contain commas
- Used for request/response correlation

### JSON Message Structure

**Standard Fields:**
- `from`: Message origin ("client", "api", "backend", "api2backend", etc.)
- `type`: Message type ("call", "connected", "resource_connected", "error", etc.)
- `command`: Command identifier (e.g., "backend-generateQRCode")

### Instance Management

**WhatsApp instances:**
- Each connection gets unique `clientInstanceId` (UUID hex)
- Tracked in `WhatsAppWeb.clientInstances` dict
- Cleanup happens on connection close
- `resource_instance_id` used to track instances across layers

### Callback Pattern

Python backend uses callback objects with this structure:
```python
callback = {
    "func": lambda obj, cbSelf: handlerFunction(obj),
    "tag": messageTag,
    "args": { "additional": "context" }
}
```

## Protocol Details

### WhatsApp Web Connection Flow

1. **Initialize Connection**
   - Connect to `wss://w[1-8].web.whatsapp.com/ws`
   - Set header: `Origin: https://web.whatsapp.com`
   - Generate 16-byte base64 clientId

2. **Send Init Message**
   ```
   tag,["admin","init",[0,3,2390],["Browser Description","ShortDesc"],"clientId",true]
   ```

3. **Receive Server Response**
   - Contains `ref` (server ID for QR code)
   - Contains `ttl` (QR code validity, typically 20000ms)

4. **Generate QR Code**
   - Create Curve25519 private key
   - QR content: `ref,base64(publicKey),clientId`

5. **After QR Scan**
   - Receive `Conn` message with `secret`, `clientToken`, `serverToken`
   - Receive `Stream` and `Props` messages
   - Generate encryption keys (encKey, macKey)

### Encryption Key Derivation

```
secret (144 bytes, base64 encoded)
├─ [0:32]   → Public key for shared secret generation
├─ [32:64]  → HMAC for validation
└─ [64:144] → Part of encrypted keys

sharedSecret = DH(privateKey, secret[0:32])
sharedSecretExpanded = HKDF(sharedSecret, 80 bytes)

Validation:
  HMAC-SHA256(sharedSecretExpanded[32:64], secret[0:32] + secret[64:])
  should equal secret[32:64]

keysEncrypted = sharedSecretExpanded[64:] + secret[64:]
keysDecrypted = AES-Decrypt(sharedSecretExpanded[0:32], keysEncrypted)

encKey = keysDecrypted[0:32]   # For message encryption/decryption
macKey = keysDecrypted[32:64]  # For message authentication
```

### Binary Message Format

**Message Structure:**
```
[32 bytes HMAC] + [Encrypted Content]
```

**Validation:**
```python
calculated_hmac = HMAC-SHA256(macKey, messageContent[32:])
assert calculated_hmac == messageContent[0:32]
```

**Decryption:**
```python
decrypted = AES-CBC-Decrypt(encKey, messageContent[32:])
```

**Binary Decoding:**
See `backend/whatsapp_binary_reader.py` for full implementation of the node-based binary format.

### Chat Identification (JID Format)

- **Individual chats:** `[countrycode][number]@c.us`
  Example: `49123456789@c.us`
- **Group chats:** `[creator_number]-[timestamp]@g.us`
  Example: `49123456789-1509911919@g.us`
- **Broadcast channels:** `[timestamp]@broadcast`
  Example: `1509911919@broadcast`

### Protocol Buffer Messages

The `doc/spec/def.proto` file defines the message structure. Key message types:
- `WebMessageInfo` - Wrapper for all messages
- `Message` - Contains actual message content
- `ContextInfo` - Message context (quotes, mentions, forwarding)
- Various media types (Image, Video, Audio, Document)
- Interactive elements (Buttons, Templates, Locations)

Generate Python bindings:
```bash
cd doc/spec
protoc --python_out=../../backend/ def.proto
```

## File Patterns and Conventions

### Session Persistence
- `session.json` - Stores session data (gitignored in production)
- Contains `clientToken`, `serverToken` for session restoration

### Generated/Ignored Files
```
node_modules/          # Node.js dependencies
.sass-cache/           # SCSS compilation cache
backend/decodable_msgs    # Successfully decoded messages
backend/undecodable_msgs  # Failed decoding attempts
log.txt                # Runtime logs
misc/                  # Miscellaneous files
.vscode/, .idea/       # IDE configurations
```

### CSS/SCSS
- Source: `client/css/main.scss`
- Compiled: `client/css/main.css`
- Auto-compiled via `sass --watch` during development

## Testing and Debugging

### Running the Application

1. Start all services:
   ```bash
   npm start
   ```

2. Open browser to `http://localhost:2018/`

3. Click "Connect to Backend" → "Connect to WhatsApp" → "Generate QR Code"

4. Scan QR code with WhatsApp mobile app

### Debug Output

**Python backend:**
- Uses `eprint()` for stderr logging
- All messages logged automatically
- Check console for WebSocket traffic

**Node.js API:**
- Console.log for standard output
- Monitor WebSocket connections on ports 2019/2020

**Browser client:**
- Open Developer Console
- WebSocket traffic visible in Network → WS tab
- JSON tree visualization for messages

### Common Issues

1. **Port conflicts:** Ensure ports 2018, 2019, 2020 are available
2. **Python 2.7 requirement:** Some crypto libraries require Python 2.7
3. **Windows setup:** Requires Visual C++ 9.0 for curve25519-donna
4. **Session expiry:** QR codes expire after `ttl` milliseconds (20 seconds)

## Dependencies

### Node.js (package.json)
**Runtime:**
- `express` ^4.17.1 - HTTP server
- `ws` ^5.2.3 - WebSocket implementation
- `lodash` ^4.17.21 - Utility library
- `fs`, `path`, `string_decoder` - Node.js built-ins

**Development:**
- `concurrently` ^3.6.1 - Run multiple commands
- `nodemon` ^1.19.4 - Auto-restart on file changes
- `sass` ^1.25.0 - SCSS compilation

### Python (requirements.txt)
- `websocket-client` - WebSocket client
- `simple-websocket-server` - WebSocket server (from GitHub)
- `curve25519-donna` - Elliptic curve cryptography
- `pycryptodome` - AES encryption
- `pyqrcode` - QR code generation
- `protobuf` - Protocol buffer support

### Client Libraries (`client/lib/`)
- jQuery 3.2.1
- Bootstrap 3.3.7 + Bootbox 4.4.0
- Curve25519-js (axlsign)
- SJCL 1.0.7 (crypto library)
- Moment.js 2.20.1
- Lodash
- QRCode generator
- JSON Tree visualizer
- colResizable (table columns)

## Important Notes for AI Assistants

### What to Be Careful About

1. **Encryption Security:** This code handles cryptographic operations. Do not modify encryption/decryption logic without thorough understanding.

2. **Python 2.7 Legacy:** This project uses Python 2.7. Be aware of:
   - `print` requires `from __future__ import print_function`
   - Unicode handling differs from Python 3
   - Some dependencies may not work with Python 3

3. **Message Format:** The `tag,JSON` format is critical. Breaking this breaks all communication.

4. **WebSocket Lifecycle:** Proper connection/disconnection handling is essential to prevent resource leaks.

5. **Binary Format:** The binary message decoder is complex and fragile. Changes require deep protocol knowledge.

### When Making Changes

1. **Test the full chain:** Client → API → Backend → WhatsApp
2. **Check all three console outputs** (browser, Node.js, Python)
3. **Verify encryption** still works after any crypto-related changes
4. **Follow the callback pattern** when adding new commands
5. **Use proper error handling** at each layer

### Current Limitations

- Python 2.7 only (consider migration to Python 3 as future improvement)
- No persistent session storage (session.json is basic)
- Limited error recovery mechanisms
- Hardcoded ports (consider environment variables)
- No automated tests
- Binary decoder may fail on new message types

### Future Improvements Suggested

- [ ] Migrate to Python 3
- [ ] Add comprehensive error handling
- [ ] Implement session restoration (clientToken/serverToken)
- [ ] Add automated tests
- [ ] Support configuration via environment variables
- [ ] Create proper logging framework
- [ ] Document binary message format more thoroughly
- [ ] Add WebSocket reconnection logic
- [ ] Implement message sending capabilities
- [ ] Create more user-friendly UI

## Resources

### Internal Documentation
- `README.md` - User-facing documentation with protocol details
- `doc/spec/def.proto` - Protocol Buffer message definitions
- Architecture diagram: `doc/img/app-architecture1000.png`

### Related Projects
- **Baileys** (Node.js): https://github.com/WhiskeySockets/Baileys
- **WaJs** (TypeScript): https://github.com/ndunks/WaJs
- **kyros** (Python): https://github.com/p4kl0nc4t/kyros
- **whatsappweb-rs** (Rust): https://github.com/wiomoc/whatsappweb-rs
- **go-whatsapp** (Go): https://github.com/Rhymen/go-whatsapp

### External References
- WhatsApp Web: https://web.whatsapp.com/
- WebSocket Protocol: RFC 6455
- Curve25519: https://en.wikipedia.org/wiki/Curve25519
- HKDF: https://en.wikipedia.org/wiki/HKDF

## Legal and Security Notices

**Legal:** This code is not affiliated with WhatsApp. It is independent and unofficial software. Use at your own risk.

**Cryptography:** This software contains encryption technology. Check your local laws regarding import, possession, use, and re-export of encryption software before using.

**Security Analysis:** This is a reverse-engineered implementation of a proprietary protocol. It should be used for educational and research purposes only.

---

**Last Updated:** 2025-11-14
**Version:** 1.0.0
**For Questions:** Refer to original repository at https://github.com/sigalor/whatsapp-web-reveng
