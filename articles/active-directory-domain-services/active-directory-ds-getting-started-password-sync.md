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
W poprzednich zadaniach włączono usługę Azure AD Domain Services dla dzierżawy usługi Azure AD. Kolejnym krokiem jest włączenie skrótów poświadczeń wymaganych do uwierzytelniania NTLM i Kerberos w celu synchronizacji z usługą Azure AD Domain Services. Po skonfigurowaniu synchronizacji poświadczeń użytkownik może zalogować się do domeny zarządzanej przy użyciu poświadczeń firmowych.

Kroki do wykonania różnią się w zależności od tego, czy organizacja ma dzierżawę usługi Azure AD tylko w chmurze, czy też wykonuje synchronizację z katalogiem lokalnym za pomocą programu Azure AD Connect.

<br>

> [!div class="op_single_selector"]
> * [Dzierżawa usługi Azure AD tylko w chmurze](active-directory-ds-getting-started-password-sync.md)
> * [Zsynchronizowana dzierżawa usługi Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)
> 
> 

<br>

## Zadanie 5. Włączanie synchronizacji haseł w usłudze AAD Domain Services dla dzierżawy usługi Azure AD tylko w chmurze
Usługa Azure AD Domain Services wymaga skrótów poświadczeń w formacie odpowiednim do uwierzytelniania NTLM i Kerberos, aby uwierzytelnić użytkowników w domenie zarządzanej. Jeśli nie zostanie włączona usługa AAD Domain Services dla dzierżawy, usługa Azure AD nie będzie generować ani przechowywać skrótów poświadczeń w formacie wymaganym podczas uwierzytelniania NTLM i Kerberos. Ze względów bezpieczeństwa usługa Azure AD nie przechowuje również żadnych poświadczeń w postaci zwykłego tekstu. W związku z tym usługa Azure AD nie ma możliwości generowania skrótów poświadczeń protokołu NTLM ani Kerberos w oparciu o istniejące poświadczenia użytkownika.

> [!NOTE]
> Jeśli organizacja ma dzierżawę usługi Azure AD tylko w chmurze, użytkownicy, którzy muszą korzystać z usługi Azure AD Domain Services, muszą zmienić swoje hasła.
> 
> 

Ten proces zmiany haseł powoduje generowanie w usłudze Azure AD skrótów poświadczeń wymaganych przez Usługi domenowe Azure AD na potrzeby uwierzytelniania Kerberos i NTLM. Możesz unieważnić hasła wszystkich użytkowników w dzierżawie, którzy muszą korzystać z Usług domenowych Azure AD, lub poinformować ich o konieczności zmiany haseł.

### Włączanie generowania skrótów poświadczeń NTLM i Kerberos w dzierżawie usługi Azure AD tylko w chmurze
Poniżej znajdują się instrukcje zmieniania haseł dla użytkowników końcowych:

1. Przejdź do strony panelu dostępu do usługi Azure AD dla organizacji [http://myapps.microsoft.com](http://myapps.microsoft.com).
2. Wybierz kartę **profilu** na tej stronie.
3. Kliknij kafelek **Zmień hasło** na tej stronie.
   
    ![Utwórz sieć wirtualną dla Usług domenowych Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)
   
   > [!NOTE]
   > Jeśli nie widzisz opcji **Zmień hasło** na stronie panelu dostępu, upewnij się, że Twoja organizacja ma skonfigurowane [zarządzanie hasłami w usłudze Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   > 
   > 
4. Na stronie **zmienianie hasła** wpisz istniejące hasło (stare), a następnie wpisz nowe hasło i je zatwierdź. Kliknij przycisk **prześlij**.
   
    ![Utwórz sieć wirtualną dla Usług domenowych Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Nowego hasła w usłudze Azure AD Domain Services można używać wkrótce po jego zmienieniu. Po kilku minutach (zazwyczaj około 20 minutach) użytkownicy będą mogli logować się na komputerach dołączonych do domeny zarządzanej przy użyciu nowo zmienionego hasła.

<br>

## Powiązana zawartość
* [Aktualizowanie hasła](../active-directory/active-directory-passwords-update-your-own-password.md)
* [Wprowadzenie do zarządzania hasłami w usłudze Azure AD](../active-directory/active-directory-passwords-getting-started.md).
* [Włączanie synchronizacji haseł w usłudze AAD Domain Services dla zsynchronizowanej dzierżawy usługi Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Administrowanie domeną zarządzaną Usług domenowych Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Dołączanie maszyny wirtualnej z systemem Windows do domeny zarządzanej Usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Dołączanie maszyny wirtualnej z systemem Red Hat Enterprise Linux do domeny zarządzanej Usług domenowych Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!--HONumber=Sep16_HO3-->


