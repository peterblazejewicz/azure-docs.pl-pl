---
title: Reagowanie na alerty zabezpieczeń i zarządzanie nimi w Centrum zabezpieczeń Azure | Microsoft Docs
description: Ten dokument ułatwia zarządzanie alertami zabezpieczeń i reagowanie na nie przy użyciu funkcji Centrum zabezpieczeń Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: ''

ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: yurid

---
# Reagowanie na alerty zabezpieczeń i zarządzanie nimi w Centrum zabezpieczeń Azure
Ten dokument ułatwia zarządzanie alertami zabezpieczeń i reagowanie na nie przy użyciu usługi Azure Security Center.

> [!NOTE]
> Aby włączyć wykrywanie zaawansowane, przeprowadź uaktualnienie usługi Azure Security Center do wersji Standard. Dostępna jest bezpłatna 90-dniowa wersja próbna. W celu uaktualnienia wybierz warstwę cenową w [Zasadach zabezpieczeń](security-center-policies.md). Aby dowiedzieć się więcej, zobacz [stronę cen](https://azure.microsoft.com/pricing/details/security-center/).
> 
> 

## Czym są alerty zabezpieczeń?
Usługa Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich, takich jak rozwiązania zapory i ochrony punktów końcowych, aby wykrywać prawdziwe zagrożenia i redukować liczbę fałszywych alarmów. W usłudze Security Center jest wyświetlana lista alertów zabezpieczeń uporządkowanych według priorytetu oraz informacje potrzebne do szybkiego analizowania problemu i zalecenia dotyczące postępowania w razie ataku. Usługa Azure Security Center agreguje również alerty, które przekształcają wzorce łańcuchowe w [zdarzenia](security-center-incident.md). 

> [!NOTE]
> Aby uzyskać więcej informacji na temat sposobu działania funkcji wykrywania usługi Security Center, przeczytaj [Funkcje wykrywania usługi Azure Security Center](security-center-detection-capabilities.md).
> 
> 

## Zarządzanie alertami zabezpieczeń
Bieżące alerty można przeglądać przy użyciu kafelka **Alerty zabezpieczeń**. Otwórz witrynę Azure Portal i wykonaj poniższe kroki, aby wyświetlić więcej szczegółowych informacji dotyczących poszczególnych alertów:

1. Na pulpicie nawigacyjnym Centrum zabezpieczeń widoczny jest kafelek **Alerty zabezpieczeń**.
   
    ![Kafelek Alerty zabezpieczeń w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)
2. Kliknij kafelek, aby otworzyć blok **Alerty zabezpieczeń** zawierający więcej szczegółowych informacji o alertach, jak pokazano poniżej.
   
   ![Blok kafelka Alerty zabezpieczeń w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

W dolnej części tego bloku znajdują się szczegółowe informacje o każdym alercie. Aby posortować dane, kliknij kolumnę, według której chcesz wykonać sortowanie. Poniżej znajdują się definicje poszczególnych kolumn:

* **Alert**: krótki opis alertu.
* **Liczba**: lista wszystkich alertów określonego typu, które zostały wykryte w określonym dniu.
* **Wykryte przez**: usługa odpowiedzialna za wyzwolenie alertu.
* **Data**: dzień, w którym wystąpiło zdarzenie.
* **Stan**: bieżący stan alertu. Istnieją dwa typy stanów:
  
  * **Aktywny**: alert zabezpieczeń został wykryty.
  * **Odrzucony**: alert zabezpieczeń został odrzucony przez użytkownika. Ten stan jest zwykle używany w przypadku alertów, które zostały zbadane, ale ryzyko związane z nimi zostało zminimalizowane albo alert nie dotyczył rzeczywistego ataku.
* **Ważność**: poziom ważności (wysoki, średni lub niski).

### Filtrowanie alertów
Alerty można filtrować na podstawie daty, stanu i ważności. Filtrowanie alertów może być przydatne w przypadku scenariuszy, w których należy zawęzić zakres wyświetlanych alertów zabezpieczeń. Możesz na przykład sprawdzić alerty zabezpieczeń, które wystąpiły w ciągu ostatnich 24 godzin, ponieważ badasz potencjalne naruszenie zabezpieczeń systemu.

1. Kliknij pozycję **Filtr** w bloku **Alerty zabezpieczeń**. Zostanie otwarty blok **Filtr**. Wybierz wartości daty, stanu i ważności, które chcesz wyświetlić.
   
    ![Filtrowanie alertów w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-ga.png)
2. Po zbadaniu alertu zabezpieczeń może okazać się, że było to fałszywe ostrzeżenie związane ze środowiskiem lub że wskazuje on oczekiwane zachowanie określonego zasobu. Niezależnie od przyczyny wyświetlenia, jeśli uważasz, że alert zabezpieczeń nie ma zastosowania, możesz go odrzucić i odfiltrować z widoku. Istnieją dwa sposoby odrzucania alertu zabezpieczeń. Kliknij alert prawym przyciskiem myszy i wybierz pozycję **Odrzuć** lub umieść wskaźnik myszy nad elementem, kliknij przycisk z wielokropkiem po prawej stronie i wybierz pozycję **Odrzuć**. Odrzucone alerty zabezpieczeń możesz wyświetlić, klikając pozycję **Filtr** i wybierając opcję **Odrzucone**.
   
   ![Odrzucanie alertów w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4-ga.png)

### Odpowiadanie na alerty zabezpieczeń
Wybierz alert zabezpieczeń, aby dowiedzieć się więcej o zdarzeniach, które go wywołały, oraz o czynnościach, które należy wykonać w celu wyeliminowania skutków ataku (jeśli ma to zastosowanie). Alerty zabezpieczeń są grupowane według typu i daty. Kliknięcie alertu zabezpieczeń spowoduje otwarcie bloku zawierającego listę pogrupowanych alertów.

![Odpowiadanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

W tym przypadku wyzwolone alerty dotyczą podejrzanego działania protokołu RDP (Remote Desktop Protocol). Pierwsza kolumna zawiera zaatakowane zasoby, druga przedstawia liczbę ataków na zasób, trzecia — czas ataku, czwarta — stan alertu, a piąta — ważność ataku. Po przejrzeniu tych informacji kliknij zaatakowany zasób. Zostanie otwarty nowy blok.

![Sugestie dotyczące zalecanych działań związanych z alertami zabezpieczeń w Centrum zabezpieczeń Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

W polu **Opis** tego bloku znajdują się dalsze szczegółowe informacje na temat danego zdarzenia. Te dodatkowe szczegóły dotyczą przyczyn wyzwolenia alertu zabezpieczeń, zasobu docelowego, źródłowego adresu IP (jeśli ma to zastosowanie) i zaleceń dotyczących sposobu wyeliminowania skutków ataku.  W niektórych przypadkach źródłowy adres IP będzie pusty (niedostępny), ponieważ nie wszystkie dzienniki zdarzeń zabezpieczeń systemu Windows obejmują adres IP.

Czynności naprawcze sugerowane w Centrum zabezpieczeń będą różnić się w zależności od alertu zabezpieczeń. W niektórych przypadkach do wykonania zalecanych czynności naprawczych trzeba będzie użyć innych funkcji platformy Azure. Na przykład aby wyeliminować skutki takiego ataku, należy utworzyć listę niedozwolonych adresów IP generujących atak przy użyciu [sieciowej listy ACL](../virtual-network/virtual-networks-acl.md) lub reguły [sieciowej grupy zabezpieczeń](../virtual-network/virtual-networks-nsg.md).

> [!NOTE]
> Aby znaleźć więcej informacji na temat różnych typów alertów, przeczytaj artykuł [Alerty zabezpieczeń według typu w usłudze Azure Security Center](security-center-alerts-type.md).
> 
> 

## Zobacz też
W tym dokumencie przedstawiono sposób konfigurowania zasad zabezpieczeń w Centrum zabezpieczeń. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Obsługa zdarzeń naruszenia zabezpieczeń w usłudze Azure Security Center](security-center-incident.md)
* [Funkcje wykrywania usługi Azure Security Center](security-center-detection-capabilities.md)
* [Przewodnik planowania i obsługi Centrum zabezpieczeń Azure](security-center-planning-and-operations-guide.md)
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

<!--HONumber=Sep16_HO3-->


