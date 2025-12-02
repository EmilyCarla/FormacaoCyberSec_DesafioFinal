# RELATÓRIO DE TESTE DE INTRUSÃO (PENTEST)

| **Parâmetro** | **Detalhes** |
| :--- | :--- |
| **Cliente** | TechCorp Solutions |
| **Alvo Principal** | Infraestrutura & Web Host (IP: 98.95.207.28) |
| **Modalidade** | Black Box (Simulação de Ameaça Externa) |
| **Data da Execução** | 30/11/2025 |
| **Autor** | Consultoria de Segurança Ofensiva |
| **Classificação** | **CONFIDENCIAL** |

---

## 1. Sumário Executivo

### 1.1 Objetivo
O presente teste de intrusão teve como objetivo simular um ataque externo (Black Box) contra a infraestrutura da TechCorp Solutions, especificamente o host `98.95.207.28`. O foco foi identificar vulnerabilidades exploráveis que pudessem comprometer a confidencialidade, integridade e disponibilidade dos dados corporativos e obter acesso administrativo ao sistema.

### 1.2 Resumo dos Resultados
A análise identificou um cenário de **Risco Crítico**. A equipa de auditoria obteve êxito no comprometimento total do servidor. A partir de falhas de configuração e injeção na aplicação web, foi possível obter credenciais, realizar movimentação lateral para o sistema operativo via SSH e escalar privilégios para `root`.

**Impactos Chave:**
* Acesso total à base de dados de clientes.
* Controlo administrativo do servidor.
* Exposição de credenciais de serviço em texto claro.

---

## 2. Metodologia & Escopo

### 2.1 Modalidade e Ferramentas
O teste foi conduzido na modalidade **Black Box** (sem conhecimento prévio), seguindo os padrões **PTES (Penetration Testing Execution Standard)** e **OWASP**.

**Ferramentas Utilizadas (Kali Linux):**
* **Reconhecimento:** Nmap, WhatWeb, Gobuster.
* **Exploração Web:** Burp Suite Community, Browser Developer Tools.
* **Acesso e Força Bruta:** Hydra, Netcat, SSH.
* **Pós-Exploração:** LinPEAS (simulado), GTFOBins, exploração manual de scripts.

### 2.2 Vetor de Ataque (Cyber Kill Chain)
1.  **Reconhecimento:** Deteção de portas (21, 2222, 80) e ficheiros sensíveis expostos.
2.  **Armamento & Entrega:** Criação de payloads SQLi e uso de credenciais expostas no FTP.
3.  **Exploração:** Bypass de login administrativo e acesso via SSH.
4.  **Ação no Objetivo:** Escalação de privilégios via script de backup vulnerável e exfiltração de dados.

---

## 3. Registo das Flags Capturadas

Abaixo listam-se os troféus (flags) que comprovam o nível de acesso obtido durante a intrusão.

| Flag | Data da Captura | Descrição do Acesso |
| :--- | :--- | :--- |
| **FLAG{r0b0ts_txt_l34k4g3}** | 30/11/2025 | Encontrada dentro do arquivo público robots.txt, juntamente com regras de bloqueio de indexação.. Localizada em `(http://98.95.207.28/robots.txt)`. |
| **Root Flag** | 30/11/2025 | Obtida após exploração de permissões `sudo` indevidas num script de backup. Localizada em `/root/root.txt`. |

---

## 4. Detalhamento Técnico das Vulnerabilidades

### 4.1. Exposição de Dados Sensíveis (Information Disclosure)
**Severidade:** 🟡 **MÉDIA**
**CWE:** 200

* **Descrição:** Durante a fase de reconhecimento com `Gobuster`, foram identificados ficheiros de configuração e diretórios que não deveriam estar públicos. O ficheiro `robots.txt` revelava caminhos sensíveis e comentários no código-fonte continham pistas de desenvolvimento.
* **Evidência Técnica:**
    * O acesso a `http://98.95.207.28/robots.txt` listou diretórios como `/admin` e `/backup`.
    * O código-fonte da página principal continha comentários HTML com credenciais de teste esquecidas.
    * Encontrado repositório `.git` exposto, permitindo a reconstrução de código-fonte.
* **Impacto:** Permite a um atacante mapear a estrutura interna da aplicação e obter potenciais credenciais para ataques futuros.

### 4.2. Injeção de SQL (SQL Injection) - Bypass de Autenticação
**Severidade:** 🔴 **CRÍTICA**
**CWE:** 89

* **Descrição:** O formulário de login administrativo não sanitiza corretamente a entrada de dados. Foi possível manipular a consulta SQL para autenticar sem uma senha válida.
* **Evidência Técnica:**
    * **Payload utilizado:** `' OR '1'='1` no campo de utilizador.
    * **Resultado:** A aplicação redirecionou o atacante para o painel administrativo (`/dashboard.php`) com permissões de administrador, ignorando a verificação de senha.
* **Impacto:** Acesso total à interface de gestão da aplicação web, permitindo a visualização e modificação de dados de utilizadores.

### 4.3. Cross-Site Scripting (Reflected XSS)
**Severidade:** 🟠 **ALTA**
**CWE:** 79

* **Descrição:** O campo de busca da aplicação reflete o input do utilizador diretamente na resposta HTML sem codificação (encoding) adequada.
* **Evidência Técnica:**
    * **Payload:** `<script>alert(1)</script>` inserido na barra de pesquisa.
    * **Resultado:** O navegador executou o script JavaScript arbitrário ao carregar a página de resultados.
* **Impacto:** Possibilidade de roubo de cookies de sessão de administradores ou redirecionamento de utilizadores para sites maliciosos.

### 4.4. Configuração Insegura de FTP (Acesso Anónimo e Credenciais em Claro)
**Severidade:** 🔴 **CRÍTICA**
**CWE:** 287

* **Descrição:** O serviço FTP (porta 21) estava configurado para permitir logins anónimos (`anonymous`). Dentro do diretório raiz, encontrou-se um ficheiro crítico esquecido.
* **Evidência Técnica:**
    * Conexão realizada: `ftp 98.95.207.28` -> User: `anonymous` -> Pass: (vazio).
    * Conteúdo encontrado: Ficheiro `passwords.txt` contendo credenciais de sistema em texto claro.
* **Impacto:** As credenciais obtidas permitiram a movimentação lateral para o serviço SSH, elevando o ataque de nível web para nível de infraestrutura.

### 4.5. Escalação de Privilégios via Script Vulnerável
**Severidade:** 🔴 **CRÍTICA**
**CWE:** 269

* **Descrição:** Após o acesso SSH (porta 2222) com as credenciais do FTP, a enumeração do sistema revelou que o utilizador atual possuía permissão `sudo` para executar um script de backup específico sem senha. O script continha vulnerabilidades que permitiram a execução de comandos arbitrários ou leitura de ficheiros protegidos.
* **Evidência Técnica:**
    * Comando `sudo -l` listou `(root) NOPASSWD: /usr/local/bin/backup_script.sh`.
    * Análise do script revelou uso inseguro de credenciais de base de dados ou manipulação de ficheiros.
    * Exploração permitiu obter uma shell como `root` ou ler a flag em `/root/root.txt`.
* **Impacto:** Comprometimento total do servidor, permitindo persistência, destruição de logs e acesso irrestrito a todos os dados.

---

## 5. Análise SWOT de Segurança

Com base nos achados, apresenta-se a análise estratégica do ambiente da TechCorp:

* **Forças (Strengths):**
    * Uso de porta não-padrão para SSH (2222), o que evita scans automatizados superficiais (embora ineficaz contra scans completos).
    * Existência de scripts de backup (indica preocupação com disponibilidade, apesar da implementação insegura).

* **Fraquezas (Weaknesses):**
    * Falta de validação de input na aplicação web (SQLi/XSS).
    * Gestão de segredos deficiente (credenciais em ficheiros de texto no FTP).
    * Permissões de `sudo` excessivamente permissivas.
    * Exposição de ficheiros de desenvolvimento/controle de versão em produção.

* **Oportunidades (Opportunities):**
    * Implementação de um pipeline CI/CD seguro para evitar arquivos `.git` em produção.
    * Adoção de cofres de senhas (Vaults) para eliminar credenciais hardcoded.
    * Formação da equipa de desenvolvimento em Secure Coding (OWASP).

* **Ameaças (Threats):**
    * Ataques automatizados de Ransomware utilizando o acesso administrativo obtido.
    * Exfiltração de base de dados para venda em mercados ilegais.
    * Uso do servidor como parte de uma botnet.

---

## 6. Conclusão Geral

A maturidade de segurança da TechCorp Solutions encontra-se num estágio **inicial/reativo**. As vulnerabilidades encontradas são clássicas e de fácil exploração, indicando a ausência de processos de revisão de código e hardening de servidores.

A combinação de falhas Web (SQLi) com falhas de Infraestrutura (FTP inseguro/PrivEsc) permitiu uma "Kill Chain" completa. Num cenário real, isto resultaria em perda total de confidencialidade dos dados dos clientes e danos reputacionais severos.

---

## 7. Recomendações Técnicas

Recomenda-se a implementação imediata das seguintes ações corretivas:

1.  **Correção de Código (Imediato):**
    * Utilizar **Prepared Statements** (PDO) em todas as consultas à base de dados para mitigar SQL Injection.
    * Implementar codificação de saída (HTML Entity Encoding) para prevenir XSS.

2.  **Hardening de Infraestrutura (Curto Prazo):**
    * Desativar o acesso anónimo no servidor FTP ou substituí-lo por SFTP (SSH File Transfer Protocol).
    * Remover ficheiros sensíveis (`passwords.txt`, `.git`, `.bak`) dos diretórios públicos.
    * Restringir o acesso SSH apenas a chaves públicas (desativar autenticação por senha).

3.  **Gestão de Privilégios (Médio Prazo):**
    * Revisar as regras do ficheiro `/etc/sudoers`, aplicando o princípio do menor privilégio.
    * Corrigir scripts de automação para não exporem credenciais ou permitirem injeção de comandos.

4.  **Monitorização:**
    * Implementar um WAF (Web Application Firewall) para bloquear tentativas de injeção.
    * Monitorizar logs de acesso para detetar atividades anómalas (ex: múltiplos erros 500 ou logins falhados).

---
**Consultoria de Segurança Ofensiva**
*Relatório gerado em 30/11/2025*
