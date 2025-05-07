# 🧠 PLANO DE ESTUDO – MENTORIA DBA HANDS-ON  
👨‍🎓 ALUNO: 2025-04-02 Bruno Bernardes @DBA @M5  

---

## 🎯 OBJETIVO  
Consolidar pontos fortes, aprofundar os conhecimentos intermediários e desenvolver com solidez os temas avançados — com foco prático, visão técnica e segurança na atuação.

---

## 🔁 1. REFORÇO E CONSOLIDAÇÃO – Nível ++++ e +++++  

Você já tem domínio sólido de temas essenciais para um DBA pleno. Aqui o foco não é decorar nem repetir o que você já sabe — mas sim refinar, conectar e aplicar com profundidade:

- 🔒 Controle de concorrência: LOCK TX, LOCK TABLE  
- 🧭 Acesso e conexão: Listener, TNS x Easy Connect  
- 🔍 Diagnóstico de sessão: TOP, LOAD, waits  
- 📊 Performance SQL: plano de execução, estatísticas, parse, constraints, índices (com e sem reverso), particionamento  
- ⚙️ Tuning de instância: STATISTICS_LEVEL, parâmetros de REDO  
- 📚 Arquitetura lógica de logs: REDO, UNDO  

O desafio agora é transformar tudo isso em visão estratégica. Ser capaz de explicar, simular, ajustar e otimizar — com propriedade.

---

## ⚒️ 2. APERFEIÇOAMENTO TÉCNICO – Nível ++ e +++  

Você já tem a base conceitual de vários assuntos, mas ainda precisa ganhar fluidez, entender os detalhes que fazem diferença e aplicar com mais segurança no dia a dia.

### 🧠 Tópicos dessa etapa:

- 🧮 SGA x PGA na prática  
- 🔧 Parâmetros de instância e tuning leve  
- ♻️ UNDO: retenção, extents, concorrência  
- 🔑 Abertura e shutdown da instância  
- 🧱 LOCK TM e impacto em DDL/DML concorrente  
- 🐧 Comportamento de memória/SWAP no Linux  
- 🧪 Swappiness  
- 🧱 Large Pages (HugePages)  

Este é o meio do funil: onde o diferencial começa a se formar. Revisar documentações, simular comportamentos e gerar insights próprios é o caminho.

---

## 🔧 3. BASE EM CONSTRUÇÃO – Nível + e ++  

Temas que você já viu, mas ainda não domina plenamente.

### 🔍 Tópicos em foco:

- ⏱️ FAST_START_MTTR_TARGET e crash recovery  
- 📈 AWR (Automatic Workload Repository)  
- 🧾 COMMIT_WAIT e COMMIT_WRITE  
- 🎯 Parâmetros avançados de tuning  
- 🔒 LOCK TM e impacto de estrutura  
- 📊 Estatísticas do SO com foco em Oracle  

A proposta é sair da zona do “conheço” para o “domino”. Estude, anote dúvidas e traga para a mentoria.

---

## 🚀 4. DESENVOLVIMENTO CRÍTICO – Nível -  

Agora os grandes blocos que vão te posicionar como um DBA sênior:


### 🔁 ALTA DISPONIBILIDADE – DATA GUARD  

- 🏗️ Arquitetura física x lógica  
- 🔄 Switchover vs Failover  
- 🤖 DGMGRL (Data Guard Broker)  
- ⏳ LAG de transporte e aplicação  
- 🧩 GAP de sequence  
- 🛡️ Modos de proteção: maximum protection, availability e performance  
- 🧪 Snapshot Standby  

Estudo passo a passo: entenda, simule, monitore.

---

### 🧩 ESCALABILIDADE E REDUNDÂNCIA – ORACLE RAC  

- 🧱 Arquitetura do cluster RAC  
- 🔗 Cache Fusion (GC – Global Cache)  
- 🗂️ OCR e Voting Disk  
- 🎧 SCAN Listener vs Listener Local  
- 🌐 VIPs, Interconnects  
- ⚠️ Node Eviction  
- 🔄 Gerenciamento do cluster  
- ⚖️ Serviços e balanceamento de carga  
- 🧭 Planejamento de IPs em clusters  

---

### 💾 BACKUP E RECUPERAÇÃO – RMAN E ALÉM  

Essa é a área que separa os comuns dos indispensáveis.

- 📁 Controlfile: backup e recriação  
- 🧱 Bloco corrompido: RMAN BLOCKRECOVER  
- 🗑️ Tabela deletada: Flashback, Data Pump, RMAN (inclusive Oracle 11)  
- 🌀 Datafile fuzzy e OPEN RESETLOGS  
- 🛠️ Restore completo: controlfile, database, recover, resetlogs  
- 🕰️ PITR: recovery até SCN ou timestamp  
- 🧩 Restore de tablespace  
- 📄 Tempfile: recriação e comportamento  
- 🧭 Política de backup  

Meta: dominar o cenário. Saber o quê, quando e por quê fazer.

---

## 📌 ORIENTAÇÃO FINAL  

Bruno, sua jornada já é sólida. Agora é a hora de avançar com inteligência e profundidade.

O plano é um mapa. Você escolhe o ritmo, a rota e os atalhos.  
Mas eu estou aqui como guia pra garantir que você chegue no destino. 🗺️

Use os materiais que preferir (DBAOCM Club, Udemy, Baraka etc). Só me avise o que está usando — assim consigo afinar sua trilha.

Sem pressa. A prática vai te preparar pro desafio real.  
Você tem perfil técnico e postura de crescimento. Agora é constância, foco e entrega. 💪

👊 Vamos juntos.