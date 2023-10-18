# Apagar a pasta `Windows.old`



```cmd
takeown /F "C:\Windows.old" /A /R /D Y
```


```cmd
icacls "C:\Windows.old" /grant *S-1-5-32-544:F /T /C /Q
```

```cmd
RD /S /Q "C:\Windows.old"
```

