---
title: Jak wykonać transmisję strumieniową na żywo za pomocą koderów lokalnych przy użyciu portalu Azure | Microsoft Docs
description: W tym samouczku opisano kolejne kroki w procesie tworzenia kanału konfigurowanego do dostarczania w formie przekazywania.
services: media-services
documentationcenter: ''
author: juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/05/2016
ms.author: juliako

---
# <a name="how-to-perform-live-streaming-with-on-premise-encoders-using-the-azure-portal"></a>Jak wykonać transmisję strumieniową na żywo za pomocą koderów lokalnych przy użyciu portalu Azure
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-live-passthrough-get-started.md)
> * [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn783458.aspx)
> 
> 

W tym samouczku opisano kolejne kroki w procesie tworzenia **kanału** skonfigurowanego do dostarczania zawartości w formie przekazywania przy użyciu witryny Azure Portal. 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania czynności przedstawionych w tym samouczku są niezbędne następujące elementy:

* Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Konto usługi Media Services. Aby utworzyć konto usługi Media Services, zobacz temat [Jak utworzyć konto usługi Media Services](media-services-portal-create-account.md).
* Kamera internetowa. Na przykład [koder Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm).

Zdecydowanie zaleca się następujące artykuły:

* [Obsługa protokołu RTMP i kodery na żywo w usłudze Azure Media Services](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
* [Omówienie transmisji strumieniowej na żywo przy użyciu usługi Azure Media Services](media-services-manage-channels-overview.md)
* [Transmisja strumieniowa na żywo za pomocą koderów lokalnych, które tworzą strumienie różnej szybkości transmisji bitów](media-services-live-streaming-with-onprem-encoders.md)

## <a name="<a-id="scenario"></a>common-live-streaming-scenario"></a><a id="scenario"></a>Typowy scenariusz transmisji strumieniowej na żywo
W poniższych krokach opisano zadania związane z tworzeniem typowych aplikacji do transmisji strumieniowej na żywo używających kanałów skonfigurowanych do dostarczania zawartości w formie przekazywania. W tym samouczku przedstawiono sposób tworzenia kanału do przekazywania zawartości i transmitowania wydarzeń na żywo oraz zarządzania nimi.

1. Podłącz kamerę wideo do komputera. Uruchom i skonfiguruj lokalny koder na żywo, który wyprowadza strumień protokołu RTMP o różnej szybkości transmisji bitów lub pofragmentowany strumień MP4. Aby uzyskać więcej informacji, zobacz temat [Obsługa protokołu RTMP i kodery na żywo w usłudze Azure Media Services](http://go.microsoft.com/fwlink/?LinkId=532824).
   
    Ten krok można również wykonać po utworzeniu kanału.
2. Utwórz i uruchom kanał w formie przekazywania.
3. Pobierz adres URL pozyskiwania kanału. 
   
    Koder na żywo używa adresu URL pozyskiwania do wysyłania strumienia do kanału.
4. Pobierz adres URL podglądu kanału. 
   
    Użyj tego adresu URL, aby sprawdzić, czy kanał prawidłowo odbiera strumień na żywo.
5. Utwórz program lub wydarzenie na żywo. 
   
    W przypadku korzystania z portalu Azure utworzenie wydarzenia na żywo spowoduje również utworzenie elementu zawartości. 
   
   > [!NOTE]
   > Upewnij się, że istnieje co najmniej jedna jednostka zarezerwowanego przesyłania strumieniowego w punkcie końcowym, z którego zawartość ma być przesyłana strumieniowo.
   > 
   > 
6. Uruchom wydarzenie/program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji.
7. Opcjonalnie można przesłać do kodera na żywo sygnał o rozpoczęciu reklamy. Reklama jest wstawiana do strumienia wyjściowego.
8. Zatrzymaj wydarzenie/program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.
9. Usuń wydarzenie/program (opcjonalnie można również usunąć element zawartości).     

> [!IMPORTANT]
> W temacie [Transmisja strumieniowa na żywo za pomocą koderów lokalnych tworzących strumienie o różnej szybkości transmisji bitów](media-services-live-streaming-with-onprem-encoders.md) opisano założenia i zagadnienia dotyczące transmisji strumieniowej na żywo za pomocą koderów lokalnych i kanałów w formie przekazywania.
> 
> 

## <a name="to-view-notifications-and-errors"></a>Wyświetlanie powiadomień i błędów
Jeśli chcesz wyświetlić powiadomienia i błędy wygenerowane w portalu Azure, kliknij ikonę Powiadomienia.

![Powiadomienia](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

## <a name="configure-streaming-endpoints"></a>Konfigurowanie punktów końcowych przesyłania strumieniowego
Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów, która pozwala dostarczać pliki MP4 o różnych szybkościach transmisji bitów w formatach przesyłania strumieniowego MPEG DASH, HLS, Smooth Streaming lub HDS bez konieczności ponownego tworzenia pakietów w tych formatach. Dzięki funkcji dynamicznego tworzenia pakietów wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services skompiluje oraz udostępni właściwą odpowiedź na podstawie żądań klienta.

Aby skorzystać z dynamicznego tworzenia pakietów, pobierz co najmniej jedną jednostkę przesyłania strumieniowego punktu końcowego przesyłania strumieniowego, z którego planujesz dostarczać zawartość.  

Aby utworzyć i zmienić liczbę jednostek zarezerwowanego przesyłania strumieniowego, wykonaj następujące czynności:

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).
2. W oknie **Ustawienia** kliknij przycisk **Punkty końcowe przesyłania strumieniowego**. 
3. Kliknij domyślny punkt końcowy przesyłania strumieniowego. 
   
    Zostanie wyświetlone okno **SZCZEGÓŁY DOMYŚLNEGO PUNKTU KOŃCOWEGO PRZESYŁANIA STRUMIENIOWEGO**.
4. Aby określić liczbę jednostek przesyłania strumieniowego, przesuń suwak **Jednostki przesyłania strumieniowego**.
   
    ![Jednostki przesyłania strumieniowego](./media/media-services-portal-passthrough-get-started/media-services-streaming-units.png)
5. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
   
   > [!NOTE]
   > Alokacja nowych jednostek może zająć maksymalnie 20 minut.
   > 
   > 

## <a name="create-and-start-pass-through-channels-and-events"></a>Tworzenie i uruchamianie kanałów i wydarzeń w formie przekazywania
Kanał jest skojarzony z wydarzeniami/programami, które umożliwiają kontrolowanie publikowania i przechowywania segmentów strumienia na żywo. Kanały zarządzają wydarzeniami. 

Można określić liczbę godzin, aby zachować zarejestrowaną zawartość na potrzeby programu przez ustawienie długości **Okna archiwum**. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin. Długość okna archiwum określa również dostępny dla klientów zakres cofania odtwarzania pliku od bieżącego momentu transmisji na żywo. Wydarzenia mogą być uruchamiane w określonym czasie, ale zawartość, która wykracza poza długość okna jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każde wydarzenie jest skojarzone z elementem zawartości. Aby opublikować wydarzenie, należy utworzyć lokalizator OnDemand dla skojarzonego elementu zawartości. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione wydarzenia, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany na potrzeby archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Drugi z programów jest skonfigurowany w celu archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

Istniejących wydarzeń na żywo nie należy używać ponownie. Zamiast tego należy utworzyć i uruchomić nowe wydarzenie dla każdego wydarzenia.

Uruchom wydarzenie, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia. 

Aby usunąć zarchiwizowaną zawartość, zatrzymaj i usuń wydarzenie, a następnie usuń skojarzony element zawartości. Nie można usunąć elementu zawartości, jeśli jest on używany przez wydarzenie. Najpierw należy usunąć wydarzenie. 

Nawet po zatrzymaniu i usunięciu wydarzenia użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość wideo na żądanie tak długo, jak zasoby nie zostaną usunięte.

Jeśli chcesz zachować zarchiwizowaną zawartość, ale bez udostępniania jej do przesyłania strumieniowego, usuń lokalizator przesyłania strumieniowego.

### <a name="to-use-the-portal-to-create-a-channel"></a>Aby utworzyć kanał za pomocą portalu
W tej sekcji przedstawiono, jak użyć opcji **Szybkie tworzenie** do utworzenia kanału przekazującego.

Więcej szczegółowych informacji dotyczących kanałów w formie przekazywania można znaleźć w temacie [Transmisja strumieniowa na żywo za pomocą koderów lokalnych, które tworzą strumienie o różnej szybkości transmisji bitów](media-services-live-streaming-with-onprem-encoders.md).

1. W oknie **Ustawienia** kliknij przycisk **Transmisja strumieniowa na żywo**. 
   
    ![Wprowadzenie](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
   
    Zostanie wyświetlone okno **Transmisja strumieniowa na żywo**.
2. Kliknij przycisk **Szybkie tworzenie**, aby utworzyć kanał w formie przekazywania za pomocą protokołu pozyskiwania RTMP.
   
    Zostanie wyświetlone okno **UTWÓRZ NOWY KANAŁ**.
3. Nadaj nazwę nowemu kanałowi, a następnie kliknij przycisk **Utwórz**. 
   
    Spowoduje to utworzenie kanału przekazującego za pomocą protokołu pozyskiwania RTMP.

## <a name="create-events"></a>Tworzenie wydarzeń
1. Wybierz kanał, do którego chcesz dodać wydarzenie.
2. Naciśnij przycisk **Wydarzenie na żywo**.

![Wydarzenie](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)

## <a name="get-ingest-urls"></a>Pobieranie adresów URL pozyskiwania
Po utworzeniu kanału można pobrać adresy URL pozyskiwania, które należy udostępnić koderowi na żywo. Koder używa tych adresów URL do wprowadzenia strumienia na żywo.

![Utworzone](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

## <a name="watch-the-event"></a>Oglądanie wydarzenia
Aby oglądać wydarzenie, kliknij przycisk **Oglądaj** w witrynie Azure Portal lub skopiuj adres URL przesyłania strumieniowego i użyj wybranego odtwarzacza. 

![Utworzone](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Po zatrzymaniu wydarzenia na żywo jest ono automatycznie konwertowane na zawartość na żądanie.

## <a name="clean-up"></a>Czyszczenie
Więcej szczegółowych informacji dotyczących kanałów w formie przekazywania można znaleźć w temacie [Transmisja strumieniowa na żywo za pomocą koderów lokalnych, które tworzą strumienie o różnej szybkości transmisji bitów](media-services-live-streaming-with-onprem-encoders.md).

* Kanał można zatrzymać tylko wtedy, jeśli wszystkie wydarzenia/programy na kanale zostały zatrzymane.  Po zatrzymaniu kanału opłaty nie są naliczane. W razie potrzeby ponownego uruchomienia kanał będzie miał ten sam adres URL pozyskiwania, więc nie trzeba będzie ponownie konfigurować kodera.
* Kanał można usunąć tylko wtedy, gdy wszystkie wydarzenia na żywo na kanale zostały usunięte.

## <a name="view-archived-content"></a>Wyświetlanie zarchiwizowanej zawartości
Nawet po zatrzymaniu i usunięciu wydarzenia użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość wideo na żądanie tak długo, jak zasoby nie zostaną usunięte. Nie można usunąć elementu zawartości, jeśli jest on używany przez wydarzenie. Najpierw należy usunąć wydarzenie. 

Aby zarządzać elementami zawartości, wybierz pozycję **Ustawienie** i kliknij przycisk **Elementy zawartości**.

![Elementy zawartości](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

## <a name="next-step"></a>Następny krok
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!--HONumber=Oct16_HO3-->


