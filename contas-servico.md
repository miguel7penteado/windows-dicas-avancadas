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

Vale lembrar que no registro do windows, os serviços ficam armazenados em `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services`


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
 Quando a conta **Network Service** foi introduzida nos sistemas operacionais Windows 2003 como uma alternativa para a conta **LocalSystem** que por sua vez oferecia riscos de segurança por ter todos os privilégios da máquina, resolveu-se o problema de
segurança por excesso de privilégios. Contudo, ficava difícil fazer auditorias de contas, uma vez que, se os serviços rodam 
dentro de uma conta com o mesmo nome, tudo que os processos dessa conta genérica fazem fica logados dentro desta conta.
Não é muito conveniente se vocẽ quiser filtrar ações de um processo específico.
 Portanto foram criadas as  **Contas Virtuais de Serviço**. Estas contas tem o status de contas **Network Service**, mas sempre levam o nome do serviço e não requerem senha. Não precisam ser criadas ou apagadas. Para utiliza-las, o serviço deve se logar na máquina local como ** NT SERVICE\NomeDoServiço**.
 
* - Nome:  NT SERVICE\NomeDoServiço
* - Esta conta não tem senha (qualquer senha definida será ignorada)
* - HKCU representa a conta de usuário **NetworkService**
* - Tem privilégios mínimos no computador local
* - Apresenta as credenciais do computador (e.g. MANGO$) a servidores remotos.

## 3. Contas de Serviço Gerenciadas
Applies To: Windows 7, Windows Server 2008 R2

Managed service accounts and virtual accounts are two new types of accounts introduced in Windows Server® 2008 R2 and Windows® 7 to enhance the service isolation and manageability of network applications such as Microsoft Exchange and Internet Information Services (IIS).

This step-by-step guide provides detailed information about how to set up and administer managed service accounts and virtual accounts on client computers running Windows Server 2008 R2 and Windows 7. This document includes:

    Managed service account and virtual account concepts.

    Client and domain controller support requirements for managed service accounts and virtual accounts.

    Tools needed to configure and administer managed service accounts and virtual accounts.

    Steps for configuring and administering managed service accounts and virtual accounts.

    Using virtual accounts.

    Troubleshooting problems with managed service accounts and virtual accounts.

    Application programming interfaces (APIs) for managed service accounts.

Managed service account and virtual account concepts

One of the security challenges for crucial network applications such as Exchange and IIS is selecting the appropriate type of account for the application to use.

On a local computer, an administrator can configure the application to run as Local Service, Network Service, or Local System. These service accounts are simple to configure and use but are typically shared among multiple applications and services and cannot be managed on a domain level.

If you configure the application to use a domain account, you can isolate the privileges for the application, but you need to manually manage passwords or create a custom solution for managing these passwords. Many server applications use this strategy to enhance security, but this strategy requires additional administration and complexity.

In these deployments, service administrators spend a considerable amount of time on maintenance tasks such as managing service passwords and service principal names (SPNs), which are required for Kerberos authentication. In addition, these maintenance tasks can disrupt service.

Two new types of accounts available in Windows Server 2008 R2 and Windows 7—the managed service account and the virtual account—are designed to provide crucial applications such as Exchange or IIS with the isolation of their own accounts, while eliminating the need for an administrator to manually administer the SPN and credentials for these accounts.

Managed service accounts in Windows Server 2008 R2 and Windows 7 are managed domain accounts that provide the following features to simplify service administration:

    Automatic password management.

    Simplified SPN management, including delegation of management to other administrators. Additional automatic SPN management is available at the Windows Server 2008 R2 domain functional level. For more information, see "Requirements for using managed service accounts and virtual accounts" in this document.

Virtual accounts in Windows Server 2008 R2 and Windows 7 are "managed local accounts" that provide the following features to simplify service administration:

    No password management is required.

    The ability to access the network with a computer identity in a domain environment.

Requirements for using managed service accounts and virtual accounts

To use managed service accounts and virtual accounts, the client computer on which the application or service is installed must be running Windows Server 2008 R2 or Windows 7. In addition, the hot fix as described in KB 2494158: “Managed service account authentication fails after its password is changed in Windows 7 or in Windows Server 2008 R2 must be applied to the computer where the managed service account exists.

In Windows Server 2008 R2 and Windows 7, one managed service account can be used for services on a single computer. Managed service accounts cannot be shared between multiple computers and cannot be used in server clusters where a service is replicated on multiple cluster nodes.

Domains at the Windows Server 2008 R2 functional level provide native support for both automatic password management and SPN management. If the domain is running at the Windows Server 2003 functional level or the Windows Server 2008 functional level, additional configuration steps will be needed to support managed service accounts. This means that:

    If the domain is at the Windows Server 2008 R2 functional level, the SPN management of managed service accounts is simplified. Specifically, the DNS part of the managed service account SPN is changed from oldname.domain-dns-suffix.com to newname.domain-dns-suffix.com for all managed service accounts installed on the computer in the following four situations:

        The samaccountname property of the computer is changed.

        The DNS name property of the computer is changed.

        A samaccountname property is added for the computer.

        A dns-host-name property is added for the computer.

    If the domain controller is on a computer running Windows Server 2008 or Windows Server 2003 but the Active Directory schema has been updated to Windows Server 2008 R2 in order to support this feature, managed service accounts can be used and service account passwords will be managed automatically. However, the domain administrator using these server operating systems will still need to manually configure SPN data for managed service accounts.

To use managed service accounts in Windows Server 2008, Windows Server 2003, or mixed-mode domain environments, you must complete the following tasks:

    Run adprep /forestprep at the forest level.
    Run adprep /domainprep in every domain where you want to create and use managed service accounts.

    Deploy a domain controller running one of the following operating systems in the domain:

    Windows Server 2008 R2

    Windows Server 2008 with the Active Directory Management Gateway Service

    Windows Server 2003 with the Active Directory Management Gateway Service

For more information about managing SPNs, see [Service Principal Names](https://technet.microsoft.com/library/Cc961723).

The tools in the following table are needed to configure and administer managed service accounts.
 
Tool 	Where available

Windows PowerShell command-line interface
	

Windows Server 2008 R2 and Windows 7

Managed service account cmdlets
	

Windows Server 2008 R2 and Windows 7

Dsacls.exe
	

Windows Server 2008 R2 and Windows 7

Installutil.exe
	

Windows Server 2008 R2 and Windows 7

Sc.exe command-line tool and the Service Control Manager UI
	

Windows Server 2008 R2 and Windows 7

Services snap-in console
	

Windows Server 2008 R2 and Windows 7

SetSPN.exe
	

Download from http://go.microsoft.com/fwlink/?LinkID=44321

NTRights.exe
	

Download from http://go.microsoft.com/fwlink/?LinkId=130308

# Referencias
[StackOverflow - https://stackoverflow.com/questions/510170/the-difference-between-the-local-system-account-and-the-network-service-acco](https://stackoverflow.com/questions/510170/the-difference-between-the-local-system-account-and-the-network-service-acco)
[msdn - https://msdn.microsoft.com/en-us/library/ms686005(v=VS.85).aspx](https://msdn.microsoft.com/en-us/library/ms686005(v=VS.85).aspx)
[Contas de Serviço Gerenciadas - https://technet.microsoft.com/en-us/library/810e6d22-fb4c-4d0b-9649-1892d1942e03](https://technet.microsoft.com/en-us/library/810e6d22-fb4c-4d0b-9649-1892d1942e03)
