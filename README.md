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

## Apagando Perfis de Usuário no Windows
Existe uma ferramenta (e quando falamos de windows, normalmente não falamos de código aberto) chamada [Deflprof2](https://www.sepago.com/blog/2011/05/01/new-free-delprof2-user-profile-deletion-tool), 
criada por um 
Esta ferramentas, além de apagar perfis, tem algumas vantagens

*. - Delprof2 deixa você explicitar qual perfil de usuário vocẽ quer apagar.
*. - Delprof2 sobrepassa permissões de segurança do usuário detentor do perfil.
*. - Delprof2 suporta tamanho de nomes de diretórios maiores que 260 caracteres.
*. - Delprof2 funciona em todas as versões modernas do windows.
*. - Delprof2 é liberado para uso pessoal e comercial.

Exemplo: salve o executável em um pendrive, autentique-se no sistema com perfil administrador. 
Liste os perfis de usuários existentes e escolha quais vocẽ quer apagar.
```cmd
rem "liste os perfis dos usuários existentes"
Delprof2 /c:nome_meu_computador /l

rem "Agora apague os perfis. O programa vai perguntar para você se deseja apagar ou pular"
Delprof2 /c:nome_meu_computador -p

```

