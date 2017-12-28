# Serviços no Windows - Contas de Usuário tipo Serviço


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



### 1. Conta LocalService (ou Serviço Local):

 Uma conta de serviço limitada que é muito similar a conta **Network Service** e é designada para rodar serviços padrões 
menos privilegiados. Toda caso, diferentemente da conta **Network Service**, a conta **LocalService**  não tem a 
habilidade de acessar a rede por exemplo, como faria uma máquina utilizando o usuário **Anonymous**.
*. Nome: NT AUTHORITY\LocalService
*. Esta conta não tem senha (qualquer senha definida será ignorada)
*. HKCU representa a conta de usuário LocalService
*. Tem privilégios mínimos, limitados ao computador local
*. Apresenta a autenticação **anonymous** se precisar acessar a rede
*. SID: S-1-5-19
*. Tem seu próprio perfil dentro de da chave de registro HKEY_USERS (HKEY_USERS\S-1-5-19)

     

### 2. NetworkService account

    Limited service account that is meant to run standard privileged services. This account is far more limited than Local System (or even Administrator) but still has the right to access the network as the machine (see caveat above).
        NT AUTHORITY\NetworkService
        the account has no password (any password information you provide is ignored)
        HKCU represents the NetworkService user account
        has minimal privileges on the local computer
        presents the computer's credentials (e.g. MANGO$) to remote servers
        SID: S-1-5-20
        has its own profile under the HKEY_USERS registry key (HKEY_USERS\S-1-5-20)
        If trying to schedule a task using it, enter NETWORK SERVICE into the Select User or Group dialog

     

### 3. LocalSystem account (dangerous, don't use!)

    Completely trusted account, more so than the administrator account. There is nothing on a single box that this account cannot do, and it has the right to access the network as the machine (this requires Active Directory and granting the machine account permissions to something)
        Name: .\LocalSystem (can also use LocalSystem or ComputerName\LocalSystem)
        the account has no password (any password information you provide is ignored)
        SID: S-1-5-18
        does not have any profile of its own (HKCU represents the default user)
        has extensive privileges on the local computer
        presents the computer's credentials (e.g. MANGO$) to remote servers
