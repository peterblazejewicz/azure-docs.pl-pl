---
title: Analiza aplikacji sieci Web w języku Java za pomocą usługi Application Insights | Microsoft Docs
description: 'Monitorowanie wydajności i użycia witryny sieci Web w języku Java za pomocą usługi Application Insights. '
services: application-insights
documentationcenter: java
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: awills

---
# Wprowadzenie do usługi Application Insights w projekcie sieci Web w języku Java
*Usługa Application Insights jest dostępna w wersji zapoznawczej.*

[!INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

[Application Insights](https://azure.microsoft.com/services/application-insights/) jest rozszerzalną usługą analizy, która ułatwia zrozumienie wydajności i użycia aktywnej aplikacji. Służy do [wykrywania i diagnozowania problemów z wydajnością i wyjątków](app-insights-detect-triage-diagnose.md) oraz [pisania kodu][api] do śledzenia korzystania z aplikacji przez użytkowników.

![dane przykładowe](./media/app-insights-java-get-started/5-results.png)

Usługa Application Insights obsługuje aplikacje w języku Java działające w systemach Linux, Unix lub Windows.

Potrzebne elementy:

* Środowisko Oracle JRE 1.6 lub nowsze albo Zulu JRE 1.6 lub nowsze
* Subskrypcja platformy [Microsoft Azure](https://azure.microsoft.com/). (Możesz rozpocząć od [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/)).

*Jeśli masz aplikację sieci Web, która już działa, możesz wykonać alternatywną procedurę, aby [dodać zestaw SDK w czasie wykonywania na serwerze sieci Web](app-insights-java-live.md). Ta alternatywa pozwala uniknąć ponownego kompilowania kodu, ale nie udostępnia opcji pisania kodu w celu śledzenia działań użytkownika.*

## 1. Uzyskiwanie klucza instrumentacji usługi Application Insights
1. Zaloguj się do [Portalu Microsoft Azure](https://portal.azure.com).
2. Utwórz zasób usługi Application Insights. Jako typ aplikacji ustaw wartość Aplikacja sieci Web Java.
   
    ![Wypełnij nazwę, wybierz aplikację sieci Web Java i kliknij przycisk Utwórz](./media/app-insights-java-get-started/02-create.png)
3. Znajdź klucz instrumentacji nowego zasobu. Wkrótce będzie trzeba wkleić ten klucz do projektu kodu.
   
    ![W opisie nowego zasobu kliknij opcję Właściwości i skopiuj klucz instrumentacji](./media/app-insights-java-get-started/03-key.png)

## 2. Dodawanie zestawu SDK usługi Application Insights dla środowiska Java do projektu
*Wybierz odpowiedni sposób dla danego projektu.*

#### Jeśli używasz środowiska Eclipse do tworzenia projektu Maven lub dynamicznego projektu sieci Web...
Użyj [wtyczki zestawu SDK Application Insights dla środowiska Java][eclipse].

#### Jeśli używasz narzędzia Maven...
Jeśli projekt jest już skonfigurowany do używania narzędzia Maven w celu kompilacji, scal następujący kod z plikiem pom.xml.

Następnie odśwież zależności projektu, aby pliki binarne zostały pobrane.

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[1.0,)</version>
      </dependency>
    </dependencies>


* *Błędy kompilacji lub walidacji sumy kontrolnej?* Spróbuj użyć określonej wersji, np.: `<version>1.0.n</version>`. Najbardziej aktualną wersję z najdziesz w [informacjach o wersji zestawu SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) lub wśród naszych [artefaktów Maven](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Trzeba zaktualizować zestaw SDK?* Odśwież zależności projektu.

#### Jeśli używasz narzędzia Gradle...
Jeśli projekt jest już skonfigurowany do używania narzędzia Gradle w celu kompilacji, scal następujący kod z plikiem build.gradle.

Następnie odśwież zależności projektu, aby pliki binarne zostały pobrane.

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '1.+'
      // or applicationinsights-core for bare API
    }

* *Błędy kompilacji lub walidacji sumy kontrolnej? Spróbuj użyć określonej wersji, np.:* `version:'1.0.n'`. *Najbardziej aktualną wersję z najdziesz w [informacjach o wersji zestawu SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).*
* *Aby zaktualizować zestaw SDK*
  * Odśwież zależności projektu.

#### W innym przypadku...
Ręcznie dodaj zestaw SDK:

1. Pobierz [Zestaw SDK usługi Application Insights dla środowiska Java](https://aka.ms/aijavasdk).
2. Wyodrębnij pliki binarne z pliku zip i dodaj je do projektu.

### Pytania...
* *Jaki jest związek między składnikami `-core` i `-web` w pliku zip?*
  
  * `applicationinsights-core` dostarcza podstawowy interfejs API. Ten składnik jest zawsze potrzebny.
  * `applicationinsights-web` dostarcza metryki do śledzenia liczby żądań HTTP i czasów odpowiedzi. Możesz pominąć ten składnik, jeśli nie chcesz automatycznie zbierać tych danych telemetrycznych. Na przykład jeśli chcesz zaprogramować zbieranie samodzielnie.
* *Aby zaktualizować zestaw SDK po opublikowaniu zmian*
  
  * Pobierz najnowszy [Zestaw SDK usługi Application Insights dla środowiska Java](https://aka.ms/qqkaq6) i zastąp nim stary.
  * Zmiany są opisane w [informacjach o wersji zestawu SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## 3. Dodawanie pliku .xml usługi Application Insights
Dodaj plik ApplicationInsights.xml do folderu zasobów w projekcie lub upewnij się, że jest dodany do ścieżki klas wdrażania projektu. Skopiuj do niego następujący kod XML.

Zastąp klucz instrumentacji kluczem pobranym z portalu Azure.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* Klucz instrumentacji jest wysyłany wraz z każdym elementem telemetrii i dzięki temu te elementy mogą być wyświetlane dla odpowiedniego zasobu usługi Application Insights.
* Składnik żądań HTTP jest opcjonalny. Powoduje automatyczne wysyłanie telemetrii dotyczącej żądań i czasów odpowiedzi do portalu.
* Korelacja zdarzeń jest dodatkiem do składnika żądań HTTP. Przypisuje identyfikator do każdego żądania odebranego przez serwer i dodaje go do każdego elementu telemetrii jako właściwość „Operation.Id”. Umożliwia korelowanie telemetrii skojarzonej z każdym żądaniem przez ustawienie filtru w [wyszukiwaniu diagnostycznym][diagnostyka].
* Klucz usługi Application Insights może zostać przekazany dynamicznie z witryny Azure Portal jako właściwość systemu (-DAPPLICATION_INSIGHTS_IKEY=Twój_klucz_ikey). Jeśli nie zdefiniowano żadnej właściwości, sprawdzana jest zmienna środowiskowa (APPLICATION_INSIGHTS_IKEY) w ustawieniach aplikacji platformy Azure. Jeśli obie właściwości nie są zdefiniowane, używana jest domyślna właściwość InstrumentationKey z pliku ApplicationInsights.xml. Ta sekwencja ułatwia dynamiczne zarządzanie elementami InstrumentationKey dla różnych środowisk.

### Alternatywne sposoby ustawienia klucza instrumentacji
Zestaw SDK usługi Application Insights szuka klucza w następującej kolejności:

1. Właściwość systemu: -DAPPLICATION_INSIGHTS_IKEY=Twój_klucz_ikey
2. Zmienna środowiskowa: APPLICATION_INSIGHTS_IKEY
3. Plik konfiguracji: ApplicationInsights.xml

Możesz również [ustawić klucz w kodzie](app-insights-api-custom-events-metrics.md#ikey):

    telemetryClient.InstrumentationKey = "...";


## 4. Dodawanie filtru HTTP
Ostatni krok konfiguracji umożliwia składnikowi żądania HTTP rejestrowanie wszystkich żądań sieci Web. (Nie jest to wymagane, jeśli potrzebujesz tylko podstawowego interfejsu API).

Znajdź i otwórz plik web.xml w projekcie, a następnie scal poniższy kod w obszarze węzła web-app, w którym skonfigurowano filtry aplikacji.

Aby uzyskać najbardziej dokładne wyniki, ten filtr powinien być mapowany przed wszystkimi innymi filtrami.

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

#### Jeśli używasz środowiska Spring Web MVC 3.1 lub nowszego
Edytuj te elementy, aby załączyć pakiet Application Insights:

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>

#### Jeśli używasz środowiska Struts 2
Dodaj ten element do pliku konfiguracyjnego Struts (zwykle o nazwie struts.xml lub struts-default.xml):

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

(Jeśli masz interceptory zdefiniowane w stosie domyślnym, możesz po prostu dodać interceptor do tego stosu).

## 5. Uruchamianie aplikacji
Uruchom aplikację w trybie debugowania na komputerze deweloperskim albo opublikuj na serwerze.

## 6. Wyświetlanie telemetrii w usłudze Application Insights
Wróć do zasobu usługi Application Insights w witrynie [Microsoft Azure Portal](https://portal.azure.com).

W bloku przeglądu zostaną wyświetlone dane żądań HTTP. (Jeśli ich tam nie ma, odczekaj kilka sekund, a następnie kliknij przycisk Odśwież).

![dane przykładowe](./media/app-insights-java-get-started/5-results.png)

[Dowiedz się więcej o metrykach.][metrics]

Klikaj elementy wykresów, aby wyświetlać bardziej szczegółowe metryki zagregowane.

![](./media/app-insights-java-get-started/6-barchart.png)

> Usługa Application Insights zakłada, że format żądania HTTP dla aplikacji MVC to: `VERB controller/action`. Na przykład żądania `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` i `GET Home/Product/sdf96vws` są grupowane w ramach pozycji `GET Home/Product`. To grupowanie umożliwia zrozumiałe agregowanie żądań, na przykład podawanie liczby żądań i średniego czasu ich wykonania.
> 
> 

### Dane wystąpienia
Kliknij określony typ żądania, aby wyświetlić poszczególne wystąpienia. 

W usłudze Application Insights są wyświetlane dwa rodzaje danych: dane zagregowane, przechowywane i wyświetlane jako średnie, liczniki i sumy, oraz dane wystąpienia — indywidualne raporty dotyczące żądań HTTP, wyjątków, wyświetleń stron lub zdarzeń niestandardowych.

Podczas wyświetlania właściwości żądania można wyświetlić skojarzone zdarzenia telemetrii, takie jak żądania i wyjątki.

![](./media/app-insights-java-get-started/7-instance.png)

### Analiza: zaawansowany język zapytań
W miarę zgromadzenia większej ilości danych można uruchamiać zapytania zarówno w celu agregowania danych, jak i w celu znajdowania poszczególnych wystąpień. [Analiza]() jest zaawansowanym narzędziem, którego można używać zarówno w celu poznania wydajności i użycia, jak i do celów diagnostycznych.

![Przykład analizy](./media/app-insights-java-get-started/025.png)

## 7. Instalowanie aplikacji na serwerze
Teraz opublikuj aplikację na serwerze, pozwól z niej korzystać innym osobom, a następnie obejrzyj telemetrię wyświetlaną w portalu.

* Upewnij się, że zapora pozwala aplikacji na wysłanie telemetrii do tych portów:
  
  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443
* Na serwerach systemu Windows zainstaluj:
  
  * [Pakiety Microsoft Visual C++ Redistributable](http://www.microsoft.com/download/details.aspx?id=40784)
    
    Ten składnik umożliwia działanie liczników wydajności.

## Wyjątki i błędy żądań
Nieobsługiwane wyjątki są zbierane automatycznie:

![Otwórz ustawienia, błędy](./media/app-insights-java-get-started/21-exceptions.png)

Istnieją dwie opcje zbierania danych o innych wyjątkach:

* [Wstawianie wywołań metody trackException() w kodzie][wyjątki_api]. 
* [Instalacja agenta Java na serwerze](app-insights-java-agent.md). Trzeba określić metody, które chcesz śledzić.

## Monitorowanie wywołań metod i zależności zewnętrznych
[Zainstaluj agenta programu Java](app-insights-java-agent.md) w celu rejestrowania określonych metod wewnętrznych i wywołań za pośrednictwem JDBC z danymi chronometrażu.

## Liczniki wydajności
Otwórz pozycję **Ustawienia**, **Serwery**, aby wyświetlić zakres liczników wydajności.

![](./media/app-insights-java-get-started/11-perf-counters.png)

### Dostosowywanie zbierania danych liczników wydajności
Aby wyłączyć zbieranie standardowego zestawu liczników wydajności, dodaj następujący kod w węźle głównym pliku ApplicationInsights.xml:

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### Zbieranie danych dodatkowych liczników wydajności
Możesz określić dodatkowe liczniki wydajności do zbierania danych.

#### Liczniki JMX (udostępniane przez maszynę wirtualną Java)
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

* `displayName` — nazwa wyświetlana w portalu Application Insights.
* `objectName` — nazwa obiektu JMX.
* `attribute` — atrybut nazwy obiektu JMX do pobrania.
* `type` (opcjonalnie) — typ atrybutu obiektu JMX:
  * wartość domyślna: typ prosty, np. int lub long.
  * `composite`: dane licznika wydajności są w formacie „Atrybut.Dane”
  * `tabular`: dane licznika wydajności są w formacie wiersza tabeli

#### Liczniki wydajności systemu Windows
Każdy [licznik wydajności systemu Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) należy do kategorii (w taki sam sposób, w jaki pole należy do klasy). Kategorie mogą być globalne lub mogą mieć wystąpienia numerowane lub nazwane.

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

* displayName — nazwa wyświetlana w portalu Application Insights.
* categoryName — kategoria licznika wydajności (obiekt wydajności), z którą skojarzony jest ten licznik wydajności.
* counterName — nazwa licznika wydajności.
* instanceName — nazwa wystąpienia kategorii licznika wydajności lub ciąg pusty (""), jeśli kategoria zawiera jedno wystąpienie. Jeśli categoryName to Proces, a licznik wydajności, którego dane chcesz zbierać, pochodzi z bieżącego procesu maszyny JVM, na której jest uruchomiona aplikacja, podaj wartość `"__SELF__"`.

Liczniki wydajności są widoczne jako metryki niestandardowe w [Eksploratorze metryk][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)

### Liczniki wydajności sytemu Unix
* [Zainstaluj program collectd z wtyczką Application Insights](app-insights-java-collectd.md), aby uzyskać szeroką gamę danych na temat systemu i sieci.

## Pobieranie danych użytkownika i sesji
Wysyłasz już telemetrię z serwera sieci Web. Teraz, aby zyskać pełny wgląd w dane aplikacji, możesz dodać więcej opcji monitorowania:

* [Dodaj telemetrię do stron sieci Web][użycie], aby monitorować wyświetlenia stron i metryki użytkowników.
* [Konfigurowanie testów sieci Web][availability], aby upewnić się, że aplikacja stale działa i odpowiada.

## Przechwytywanie danych dziennika śledzenia
Usługi Application Insights mogą służyć do analizowania pod różnymi kątami danych z dzienników Log4J, Logback lub innych platform rejestrowania. Dzienniki można skorelować z żądaniami HTTP i inną telemetrią. [Naucz się][dzienniki_java].

## Wysyłanie własnej telemetrii
Teraz, po zainstalowaniu zestawu SDK, możesz użyć interfejsu API do wysyłania własnej telemetrii.

* [Śledź niestandardowe zdarzenia i metryki][api], aby dowiedzieć się, jak użytkownicy korzystają z aplikacji.
* [Wyszukuj zdarzenia i dzienniki][diagnostyka], aby łatwiej diagnozować problemy.

## Testy dostępności sieci Web
Usługa Application Insights może służyć do testowania witryny sieci Web w regularnych odstępach czasu, aby sprawdzić, czy witryna działa i odpowiada poprawnie. [Aby skonfigurować][availability], kliknij pozycję Testy sieci Web.

![Kliknij pozycję Testy sieci Web, a następnie kliknij pozycję Dodaj test sieci Web](./media/app-insights-java-get-started/31-config-web-test.png)

Uzyskasz wykresy czasów odpowiedzi oraz powiadomienia e-mail w razie wyłączenia witryny.

![Przykład testu sieci Web](./media/app-insights-java-get-started/appinsights-10webtestresult.png)

[Dowiedz się więcej o testach dostępności sieci Web.][availability] 

## Pytania? Problemy?
[Rozwiązywanie problemów z technologią Java](app-insights-java-troubleshoot.md)

## Następne kroki
Więcej informacji możesz znaleźć w [Centrum deweloperów języka Java](/develop/java/).

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[wyjątki_api]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostyka]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[dzienniki_java]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[użycie]: app-insights-web-track-usage.md



<!--HONumber=sep16_HO1-->


