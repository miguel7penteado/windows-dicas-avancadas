
```cmd
:: Listar as interfaces
netsh interface ipv4 show interface

:: Renomear interfaces para melhor trabalhar
netsh interface set interface name = "NomeAntigo" newname = "NomeNovo"

:: Fixar o enederço IP, mascara e gateway se necessário
netsh interface ipv4 set address name="NomeInterface" static ENDERECO_IP MASCARA_REDE GATEWAY

:: Setar a interface em modo DHCP
netsh interface ipv4 set address name="NomeInterface" source=dhcp

:: Definir um endereço IP estático de servidor DNS para uma determinada interface
netsh interface ipv4 set dns name="NomeInterface" static IP_SERVIDOR_DNS

:: Definir que a interface obtenha DNS a partir do serviço DHCP
netsh interface ipv4 set dnsservers name="NomeInterface" source=dhcp

:: Exemplo simples

:: Setando um DNS na interface
netsh interface ipv4 set dns name="Lan" static 172.16.0.21

:: Setando o IPv4 principal na interface
netsh interface ipv4 set address name="Lan" static 172.16.2.71  255.255.254.0 172.16.2.1

:: Setando o IPv4 secundário na interface
Netsh int ipv4 add address name="Lan" 172.16.60.20 255.255.255.0 SkipAsSource=True

:: Firewall

netsh advfirewall firewall add rule name=ICMPV4 protocol=icmpv4 dir=in action=allow

netsh advfirewall firewall show rule name=icmpv4

```
