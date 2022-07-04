# Apaga usuário Windows
## Apaga um usuário do windows usando o powershell

Apaga todos os usuários locais exceto `usuario1` e `usuario2`

1-Perfis

```powershell
$perfis = $null

$perfis = Get-WMIObject -class Win32_UserProfile | Where {((!$_.Special) -and ($_.LocalPath -ne "C:\Users\usuario1") -and ($_.LocalPath -ne "C:\Users\usuario2"))}

if ($perfis -ne $null) {
$perfis | Remove-WmiObject
}

Exit
```

2- Usuários em sí:

```cmd
net user usuário1 /delete
net user usuário2 /delete
net user usuário3 /delete
```

