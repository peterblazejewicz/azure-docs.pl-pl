---
title: Brama usług pulpitu zdalnego i serwer Azure Multi-Factor Authentication korzystające z usługi RADIUS
description: Jest to strona poświęcona usłudze Azure Multi-Factor Authentication i zawiera informacje pomocne we wdrażaniu bramy usług pulpitu zdalnego (Remote Desktop, RD) i serwera Azure Multi-Factor Authentication przy użyciu usługi RADIUS.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: kgremban

---
# Brama usług pulpitu zdalnego i serwer Azure Multi-Factor Authentication korzystające z usługi RADIUS
W wielu przypadkach brama usług pulpitu zdalnego używa lokalnego serwera NPS do uwierzytelniania użytkowników. Ten dokument zawiera informacje dotyczące określenia trasy żądania usługi RADIUS z bramy usług pulpitu zdalnego (za pośrednictwem lokalnego serwera NPS) do serwera Multi-Factor Authentication.

Serwer Multi-Factor Authentication należy zainstalować na osobnym serwerze, który następnie przekaże żądanie usługi RADIUS do serwera NPS na serwerze bramy usług pulpitu zdalnego. Po sprawdzeniu poprawności nazwy użytkownika i hasła serwer NPS zwróci odpowiedź do serwera Multi-Factor Authentication, który realizuje drugi składnik uwierzytelniania przed zwróceniem wyników do bramy.

## Konfigurowanie bramy usług pulpitu zdalnego
Brama usług pulpitu zdalnego musi być skonfigurowana do wysyłania uwierzytelniania usługi RADIUS do serwera Azure Multi-Factor Authentication. Po zainstalowaniu, skonfigurowaniu i rozpoczęciu działania bramy usług pulpitu zdalnego przejdź do właściwości bramy usług pulpitu zdalnego. Przejdź do karty Magazyn zasad autoryzacji połączeń usług pulpitu zdalnego i ustaw wartość na Centralny serwer, na którym działa serwer NPS zamiast Lokalny serwer, na którym działa serwer NPS. Dodaj jeden lub więcej serwerów Azure Multi-Factor Authentication jako serwery usługi RADIUS i określ wspólny klucz tajny dla każdego serwera.

## Konfigurowanie serwera NPS
Brama usług pulpitu zdalnego używa serwera NPS do wysyłania żądań usługi RADIUS do usługi Azure Multi-Factor Authentication. Aby zapobiec przekroczeniu limitu czasu bramy usług pulpitu zdalnego przed ukończeniem uwierzytelniania wieloskładnikowego, należy zmienić wartość limitu czasu. Poniższa procedura służy do konfigurowania serwera NPS.

1. Na serwerze NPS rozwiń menu klientów i serwera usługi RADIUS w lewej kolumnie i kliknij pozycję Grupy zdalnych serwerów usługi RADIUS. Przejdź do właściwości pozycji GRUPA SERWERÓW BRAMY USŁUG TERMINALOWYCH. Edytuj wyświetlane serwery usługi RADIUS i przejdź do karty Równoważenie obciążenia. Zmień wartość opcji „Liczba sekund bez odpowiedzi zanim żądanie zostanie uznane za porzucone” i „Liczba sekund między żądaniami, gdy serwer jest Zidentyfikowany jako niedostępny” na od 30 do 60 sekund. Kliknij kartę Uwierzytelnianie/konto i upewnij się, że określone porty usługi RADIUS są zgodne z portami, na których będzie nasłuchiwać serwer Multi-Factor Authentication.
2. Serwer NPS należy również skonfigurować do odbierania uwierzytelnień usługi RADIUS z serwera Azure Multi-Factor Authentication. Kliknij pozycję Klienci usługi RADIUS w lewym menu. Dodaj serwer Azure Multi-Factor Authentication jako klienta usługi RADIUS. Wybierz przyjazną nazwę i określ wspólny klucz tajny.
3. Rozwiń sekcję Zasady w lewym pasku nawigacyjnym, a następnie kliknij opcję Zasady żądań połączeń. Powinna ona zawierać zasady żądań połączeń o nazwie ZASADY BRAMY USŁUG TERMINALOWYCH, które zostały utworzone podczas konfigurowania bramy usług pulpitu zdalnego. TE zasady powodują przesyłanie żądań usługi RADIUS do serwera Multi-Factor Authentication.
4. Skopiuj te zasady, aby utworzyć nowe. W nowych zasadach dodaj warunek, który dopasowuje przyjazną nazwę klienta do przyjaznej nazwy określonej w kroku 2 powyżej dla klienta usługi RADIUS serwera Azure Multi-Factor Authentication. Zmień dostawcę uwierzytelniania na Komputer lokalny. Te zasady zapewniają, że po odebraniu żądania usługi RADIUS z serwera Azure Multi-Factor Authentication uwierzytelnianie odbywa się lokalnie, zamiast wysyłania żądań usługi RADIUS ponownie do serwera Azure Multi-Factor Authentication, co mogłoby spowodować zapętlenie. Aby zapobiec zapętleniu, nowe zasady muszą być umieszczone POWYŻEJ oryginalnych zasad, które powodują przekazanie do serwera Multi-Factor Authentication.

## Konfigurowanie usługi Azure Multi-Factor Authentication
- - -
Serwer Azure Multi-Factor Authentication jest konfigurowany jako serwer proxy usługi RADIUS pomiędzy bramą usług pulpitu zdalnego a serwerem NPS.  Powinien zostać zainstalowany na serwerze przyłączonym do domeny, który jest oddzielony od serwera bramy usług pulpitu zdalnego. Poniższa procedura umożliwia skonfigurowanie serwera Azure Multi-Factor Authentication.

1. Otwórz serwer Azure Multi-Factor Authentication i kliknij ikonę uwierzytelniania usługi RADIUS. Zaznacz pole wyboru Włącz uwierzytelnianie usługi RADIUS.
2. Na karcie Klienci upewnij się, że porty są zgodne z portami skonfigurowanymi na serwerze NPS, a następnie kliknij przycisk Dodaj... Dodaj... Dodaj adres IP serwera bramy usług pulpitu zdalnego, nazwę aplikacji (opcjonalnie) i wspólny klucz tajny. Wspólny klucz tajny musi być taki sam dla serwera Azure Multi-Factor Authentication Server i bramy usług pulpitu zdalnego.
3. Kliknij kartę Cel, a następnie wybierz przycisk radiowy Serwery RADIUS.
4. Kliknij przycisk Dodaj... Wprowadź adres IP, wspólny klucz tajny i porty serwera NPS. Jeśli nie jest używany centralny serwer NPS, klient usługi RADIUS i cel usługi RADIUS będą takie same. Wspólny klucz tajny musi być zgodny z kluczem skonfigurowanym w sekcji klienta usługi RADIUS serwera NPS.

![Uwierzytelnianie usługi Radius](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

<!--HONumber=Sep16_HO3-->


