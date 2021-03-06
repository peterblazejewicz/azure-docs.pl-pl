---
title: Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web | Microsoft Docs
description: Konfigurowanie testów sieci Web w usłudze Application Insights. Otrzymywanie alertów, kiedy witryna sieci Web staje się niedostępna lub wolno odpowiada.
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2016
ms.author: awills

---
# Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web
Po wdrożeniu aplikacji sieci Web na dowolnym hoście można skonfigurować testy sieci Web, aby monitorować jej dostępność i czas odpowiedzi. Usługa [Application Insights w programie Visual Studio](app-insights-overview.md) wysyła żądania sieci Web w regularnych odstępach czasu z punktów na całym świecie, a jeśli aplikacja będzie odpowiadać powoli lub wcale, usługa może poinformować Cię o tym za pomocą alertu.

![Przykład testu sieci Web](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

Testy sieci Web można skonfigurować dla dowolnego punktu końcowego protokołów HTTP lub HTTPS, który jest dostępny za pośrednictwem publicznej sieci Internet.

Istnieją dwa typy testów sieci Web:

* [Test ping adresu URL](#set-up-a-url-ping-test): prosty test, który można utworzyć w portalu Azure.
* [Wieloetapowy test sieci Web](#multi-step-web-tests): tworzony w programach Visual Studio Ultimate lub Visual Studio Enterprise i przekazywany do portalu.

Można utworzyć maksymalnie 10 testów sieci Web na każdy zasób aplikacji.

## Konfigurowanie testu ping adresu URL
### <a name="create"></a>1. Tworzysz nowy zasób?
Pomiń ten krok, jeśli masz już [skonfigurowany zasób usługi Application Insights][rozpoczynanie] dla tej aplikacji i chcesz zobaczyć dane dostępności w tym samym miejscu.

Zaloguj się do platformy [Microsoft Azure](http://azure.com), przejdź do witryny [Azure Portal](https://portal.azure.com) i utwórz zasób usługi Application Insights.

![Nowy > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Zostanie otwarty blok Przegląd dla nowego zasobu. Aby znaleźć go w dowolnym momencie w [portalu Azure](https://portal.azure.com), kliknij przycisk **Przeglądaj**.

### <a name="setup"></a>2. Tworzenie testu sieci Web
W zasobie usługi Application Insights poszukaj kafelka Dostępność. Kliknij go, aby otworzyć blok Testy sieci Web dla aplikacji, a następnie dodaj test sieci Web.

![Podaj przynajmniej adres URL swojej witryny sieci Web](./media/app-insights-monitor-web-app-availability/13-availability.png)

* **Adres URL** musi być widoczny z publicznej sieci Internet. Może zawierać ciąg zapytania &#151; umożliwi to np. szybkie sprawdzenie działania bazy danych. Jeśli adres URL jest rozpoznawany jako przekierowanie, zostanie prześledzonych maksymalnie 10 przekierowań.
* **Analizuj zależne żądania**: obrazy, skrypty, pliki stylów i inne zasoby strony są żądane w ramach testu. Test zakończy się niepowodzeniem, jeśli nie uda się pobrać tych zasobów w ramach limitu czasu dla całego testu.
* **Włącz ponawianie próby**: jeśli test nie powiedzie się, zostanie ponowiony po krótkim czasie. Błąd jest zgłaszany dopiero wtedy, gdy trzy kolejne próby się nie powiodą. Kolejne testy są następnie wykonywane ze zwykłą częstotliwością. Ponawianie prób jest tymczasowo wstrzymane do czasu następnego sukcesu. Ta reguła jest stosowana niezależnie w każdej lokalizacji testu. (To ustawienie jest zalecane. Średnio około 80% błędów znika po ponowieniu testu).
* **Częstotliwość testu**: określa, jak często wykonywane są testy w poszczególnych lokalizacjach testowych. Przy częstotliwości równej 5 minut i 5 lokalizacjach testu witryna będzie testowana średnio co minutę.
* **Lokalizacje testu** są to miejsca, z których nasze serwery wysyłają żądania sieci Web do Twojego adresu URL. Wybierz więcej niż jedną lokalizację, aby móc odróżnić problemy z witryną od problemów z siecią. Wybrać można maksymalnie 16 lokalizacji.
* **Kryteria powodzenia**:
  
    **Limit czasu testu**: zmniejsz tę wartość, aby otrzymywać alerty o powolnych odpowiedziach. Test jest uznawany za błąd, jeśli w tym okresie nie odebrano odpowiedzi z witryny. W przypadku wybrania opcji **Analizuj zależne żądania** wszystkie obrazy, pliki stylów, skrypty i inne zasoby zależne muszą zostać odebrane w tym okresie.
  
    **Odpowiedź HTTP**: zwrócony kod stanu, który będzie uznawany za sukces. Kod 200 oznacza, że została zwrócona normalna strona sieci Web.
  
    **Zgodność zawartości**: ciąg znaków, np. „Witaj!” Będziemy testować występowanie tego ciągu w każdej odpowiedzi. Musi to być zwykły ciąg znaków bez symboli wieloznacznych. Pamiętaj, że w razie zmiany zawartości strony może być konieczne zaktualizowanie tego ciągu.
* **Alerty** są domyślnie wysyłane, jeśli błędy występują w trzech lokalizacjach przez ponad pięć minut. Błąd w jednej lokalizacji prawdopodobnie wynika z problemu z siecią, a nie z witryną. Próg błędu można jednak zmienić na mniej lub bardziej wrażliwy. Zmienić można też adresata wiadomości e-mail z alertami.
  
    Skonfigurować można również [element webhook](../azure-portal/insights-webhooks-alerts.md), który jest wywoływany w momencie zgłoszenia alertu. Należy jednak pamiętać, że obecnie parametry zapytania nie są przekazywane jako właściwości.

#### Testowanie większej liczby adresów URL
Dodaj więcej testów. Na przykład oprócz testowania strony głównej możesz sprawdzić, czy działa baza danych, testując adres URL dla wyszukiwania.

### <a name="monitor"></a>3. Wyświetlanie raportów dotyczących dostępności
Po 1–2 minutach kliknij przycisk **Odśwież** w bloku Dostępność / Testy sieci Web (odświeżanie nie odbywa się automatycznie).

![Podsumowanie wyników w bloku głównym](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

Kliknij dowolny słupek na wykresie podsumowania, aby uzyskać bardziej szczegółowy widok tego okresu.

Wykresy zawierają wyniki wszystkich testów sieci Web dotyczących tej aplikacji.

#### Składniki strony sieci Web
Obrazy, arkusze stylów i skrypty, a także inne składniki statyczne strony sieci Web, którą testujesz, są żądane w ramach testu.  

Zarejestrowany czas odpowiedzi to czas potrzebny na załadowanie wszystkich tych składników.

Jeśli nie uda się załadować jakiegoś składnika, test jest oznaczany jako błąd.

## <a name="failures"></a>Jeśli widzisz błędy...
Kliknij czerwoną kropkę.

![Kliknij czerwoną kropkę](./media/app-insights-monitor-web-app-availability/14-availRedDot.png)

Lub przewiń w dół i kliknij test, który ma wynik niższy niż 100%.

![Kliknij konkretny test sieci Web](./media/app-insights-monitor-web-app-availability/15-webTestList.png)

Zostaną otwarte wyniki tego testu.

![Kliknij konkretny test sieci Web](./media/app-insights-monitor-web-app-availability/16-1test.png)

Test jest uruchamiany z kilku lokalizacji &#151; wybierz taką, której wyniki są poniżej 100%.

![Kliknij konkretny test sieci Web](./media/app-insights-monitor-web-app-availability/17-availViewDetails.png)

Przewiń w dół do sekcji **Testy zakończone niepomyślnie** i wybierz wynik.

Kliknij wynik, aby ocenić go w portalu i zobaczyć, dlaczego zakończył się błędem.

![Wynik działania testu sieci Web](./media/app-insights-monitor-web-app-availability/18-availDetails.png)

Alternatywnie możesz pobrać plik wynikowy i przejrzeć go w programie Visual Studio.

*Test wygląda dobrze, ale jest raportowany jako błąd?* Sprawdź wszystkie obrazy, skrypty, arkusze stylów i inne pliki ładowane przez stronę. Jeśli pobranie dowolnego z nich nie powiedzie się, test zostanie zgłoszony jako nieudany — nawet wtedy, gdy główna strona HTML ładuje się poprawnie.

## Wieloetapowe testy sieci Web
Możliwe jest monitorowanie scenariusza, który obejmuje sekwencję adresów URL. Jeśli na przykład monitorujesz witrynę sklepu, możesz sprawdzić, czy dodawanie towarów do koszyka działa prawidłowo.

Aby utworzyć test wieloetapowy, nagraj scenariusz przy użyciu programu Visual Studio, a następnie przekaż nagranie do usługi Application Insights. Usługa Application Insights odtwarza ten scenariusz w określonych odstępach czasu i weryfikuje odpowiedzi.

Podczas testów nie można jednak używać zakodowanych funkcji. Kroki scenariusza muszą być umieszczone w pliku .webtest jako skrypt.

#### 1. Nagrywanie scenariusza
Nagraj sesję sieci Web w programie Visual Studio Enterprise lub Ultimate. 

1. Utwórz projekt testu wydajności sieci Web.
   
    ![W programie Visual Studio utwórz projekt z szablonu Projekt testu sieci Web i obciążenia.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)
2. Otwórz plik .webtest i rozpocznij nagrywanie.
   
    ![Otwórz plik .webtest i kliknij przycisk Nagraj.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Wykonaj działania użytkownika, które chcesz symulować w teście: otwórz witrynę sieci Web, dodaj produkt do koszyka itd. Następnie zatrzymaj test.
   
    ![Nagrywanie testów sieci Web w programie Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)
   
    Scenariusz nie powinien być długi. Jest ograniczony do 100 kroków i 2 minut.
4. Zmodyfikuj test, aby:
   
   * Dodać operacje sprawdzania poprawności odebranego tekstu i kodów odpowiedzi,
   * Usunąć zbędne interakcje. Możesz również usunąć zależne żądania dotyczące obrazów, witryn z reklamami lub witryn służących do śledzenia.
     
     Pamiętaj, że możesz edytować tylko skrypt testu — nie możesz dodawać niestandardowego kodu ani wywołań do innych testów sieci Web. Nie wstawiaj pętli do testu. Możesz używać standardowych wtyczek testów sieci Web.
5. Uruchom test w programie Visual Studio, aby upewnić się, że działa.
   
    Moduł uruchamiający testy sieci web otwiera przeglądarkę sieci Web i powtarza nagrane akcje. Upewnij się, że działanie jest zgodne z oczekiwaniami.
   
    ![Otwórz plik .webtest w programie Visual Studio i kliknij przycisk Uruchom.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### 2. Przekaż test sieci Web do usługi Application Insights
1. W portalu Application Insights utwórz nowy test sieci Web.
   
    ![W bloku Testy sieci Web wybierz przycisk Dodaj.](./media/app-insights-monitor-web-app-availability/16-another-test.png)
2. Wybierz test wieloetapowy i przekaż plik .webtest.
   
    ![Wybieranie wieloetapowego testu sieci Web.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)
   
    Ustaw lokalizacje testu, częstotliwość i parametry alertu w taki sam sposób, jak w przypadku testów ping.

Wyświetl wyniki testu i ewentualne błędy w taki sam sposób, jak w przypadku testów pojedynczego adresu URL.

Typową przyczyną błędu jest to, że test trwa zbyt długo. Nie może trwać dłużej niż dwie minuty.

Pamiętaj, że wszystkie zasoby strony (skrypty, arkusze stylów, obrazy itd.) muszą załadować się poprawnie, aby test zakończył się powodzeniem.

Test sieci Web musi być całkowicie zawarty w pliku .webtest — w teście nie można użyć funkcji zakodowanych.

### Dodawanie wtyczek czasu i liczb losowych do testu wieloetapowego
Załóżmy, że testujesz narzędzie, które pobiera dane zależne od czasu (np. ceny akcji) z zewnętrznego źródła. Podczas rejestrowania testu sieci Web należy używać określonych godzin, ale ustawionych jako parametry testu: StartTime (Godzina rozpoczęcia) i EndTime (Godzina zakończenia).

![Test sieci Web z parametrami.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

Po uruchomieniu testu parametr EndTime powinien zawsze zawierać aktualny czas, a StartTime wartość czasu 15 minut wcześniej.

Wtyczki testu sieci Web umożliwiają parametryzowanie czasu.

1. Dodaj wtyczkę testu sieci Web do każdej wartości parametru zmiennej, która jest potrzebna. Na pasku narzędzi testu sieci Web wybierz polecenie **Dodaj wtyczkę testu sieci Web**.
   
    ![Wybierz polecenie Dodaj wtyczkę testu sieci Web i wskaż jej typ.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)
   
    W tym przykładzie użyte zostaną dwa wystąpienia wtyczki typu Data i godzina. Jedno wystąpienie odpowiada wartości „15 minut temu”, a drugie „teraz”.
2. Otwórz właściwości każdej wtyczki. Nadaj jej nazwę i skonfiguruj, aby używać czasu bieżącego. W jednej z wtyczek ustaw właściwość Dodaj minuty = -15.
   
    ![Ustawianie właściwości Nazwa, Użyj czasu bieżącego i Dodaj minuty.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. W parametrach testu sieci Web użyj {{nazwa wtyczki}}, aby odwoływać się do nazwy wtyczki.
   
    ![Używanie {{nazwa wtyczki}} w parametrze testu.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Teraz przekaż test do portalu. Wartości dynamiczne zostaną zastosowane w każdym przebiegu testu.

## Obsługa logowania
Jeśli użytkownicy logują się do aplikacji, dostępne są różne opcje symulowania logowania, które pozwolą przetestować strony dostępne po zalogowaniu. Zastosowane podejście zależy od typu zabezpieczeń zapewnianych przez aplikację.

We wszystkich przypadkach należy utworzyć konto w ramach aplikacji tylko na potrzeby testowania. Jeśli to możliwe, należy ograniczyć uprawnienia tego konta testowego, aby nie było możliwości, że testy sieci Web będą miały wpływ na rzeczywistych użytkowników.

### Prosta nazwa użytkownika i hasło
Rejestrowanie testu sieci Web w zwykły sposób. Najpierw usuń pliki cookie.

### Uwierzytelnianie SAML
Użyj wtyczki SAML, która jest dostępna do testów sieci Web.

### Klucz tajny klienta
Jeśli aplikacja ma trasę logowania, która obejmuje klucz tajny klienta, użyj tej trasy. Azure Active Directory (AAD) to przykład usługi, która umożliwia logowanie za pomocą klucza tajnego klienta. W usłudze AAD klucz tajny klienta jest kluczem aplikacji. 

Poniżej przedstawiono przykładowy test sieci Web aplikacji sieci Web platformy Azure przy użyciu klucza aplikacji:

![Przykład klucza tajnego klienta](./media/app-insights-monitor-web-app-availability/110.png)

1. Pobierz token z usługi AAD przy użyciu klucza tajnego klienta (AppKey).
2. Wyodrębnij token elementu nośnego z odpowiedzi.
3. Wywołaj interfejs API przy użyciu tokenu elementu nośnego w nagłówku autoryzacji.

Upewnij się, że test sieci Web jest rzeczywistym klientem, to znaczy ma własną aplikację w usłudze AAD, i użyj jego identyfikatora klienta i klucza aplikacji. Testowana usługa również ma własną aplikację w usłudze AAD: identyfikator URI identyfikatora tej aplikacji jest uwzględniany w teście sieci Web w polu „resource”. 

### Uwierzytelnianie otwarte
Przykładem uwierzytelniania otwartego jest logowanie przy użyciu konta Microsoft lub Google. Wiele aplikacji, które używają protokołu OAuth, zapewnia alternatywną obsługę klucza tajnego klienta, więc przede wszystkim należy zbadać tę możliwość. 

Jeśli w ramach testu należy zalogować się przy użyciu protokołu OAuth, ogólne podejście jest następujące:

* Użyj narzędzia takiego jak Fiddler, aby sprawdzić ruch między przeglądarką sieci Web, witryną uwierzytelniającą a aplikacją. 
* Wykonaj co najmniej dwa logowania na różnych komputerach lub w różnych przeglądarkach albo w długich odstępach czasu (umożliwi to wygaśnięcie tokenów).
* Porównując różne sesje, zidentyfikuj token przekazywany z powrotem z witryny uwierzytelniającej, który jest następnie przekazywany do serwera aplikacji po zalogowaniu. 
* Nagraj test sieci Web przy użyciu programu Visual Studio. 
* Sparametryzuj tokeny, ustawiając parametr, gdy token jest zwracany z witryny uwierzytelniającej i używając go w zapytaniu do tej witryny.
  Program Visual Studio podejmie próby parametryzacji testu, ale parametryzacja tokenów nie przebiegnie poprawnie.

## <a name="edit"></a> Edytowanie lub wyłączanie testu
Otwórz wybrany test, aby go edytować lub wyłączyć.

![Edytowanie lub wyłączanie testu sieci Web](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

Wyłączenie testów sieci Web może być wskazane, gdy w Twojej usłudze trwa konserwacja.

## Testy wydajności
W witrynie sieci Web można uruchomić test obciążenia. Podobnie jak w przypadku testu dostępności można wysłać proste żądania lub żądania wieloetapowe z naszych punktów na całym świecie. W przeciwieństwie do testu dostępności wysyłanych jest wiele żądań symulujących wielu równoczesnych użytkowników.

Z poziomu bloku Przegląd otwórz pozycję **Ustawienia**, **Testy wydajności**. Podczas tworzenia testu będzie miało miejsce zaproszenie do połączenia się z kontem usługi Visual Studio Team Services lub utworzenia go. 

Po zakończeniu testu wyświetlane są czasy reakcji i współczynniki powodzenia.

## Automatyzacja
* Automatyczne [konfigurowanie testów sieci web za pomocą skryptów środowiska PowerShell](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/). 
* Konfigurowanie [elementu webhook](../azure-portal/insights-webhooks-alerts.md) który jest wywoływany przy zgłaszaniu alertu.

## Pytania? Problemy?
* *Czy mogę wywołać kod z mojego testu sieci Web?*
  
    Nie. Kroki testu muszą być zawarte w pliku .webtest. Nie można też wywoływać innych testów sieci Web ani używać pętli. Istnieje jednak kilka wtyczek, które mogą być przydatne.
* *Czy jest obsługiwany protokół HTTPS?*
  
    Obsługujemy protokół TLS 1.1 i TLS 1.2.
* *Czym różnią się „testy sieci Web” i „testy dostępności”?*
  
    Używamy tych terminów zamiennie.
* *Chcę użyć testów dostępności na naszym serwerze wewnętrznym działającym za zaporą.*
  
    Skonfiguruj zaporę, aby zezwolić na żądania z [adresów IP agentów testów sieci Web](app-insights-ip-addresses.md#availability).
* *Przekazywanie wieloetapowego testu sieci web kończy się niepowodzeniem.*
  
    Limit rozmiaru to 300 KB.
  
    Pętle nie są obsługiwane.
  
    Odwołania do innych testów sieci Web nie są obsługiwane.
  
    Źródła danych nie są obsługiwane.
* *Mój test wieloetapowy nie jest wykonywany w całości*
  
    Istnieje limit 100 żądań na test.
  
    Test zostanie zatrzymany, jeśli działa dłużej niż dwie minuty.
* *Jak uruchomić test z wykorzystaniem certyfikatów klienta?*
  
    Niestety nie jest to obsługiwane.

## <a name="video"></a>Film wideo
> [!VIDEO https://channel9.msdn.com/Series/Application-Insights-on-Azure-Preview-Portal/Monitoring-Availability-with-Application-Insights/player]
> 
> 

## <a name="next"></a>Następne kroki
[Wyszukiwanie dzienników diagnostycznych][diagnostyka]

[Rozwiązywanie problemów][pytania_i_odpowiedzi].

[Adresy IP agentów testów sieci Web](app-insights-ip-addresses.md)

<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostyka]: app-insights-diagnostic-search.md
[pytania_i_odpowiedzi]: app-insights-troubleshoot-faq.md
[rozpoczynanie]: app-insights-overview.md



<!--HONumber=sep16_HO1-->


