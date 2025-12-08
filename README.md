# 🛡️ Portfólio - Formação em Cibersegurança

Este repositório reúne os projetos práticos desenvolvidos ao longo da **Formação em Cibersegurança** oferecida pela escola **[Vai na Web](https://vainaweb.com.br/)** em parceria com a **[Kensei Cybersecurity](https://www.kensei.seg.br/)**.

O objetivo deste portfólio é documentar a evolução técnica através de desafios que simulam cenários reais do mercado de trabalho, abrangendo desde a estruturação de redes seguras até a defesa estratégica e a execução de testes de intrusão ofensivos (Pentest).

---

## 📂 Estrutura dos Projetos

A formação é dividida em módulos práticos, onde cada projeto foca em uma pilastra essencial da Segurança da Informação.

### 🔹 Módulo 1: Fundamentos e Arquitetura de Redes (Secure By Design)
**Projeto:** Infraestrutura de Rede Corporativa – *Fictício S/A*

Neste módulo, o desafio foi projetar uma arquitetura de rede lógica robusta e escalável para uma empresa com matriz em São Paulo e filiais no Rio de Janeiro e Minas Gerais. O foco foi a aplicação de conceitos de *Security by Design*.

* **Principais Entregas:**
    * Segmentação de rede utilizando **VLANs** (TI, Financeiro, ADM, Visitantes).
    * Implementação de **Firewalls de Próxima Geração (NGFW)** para inspeção de pacotes (DPI/IPS).
    * Conectividade segura entre filiais via **VPN Site-to-Site** e **Client-to-Site** para trabalho remoto.
    * Isolamento total da rede de visitantes para mitigar riscos de movimentação lateral.
* **Tecnologias/Conceitos:** TCP/IP, Subnetting, VLAN, VPN, NGFW, Diagramação de Topologia.

---

### 🔹 Módulo 2: Defesa Cibernética e Blue Team
**Projeto:** Consultoria de Segurança e SIEM – *LojaZeta*

O segundo projeto consistiu em atuar como consultor de segurança para um e-commerce que sofria com ataques web e falta de visibilidade. O objetivo foi criar um plano de defesa em profundidade e resposta a incidentes.

* **Principais Entregas:**
    * Estratégia de **Defesa em Profundidade** (Camadas: Perímetro, Rede, Host, Aplicação, Dados).
    * Implementação de **WAF (Web Application Firewall)** para mitigação de OWASP Top 10.
    * Centralização de logs e monitoramento com **SIEM (Wazuh)** para detecção de ameaças em tempo real.
    * Plano de **Resposta a Incidentes** baseado no framework **NIST** (Preparação, Detecção, Contenção e Pós-Incidente).
    * Estratégias de Backup e Disaster Recovery (RPO/RTO).
* **Tecnologias/Conceitos:** Blue Team, Hardening, SIEM, WAF, NIST Cybersecurity Framework, Gestão de Identidade (MFA).

---

### 🔹 Módulo 3: Segurança Ofensiva e Pentest (Red Team)
**Projeto:** Teste de Intrusão Black Box – *TechCorp Solutions*

No desafio final, a atuação mudou para o lado ofensivo (Red Team). Foi realizado um Pentest na modalidade *Black Box* contra um servidor alvo, visando identificar vulnerabilidades, explorar falhas e capturar "flags" que comprovam o comprometimento do sistema.

* **Principais Entregas:**
    * Reconhecimento passivo e ativo (enumeração de serviços e diretórios).
    * Identificação e exploração de falhas críticas: **SQL Injection** (Authentication Bypass), **XSS Refletido** e **Information Disclosure**.
    * Exploração de configurações inseguras (FTP Anônimo e arquivos de backup expostos).
    * Elaboração de um **Relatório Técnico Profissional** detalhando a metodologia (PTES/Cyber Kill Chain), evidências, impacto de negócio e recomendações de correção.
* **Tecnologias/Ferramentas:** Kali Linux, Nmap, Gobuster, Browser DevTools, Técnicas de SQLi e XSS.

---

## 🚀 Competências Desenvolvidas

* **Arquitetura de Segurança:** Capacidade de desenhar redes seguras desde a base.
* **Análise de Vulnerabilidades:** Habilidade para identificar falhas em código e infraestrutura.
* **Consultoria:** Visão estratégica para alinhar segurança aos objetivos de negócio.
* **Pentest:** Execução técnica de testes de intrusão seguindo padrões éticos e metodologias internacionais (OWASP, PTES).
* **Documentação Técnica:** Produção de relatórios executivos e técnicos de alto nível.

---

Developed by **Emily Carla** | CyberSec Enthusiast
