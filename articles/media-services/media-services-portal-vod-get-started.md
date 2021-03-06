---
title: " Wprowadzenie do dostarczania zawartości na żądanie przy użyciu portalu Azure | Microsoft Docs"
description: W tym samouczku przedstawiono kolejne kroki wdrażania podstawowej usługi do dostarczania zawartości wideo na żądanie (VoD) za pomocą aplikacji Azure Media Services (AMS) przy użyciu portalu Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: juliako

---
# Wprowadzenie do dostarczania zawartości na żądanie przy użyciu portalu Azure
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

W tym samouczku przedstawiono kolejne kroki wdrażania podstawowej usługi do dostarczania zawartości wideo na żądanie (VoD) za pomocą aplikacji Azure Media Services (AMS) przy użyciu portalu Azure.

> [!NOTE]
> Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

W tym samouczku opisano następujące zadania:

1. Tworzenie konta usługi Azure Media Services.
2. Konfigurowanie punktów końcowych przesyłania strumieniowego.
3. Ładowanie pliku wideo.
4. Kodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów.
5. Publikowanie elementu zawartości i uzyskiwanie adresów URL na potrzeby przesyłania strumieniowego i pobierania progresywnego.  
6. Odtwarzanie zawartości.

## Tworzenie konta usługi Azure Media Services
W tej sekcji opisano kroki w procesie tworzenia konta usługi AMS.

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).
2. Kliknij kolejno pozycje **+Nowe** > **Media + CDN** > **Media Services**.
   
    ![Tworzenie usługi Media Services](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. Na stronie **TWORZENIE KONTA USŁUGI MEDIA SERVICES** wprowadź wymagane wartości.
   
    ![Tworzenie usługi Media Services](./media/media-services-portal-vod-get-started/media-services-new3.png)
   
   1. W polu **Nazwa konta** wprowadź nazwę nowego konta usługi AMS. Nazwa konta usługi Media Services składa się z małych liter i cyfr (bez spacji) i może zawierać od 3 do 24 znaków.
   2. W subskrypcji wybierz jedną z różnych subskrypcji Azure, do których masz dostęp.
   3. W polu **Grupa zasobów** wybierz nowy lub istniejący zasób.  Grupa zasobów jest kolekcją zasobów, które mają ten sam cykl życia, uprawnienia i zasady. Więcej informacji można znaleźć [tutaj](../resource-group-overview.md#resource-groups).
   4. W polu **Lokalizacja** wybierz region geograficzny używany do przechowywania nośników i rekordów metadanych dla konta usługi Media Services. Ten region służy do przetwarzania i przesyłania strumieniowego multimediów. Na liście rozwijanej są wyświetlane tylko regiony dostępne w usłudze Media Services. 
   5. W polu **Konto magazynu** wybierz konto magazynu, aby udostępnić magazyn obiektów Blob dla zawartości multimedialnej z konta usługi Media Services. Można wybrać istniejące konto magazynu w tym samym regionie geograficznym co konto usługi Media Services albo utworzyć konto magazynu. Nowe konto magazynu jest tworzone w tym samym regionie. Reguły dotyczące nazw kont magazynów są takie same, jak w przypadku kont usługi Media Services.
      
       Więcej informacji o magazynie można znaleźć [tutaj](../storage/storage-introduction.md).
   6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**, aby wyświetlić postęp wdrażania konta.
4. Kliknij opcję **Utwórz** w dolnej części formularza.
   
    Po pomyślnym utworzeniu konta stan zmieni się na **Uruchomiony**. 
   
    ![Ustawienia usługi Media Services](./media/media-services-portal-vod-get-started/media-services-settings.png)
   
    Do zarządzania kontem AMS (na przykład przekazywania plików wideo, kodowania elementów zawartości, monitorowania postępu zadania) używaj okna **Ustawienia**.

## Zarządzanie kluczami
Do uzyskania programowego dostępu do konta usługi Media Services będą wymagane nazwa konta i informacje o kluczu podstawowym.

1. W portalu Azure wybierz konto. 
   
    Po prawej stronie zostanie wyświetlone okno **Ustawienia**. 
2. W oknie **Ustawienia** wybierz opcję **Klucze**. 
   
    W oknie **Zarządzanie kluczami** widoczna jest nazwa konta oraz wyświetlane są klucze podstawowe i pomocnicze. 
3. Naciśnij przycisk kopiowania, aby skopiować wartości.
   
    ![Klucze usługi Media Services](./media/media-services-portal-vod-get-started/media-services-keys.png)

## Konfigurowanie punktów końcowych przesyłania strumieniowego
Podczas pracy w usłudze Azure Media Services jednym z najbardziej typowych scenariuszy jest zapewnianie klientom obrazu wideo za pośrednictwem przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów. Usługa Media Services obsługuje następujące technologie przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH i HDS (tylko dla posiadaczy licencji Adobe PrimeTime/Access).

Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów, która pozwala dostarczać kodowaną zawartość plików MP4 z adaptacyjną szybkością transmisji bitów w formatach transmisji strumieniowej obsługiwanych przez usługę Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) w odpowiednim czasie bez konieczności przechowywania wersji wstępnie utworzonych pakietów poszczególnych formatów przesyłania strumieniowego.

Aby skorzystać z funkcji dynamicznego tworzenia pakietów, należy wykonać następujące czynności:

* Koduj plik (źródłowy) mezzanine do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów (kroki kodowania przedstawiono w dalszej części tego samouczka).  
* Utwórz co najmniej jedną jednostkę przesyłania strumieniowego dla *punktu końcowego przesyłania strumieniowego*, z którego planujesz dostarczać zawartość. W poniższych krokach przedstawiono, jak zmienić liczbę jednostek przesyłania strumieniowego.

Dzięki funkcji dynamicznego tworzenia pakietów wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services skompiluje oraz udostępni właściwą odpowiedź na podstawie żądań klienta.

Aby utworzyć i zmienić liczbę jednostek zarezerwowanego przesyłania strumieniowego, wykonaj następujące czynności:

1. W oknie **Ustawienia** kliknij przycisk **Punkty końcowe przesyłania strumieniowego**. 
2. Kliknij domyślny punkt końcowy przesyłania strumieniowego. 
   
    Zostanie wyświetlone okno **SZCZEGÓŁY DOMYŚLNEGO PUNKTU KOŃCOWEGO PRZESYŁANIA STRUMIENIOWEGO**.
3. Aby określić liczbę jednostek przesyłania strumieniowego, przesuń suwak **Jednostki przesyłania strumieniowego**.
   
    ![Jednostki przesyłania strumieniowego](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)
4. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
   
   > [!NOTE]
   > Alokacja nowych jednostek może zająć maksymalnie 20 minut.
   > 
   > 

## Przekazywanie plików
Aby przesłać strumieniowo pliki wideo przy użyciu usługi Azure Media Services, musisz przekazać źródłowe pliki wideo, zakodować je do wielokrotnych szybkości transmisji bitów oraz opublikować wynik. Pierwszy krok został omówiony w tej sekcji. 

1. W oknie **Ustawienie** kliknij przycisk **Elementy zawartości**.
   
    ![Przekazywanie plików](./media/media-services-portal-vod-get-started/media-services-upload.png)
2. Kliknij przycisk **Przekaż**.
   
    Zostanie wyświetlone okno **Przekaż element zawartości wideo**.
   
   > [!NOTE]
   > Nie ma żadnego limitu rozmiaru pliku.
   > 
   > 
3. Przejdź do wybranego pliku wideo na komputerze, zaznacz go, a następnie kliknij przycisk OK.  
   
    Rozpocznie się przekazywanie, a postęp będzie widoczny pod nazwą pliku.  

Po zakończeniu przekazywania na liście w oknie **Elementy zawartości** pojawi się nowy element zawartości. 

## Kodowanie elementów zawartości
Podczas pracy w usłudze Azure Media Services jednym z najbardziej typowych scenariuszy jest zapewnianie klientom przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów. Usługa Media Services obsługuje następujące technologie przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH i HDS (tylko dla posiadaczy licencji Adobe PrimeTime/Access). Aby przygotować pliki wideo do przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów, należy zakodować źródłowy plik wideo do plików o różnej szybkości transmisji bitów. Do kodowania plików wideo należy używać kodera **Media Encoder Standard**.  

Usługa Media Services udostępnia również funkcję dynamicznego tworzenia pakietów, która pozwala dostarczać pliki MP4 o różnej szybkości transmisji bitów w formatach przesyłania strumieniowego MPEG DASH, HLS, Smooth Streaming lub HDS bez konieczności ponownego tworzenia pakietów w tych formatach. Dzięki funkcji dynamicznego tworzenia pakietów wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services skompiluje oraz udostępni właściwą odpowiedź na podstawie żądań klienta.

Aby skorzystać z funkcji dynamicznego tworzenia pakietów, należy wykonać następujące czynności:

* Koduj plik źródłowy do zestawu plików MP4 o różnej szybkości transmisji bitów (kroki kodowania przedstawiono w dalszej części tej sekcji).
* Pobierz co najmniej jedną jednostkę przesyłania strumieniowego dla punktu końcowego przesyłania strumieniowego, z którego planujesz dostarczać zawartość. Aby uzyskać więcej informacji, zobacz temat dotyczący [konfigurowania punktów końcowych przesyłania strumieniowego](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

### Aby korzystać z portalu do kodowania
W tej sekcji opisano kroki, które należy wykonać w celu zakodowania zawartości przy użyciu standardu Media Encoder Standard.

1. W oknie **Ustawienia** wybierz opcję **Elementy zawartości**.  
2. W oknie **Elementy zawartości** wybierz element zawartości, który chcesz kodować.
3. Kliknij przycisk **Koduj**.
4. W oknie **Kodowanie elementu zawartości** wybierz procesor „Media Encoder Standard” oraz ustawienie wstępne. Na przykład: jeśli wejściowy plik wideo ma rozdzielczość 1920 x 1080 pikseli, można użyć ustawienia wstępnego „Wielokrotna szybkość transmisji bitów H264 1080p”. Aby uzyskać więcej informacji dotyczących ustawień wstępnych, zobacz [ten artykuł](https://msdn.microsoft.com/library/azure/mt269960.aspx) — ważne jest, aby wybrać ustawienie wstępne, które jest najbardziej odpowiednie dla wejściowego pliku wideo. Jeśli wideo jest w niskiej rozdzielczości (640 x 360), nie używaj ustawienia wstępnego „Wielokrotna szybkość transmisji bitów H264 1080p”.
   
   Aby ułatwić zarządzanie istnieje możliwość edytowania nazwy wyjściowego elementu zawartości oraz nazwy zadania.
   
   ![Kodowanie elementów zawartości](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Kliknij przycisk **Utwórz**.

### Monitorowanie postępu zadania kodowania
Aby monitorować postęp zadania kodowania, kliknij pozycję **Ustawienia** (w górnej części strony), a następnie wybierz pozycję **Zadania**.

![Zadania](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## Publikowanie zawartości
Aby podać użytkownikowi adres URL, który może służyć do przesyłania strumieniowego lub pobierania zawartości, należy najpierw „opublikować” element zawartości przez utworzenie lokalizatora. Lokalizatory zapewniają dostęp do plików znajdujących się w elemencie zawartości. Usługa Media Services obsługuje dwa typy lokalizatorów: 

* Lokalizatory przesyłania strumieniowego (OnDemandOrigin) używane do adaptacyjnego przesyłania strumieniowego (na przykład, przesyłania MPEG DASH, HLS lub Smooth Streaming). Aby utworzyć lokalizator przesyłania strumieniowego element zawartości musi zawierać plik .ism. 
* Progresywne lokalizatory (SAS) używane do dostarczania plików wideo przy użyciu pobierania progresywnego.

Adres URL przesyłania strumieniowego ma następujący format i służy do odtwarzania elementów zawartości przy użyciu protokołu Smooth Streaming.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Aby utworzyć adres URL przesyłania strumieniowego w protokole HLS, dołącz do adresu URL ciąg (format=m3u8-aapl).

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Aby utworzyć adres URL przesyłania strumieniowego w protokole MPEG DASH, dołącz do adresu URL ciąg (format=mpd-time-csf).

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Adres URL SAS ma następujący format.

    {blob container name}/{asset name}/{file name}/{SAS signature}

> [!NOTE]
> Jeśli użyto portalu do utworzenia lokalizatorów przed marcem 2015 r., zostały utworzone lokalizatory z dwuletnim okresem ważności.  
> 
> 

Do aktualizacji daty wygaśnięcia na lokalizatorze użyj interfejsu API [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) lub [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Po zaktualizowaniu daty wygaśnięcia lokalizatora SAS następuje zmiana adresu URL.

### Aby opublikować element zawartości za pomocą portalu
Aby opublikować element zawartości za pomocą portalu, wykonaj następujące czynności:

1. Wybierz kolejno pozycje **Ustawienia** > **Elementy zawartości**.
2. Wybierz element zawartości, który chcesz opublikować.
3. Kliknij przycisk **Publikuj**.
4. Wybierz typ lokalizatora.
5. Kliknij przycisk **Dodaj**.
   
    ![Publikowanie](./media/media-services-portal-vod-get-started/media-services-publish1.png)

Adres URL jest dodawany do listy **Opublikowane adresy URL**.

## Odtwarzanie zawartości z portalu
Portal Azure udostępnia odtwarzacz zawartości, który może służyć do testowania pliku wideo.

Kliknij wybrany plik wideo, a następnie kliknij przycisk **Odtwórz**.

![Publikowanie](./media/media-services-portal-vod-get-started/media-services-play.png)

Zagadnienia do rozważenia:

* Zadbaj o to, aby film wideo został opublikowany.
* Ten **odtwarzacz multimediów** odtwarza z domyślnego punktu końcowego przesyłania strumieniowego. Aby odtworzyć z punktu końcowego przesyłania strumieniowego innego niż domyślny, kliknij, aby skopiować adres URL i użyć innego odtwarzacza (np. [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)).

## Następne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!--HONumber=Sep16_HO3-->


