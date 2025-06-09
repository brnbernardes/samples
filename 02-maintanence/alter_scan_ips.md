
## ✅ **Alterar Nome e IPs do SCAN no Oracle RAC – via srvctl**

> ⚠️ **Pré-requisitos:**
>
> * Os novos IPs devem estar livres e acessíveis na rede.
> * `/etc/hosts` já atualizado em **todos os nodes** com os novos IPs e nome:
>
>   ```ini
>   192.168.56.72 srvora-scan-v02.localdomain srvora-scan-v02
>   192.168.56.73 srvora-scan-v02.localdomain srvora-scan-v02
>   192.168.56.74 srvora-scan-v02.localdomain srvora-scan-v02
>   ```

---

### 🔁 1. **Parar os SCANs e SCAN listeners**

```bash
sudo su - grid
srvctl stop scan_listener
srvctl stop scan
```



### 🗑️ 2. **Remover SCANs antigo**

```bash
srvctl remove scan_listener
srvctl remove scan
```

> ❗ Pode ser necessário confirmar (`y`) para excluir.



### ➕ 3. **Criar novo SCAN com o novo nomem, logo após adicionar SCAN_LISTENER**

```bash
srvctl add scan -n srvora-scan-v02
srvctl add scan_listener
```

> O Oracle criará automaticamente até **3 SCAN VIPs** usando round-robin DNS (ou `/etc/hosts` se não houver DNS configurado).



### 🚀 4. **Iniciar novamente os serviços**

```bash
srvctl start scan
srvctl start scan_listener
```


### 🔍 6. **Validar se os novos IPs foram associados com sucesso**

```bash
srvctl config scan
srvctl status scan
srvctl status scan_listener
```

Saída esperada (exemplo):

```
SCAN name: srvora-scan-v02, Network: 1
SCAN VIP name: scan1, IP: 192.168.56.72
SCAN VIP name: scan2, IP: 192.168.56.73
SCAN VIP name: scan3, IP: 192.168.56.74
```



### 🧪 7. **Testar resolução e conexão**

```bash
nslookup srvora-scan-v02
tnsping srvora-scan-v02
sqlplus user@//srvora-scan-v02:1521/servicename
sqlplus sys@//srvora-scan-v02:1521/pdb1
```


### ✅ 8. **Atualizar TNS e aplicação**

* Corrigir `CONFIG.dd` da aplicação para apontar para `srvora-scan-v02`

```init
# CONFIGURAÇÃO DE EXECUÇÃO DO CENÁRIO STRESS_TEST
# --
# STAT[1] → 1 = Ativo (executa o teste), 2 = Pausado (não executa, apenas aguarda)
# USRS[15] → Quantidade de usuários simultâneos que vão gerar carga
# TIME[2] → Tempo de execução do teste em minutos
# CNEX[//srvora-scanv01:1521/APP_pdb_auto] → String de conexão completa para o banco (EZCONNECT)

STAT[1]
USRS[10]
TIME[5]
CNEX[//srvora-scan-v02:1521/APP_pdb_auto]
```
  
* Validar que as conexões estão chegando por ele


