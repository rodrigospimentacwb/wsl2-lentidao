# Configurando Regra de Firewall para WSL no Windows 11

Este guia explica como configurar uma regra de firewall para o WSL no Windows 11, corrigindo problemas de conectividade e lentidão.

---

## **1️⃣ Identificar o Nome Completo da Interface do WSL**

Antes de criar a regra de firewall, precisamos encontrar o nome correto da interface do WSL.

1. **Abra o PowerShell** (pressione `Win + S`, digite **PowerShell**, clique com o botão direito e escolha **Executar como Administrador**).
2. **Rode o comando abaixo para listar as interfaces de rede:**
   ```powershell
   Get-NetAdapter | Where-Object { $_.Name -like "*WSL*" }
   ```
3. **Anote o nome completo da interface do WSL**, que geralmente tem um formato como:
   ```
   vEthernet (WSL (Hyper-V firewall))
   ```

---

## **2️⃣ Criar a Regra de Firewall para o WSL**

Agora que temos o nome da interface, podemos criar a regra de firewall.

1. **No PowerShell (como Administrador), execute:**
   ```powershell
   New-NetFirewallRule -DisplayName "WSL" -Direction Inbound -InterfaceAlias "vEthernet (WSL (Hyper-V firewall))" -Action Allow
   ```

2. **Se der erro de permissão**, execute o PowerShell como Administrador e tente novamente.

3. **Se precisar criar a regra sem especificar a interface**, use:
   ```powershell
   New-NetFirewallRule -DisplayName "WSL" -Direction Inbound -Action Allow
   ```

4. **Para confirmar que a regra foi criada, execute:**
   ```powershell
   Get-NetFirewallRule -DisplayName "WSL"
   ```
   Se `Enabled : True` aparecer, a regra foi aplicada corretamente.

---

## **3️⃣ Reiniciar o WSL e a Interface de Rede**

Após criar a regra, reinicie o WSL e a interface de rede:

```powershell
wsl --shutdown
netsh interface set interface "vEthernet (WSL (Hyper-V firewall))" admin=disable
netsh interface set interface "vEthernet (WSL (Hyper-V firewall))" admin=enable
wsl
```

Isso garante que as mudanças sejam aplicadas corretamente.

---

## **4️⃣ Testar a Performance do WSL**

Agora, abra o **WSL** e rode um comando para testar se a lentidão foi resolvida:
```bash
time ls -l /
```
Se o tempo de resposta for rápido, a configuração foi bem-sucedida!

Caso ainda enfrente problemas, verifique se a interface de rede está ativa ou tente desativar e reativar o WSL:
```powershell
wsl --shutdown
wsl
```

---

Data: 03/2025
[Ref no reddit](https://www.reddit.com/r/vscode/comments/sulebx/slow_git_in_wsl_after_updating_to_windows_11/?tl=pt-br&rdt=34611)
