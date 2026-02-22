# Análise Técnica Completa: Codabix e Alternativa Custom

**Data:** 2026-02-22  
**Status:** Pesquisa detalhada

---

## 1. O que é o Codabix

**CoDaBix®** (Connection of Data by Index) é um **middleware industrial (IoT gateway)** desenvolvido pela **Traeger GmbH** (Weiden, Alemanha), empresa que atua em comunicação industrial desde 1992.

### Conceito Central
- **Middleware para Industry 4.0** que conecta equipamentos heterogêneos (PLCs, sensores, bancos de dados) a sistemas de nível superior (MES, ERP, SCADA, cloud)
- Modelo de dados baseado em **OPC UA Node Tree** — toda variável (real ou virtual) é organizada em uma árvore hierárquica OPC UA
- Funciona como **gateway/broker**: coleta dados de fontes diversas, normaliza-os e expõe via interfaces padronizadas

### Funcionalidades Principais
| Funcionalidade | Descrição |
|---|---|
| **Coleta de dados** | Lê dados de PLCs (Siemens S7, Modbus, etc.), bancos de dados, arquivos |
| **OPC UA Server** | Expõe todos os dados coletados como nós OPC UA padronizados |
| **REST/JSON API** | Interface HTTP para acesso web/mobile |
| **Cache interno** | High-speed cache com valor atual, timestamp, min/max para cada variável |
| **Historização** | Banco de dados integrado para armazenar séries temporais |
| **Scripting** | Plugin de JavaScript para lógica customizada |
| **Arquitetura de plugins** | Extensível via plugins para protocolos e integrações |
| **Bidirecional** | Leitura E escrita nas variáveis dos PLCs |
| **Event-driven** | Troca de dados por mudança, evento ou trigger |

### Protocolos Suportados (via plugins)
- **Siemens S7** (S7comm / ISO-on-TCP) — S7-300, S7-400, S7-1200, S7-1500
- **Modbus TCP/RTU**
- **OPC UA** (client e server)
- **OPC DA** (classic)
- **MQTT**
- **REST/JSON**
- **Conexões a bancos de dados** (SQL Server, MySQL, etc.)

### Plataformas
- Windows (principal)
- Linux (suporte crescente)
- Pode rodar em IPCs industriais

---

## 2. Comunicação com PLCs Siemens S7-400

### Protocolo: S7comm sobre ISO-on-TCP (RFC 1006)

A comunicação com PLCs Siemens da série S7 segue esta stack de protocolos:

```
┌─────────────────────────┐
│   Aplicação (S7comm)    │  ← Protocolo proprietário Siemens
├─────────────────────────┤
│   COTP (ISO 8073)       │  ← Connection-Oriented Transport Protocol
├─────────────────────────┤
│   TPKT (RFC 1006)       │  ← ISO transport sobre TCP
├─────────────────────────┤
│   TCP/IP (porta 102)    │
└─────────────────────────┘
```

### Como funciona a comunicação:

1. **Conexão TCP** na porta 102 do PLC
2. **COTP Connection Request** — estabelece sessão ISO transport
3. **S7 Communication Setup** — negocia parâmetros (tamanho PDU, max jobs)
4. **Read/Write requests** — lê/escreve áreas de memória:
   - **DB (Data Blocks)** — `DB100.DBW0`, `DB100.DBD4`
   - **Inputs (I)** — `I0.0`, `IW10`
   - **Outputs (Q)** — `Q0.0`, `QW20`
   - **Markers/Flags (M)** — `M0.0`, `MW100`
   - **Timers (T)**, **Counters (C)**

### Especificidades do S7-400:
- **Rack/Slot**: Tipicamente rack 0, slot 2 (CPU) ou slot 3
- **PDU Size**: Até 960 bytes (negocia na conexão)
- **Sem proteção de acesso**: S7-400 não tem mecanismo de "optimized block access" como S7-1200/1500 — acesso direto a DBs sem configuração extra
- **Endereçamento absoluto**: leitura por offset em bytes dentro dos DBs
- **Múltiplas conexões**: S7-400 suporta mais conexões simultâneas que S7-300

### O que o Codabix faz internamente:
1. Configura conexão com IP, rack, slot do PLC
2. Usuário mapeia variáveis (ex: `DB100.DBW0` → `Temperatura_Forno_1`)
3. Codabix faz polling periódico (configurável, ex: 100ms, 500ms, 1s)
4. Otimiza leituras agrupando variáveis contíguas em um único PDU read
5. Armazena no cache interno com timestamp
6. Dispara eventos de mudança para subscribers (OPC UA, REST, etc.)

---

## 3. Exposição via OPC-UA Server

### OPC UA (IEC 62541) — Visão Geral
OPC UA é o padrão industrial para interoperabilidade de dados, substituindo o antigo OPC DA (COM/DCOM). Características:
- **Plataforma-independente** (não depende de Windows/COM)
- **Seguro** (TLS, certificados X.509, autenticação)
- **Modelagem rica** — nós com tipos, referências hierárquicas, métodos
- **Histórico** — Historical Access (HA) nativo
- **Pub/Sub** (nas versões mais recentes)

### Como o Codabix expõe dados via OPC UA:

```
PLC S7-400 ──S7comm──► CoDaBix ──OPC UA──► Clientes OPC UA
                         │                    (SCADA, MES, ERP,
                         │                     dashboards, etc.)
                         │
                         ├──REST/JSON──► Aplicações Web
                         └──DB──► Historiador
```

1. **Address Space**: Cada variável mapeada no Codabix torna-se um `UANode` na árvore OPC UA
2. **Node Tree hierárquico**: O usuário organiza livremente (ex: `Planta1/Forno1/Temperatura`)
3. **Data Types**: Codabix mapeia tipos S7 (INT, REAL, DINT, STRING) para tipos OPC UA equivalentes
4. **Subscriptions**: Clientes OPC UA podem criar subscriptions com monitored items — recebem updates por mudança de valor ou intervalo
5. **Historical Access**: Valores armazenados no banco interno ficam acessíveis via OPC UA HA
6. **Security**: Suporte a Security Policies (Basic256Sha256, etc.), autenticação por certificado ou usuário/senha
7. **Discovery**: O server se registra em Discovery Servers para ser encontrado por clientes

### Porta padrão OPC UA: tipicamente 4840 ou configurável

---

## 4. Stack Tecnológica do Codabix

### Análise (baseada em evidências indiretas):

| Aspecto | Provável tecnologia |
|---|---|
| **Linguagem principal** | **C# / .NET** |
| **Plataforma** | .NET Framework (Windows) e .NET Core/5+ (cross-platform) |
| **OPC UA Stack** | Provavelmente stack própria ou baseada na Traeger OPC UA SDK (.NET) |
| **S7 Communication** | Implementação proprietária da Traeger (eles vendem SDKs de comunicação S7) |
| **Interface Web** | HTML5/JavaScript frontend, REST API backend |
| **Banco de dados** | SQLite ou SQL Server Compact (embarcado) |
| **Scripting** | V8 ou Jint (JavaScript engine para .NET) |

**Evidências:**
- Traeger GmbH vende SDKs .NET para comunicação industrial
- A empresa tem forte expertise em .NET (produtos anteriores baseados em .NET)
- OPC UA SDK da Traeger é .NET-based
- CoDaBix roda como Windows Service — padrão .NET
- Plugin JavaScript sugere engine embeddada tipo Jint

---

## 5. Alternativas Open-Source

### 5.1 Comunicação S7 (S7comm/ISO-on-TCP)

| Biblioteca | Linguagem | Maturidade | Stars | Notas |
|---|---|---|---|---|
| **Snap7** | C/C++ | ⭐⭐⭐⭐⭐ | ~1500 | Referência do setor, estável, multiplataforma |
| **python-snap7** | Python | ⭐⭐⭐⭐ | ~800 | Wrapper Python do Snap7 |
| **node-snap7** | Node.js | ⭐⭐⭐ | ~200 | Binding Node.js para Snap7 |
| **node-red-contrib-s7** | Node.js | ⭐⭐⭐⭐ | ~150 | Plugin Node-RED, usa node-snap7 internamente |
| **s7netplus** | C# | ⭐⭐⭐⭐ | ~700 | Implementação .NET nativa |
| **node-s7comm** | Node.js | ⭐⭐ | ~50 | Implementação JS pura do protocolo |
| **Apache PLC4X** | Java | ⭐⭐⭐⭐ | ~900 | Multi-protocolo (S7, Modbus, etc.), maduro |
| **Rust7** | Rust | ⭐⭐ | 18 | Nativo Rust, ~1ms/PDU, novo (2025) |
| **snap7-sys** | Rust | ⭐ | 0 | Bindings FFI para Snap7 em Rust |

### 5.2 OPC UA Server

| Biblioteca | Linguagem | Maturidade | Stars | Notas |
|---|---|---|---|---|
| **open62541** | C | ⭐⭐⭐⭐⭐ | ~2800 | Referência C, certificado pela OPC Foundation |
| **node-opcua** | Node.js | ⭐⭐⭐⭐⭐ | ~1500 | Mais completo em JS, client+server, HA |
| **python-opcua/opcua-asyncio** | Python | ⭐⭐⭐⭐ | ~1200 | Bom para prototipagem |
| **Eclipse Milo** | Java | ⭐⭐⭐⭐⭐ | ~800 | Referência Java, usado no Eclipse IoT |
| **OPC UA for Rust (locka99)** | Rust | ⭐⭐⭐ | ~500+ | Client + Server, embedded/micro/nano profiles, MPL-2.0 |
| **FreeOpcUa** | C++ | ⭐⭐⭐ | ~500 | C++ nativo |

---

## 6. Análise de Viabilidade: Alternativa Custom

### 6.1 Funcionalidade-Alvo
Criar uma aplicação que:
1. Conecta a um PLC Siemens S7-400 via S7comm/ISO-on-TCP
2. Lê variáveis configuráveis (DBs, I, Q, M)
3. Expõe essas variáveis como nós OPC UA via server embarcado
4. Interface gráfica para configuração (mapeamento de variáveis, status)

### 6.2 Opção A: Tauri (Rust Backend + WebView Frontend)

#### Bibliotecas Rust Disponíveis

**Para S7comm:**
| Crate | Status | Adequação |
|---|---|---|
| **Rust7** (davenardella) | Ativo, 18⭐, MIT, 2025 | ✅ Melhor opção nativa. Pure Rust, sem unsafe, ~1ms/PDU. Suporta S7-300/1200/1500. **S7-400 precisa validação** (protocolo é o mesmo, mas connect helper pode não ter) |
| **snap7-sys** (bindings) | Poucos stars, 2024 | ⚠️ FFI binding — funciona mas adiciona dependência C++ |
| **snap7-rs** (ruisv) | 0 stars, recente | ⚠️ Pure Rust mas muito novo |

**Para OPC UA Server:**
| Crate | Status | Adequação |
|---|---|---|
| **opcua** (locka99) | ~500⭐, ativo, MPL-2.0 | ✅ Boa opção. Server + Client. Embedded/micro/nano profiles. Samples inclusos. |

#### Arquitetura Proposta (Tauri)

```
┌────────────────────────────────────────────────────┐
│                    Tauri App                         │
│  ┌──────────────────────────────────────────────┐   │
│  │          WebView (Frontend)                   │   │
│  │   - React/Svelte/Vue                         │   │
│  │   - Config de variáveis (drag & drop)        │   │
│  │   - Dashboard de status                      │   │
│  │   - Monitoramento live de valores            │   │
│  └──────────────────────┬───────────────────────┘   │
│                         │ Tauri IPC (commands)        │
│  ┌──────────────────────┴───────────────────────┐   │
│  │          Rust Backend                         │   │
│  │                                              │   │
│  │  ┌──────────┐    ┌──────────┐   ┌────────┐  │   │
│  │  │ S7 Client│    │ Variable │   │OPC UA  │  │   │
│  │  │ (Rust7)  │───►│  Cache   │──►│Server  │  │   │
│  │  │          │    │          │   │(locka99)│  │   │
│  │  └──────────┘    └──────────┘   └────────┘  │   │
│  │       │                              │       │   │
│  │       ▼                              ▼       │   │
│  │  PLC S7-400                    Clientes OPC  │   │
│  │  (porta 102)                   UA (4840)     │   │
│  └──────────────────────────────────────────────┘   │
└────────────────────────────────────────────────────┘
```

#### Estimativa de Esforço (Tauri/Rust)

| Componente | Horas | Complexidade | Notas |
|---|---|---|---|
| **Setup Tauri + scaffold** | 8-16h | Baixa | Boilerplate, build system |
| **Módulo S7 Client** | 40-80h | Alta | Integrar Rust7, tratar reconexão, timeout, multi-PLC, parsing de tipos S7 |
| **Variable Manager** | 24-40h | Média | Config de variáveis, mapeamento endereço↔nome, persistência (JSON/SQLite) |
| **Cache de variáveis** | 16-24h | Média | Thread-safe, timestamp, change detection |
| **Polling engine** | 24-40h | Alta | Async loop, agrupamento de reads, otimização PDU, error handling |
| **OPC UA Server** | 60-100h | Muito Alta | Integrar locka99/opcua, mapear nós dinamicamente, subscriptions, security |
| **Frontend (configuração)** | 40-60h | Média | Formulários, árvore de variáveis, status, monitoring |
| **Frontend (dashboard)** | 24-40h | Média | Valores live, gráficos, alarmes |
| **Testes com PLC real** | 40-80h | Alta | Depende de acesso a hardware, debugging de protocolo |
| **Documentação** | 16-24h | Baixa | — |
| **TOTAL** | **290-500h** | — | **~2-4 meses** (1 dev full-time) |

#### Riscos (Tauri/Rust)

| Risco | Severidade | Mitigação |
|---|---|---|
| **Rust7 não suportar S7-400 bem** | Alta | S7-400 usa mesmo protocolo, mas rack/slot e PDU size podem diferir. Testar cedo. Fallback: snap7 via FFI |
| **locka99/opcua immaturidade** | Média | Lib tem 500+ stars mas não é certificada OPC Foundation. Pode ter gaps em subscriptions ou HA |
| **Complexidade async Rust** | Média | Tokio + async channels para coordenar S7 polling com OPC UA server |
| **Debugging difícil** | Alta | Erros de protocolo S7 são crípticos. Wireshark com dissector S7comm é essencial |
| **Sem Historical Access** | Média | Adicionar HA ao OPC UA server é trabalho significativo extra |
| **Segurança OPC UA** | Média | Certificados, security policies — complexo de implementar corretamente |

### 6.3 Opção B: Node.js

#### Arquitetura

```
Node.js Application
├── node-snap7 (ou nodes7)     → Comunicação S7
├── node-opcua                  → OPC UA Server
├── Express/Fastify             → REST API
└── Electron (ou web app)       → Frontend
```

#### Bibliotecas Disponíveis

| Lib | Maturidade | Notas |
|---|---|---|
| **node-snap7** | ⭐⭐⭐ | Binding nativo do Snap7 (C++), estável, bem documentado |
| **node-opcua** | ⭐⭐⭐⭐⭐ | A melhor implementação OPC UA em JS. Server + Client + HA + Discovery. Muito madura |
| **nodes7** | ⭐⭐⭐ | Pure JS S7 comm, sem dependência nativa |

#### Estimativa de Esforço (Node.js)

| Componente | Horas | Complexidade | Notas |
|---|---|---|---|
| **Setup projeto** | 4-8h | Baixa | npm init, typescript, estrutura |
| **Módulo S7 Client** | 16-32h | Média | node-snap7 é maduro, API simples |
| **Variable Manager** | 16-24h | Média | Config JSON, validação |
| **Cache + Polling** | 16-24h | Média | setInterval, Map, EventEmitter |
| **OPC UA Server** | 32-48h | Média-Alta | node-opcua é excelente, mas setup ainda requer conhecimento OPC UA |
| **Frontend (Electron)** | 32-48h | Média | React + Electron, ou web app puro |
| **Testes com PLC** | 24-40h | Alta | Mesmo risco de acesso a hardware |
| **Documentação** | 8-16h | Baixa | — |
| **TOTAL** | **150-240h** | — | **~1-2 meses** (1 dev full-time) |

### 6.4 Comparação Direta

| Critério | Tauri/Rust | Node.js/Electron |
|---|---|---|
| **Tempo de desenvolvimento** | 290-500h | 150-240h |
| **Maturidade das libs** | ⚠️ Libs Rust jovens (Rust7: 18⭐) | ✅ node-opcua e node-snap7 maduros |
| **Performance** | ✅ Excelente (~1ms/PDU, baixo footprint) | ⚡ Boa (suficiente para este caso) |
| **Consumo de memória** | ✅ ~10-30MB | ⚠️ ~80-200MB (V8 + Electron) |
| **Segurança** | ✅ Memory-safe por design | ⚠️ Node é safe mas menos robusto |
| **Binário final** | ✅ ~20-50MB standalone | ⚠️ ~150-300MB (Electron) |
| **Cross-compilation** | ✅ Excelente (Rust target triples) | ⚠️ Mais complicado (node-gyp, snap7 nativo) |
| **Curva de aprendizado** | ⚠️ Rust é difícil, async + lifetimes | ✅ JavaScript é familiar |
| **Ecossistema industrial** | ⚠️ Pequeno | ✅ Grande (node-red, node-opcua ecosystem) |
| **Risco técnico** | 🔴 Alto (libs não provadas em produção) | 🟡 Médio (libs usadas em produção) |
| **Manutenibilidade** | ✅ Se funcionar, é robusto | ✅ Fácil de manter, mais devs disponíveis |

---

## 7. Recomendação

### Para MVP / Prova de Conceito: **Node.js**
- **2x mais rápido** de desenvolver
- Bibliotecas maduras e provadas
- node-opcua é praticamente o padrão para OPC UA em JS
- Risco técnico significativamente menor
- Pode ser empacotado com Electron ou rodar headless como serviço

### Para Produto de Longo Prazo: **Considerar Rust, mas com ressalvas**
- Só faz sentido se performance e footprint forem requisitos reais (ex: rodar em gateway ARM)
- **Rust7** é promissor mas precisa amadurecer
- Recomendo: **MVP em Node.js primeiro**, depois migrar componentes críticos para Rust se necessário

### Alternativa Pragmática: **Rust backend puro (sem Tauri)**
- Se o objetivo é um **serviço headless** (sem GUI) que faz S7→OPC UA bridging
- Tauri adiciona complexidade desnecessária se a configuração puder ser feita via arquivo/web
- Um serviço Rust puro + web UI separada (React servida por actix-web) seria mais limpo

---

## 8. Comparação com Codabix

| Feature | Codabix | Custom (Node.js) | Custom (Rust) |
|---|---|---|---|
| Multi-protocolo | ✅ 10+ protocolos | ⚠️ Apenas S7 + OPC UA | ⚠️ Apenas S7 + OPC UA |
| OPC UA Server | ✅ Completo, certificável | ✅ node-opcua (muito bom) | ⚠️ locka99 (funcional) |
| Historical Access | ✅ Integrado | ⚠️ Precisa implementar | ⚠️ Precisa implementar |
| Scripting | ✅ JavaScript | ✅ Nativo (é JS) | ⚠️ Embarcaria engine |
| GUI de config | ✅ Web completa | 🔨 Precisa criar | 🔨 Precisa criar |
| Suporte comercial | ✅ Traeger GmbH | ❌ DIY | ❌ DIY |
| Custo | 💰 Licença paga | 🆓 Open source | 🆓 Open source |
| Tempo para produção | ✅ Imediato | ⏳ 1-2 meses | ⏳ 2-4 meses |

---

## 9. Conclusão

Criar um **subset funcional** do Codabix (S7 → OPC UA bridge) é **totalmente viável** com tecnologias open-source. O ecossistema **Node.js** oferece o melhor custo-benefício para este projeto específico, enquanto **Rust** é uma aposta de longo prazo com maior risco mas melhor performance.

O Codabix se justifica quando se precisa de:
- Suporte a múltiplos protocolos industriais
- Certificação OPC UA
- Suporte comercial garantido
- Deploy imediato sem desenvolvimento

Para um caso de uso específico (1 PLC S7-400 → OPC UA), uma solução custom é economicamente justificável, especialmente se já há expertise interna em Node.js ou Rust.

---

*Pesquisa realizada em 2026-02-22. Fontes: codabix.com, traeger.de, GitHub (locka99/opcua, davenardella/Rust7, snap7), documentação OPC UA Foundation.*
