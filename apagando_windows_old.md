# Apagar a pasta `Windows.old`


### Linha de Comando

Abra o prompt do MS-DOS (aperte a tecla `Windows` + tecla `R` simultaneamente e digite CMD.exe na caixa que vai aparecer).
Vai aparecer a janela de comandos (prompt de comandos estilo MS-DOS).

```cmd
takeown /F "C:\Windows.old" /A /R /D Y
```
![](figuras/figura1.jpg)


```cmd
icacls "C:\Windows.old" /grant *S-1-5-32-544:F /T /C /Q
```
![](figuras/figura2.jpg)

```cmd
RD /S /Q "C:\Windows.old"
```
![](figuras/figura3.jpg)

