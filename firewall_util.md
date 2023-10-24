# Firewall windows

Liberar o PING
```cmd
netsh advfirewall firewall add rule name="ICMPv4 Allow Ping Requests" protocol=icmpv4:8,any dir=in action=allow
netsh advfirewall firewall add rule name="ICMPv6 Allow Ping Requests" protocol=icmpv6:8,any dir=in action=allow
```

Liberar o protocolo ICMP para compartilhamento de impressoras
```cmd
netsh advfirewall firewall add rule name="File and Printer Sharing (Echo Request – ICMPv4-In)" protocol=icmpv4:8,any dir=in action=allow
netsh advfirewall firewall add rule name="File and Printer Sharing (Echo Request – ICMPv6-In)" protocol=icmpv6:8,any dir=in action=allow
```

Liberar o compartilhamento de impressoras
```cmd
netsh advfirewall firewall set rule group="Compartilhamento de Arquivo e Impressora" new enable=Yes
;;netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

Liberar a descoberta de Rede
```cmd
;; Inglês
netsh advfirewall firewall set rule group="Descoberta de Rede" new enable=Yes
;;netsh advfirewall firewall set rule group="Network Discovery" new enable=Yes
```

Desativar autoridade de segurança local - Local Security Authority (LSA)
```cmd
REG ADD "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa" /V LimitBlankPasswordUse /T REG_DWORD /D 0 /F
```

Ativar "arquivos offline"
```cmd
REG ADD "HKLM\SYSTEM\CurrentControlSet\Services\Csc\Parameters" /V FormatDatabase /T REG_DWORD /D 1 /F

```

Ativar serviço de descoberta de Host
```cmd
sc start fdPhost
```


Desligar e ligar
```cmd
NetSh Advfirewall set allprofiles state off
NetSh Advfirewall set allprofiles state on
```

Listar os perfis
```cmd
Netsh Advfirewall show allprofiles
```
