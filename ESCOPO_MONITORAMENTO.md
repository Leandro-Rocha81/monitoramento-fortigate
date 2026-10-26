# Escopo de Projeto: Monitoramento FortiGate 80F

## 1. Dados do Dispositivo
- **Modelo:** FortiGate 80F (`FGT80FTK22021058`)
- **Firmware:** FortiOS v7.6.7 build 3704 (Mature)
- **Licenças Atuais:**
  - FortiCare Support
  - Firmware & General Updates
  - IPS (Intrusion Prevention System)
  - AntiVirus
  - Web Filter
- **Característica de Hardware Relevante:** O modelo **80F** (sem sufixo '1') não possui disco SSD interno para retenção prolongada de logs locais. Portanto, **coleta remota (API/Syslog/SNMP) para um servidor externo é indispensável**.

---

## 2. Análise de Licenciamento
> **Pergunta:** *Quais licenças devo utilizar?*

### Veredito:
**Nenhuma licença adicional da Fortinet é obrigatória para realizar o monitoramento!**

- O **FortiOS nativo** disponibiliza gratuitamente e sem necessidade de licença extra:
  - **REST API completa** (endpoints de métricas `/api/v2/monitor/` e configuração `/api/v2/cmdb/`).
  - **Agente SNMP v2c / v3** com MIBs completas da Fortinet.
  - **Encaminhamento Syslog** para servidores externos.
  - **NetFlow / sFlow / IPFIX** para análise de tráfego.

### O que suas licenças atuais permitem monitorar:
Com o pacote atual (**FortiCare + IPS + AntiVirus + Web Filter**), você pode e deve monitorar:
1. **Segurança & Ameaças:** Volume de ataques bloqueados por IPS, malwares barrados pelo AntiVirus e domínios/categorias bloqueadas pelo Web Filter.
2. **Saúde de Hardware:** Consumo de CPU, Memória RAM, Sessões ativas e temperatura do SOC4.
3. **Disponibilidade de Rede & Links:** Status das portas WAN, failover, perda de pacotes, jitter e latência (SLAs de SD-WAN).
4. **VPNs:** Status dos túneis IPsec (Up/Down) e conexões SSL-VPN ativas.

*(Opcional futuro: Apenas se quisesse relatórios analíticos prontos em nuvem da Fortinet você precisaria da licença FortiAnalyzer / FortiGate Cloud. Criando seu próprio sistema de monitoramento, você economiza esse custo).*

---

## 3. Melhor Linguagem de Programação
> **Recomendação: Python 3.11+**

### Por que Python?
1. **Ecosistema NetDevOps maduro:** Bibliotecas líderes como `httpx`/`requests` para consumo da REST API FortiOS, `pysnmp` para SNMP, e `prometheus_client` para expor métricas.
2. **Manutenção facilitada:** Código limpo, extensível e com rápido desenvolvimento de automações e alertas (Telegram, Microsoft Teams, Discord, E-mail).
3. **Alternativas consideradas:**
   - *Go (Golang):* Excelente para gerar um binário único (ex: um Prometheus Exporter compilado), mas exige maior tempo de desenvolvimento e manutenção.
   - *Node.js / TypeScript:* Bom para dashboards em tempo real, mas com bibliotecas de rede menos consolidadas do que o ecossistema Python.

---

## 4. Melhores "Skills", Protocolos e Tecnologias

### Protocolos no FortiGate:
1. **FortiOS REST API (Prioridade 1 - Métricas em Tempo Real e Diagnóstico):**
   - Autenticação via Token Bearer (REST API Admin).
   - Endpoints chave:
     - `/api/v2/monitor/system/resource/usage` (CPU, Memória, Sessões)
     - `/api/v2/monitor/system/interface` (Status de links, banda, erros)
     - `/api/v2/monitor/router/sdwan-quality` (Latência, jitter, perda de pacotes)
     - `/api/v2/monitor/vpn/ipsec` e `/api/v2/monitor/vpn/ssl` (Túneis e usuários)
     - `/api/v2/monitor/firewall/policy` (Uso de regras e tráfego)
2. **Syslog UDP/TLS (Prioridade 2 - Eventos Imediatos e Segurança):**
   - Captura em tempo real de logs de segurança (IPS, AV, Web Filter, Link Up/Down).
3. **SNMP v3 (Opcional/Secundário):**
   - Monitoramento padrão de séries temporais se integrado a sistemas legados (Zabbix/PRTG).

### Arquitetura de Stack Sugerida:
- **Opção A (Observabilidade Moderna - Recomendada):**
  - **Coletor:** Script/Daemon em Python coletando da API FortiOS a cada 30s–60s.
  - **Time-Series / Métricas:** Prometheus (ou InfluxDB).
  - **Dashboards:** Grafana (painéis interativos e responsivos).
  - **Alertas:** Alertmanager / Script Python (WhatsApp/Telegram/Teams).
- **Opção B (App Autônomo Tudo-em-Um):**
  - **Backend:** Python (FastAPI + SQLite/PostgreSQL + APScheduler).
  - **Frontend:** Dashboard Web leve (HTML5, Tailwind/Vanilla CSS, Chart.js).
  - **Notificações:** Bot de Alertas direto.

---

## 5. Estrutura de Pastas do Código

Estrutura modular recomendada para o projeto em Python:

```text
monitoramento-fortigate/
│
├── .env.example                 # Modelo de variáveis de ambiente (IP, Token API, portas)
├── .gitignore                   # Arquivos ignorados pelo Git (.env, venv, logs)
├── README.md                    # Documentação de instalação e uso
├── requirements.txt             # Dependências Python (httpx, pydantic, loguru, etc.)
│
├── config/                      # Configurações do sistema
│   ├── __init__.py
│   └── settings.py              # Leitura de variáveis de ambiente com Pydantic Settings
│
├── src/                         # Código-fonte principal
│   ├── __init__.py
│   │
│   ├── core/                    # Núcleo da aplicação
│   │   ├── __init__.py
│   │   ├── fortigate_client.py  # Cliente HTTP para comunicação com a REST API FortiOS
│   │   └── exceptions.py        # Tratamento de exceções customizadas
│   │
│   ├── collectors/              # Módulos de coleta especializada
│   │   ├── __init__.py
│   │   ├── system_collector.py  # CPU, Memória, Sessões, Disco
│   │   ├── network_collector.py # Portas, Interfaces, Largura de Banda
│   │   ├── sdwan_collector.py   # Latência, Jitter, Packet Loss dos links
│   │   ├── vpn_collector.py     # Túneis IPsec e conexões SSL-VPN
│   │   └── security_collector.py# Eventos de IPS, AntiVirus e WebFilter
│   │
│   ├── alerts/                  # Gerenciador de alertas e notificações
│   │   ├── __init__.py
│   │   ├── alert_manager.py     # Lógica de thresholds (ex: CPU > 85%, Link Down)
│   │   ├── notifiers/           # Canais de notificação
│   │   │   ├── __init__.py
│   │   │   ├── telegram.py      # Notificações via Telegram Bot
│   │   │   ├── teams.py         # Notificações via Webhook Teams
│   │   │   └── email_client.py  # Envio de e-mails de alerta
│   │
│   ├── storage/                 # Persistência de dados coletados
│   │   ├── __init__.py
│   │   ├── database.py          # Conexão SQLite / PostgreSQL ou InfluxDB
│   │   └── models.py            # Definição das tabelas de métricas e eventos
│   │
│   └── exporters/               # (Opcional) Exportadores para Grafana / Prometheus
│       ├── __init__.py
│       └── prometheus_exporter.py # Expõe métricas na porta /metrics
│
├── tests/                       # Testes automatizados
│   ├── test_fortigate_client.py
│   └── test_collectors.py
│
└── main.py                      # Ponto de entrada (inicia o agendador e coletas)
```

---

## 6. Fases de Execução do Escopo

| Fase | Descrição | Entregáveis |
|---|---|---|
| **Fase 1: Preparação no FortiGate** | Criar perfil de Administrador API Read-Only e gerar API Token com restrição de IP. | Token API ativo e conectividade validada. |
| **Fase 2: Estrutura Base & Cliente API** | Implementar `FortiGateClient` com autenticação, retry e controle de erros. | Teste de requisição para `/api/v2/monitor/system/status`. |
| **Fase 3: Coletores de Métricas** | Criar coletores para Sistema, Interfaces, SD-WAN, VPN e Segurança. | Dados estruturados em formato JSON / Dicionários Python. |
| **Fase 4: Alertas em Tempo Real** | Definir regras críticas (Link Down, Queda de Túnel VPN, Ataques IPS). | Integração com Telegram/Teams/E-mail. |
| **Fase 5: Visualização / Persistência** | Gravação de métricas históricas e visualização via Grafana ou Dashboard Web. | Painel visual operacional para a equipe de TI. |
