──────────────────────────────────────────────\
📘 PLANO DE ESTUDO – MENTORIA DBA HANDS-ON  
👨‍💻 ALUNO: 2025-04-02 Bruno Bernardes @DBA @M5  
──────────────────────────────────────────────
## 🎯 OBJETIVO  
Consolidar pontos fortes, aprofundar os conhecimentos intermediários e desenvolver com solidez os temas avançados, com foco prático, visão técnica e segurança na atuação.

---
### 1️⃣REFORÇO E CONSOLIDAÇÃO – Nível ++++ e +++++  
  
📣 Você já tem domínio sólido de temas essenciais para um DBA pleno. Aqui o foco não é decorar nem repetir o que você já sabe — mas sim refinar, conectar e aplicar com profundidade:

🔸 Controle de concorrência: LOCK TX, LOCK TABLE  
🔸 Acesso e conexão: Listener, TNS x Easy Connect  
🔸 Diagnóstico de sessão: TOP, LOAD, waits  
🔸 Performance SQL: plano de execução, estatísticas, parse, constraints, índices (com e sem reverso), particionamento  
🔸 Tuning de instância: STATISTICS_LEVEL, parâmetros de REDO  
🔸 Arquitetura lógica de logs: REDO, UNDO  

💡 O desafio agora é transformar tudo isso em visão estratégica. Ser capaz de explicar, simular, ajustar e otimizar — com propriedade.

---
### 2️⃣ APERFEIÇOAMENTO TÉCNICO – Nível ++ e +++  

📣 Você já tem a base conceitual de vários assuntos, mas ainda precisa ganhar fluidez, entender os detalhes que fazem diferença e aplicar com mais segurança no dia a dia. Essa etapa é fundamental para desenvolver uma atuação mais segura e técnica, elevando a qualidade das decisões e respostas frente a situações reais.

🔸 SGA x PGA na prática  
Entenda os principais componentes da SGA (shared pool, buffer cache, large pool, redo log buffer) e como a PGA atua no contexto de operações individuais (sorts, joins, hash). Avalie os impactos da política workarea_size_policy e saiba como ajustar memória para OLTP e batch.

🔸 Parâmetros de instância e tuning leve  
Conheça e pratique o uso dos principais parâmetros que influenciam desempenho e estabilidade (sga_target, pga_aggregate_target, memory_target, db_cache_size). Aprenda a diagnosticar gargalos a partir da parametrização inadequada.

🔸 UNDO: retenção, extents, comportamento sob concorrência  
Estude o funcionamento do undo automático, sua retenção lógica x física, e como interpretar sessões com alto consumo de undo. Use as views v$transaction e dba_undo_extents para análise real.

🔸 Abertura e shutdown da instância  
Entenda as fases NOMOUNT, MOUNT, OPEN. Quando usar cada uma, e qual o papel de cada estágio em operações como duplicação, restore e maintenance. Compare os efeitos de SHUTDOWN NORMAL, IMMEDIATE, TRANSACTIONAL e ABORT.

🔸 LOCK TM e impacto em DDL/DML concorrente  
Uma das causas mais comuns de bloqueio TM é a existência de foreign keys sem índices associados. Isso faz com que, durante operações de DML na tabela pai, o Oracle tenha que fazer varredura completa na tabela filha para verificar integridade referencial, o que gera locks pesados. Explore como identificar FKs sem índice (consultando dba_constraints e dba_ind_columns) e os impactos que isso causa em ambientes com alto volume de transações.  
Além disso, compreenda situações de lock em estruturas de dicionário, como alterações de tabela enquanto outra sessão mantém transações abertas. Aprenda a diferenciar bloqueios legítimos de anomalias.

🔸 Comportamento de memória/SWAP no Linux  
Aprenda a analisar uso de memória real, buffers, cached e swap. Diferencie pressão real de uso excessivo de swap por configuração indevida.

🔸 Swappiness  
Entenda o que representa o parâmetro swappiness e como ele interfere na decisão do kernel de utilizar a swap. Saiba quando ajustar esse valor (por exemplo, de 60 para 10) e por que isso melhora a previsibilidade do Oracle.

🔸 Large Pages (HugePages)  
Compreenda o conceito de HugePages, sua função no mapeamento da SGA e os ganhos de performance e estabilidade que proporciona. Aprenda a calcular, configurar e validar a ativação correta para ambientes com AMM desabilitado.

💡 Esse bloco é o meio do funil, onde você começa a se destacar pelo refinamento do que já conhece. Vale revisar documentações, simular comportamentos e criar observações próprias.

---
### 3️⃣ BASE EM CONSTRUÇÃO – Nível + e ++  

📣 Aqui estão os temas que você já viu ou ouviu falar, mas ainda não domina a ponto de aplicar com clareza em um ambiente produtivo.

🔸 FAST_START_MTTR_TARGET e comportamento do crash recovery  
🔸 AWR (Automatic Workload Repository)  
🔸 COMMIT_WAIT e COMMIT_WRITE  
🔸 Parâmetros avançados de tuning  
🔸 LOCK TM e impacto de estrutura  
🔸 Interpretação de estatísticas do SO (com foco em comportamento sob carga)  

💡 A proposta é transformar essa familiaridade em segurança real. Vale revisar materiais, anotar dúvidas e trazer pra discussão sempre que travar.

---
### 4️⃣ DESENVOLVIMENTO CRÍTICO – Nível -  
  
📣 Agora sim, os grandes blocos que vão te levar para o próximo patamar como DBA sênior.

#### 🛡️ ALTA DISPONIBILIDADE – DATA GUARD  
🔹 Como funciona a arquitetura física e lógica do Data Guard  
🔹 Diferença entre switchover e failover, e quando aplicar cada um  
🔹 O papel do broker (DGMGRL) na automação e no monitoramento  
🔹 Como monitorar e interpretar o LAG de transporte e aplicação  
🔹 O que é e como funciona um GAP de sequence  
🔹 Modos de proteção: maximum protection, availability e performance  
🔹 O que é o snapshot standby e quando pode ser útil  

📌 Aqui o estudo precisa ser passo a passo. Entenda o conceito, simule a criação, experimente o switch, monitore o apply.

#### ⚙️ ESCALABILIDADE E REDUNDÂNCIA – ORACLE RAC  
🔹 A arquitetura do cluster RAC, instâncias paralelas e acesso ao mesmo banco  
🔹 Conceito de Cache Fusion (GC – Global Cache)  
🔹 O papel dos arquivos críticos: OCR e Voting Disk  
🔹 Diferença entre SCAN listener e listener local  
🔹 O que são VIPs, interconnects e como funcionam  
🔹 Conceito de node eviction  
🔹 Gerenciamento do cluster  
🔹 Serviços no RAC e balanceamento de carga  
🔹 Planejamento de endereçamento IP para 2+ nós  

#### 💾 BACKUP E RECUPERAÇÃO – RMAN E ALÉM  
🔹 Controlfile: backup e recriação  
🔹 Bloco corrompido: RMAN BLOCKRECOVER  
🔹 Tabela deletada: opções (Flashback, Data Pump, RMAN)  
🔹 Datafile fuzzy e OPEN RESETLOGS  
🔹 Restore completo e PITR  
🔹 Restore de tablespace  
🔹 Tempfile: gerenciamento e recriação  
🔹 Política de backup  

---  
### 🧭 ORIENTAÇÃO FINAL  
 
📣 Bruno, sua trajetória já mostra consistência e clareza nos fundamentos. Agora é hora de expandir com inteligência, mergulhar no que ainda não é confortável e sair do outro lado com domínio técnico real.

✨ O plano está montado, mas ele é só um mapa. Você escolhe o ritmo, a rota e os atalhos — mas eu tô aqui como guia pra garantir que você chegue inteiro no destino.

🧰 Use os materiais que preferir: DBAOCM Club, cursos da Udemy como os do Baraka, ou qualquer outro conteúdo de confiança. Só me avise o que está usando em cada tema. Isso me permite ajustar sua trilha e te dar os direcionamentos certos no momento certo.

⏳ Não tenha pressa. O foco é entender com clareza, não apenas concluir etapas. A prática vai reforçar o que for necessário — e é ela que vai te preparar pra quando o desafio chegar.

🚀 Você tem potencial técnico e postura de quem quer crescer. Agora é constância, foco e entrega.
