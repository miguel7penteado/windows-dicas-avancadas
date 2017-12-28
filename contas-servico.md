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

If a service needs to recognize another service before sharing its information, the second service can either use the same account as the first service, or it can run in an account belonging to an alias that is recognized by the first service. Services that need to run in a distributed manner across the network should run in domain-wide accounts.

You can specify one of the following special accounts instead of specifying a user account for the service:



    LocalService account (preferred)

    A limited service account that is very similar to Network Service and meant to run standard least-privileged services. However, unlike Network Service it has no ability to access the network as the machine accesses the network as an Anonymous user.
        Name: NT AUTHORITY\LocalService
        the account has no password (any password information you provide is ignored)
        HKCU represents the LocalService user account
        has minimal privileges on the local computer
        presents anonymous credentials on the network
        SID: S-1-5-19
        has its own profile under the HKEY_USERS registry key (HKEY_USERS\S-1-5-19)

     

    NetworkService account

    Limited service account that is meant to run standard privileged services. This account is far more limited than Local System (or even Administrator) but still has the right to access the network as the machine (see caveat above).
        NT AUTHORITY\NetworkService
        the account has no password (any password information you provide is ignored)
        HKCU represents the NetworkService user account
        has minimal privileges on the local computer
        presents the computer's credentials (e.g. MANGO$) to remote servers
        SID: S-1-5-20
        has its own profile under the HKEY_USERS registry key (HKEY_USERS\S-1-5-20)
        If trying to schedule a task using it, enter NETWORK SERVICE into the Select User or Group dialog

     

    LocalSystem account (dangerous, don't use!)

    Completely trusted account, more so than the administrator account. There is nothing on a single box that this account cannot do, and it has the right to access the network as the machine (this requires Active Directory and granting the machine account permissions to something)
        Name: .\LocalSystem (can also use LocalSystem or ComputerName\LocalSystem)
        the account has no password (any password information you provide is ignored)
        SID: S-1-5-18
        does not have any profile of its own (HKCU represents the default user)
        has extensive privileges on the local computer
        presents the computer's credentials (e.g. MANGO$) to remote servers
