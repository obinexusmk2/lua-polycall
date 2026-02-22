# lua-polycall — LibPolyCall Trial v1 Lua Binding

![LibPolyCall Protocol](https://img.shields.io/badge/Protocol-LibPolyCall%20v1-blue)
![Lua Version](https://img.shields.io/badge/Lua-5.3%2B-blue)
![License](https://img.shields.io/badge/License-MIT-yellow)
![Architecture](https://img.shields.io/badge/Architecture-Adapter%20Pattern-red)
![LuaRocks](https://img.shields.io/badge/LuaRocks-lua--polycall-orange)

**Protocol-Compliant Lua Adapter for polycall.exe Runtime**

> Part of the LibPolyCall Trial v1 binding ecosystem — program-first architecture for every language.

---

## Critical Protocol Compliance Notice

lua-polycall is an **ADAPTER BINDING** for the LibPolyCall Trial v1 runtime system.
This binding **DOES NOT** execute user code directly. All execution must flow
through the `polycall.exe` runtime following the program-first architecture paradigm.

---

## Table of Contents

- [Installation](#installation)
- [Runtime Prerequisites](#runtime-prerequisites)
- [Quick Start](#quick-start)
- [Architecture](#architecture)
- [Configuration](#configuration)
- [Examples](#examples)
- [Project Structure](#project-structure)
- [Development](#development)

---

## Installation

### Via LuaRocks

```bash
luarocks install lua-polycall
```

### From Source

```bash
git clone https://github.com/obinexusmk2/lua-polycall
cd lua-polycall
luarocks make lua-polycall-1.0-1.rockspec
```

### System Dependencies

Install Lua 5.3+ before proceeding:

```bash
# Ubuntu / Debian / WSL
sudo apt install lua5.4 luarocks

# macOS
brew install lua luarocks
```

---

## Runtime Prerequisites

lua-polycall **requires** the `polycall.exe` runtime to function.
The binding acts as a protocol adapter and cannot operate independently.

```bash
# Start runtime server (default port 8084)
polycall.exe server --port 8084 --host localhost

# Verify connection
lua -e "require('polycall.core').ping()"
```

---

## Quick Start

### Basic Client Connection

```lua
local polycall_core = require('polycall.core')

local client = polycall_core.new_client({
    polycall_host = "localhost",
    polycall_port = 8084,
    protocol_version = "1.0",
    architecture_pattern = "adapter"
})

print("Protocol: " .. polycall_core.PROTOCOL_VERSION)
print("Architecture: " .. polycall_core.ARCHITECTURE_PATTERN)
```

### State Machine Flow

```lua
local polycall_state = require('polycall.state')

-- Valid transition sequence
-- INIT → HANDSHAKE → AUTH → READY → EXECUTING → READY

local valid = polycall_state.validate_transition(
    polycall_state.STATES.INIT,
    polycall_state.STATES.HANDSHAKE
)
print("Transition valid: " .. tostring(valid))
```

### Telemetry Observer

```lua
local polycall_telemetry = require('polycall.telemetry')

local observer = polycall_telemetry.new_observer()

polycall_telemetry.observe_protocol_event(observer, "connection_attempt", {
    timestamp = os.time(),
    host = "localhost",
    port = 8084
})

print("Observations: " .. #observer.observations)
```

---

## Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Lua Script    │────│  lua-polycall   │────│  polycall.exe   │
│   User Code     │    │   (Adapter)     │    │   (Runtime)     │
└─────────────────┘    └─────────────────┘    └─────────────────┘
        │                        │                        │
    Logic Definition    Protocol Translation       Execution
```

### State Machine

```
INIT → HANDSHAKE → AUTH → READY → EXECUTING → READY
  ↓        ↓         ↓      ↓         ↓          ↓
ERROR → ERROR → ERROR → ERROR → ERROR → SHUTDOWN
```

### Core Modules

| Module | Purpose |
|---|---|
| `polycall.core` | Main protocol adapter and client interface |
| `polycall.state` | State machine validation and transitions |
| `polycall.telemetry` | Silent protocol observation |
| `polycall.protocol` | Low-level protocol communication |
| `polycall.auth` | Zero-trust authentication |
| `polycall.config` | Configuration management via `.polycallrc` |
| `polycall.cli` | Command-line interface |

---

## Configuration

Configuration is managed via `lua.polycallrc` in the project root:

```ini
# Runtime connection
polycall_runtime_host=localhost
polycall_runtime_port=8084
protocol_version=1.0

# Security — Zero-Trust Architecture
require_auth=true
ssh_cert_required=true
zero_trust_validation=true

# Lua toolchain
lua_version=5.3
luarocks_integration=true

# Server binding
port=3004:8085
server_type=lua
max_connections=50
```

---

## Examples

The `examples/` directory contains working demonstrations:

```bash
# Protocol client and state machine demonstration
lua examples/lua-polycall-examples.lua

# Full protocol server implementation
lua examples/server.lua
```

### Server Usage

```lua
local PolyCallServer = require('examples.server')

local server = PolyCallServer:new("lua.polycallrc")
local ok, err = server:initialize()

if ok then
    server:run()
else
    print("Init failed: " .. err)
end
```

---

## Project Structure

```
lua-polycall/
├── polycall/               # Core binding modules
│   ├── core.lua
│   ├── state.lua
│   ├── telemetry.lua
│   ├── protocol.lua
│   ├── auth.lua
│   ├── config.lua
│   └── cli.lua
├── examples/               # Usage demonstrations
│   ├── lua-polycall-examples.lua
│   └── server.lua
├── bin/                    # Executable scripts
├── config/                 # Configuration templates
├── lua-polycall-1.0-1.rockspec
├── lua.polycallrc
└── README.md
```

---

## Development

```bash
# Install development dependencies
luarocks install luacheck
luarocks install busted

# Run tests
busted tests/

# Lint
luacheck polycall/
```

---

## LibPolyCall Binding Ecosystem

| Language | Package | Registry |
|---|---|---|
| Python | `pypolycall` | [PyPI](https://pypi.org/project/pypolycall) |
| Node.js | `@obinexus/node-polycall` | npm |
| Lua | `lua-polycall` | LuaRocks |

---

## Support

- **Core Runtime**: [github.com/obinexusmk2/libpolycall-v1](https://github.com/obinexusmk2/libpolycall-v1)
- **Python Binding**: [github.com/obinexusmk2/pypolycall](https://github.com/obinexusmk2/pypolycall)
- **Issues**: [gitlab.com/obinexuscomputing/libpolycall-v1trial/-/issues](https://gitlab.com/obinexuscomputing/libpolycall-v1trial/-/issues)
- **Docs**: [docs.obinexuscomputing.com/libpolycall](https://docs.obinexuscomputing.com/libpolycall)
- **Video**: [youtube.com/@obinexus](https://youtube.com/@obinexus)

---

## License

MIT License — LibPolyCall Trial v1

**Copyright (c) 2025 OBINexusComputing**

---

## Author

**Nnamdi Michael Okpala**
*Founder & Chief Architect — OBINexusComputing*

> "The future isn't coming — it's here. And it speaks every language."
