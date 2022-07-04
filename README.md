# windows-dicas-avancadas
Quem tarbalha com T.I. , seja em infra-estrutura, suporte, desenvolvimento ou análise de dados frequentemente cai em algumas
armadilhas das limitações deste sistema. Faço menção ao suporte a operações de linha de comando no Windows.
Por isso vou colocar alguns tópicos interessantes aqui.

## Ativando o Terminal Remoto (Remote Desktop)

Desbloqueando o serviço via linha de comando
```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -value 0
```
Abrindo porta do serviço no firewall do windows 10 via linha de comando
```powershell
Enable-NetFirewallRule -DisplayGroup "Área de Trabalho Remota"
```
Ativando a virtualização hyper-V e o suporte a Containers do windows
```powershell
powershell Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V,Containers -All
```

