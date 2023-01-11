# Desativar ou ativar Windows Update

```cmd
sc qdescription bits
```
Resposta:
```
NOME_DO_SERVIÇO: bits
DESCRIÇÃO:  Transfere arquivos em segundo plano usando largura de banda de rede ociosa. Se o serviço estiver desabilitado, qualquer aplicativo que dependa do BITS, como o Windows Update ou o MSN Explorer, não poderá baixar programas e outras informações automaticamente.
```

```cmd
sc qdescription dosvc
```
Resposta:
```
NOME_DO_SERVIÇO: dosvc
DESCRIÇÃO:  Executa tarefas de otimização de entrega de conteúdo.
```

```cmd
sc qdescription wuauserv
```
Resposta:
```
NOME_DO_SERVIÇO: wuauserv
DESCRIÇÃO:  Ativa a detecção, download e instalação de atualizações do Windows e de outros programas. Se o serviço for desativado, os usuários do computador não poderão usar o Windows Update ou o recurso de atualização automática, e os programas não poderão usar a API do Windows Update Agent (WUA).
```



desativando temporariamente os serviços a seguir:
```cmd
net stop wuauserv
net stop bits
net stop dosvc
```
