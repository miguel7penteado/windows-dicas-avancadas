# windows-dicas-avancadas

## Recuperado o carregador de boot do windows

```cmd
c:\DISKPART
```

```cmd
DISKPART> list disk

  Nº Disco  Status         Tam.     Livre    Din. GPT
  --------  -------------  -------  -------  ---  ---
  Disco 0    Online          931 GB      0 B
  Disco 1    Online          931 GB      0 B        *
```

```cmd
DISKPART> select disk 1

O disco 1 é o disco selecionado.
```

```cmd
DISKPART>  list partition

    Partição No.   Tipo              Tamanho  Deslocamento
  -------------  ----------------  -------  ------------
  Partição 1    Sistema            100 MB  1024 KB
  Partição 2    Reservado           16 MB   101 MB
  Partição 3    Primário           151 GB   117 MB
  Partição 4    Desconhecido       151 GB   151 GB
  Partição 5    Desconhecido       151 GB   303 GB
  Partição 6    Desconhecido       151 GB   454 GB
  Partição 7    Desconhecido       151 GB   606 GB
  Partição 8    Desconhecido       151 GB   758 GB
  Partição 9    Desconhecido      5000 MB   909 GB
  Partição 10   Desconhecido      5000 MB   914 GB
  Partição 11   Desconhecido      5000 MB   919 GB
  Partição 12   Desconhecido      7362 MB   924 GB

```
Partições são organizações lógicas do disco.
Volumes são as organizações lógicas do disco da forma como o windows reconheceu.

Veja qual é o volume da partição EFI do seu disco. No caso da minha, é 
```cmd
DISKPART> list vol 

Volume ####  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    464 GB  Healthy    Boot
  Volume 1         Recovery     NTFS   Partition    450 MB  Healthy    Hidden
  Volume 2                      FAT32  Partition     99 MB  Healthy    System
  Volume 3                      NTFS   Partition    847 MB  Healthy    Hidden
```
Minha partição de UEFO foi reconhecida como "volume 2". Ela é sempre um volume de aproximadamente 100 megabytes formatado em **FAT32**

Selecione o volume 
```cmd
DISKPART> select vol 2
Volume 2 is the selected volume.
```

Mapeie o volume com uma letra de unidade para você poder trabalhar nele pela linha de comando do DOS
```cmd
DISKPART> assign letter=z
DiskPart successfully assigned the drive letter or mount point.
```

Reconstruir o diretório de boot do windows na partição EFI
```cmd
mkdir Z:\EFI\Microsoft\Boot
```

```cmd
xcopy /s C:\Windows\Boot\EFI*.* Z:\EFI\Microsoft\Boot
```

```cmd
z:
```

```cmd
cd EFI\Microsoft\Boot
```
Agora vamos recriar as entradas do carregador de boot do windows 10

```cmd
bcdedit /createstore BCD
bcdedit /store BCD /create {bootmgr} /d "Windows Boot Manager"
bcdedit /store BCD /create /d "My Windows 10" /application osloader
; Aqui é gerado um ID aleatório , exemplo {D91FE7C2-605F-4A2B-B035-80A7C30979BF}, que você vai utilizar no proximo passo
; copie esse valor. vou chama-lo aqui de {meu_id_gerado_na_hora}
bcdedit /store BCD /set {bootmgr} default {meu_id_gerado_na_hora}
bcdedit /store BCD /set {bootmgr} path \EFI\Microsoft\Boot\bootmgfw.efi
bcdedit /store BCD /set {bootmgr} displayorder {default}
bcdedit /store BCD /set {default} device partition=c:
bcdedit /store BCD /set {default} osdevice partition=c:
bcdedit /store BCD /set {default} path \Windows\System32\winload.efi
bcdedit /store BCD /set {default} systemroot \Windows
```
Reinicialize seu windows.



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

