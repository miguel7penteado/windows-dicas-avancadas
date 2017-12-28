# Serviços no Windows - 

## 1. Contas de Usuário tipo Serviço


Cada serviço executa limitado as permissões de uma conta de usuário. Quando se trata de uma conta de serviço, o usuário e a senha
desta conta são especificados pela função **CreateService** no momento em que o serviço é instalado. 
Claro que o nome de usuário e senha podem ser mudados pela função **ChangeServiceConfig**.
Você pode usar a função **QueryServiceConfig** para obter o nome de usuário 
(mas não a senha) associada a um serviço. O service control manager (SCM) carrega automaticamente o **perfil do usuário**.

Quando você inicia um serviço, o SCM loga na conta associada ao serviço. Se a operação de logar é bem sucedida, 
o sistema produz um token de acesso e o amarra ao processo do serviço em execução. 
Este token identifica o processo do serviço em todas as sequentes interações 
com outros objetos segurados (objetos que tem um descritor de segurança, leia-se premissão, associada a ele).
Por exemplo, se o serviço tentar abrir um PIPE, o sistema verifica se o processo tem permissão para acessar aquele recurso comparando 
os descritores de segurança do processo/serviço e do recurso a ser acessado.

O SCM não mantém senhas das contas de usuário-serviço. 
Se uma senha expira, o logon falha e o serviço não inicia. 
Um administrador de sistema que quer associar contas "na mão" para um serviço pode definir uma senha que nunca expira.

Se um serviço precisa reconhecer outro serviço antes de compartilhar informação, o segundo serviço pode utilizar a mesma conta do primeiro serviço, ou ele pode rodar em uma conta reconhecida pelo primeiro serviço. Serviços que rodam distribuidos na rede devem rodar em contas de domínio.

Você pode escolher uma das contas especiais para serviço, ou especificar uma conta de usuário para o serviço:



### 1.1 Conta LocalService (ou Serviço Local):

 Uma conta de serviço limitada que é muito similar a conta **Network Service** e é designada para rodar serviços padrões 
menos privilegiados. Toda caso, diferentemente da conta **Network Service**, a conta **LocalService**  não tem a 
habilidade de acessar recursos de credenciais de rede por exemplo. Se o fizer, não poderá identificar a máquina e terá que se logar como  **Anonymous**.

* - Nome: NT AUTHORITY\LocalService
* - Esta conta não tem senha (qualquer senha definida será ignorada)
* - HKCU representa a conta de usuário LocalService
* - Tem privilégios mínimos, limitados ao computador local
* - Apresenta a autenticação **anonymous** se precisar acessar a rede
* - SID: S-1-5-19
* - Tem seu próprio perfil dentro de da chave de registro HKEY_USERS (HKEY_USERS\S-1-5-19)

     

### 1.2 Conta NetworkService (Ou Serviços de rede)

 É Uma conta de serviço de privilégio limitado que permite ao processo acessar os recursos de rede do sistema operacional. Esta conta ainda é muito limitada se comparada a conta **Local System** (ou mesmo a mais limitada que a conta Administrator) mas ao menos tem direito de acessar a rede se identificando com as credenciais da máquina.
 
* - NT AUTHORITY\NetworkService
* - Esta conta não tem senha (qualquer senha definida será ignorada)
* - HKCU representa a conta de usuário **NetworkService**
* - Tem privilégios mínimos no computador local
* - Apresenta as credenciais do computador (e.g. MANGO$) a servidores remotos.
* - SID: S-1-5-20
* - Tem seu proprio perfil dentro da chave de registro HKEY_USERS (HKEY_USERS\S-1-5-20)
* - Se está tentando agendar uma tarefa usando essa conta, entre com NETWORK SERVICE dentro do dialogo selecionar usuário ou grupo.

     

### 1.3 Conta LocalSystem (ou Sistema Local)

 Uma conta completamente confiavel, mais do que a conta de **administrator**. Não há nada no computador local que esta conta não possa fazer, e ela pode acessar a rede se identificando como a máquina (Isto requer um AD e a permissão da máquina de fazer isso)
 
* - Name: .\LocalSystem (pode usar **LocalSystem** ou **NomeComputador\LocalSystem**)
* - Esta conta não tem senha (qualquer senha definida será ignorada)
* - SID: S-1-5-18
* - Não tem perfil (HKCU representa o usuário default user)
* - tem privilégios totais
* - Apresenta as credenciais do computador (e.g. MANGO$) a servidores remotos.

## 2. Contas virtuais de Usuário tipo Serviço


# Referencias
[StackOverflow - https://stackoverflow.com/questions/510170/the-difference-between-the-local-system-account-and-the-network-service-acco](https://stackoverflow.com/questions/510170/the-difference-between-the-local-system-account-and-the-network-service-acco)
[msdn - https://msdn.microsoft.com/en-us/library/ms686005(v=VS.85).aspx](https://msdn.microsoft.com/en-us/library/ms686005(v=VS.85).aspx)
