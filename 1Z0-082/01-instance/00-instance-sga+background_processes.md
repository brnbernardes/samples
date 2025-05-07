# 💡 **Instância = Memória (SGA) + Processos de Background**

Ela é responsável por:
- Gerenciar a alocação de memória (através da SGA)
- Executar solicitações dos usuários (consultas, atualizações etc.)
- Manter a integridade e a consistência dos dados
- Controlar acesso concorrente
- Lidar com falhas e recuperação

### Componentes principais da instância Oracle:

1. **SGA (System Global Area)**  
   - Área de memória compartilhada entre os processos
   - Contém dados em cache, informações de controle, instruções SQL, buffers etc.

2. **Processos de Background**  
   - São processos auxiliares que executam tarefas como escrita em disco, gerenciamento de log, recuperação, checkpoint etc.  
   - Exemplos: DBWn (Database Writer), LGWR (Log Writer), CKPT (Checkpoint), SMON (System Monitor), PMON (Process Monitor)


### 📘 Diferença entre instância e banco de dados

| Termo         | Descrição                                                                 |
|---------------|---------------------------------------------------------------------------|
| Instância     | Conjunto de memória + processos que acessam os dados                     |
| Banco de dados| Conjunto de arquivos físicos armazenados em disco (datafiles, redo logs, control files) |

👉 A instância **gerencia** o banco de dados, mas os dados em si vivem nos arquivos físicos.

---

# 🧠 SGA

A SGA (System Global Area) é uma das partes mais importantes de uma instância Oracle. Ela é uma área de memória compartilhada usada por todos os processos da instância para armazenar dados e informações de controle. É essencial para o desempenho e funcionamento do banco de dados.

> 💡 **SGA = área de memória compartilhada por toda a instância**

Ela armazena informações que são usadas com frequência, como dados acessados por usuários, instruções SQL parseadas, buffers de dados modificados que ainda não foram gravados em disco, etc.


## 🧩 Componentes principais da SGA

| Componente                | Função                                                                 |
|---------------------------|------------------------------------------------------------------------|
| **DB Buffer Cache**       | Armazena blocos de dados lidos do disco (datafiles). Reduz acessos ao disco. |
| **Shared Pool**           | Guarda instruções SQL parseadas, PL/SQL compilado e dicionário de dados. |
| **Redo Log Buffer**       | Armazena alterações feitas nos dados (para garantir recovery em falhas). |
| **Large Pool** (opcional) | Usado para operações grandes (backup RMAN, paralelismo, etc.).         |
| **Java Pool** (opcional)  | Usado para armazenar código Java dentro do banco Oracle.               |
| **Streams Pool**          | Usado para Oracle Streams e GoldenGate.                                |
| **Fixed SGA**             | Contém informações estáticas sobre a instância e ponteiros internos.   |


---

# 🌐 Processos em Segundo Plano (Background Processes)

Os **background processes** no Oracle Database são essenciais para garantir o bom funcionamento da instância do banco de dados, como controle de memória, recuperação de falhas, monitoramento, entre outros. Esses processos são iniciados automaticamente durante a inicialização do Oracle e continuam a ser executados enquanto o banco de dados está em operação.

## Principais Processos em Segundo Plano

### 1. 📝 **DBWR (Database Writer)**
- **Função**: Escreve os blocos modificados da memória SGA (buffer cache) de volta para os datafiles.
- **Importância**: Garante que as alterações feitas nas tabelas e índices sejam persistidas no disco de forma eficiente e segura.

### 2. ✍️ **LGWR (Log Writer)**
- **Função**: Escreve os dados dos redo log buffers nos redo log files.
- **Importância**: Essencial para a integridade transacional, pois armazena todas as alterações que ocorreram no banco de dados.

### 3. 🔧 **SMON (System Monitor)**
- **Função**: Realiza manutenção da instância, recuperação de falhas após um shutdown, gerenciamento de tabelas temporárias e limpeza de recursos não utilizados.
- **Importância**: Responsável por manter a estabilidade do banco de dados durante falhas inesperadas.

### 4. 👀 **PMON (Process Monitor)**
- **Função**: Monitora os processos do Oracle e limpa os recursos do sistema deixados para trás por processos falhos.
- **Importância**: Evita o acúmulo de recursos (memória, processos) em caso de falhas, mantendo o ambiente estável.

### 5. ⏱️ **CKPT (Checkpoint Process)**
- **Função**: Garante a sincronização dos dados nos datafiles com os redo log files periodicamente.
- **Importância**: Acelera a recuperação após falhas ao marcar um ponto seguro onde o banco de dados pode ser restaurado de forma eficiente.

### 6. 🔄 **RECO (Recovery Process)**
- **Função**: Utilizado em ambientes **Data Guard** ou **RAC** para garantir a consistência dos dados entre os bancos de dados primário e standby.
- **Importância**: Em ambientes de alta disponibilidade, resolve falhas de transações no banco de dados primário e aplica as correções no banco de dados standby.

### 7. 📦 **ARCn (Archiver Process)**
- **Função**: Arquiva os redo log files quando eles estão cheios, movendo-os para arquivos de backup.
- **Importância**: Essencial para a continuidade das operações em modo **archivelog**, garantindo a persistência dos redo logs.

### 8. 🖥️ **Dnnn (Dispatcher Processes)**
- **Função**: Trabalham em sistemas **shared server** para lidar com múltiplas requisições simultâneas, melhorando a escalabilidade.
- **Importância**: Aumenta a capacidade do banco de dados para lidar com grandes volumes de usuários simultâneos.

### 9. 🧠 **MMON (Memory Monitor)**
- **Função**: Gerencia a memória dinâmica e ajusta os parâmetros de memória, como SGA e PGA, conforme necessário.
- **Importância**: Ajuda a otimizar o uso da memória, melhorando a performance do banco de dados.

### 10. 🛠️ **MMNL (Memory Monitor Light)**
- **Função**: Versão leve do MMON, realizando monitoramento e ajustes de memória sem sobrecarregar o sistema.
- **Importância**: Proporciona uma solução de monitoramento eficiente em termos de recursos para o gerenciamento de memória.

> Esses processos são essenciais para garantir a estabilidade, segurança e performance de um banco de dados Oracle. Cada um tem um papel específico e crítico para o funcionamento adequado da instância de banco de dados.
