# Monitoramento & Observabilidade FortiGate 80F 🛡️
### Boulevard Shopping Feira de Santana — Gestão de TI

Repositório oficial para o projeto de observabilidade, monitoramento de saúde de hardware, qualidade de links (SD-WAN) e segurança do firewall **FortiGate 80F**.

---

## 📌 Visão Geral do Projeto

- **Dispositivo:** FortiGate 80F (`FGT80FTK22021058`)
- **Firmware:** FortiOS v7.6.7 build 3704 (Mature)
- **Custo Adicional em Licenças:** **R$ 0,00** (utilização da REST API e telemetria nativa do FortiOS)
- **Linguagem Principal:** Python 3.11+

---

## 📂 Documentação e Apresentação

1. **[Apresentação para a Supervisão (HTML Interativo)](./apresentacao_monitoramento.html):**
   - Slide deck interativo moderno com visão geral de negócios, justificativa técnica, impacto orçamentário zero e plano de ação (passos imediatos e futuros).
   - Abra diretamente no navegador (Chrome/Edge) e aperte `F` para tela cheia ou `Ctrl+P` para exportar em PDF.
2. **[Documento de Escopo Completo](./ESCOPO_MONITORAMENTO.md):**
   - Especificação técnica completa de arquitetura, análise de licenciamento, estrutura de pastas do código e fases de implementação.

---

## 🚀 Frentes de Monitoramento

1. **🌐 Links & SD-WAN:** Monitoramento contínuo de status UP/DOWN, latência (ms), jitter e perda de pacotes para auditoria de SLAs das operadoras de telecom.
2. **⚙️ Saúde do Hardware:** Acompanhamento do consumo de CPU (SOC4), memória RAM, sessões ativas e uptime.
3. **🔒 Conectividade VPN:** Status em tempo real de túneis IPsec (matriz/filiais) e conexões SSL-VPN ativas.
4. **🛡️ Cibersegurança:** Indicadores de bloqueios efetuados pelos motores de IPS, AntiVirus e Web Filter.

---

## 🗺️ Roadmap de Implementação

- [x] Elaboração do escopo arquitetural e mapeamento de licenças.
- [x] Criação da apresentação executiva para a supervisão.
- [ ] **Fase 1 (Imediata):** Criação do usuário de API Read-Only e API Token no FortiGate.
- [ ] **Fase 1 (Imediata):** Implementação do cliente HTTP Python (`FortiGateClient`).
- [ ] **Fase 1 (Imediata):** Coleta periódica de métricas de CPU/RAM/Interfaces e alertas de queda via Telegram/Teams.
- [ ] **Fase 2 (Futura):** Telemetria avançada de SD-WAN e painel visual NOC em tempo real.
