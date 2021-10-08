Construindo discos do Windows PE no Linux | James Reynolds {"mainEntityOfPage":{"@type":"WebPage","@id":"/linux/windows/2019/01/12/windows-pe-isos-on-linux.html"},"@type":"BlogPosting","url":"/linux/windows/2019/01/12/windows-pe-isos-on-linux.html","headline":"Building Windows PE disks on Linux","dateModified":"2019-01-12T16:30:30+00:00","datePublished":"2019-01-12T16:30:30+00:00","description":"What?","@context":"http://schema.org"}  if(!(window.doNotTrack === "1" || navigator.doNotTrack === "1" || navigator.doNotTrack === "yes" || navigator.msDoNotTrack === "1")) { (function(i,s,o,g,r,a,m){i\['GoogleAnalyticsObject'\]=r;i\[r\]=i\[r\]||function(){ (i\[r\].q=i\[r\].q||\[\]).push(arguments)},i\[r\].l=1*new Date();a=s.createElement(o), m=s.getElementsByTagName(o)\[0\];a.async=1;a.src=g;m.parentNode.insertBefore(a,m) })(window,document,'script','https://www.google-analytics.com/analytics.js','ga'); ga('create', 'UA-139075725-1', 'auto'); ga('send', 'pageview'); } 

[James Reynolds](/)

[Projetos] (/ projetos /) [Sobre mim] (/ sobre /)

Criação de discos do Windows PE no Linux
========================================

Jan 12, 2019

Como assim ?
------------

![](/assets/hdd.png)

Nós modificamos a imagem ISO do Windows PE10 construída usando o Kit de Avaliação e Implantação do Windows (WADK) e precisamos inicializar essas ISOs em máquinas EC2. EC2 não tem noção de imagens ISO, então devemos transformá-las em um disco.

Por que Linux ?!
----------------

Eu vou concordar que a princípio isso soa um pouco como uma missão tola - mas há um método para essa loucura:

1. Já temos ferramentas de script para controlar a criação de VHD, partições e grub
2. Podemos fazer máquinas Linux com wimlib mais facilmente do que máquinas Windows com o WADK certo

O segundo ponto foi muito importante para nós. O sistema de compilação é enfático, por isso queremos configurações fantoches e / ou imagens de empacotador com as ferramentas integradas. Isso é muito mais fácil de fazer no Linux ainda, embora o Windows (por exemplo, via Chocolatey) tenha melhorado muito nos últimos anos.

Como ?
------

O arquivo WIM
=============

The Windows image file is just a compressed RAM Disk with a set of tools that can dump it to disk, append other bits to it etc… There will be one somewhere on the ISO:

```
iso-read -i <ISO> -o boot.wim -e /sources/boot.wim
```

Agora que temos um arquivo WIM, podemos fazer um disco.

Windows primeiro ... mas apenas uma vez
=======================================

A maioria das informações aqui vem deste tópico: ![](http://reboot.pro/topic/20468-create-a-windows-system-from-scratch-using-linux/page-2)

As únicas coisas difíceis eram fazer o BCD e fazer uma boa entrada / assinatura do MBR. Descobriu-se que a maneira _fácil_ de fazer isso ... é simplesmente copiá-lo do Windows. Sim, isso é um pouco como trapacear ... mas vale a pena colocar o resto do procedimento no sistema de CI.

Em uma máquina Windows, crie um VHD a partir do arquivo WIM montando um VHD em branco com uma única partição NTFS como `V:` e:

```cmd
dism.exe /Apply-Image /ImageFile:boot.wim /Index:1 /ApplyDir:V:\
bootsect /nt60 V:
bootsect /nt60 V: /force /mbr
bootbcd V:\Windows /s V: /f ALL
bcdedit /store V:\Boot\BCD /set {default} bootstatuspolicy ignoreall failures
bcdedit /store V:\Boot\BCD /set {default} detecthal yes
```

Em uma caixa Linux, pegue o BCD e o MBR:

```
qemu-nbd -c /dev/nbd0 my.vhd
kpartx -auv /dev/nbd0
mount /dev/mapper/nbd0p1 windows
cp windows/Boot/BCD pe10.bcd
dd if=/dev/nbd0 of=pe10.mbr bs=512 count=1
```    
    
Faça um dispositivo de disco e loop do tamanho certo
====================================================

```
dd if=/dev/zero of=disk_image.raw bs=1 count=1 seek=1536M
losetup -f disk_image.raw
```

> Realmente certifique-se de que este seja um múltiplo de 256 MB, caso contrário, o EC2 ficará um pouco chateado e não falará muito sobre isso.

Agora temos `/dev/loop0` como um dispositivo de bloco apoiado em `disk_image.raw` que é um arquivo esparso de 1,5 GB.

Particionar o disco
===================

Você pode realmente fazer isso sem o loopback, mas nós o temos de qualquer maneira, então podemos também usá-lo. Basta criar uma partição que preencha todo o disco, torná-la inicializável e garantir que seu tipo seja NTFS:

```
    fdisk /dev/loop0
    #...
    #Disklabel type: dos
    #Disk identifier: 0x59b584ff
    
    Command (m for help): p
    #Device     Boot Start     End Sectors  Size Id Type
    #/dev/loop0 *     2048 3355441 3353394  1.6G  7 HPFS/NTFS/exFAT
    
    Command (m for help): w 
    #The partition table has been altered.
    #Syncing disks.
```

Crie um novo volume NTFS e descompacte o WIM
============================================

Este bit é fácil uma vez que o wimlib é instalado. Mapeie a nova partição, construa um sistema de arquivos NTFS nela, descompacte o arquivo WIM e monte a pasta resultante:

```
kpartx -auv /dev/loop0
mkfs.ntfs /dev/mapper/loop0p1
wimapply boot.wim /dev/mapper/loop0p1
mount /dev/mapper/loop0p1 test
``` 

A parte feia ... falsificar o sistema de inicialização
======================================================

Depois de fazer a imagem do Windows, executei md5sum nos vários arquivos de inicialização e descobri que todos existem em outro lugar no WIM e podem ser copiados para o lugar certo. Fácil.

```
mkdir -p test/Boot/grub2 test/Boot/Resources/en-US
cp test/Windows/Boot/PCAT/bootmgr test/
cp test/Windows/Boot/Resources/*.dll test/Boot/Resources
cp test/Windows/Boot/Resources/en-US/* test/Boot/Resources/en-US/
``` 

A parte _realmente_ feia ... copiar BCD e MBR
==============================================

Isso foi descoberto por tentativa e erro ... o arquivo BCD é na verdade apenas um arquivo de registro, mas infelizmente ofuscado. Existem algumas informações que abrangem o que os vários bits significam ... mas isso não importa. Se você copiar o lote em massa ... então funciona!

```bash
cp pe10.bcd test/Boot/BCD
dd if=pe10.mbr of=/dev/loop0 bs=446 count=1
```

Instale o grub (não é feio assim?)
=================================

```grub
    # cat > test/Boot/grub2/grub.cfg
    set default=0
    set timeout=5
    menuentry 'Windows CD' {
        search -s root -f /Boot/BCD
        ntldr /bootmgr
    }
    # grub2-install --target=i386-pc --root-directory=test --boot-directory=test/Boot /dev/loop0
``` 

Feito!
======

Portanto, agora temos um arquivo RAW que podemos converter (`qemu-img` funciona bem) para VHD e fazer upload para EC2 como um AMI. Adicionamos um loop de teste ao sistema de CI que aciona uma instância com esta imagem para obter:

`Build Image` →` Upload Image` → `Boot image` →` Gravar screenshots`

Tudo como parte da nossa imagem CI. Assim, uma vez que a parte do Windows da compilação é concluída, a cadeia começa e obtemos um resultado bom ou ruim em 10 minutos. Nada mal.


Espere ... que tal:
--------------------

Onde eu consigo o wimlib?
======================

Está disponível em vários repositórios para muitas distros, para distros RPM, embora tenha um arquivo de especificações perfeitamente utilizável para criá-lo:

```bash
curl -LO https://wimlib.net/downloads/wimlib-${version}.tar.gz
tar xf wimlib-${version}.tar.gz
cp wimlib-${version}/rpm/wim*.spec ~/rpmbuild/SPECS
cp wimlib-${version}.tar.gz ~/rpmbuild/SOURCES
rpmbuild -ba ~/rpmbuild/SPECS/wim*.spec
```

Por que não usar wimboot em vez disso?
======================================

Isso realmente funcionou muito bem ... até que não funcionou. Os discos resultantes funcionaram perfeitamente no vSphere, KVM e Xen, mas falharam de maneiras estranhas e maravilhosas quando carregados para o EC2. Parece que carregar um sistema de arquivos suficientemente grande na RAM via Grub não funciona no EC2 - que cria falhas de E / S ao acessar conjuntos lado a lado.

[](/linux/windows/2019/01/12/windows-pe-isos-on-linux.html)

James Reynolds
--------------

* James Reynolds
* [jum.reynolds \ _at \ _ gmail.com] (mailto: jum.reynolds% 20_at_% 20gmail.com)

* [JamesReynolds] (https://github.com/JamesReynolds)
* [james-reynolds-2b1a1b8a] (https://www.linkedin.com/in/james-reynolds-2b1a1b8a)

O blog pessoal de James Reynolds e suas aventuras no software.
