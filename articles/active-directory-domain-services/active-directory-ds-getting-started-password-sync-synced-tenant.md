---
title: 'Usługi domenowe Azure AD: włączanie synchronizacji haseł | Microsoft Docs'
description: Wprowadzenie do usługi Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand

ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/20/2016
ms.author: maheshu

---
# Włączanie synchronizacji haseł w usłudze Azure AD Domain Services
W poprzednich zadaniach włączono usługę Azure AD Domain Services dla dzierżawy usługi Azure AD. Kolejnym zadaniem jest włączenie synchronizacji haseł w usłudze Azure AD Domain Services. Po skonfigurowaniu synchronizacji poświadczeń użytkownik może zalogować się do domeny zarządzanej przy użyciu poświadczeń firmowych.

Kroki do wykonania różnią się w zależności od tego, czy organizacja ma dzierżawę usługi Azure AD tylko w chmurze, czy też wykonuje synchronizację z katalogiem lokalnym za pomocą programu Azure AD Connect.

<br>

> [!div class="op_single_selector"]
> * [Dzierżawa usługi Azure AD tylko w chmurze](active-directory-ds-getting-started-password-sync.md)
> * [Zsynchronizowana dzierżawa usługi Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)
> 
> 

<br>

## Zadanie 5. Włączanie synchronizacji haseł w Usługach domenowych AAD dla zsynchronizowanej dzierżawy Azure AD
Zsynchronizowana dzierżawa usługi Azure AD jest ustawiona do synchronizacji z katalogiem lokalnym organizacji za pomocą programu Azure AD Connect. Program Azure AD Connect nie synchronizuje domyślnie skrótów poświadczeń protokołów NTLM i Kerberos w ramach usługi Azure AD. Aby korzystać z usługi Azure AD Domain Services, należy skonfigurować program Azure AD Connect w celu zsynchronizowania skrótów poświadczeń wymaganych do uwierzytelniania NTLM i Kerberos. Następujące kroki umożliwiają synchronizację wymaganych skrótów poświadczeń z dzierżawą usługi Azure AD.

### Instalowanie lub aktualizowanie programu Azure AD Connect
Na komputerze przyłączonym do domeny zainstaluj najnowszą zalecaną wersję programu Azure AD Connect. Jeśli masz istniejące wystąpienie instalatora programu Azure AD Connect, musisz zaktualizować go tak, aby można było korzystać z najnowszej wersji programu Azure AD Connect. Upewnij się, że używasz najnowszej wersji programu Azure AD Connect, aby uniknąć znanych problemów i błędów, które mogły już zostać naprawione.

**[Pobieranie programu Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Zalecana wersja: **1.1.281.0** — opublikowana 7 września 2016 r.

> [!WARNING]
> Aby umożliwić synchronizowanie starych poświadczeń haseł (wymaganych podczas uwierzytelniania NTLM i Kerberos) z dzierżawą usługi Azure AD, MUSISZ zainstalować najnowszą zalecaną wersję programu Azure AD Connect. Ta funkcja nie jest dostępna w poprzednich wersjach programu Azure AD Connect ani starszej wersji narzędzia DirSync.
> 
> 

Instrukcje dotyczące instalowania programu Azure AD Connect są dostępne w artykule [Wprowadzenie do programu Azure AD Connect](../active-directory/active-directory-aadconnect.md)

### Włączanie synchronizacji skrótów poświadczeń NTLM i Kerberos w usłudze Azure AD
Aby wymusić pełną synchronizację haseł i umożliwić synchronizowanie skrótów poświadczeń wszystkich użytkowników lokalnych z dzierżawą usługi Azure AD, uruchom następujący skrypt programu PowerShell w każdym lesie usługi AD. Ten skrypt umożliwia synchronizację skrótów poświadczeń wymaganych do uwierzytelniania NTLM i Kerberos z dzierżawą usługi Azure AD.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

Czas synchronizacji skrótów poświadczeń z usługą Azure AD zależy od rozmiaru katalogu (liczby użytkowników, grup itp.). Z haseł będzie można korzystać w domenie zarządzanej Usług domenowych Azure AD wkrótce po zsynchronizowaniu skrótów poświadczeń z usługą Azure AD.

<br>

## Powiązana zawartość
* [Włączanie synchronizacji haseł w Usługach domenowych AAD dla katalogu usługi Azure AD tylko w chmurze](active-directory-ds-getting-started-password-sync.md)
* [Administrowanie domeną zarządzaną Usług domenowych Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Dołączanie maszyny wirtualnej z systemem Windows do domeny zarządzanej Usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Dołączanie maszyny wirtualnej z systemem Red Hat Enterprise Linux do domeny zarządzanej Usług domenowych Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!--HONumber=Sep16_HO3-->


