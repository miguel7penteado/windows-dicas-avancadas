# Firewall windows

Liberar o PING
```cmd
netsh advfirewall firewall add rule name="ICMPv4 Allow Ping Requests" protocol=icmpv4:8,any dir=in action=allow
netsh advfirewall firewall add rule name="ICMPv6 Allow Ping Requests" protocol=icmpv6:8,any dir=in action=allow
```

Liberar o compartilhamento de impressoras
```cmd
netsh advfirewall firewall add rule name="File and Printer Sharing (Echo Request – ICMPv4-In)" protocol=icmpv4:8,any dir=in action=allow
netsh advfirewall firewall add rule name="File and Printer Sharing (Echo Request – ICMPv6-In)" protocol=icmpv6:8,any dir=in action=allow
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
