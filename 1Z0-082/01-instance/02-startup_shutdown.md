# 🧠 Oracle Database – Ciclo de Inicialização e Encerramento

---

## 🔵 Fases de Inicialização

1. ▶️ STARTUP
   - Inicia a instância Oracle e passa pelas fases: NOMOUNT → MOUNT → OPEN.

2. 🔹 NOMOUNT
   - A instância é iniciada, mas não está associada a um banco de dados.
   - Lê o arquivo de parâmetro (SPFILE ou PFILE) para configurar a instância.
   - Aloca a memória da SGA.
   - Inicia os processos de background.
   - O alertlog e os trace files são abertos.

3. 🔸 MOUNT
   - A instância é iniciada e está associada a um banco de dados.
   - Oracle verifica a consistência dos control files e dos data files.
   - O banco de dados ainda não está disponível para os usuários.

4. 🟢 OPEN
   - A instância é iniciada e está associada a um banco de dados aberto.
   - Os dados contidos nos data files estão acessíveis para os usuários autorizados.
   - A undo é associada à instância e os redo logs são abertos.

5. 💡 READ-ONLY 
   - Utilizado para uma manutenções
   - STARTUP MOUNT;
   - ALTER DATABASE OPEN READ ONLY;

---

## 🔴 Modos de Encerramento (SHUTDOWN)

1. ✅ SHUTDOWN NORMAL
   - Aguarda todos os usuários desconectarem voluntariamente.
   - Grava os dados pendentes em disco.
   - Não há necessidade de recuperação na próxima inicialização.

2. ⏳ SHUTDOWN TRANSACTIONAL
   - Aguarda a conclusão de todas as transações ativas.
   - Após isso, encerra normalmente e grava os dados.

3. ⚡ SHUTDOWN IMMEDIATE
   - Desconecta todos os usuários imediatamente.
   - Finaliza as transações em andamento.
   - Grava os dados em disco.
   - Sem necessidade de recuperação na próxima inicialização.

4. ❌ SHUTDOWN ABORT
   - Encerra imediatamente e à força.
   - Não grava dados pendentes.
   - Na próxima inicialização, será necessário realizar recovery automático para garantir consistência.

---