# Apaga usuário Windows
## Apaga um usuário do windows usando o powershell

```powershell
$perfis = $null

$perfis = Get-WMIObject -class Win32_UserProfile | Where {((!$_.Special) -and ($_.LocalPath -ne "C:\Users\COMPUTADOR1") -and ($_.LocalPath -ne "C:\Users\Marcia"))}

if ($perfis -ne $null) {
$perfis | Remove-WmiObject
}

Exit
```
