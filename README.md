# Projeto Final – Módulo 2: Defesa & Monitoramento

## 🚧 Status do Projeto
**Concluído** - Este projeto de laboratório demonstrou com sucesso a implementação e validação de uma arquitetura de defesa em camadas contra ataques web comuns.

## 📄 Descrição
Este repositório documenta um laboratório prático de cibersegurança focado na construção de um ambiente defensivo robusto, simulação de ataques controlados e comprovação da eficácia das defesas, monitoramento e resposta a incidentes.

O ambiente consiste em:
* Uma **aplicação web vulnerável (DVWA)** como alvo.
* Um **Web Application Firewall (WAF)** utilizando **ModSecurity** e o **OWASP Core Rule Set (CRS)** para proteção da camada de aplicação.
* Um **firewall de host (`iptables`)** para defesa da camada de rede no servidor.
* Ferramentas de **monitoramento de logs (Dozzle)** para visibilidade em tempo real.
* Um **ambiente de ataque (Kali Linux)** para simular ameaças.

O objetivo principal foi validar a capacidade do WAF em detectar e bloquear ataques comuns (SQL Injection, XSS, Command Injection, LFI) e simular um ciclo de resposta a incidentes baseado no framework NIST.

## 🎯 Objetivos do Laboratório
* Implementar uma arquitetura de "Defesa em Profundidade" com múltiplas camadas de segurança.
* Configurar e otimizar um WAF (ModSecurity + OWASP CRS) em modo de detecção e bloqueio.
* Simular e documentar a execução de ataques web comuns.
* Coletar logs e evidências para análise e comprovação.
* Aplicar os princípios de Resposta a Incidentes (NIST IR) na prática.

## 🚀 Arquitetura do Ambiente
O ambiente foi construído utilizando contêineres Docker para isolamento e facilidade de reprodução.

### Detalhes das Camadas:
* **Kali Linux:** Contêiner utilizado para orquestrar os ataques (SQLi, XSS, Command Injection, LFI).
* **Ubuntu Defense (`iptables`):** Representa o host subjacente, protegido por regras de firewall de rede (`iptables`) para limitar o acesso a portas específicas (ex: 8080 para o WAF).
* **ModSecurity + CRS:** O Web Application Firewall que inspeciona o tráfego HTTP/S. Configurado inicialmente em `DetectionOnly` para monitoramento e, posteriormente, em `On` para bloqueio.
* **DVWA:** A aplicação web deliberadamente vulnerável, servindo como alvo dos ataques e protegida pelo WAF.
* **Dozzle Log Monitor:** Ferramenta para visualizar em tempo real os logs gerados pelo WAF, essencial para a fase de detecção e análise.

### 🧪 Metodologia de Testes
O projeto seguiu uma metodologia em fases:

* **Configuração do Ambiente:** Implantação dos contêineres Docker e configuração inicial do `iptables` e do WAF em modo `DetectionOnly`.
* **Ataques em Modo Detecção:** Execução dos ataques (SQL Injection, XSS, Command Injection, LFI) para observar as detecções nos logs do WAF (esperado `HTTP Status: 302`).
* **Ataques em Modo Bloqueio:** Reconfiguração do WAF para `On` e reexecução dos mesmos ataques para comprovar o bloqueio (esperado `HTTP Status: 403`).
* **Coleta e Análise de Evidências:** Captura de logs (JSON) e screenshots em todas as fases para documentar os resultados.
* **Simulação de Resposta a Incidentes:** Aplicação dos princípios do framework NIST (Detecção, Análise, Contenção, Erradicação, Recuperação) ao longo do processo.

### 💥 Ataques Simulados e Resultados (Exemplos)
Foram testados com sucesso os seguintes ataques, com o WAF demonstrando 100% de eficácia no modo de bloqueio:

#### SQL Injection (SQLi)
* **Payload Exemplo:** `1' OR '1'='1'-- -`
* **Regras WAF Acionadas:** `942100` (SQL Injection Attack Detected via libinjection)
* **Status em Detecção:** `HTTP 302` (Log com Anomaly Score)
* **Status em Bloqueio:** `HTTP 403` (Bloqueado)

#### Cross-Site Scripting (XSS)
* **Payload Exemplo:** `<script>alert("XSS")</script>`
* **Regras WAF Acionadas:** `941100`, `941110`, `941390` (XSS Attack)
* **Status em Detecção:** `HTTP 302` (Log com Anomaly Score)
* **Status em Bloqueio:** `HTTP 403` (Bloqueado)

#### Command Injection
* **Payload Exemplo:** `127.0.0.1;cat /etc/passwd`
* **Regras WAF Acionadas:** `930120` (OS File Access Attempt), `932160` (Unix Shell Code Found)
* **Status em Detecção:** `HTTP 302` (Log com Anomaly Score)
* **Status em Bloqueio:** `HTTP 403` (Bloqueado)

#### Local File Inclusion (LFI)
* **Payload Exemplo:** `../../../../etc/passwd`
* **Regras WAF Acionadas:** `930100`, `930110` (Path Traversal Attack), `930120` (OS File Access Attempt)
* **Status em Detecção:** `HTTP 302` (Log com Anomaly Score)
* **Status em Bloqueio:** `HTTP 403` (Bloqueado)

### 📊 Conclusões e Recomendações
O laboratório validou a robustez da arquitetura de defesa em camadas e a eficácia do ModSecurity/OWASP CRS em proteger aplicações web contra um leque diversificado de ataques críticos.

* **Recomendação Principal:** Ativação imediata do WAF em modo de bloqueio (`On`) em ambientes de produção para mitigar vulnerabilidades e atuar como *virtual patching*.
* **Outras Recomendações:**
    * Priorizar a correção das vulnerabilidades no código-fonte da aplicação.
    * Integrar os logs do WAF a uma plataforma SIEM para monitoramento centralizado e análise de segurança.
    * Continuar com testes de segurança e tuning das regras do WAF.

### 📂 Conteúdo do Repositório
* `(./Projeto_hands_on/)`: Relatórios técnicos, executivos e análises de logs detalhadas.
* `./imagens/`: Evidências visuais de todas as etapas do laboratório.
* `./logs-comandos/`: Logs brutos (JSON) do WAF capturados durante os testes.
* `./docker-compose.yml`: Arquivo para orquestração do ambiente.

