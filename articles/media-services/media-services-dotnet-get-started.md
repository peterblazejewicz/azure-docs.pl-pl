---
title: Wprowadzenie do dostarczania zawartości na żądanie przy użyciu zestawu .NET | Microsoft Docs
description: Ten samouczek przedstawia kroki wdrażania aplikacji do dostarczania zawartości na żądanie w usługach Azure Media Services z użyciem platformy .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/11/2016
ms.author: juliako

---
# <a name="get-started-with-delivering-content-on-demand-using-.net-sdk"></a>Wprowadzenie do dostarczania zawartości na żądanie przy użyciu zestawu .NET SDK
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

> [!NOTE]
> Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz temat [Bezpłatna wersja próbna systemu Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 
> 
> 

## <a name="overview"></a>Omówienie
Ten samouczek przedstawia kroki wdrażania aplikacji do dostarczania zawartości wideo na żądanie (VoD) przy użyciu zestawu SDK usług Azure Media Services (AMS) dla programu .NET.

Samouczek przedstawia podstawowy przepływ pracy usług Media Services oraz najczęściej występujące obiekty i zadania programowania wymagane w celu projektowania usług Media Services. Po zakończeniu samouczka będziesz umieć przesłać strumieniowo lub pobrać progresywnie przykładowy plik multimedialny, który został wcześniej przekazany, zakodowany oraz pobrany.

## <a name="what-you'll-learn"></a>Zawartość
Ten samouczek przedstawia sposób wykonania następujących zadań:

1. Tworzenie konta usługi Media Services (przy użyciu witryny Azure Portal).
2. Konfigurowanie punktu końcowego przesyłania strumieniowego (przy użyciu witryny Azure Portal).
3. Tworzenie i konfigurowanie projektu programu Visual Studio.
4. Nawiązywanie połączenia z kontem usług Media Services.
5. Tworzenie nowego elementu zawartości i przekazywanie pliku wideo.
6. Kodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów.
7. Publikowanie elementu zawartości i uzyskiwanie adresów URL na potrzeby przesyłania strumieniowego i pobierania progresywnego.
8. Testowanie przez odtwarzanie zawartości.

## <a name="prerequisites"></a>Wymagania wstępne
Następujące elementy są wymagane do wykonania czynności przedstawionych w samouczku.

* Do ukończenia tego samouczka jest potrzebne konto platformy Azure. 
  
    Jeśli go nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Otrzymasz kredyt, który można wykorzystać do wypróbowania płatnych usług Azure. Nawet po wyczerpaniu kredytu możesz zachować konto i korzystać z bezpłatnych usług i funkcji platformy Azure, takich jak funkcja Web Apps w usłudze Azure App Service.
* Systemy operacyjne: Windows 8 lub nowszy, Windows 2008 R2, Windows 7.
* .NET Framework 4.0 lub nowszy
* Visual Studio 2010 z dodatkiem SP1 (Professional, Premium, Ultimate lub Express) lub nowszy.

## <a name="download-sample"></a>Pobieranie przykładu
Pobierz i uruchom próbkę [z tego miejsca](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Tworzenie konta usługi Azure Media Services za pomocą witryny Azure Portal
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

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Konfigurowanie punktów końcowych przesyłania strumieniowego przy użyciu witryny Azure Portal
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

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio
1. Utwórz nową aplikację konsoli w języku C# w programie Visual Studio 2013, Visual Studio 2012 lub Visual Studio 2010 SP1. Uzupełnij informacje w polach **Nazwa**, **Lokalizacja** i **Nazwa rozwiązania**, a następnie kliknij przycisk **OK**.
2. Użyj pakietu NuGet [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions), aby zainstalować **rozszerzenia zestawu .NET SDK usługi Azure Media Services**.  Rozszerzenia .NET SDK usługi Media Services to zestaw metod rozszerzeń i funkcji pomocniczych, które pozwalają uprościć kod i ułatwiają pracę z usługą Media Services. Podczas instalacji tego pakietu instalowany jest również zestaw **.NET SDK usługi Media Services** oraz dodawane są wszystkie inne wymagane zależności.
3. Dodaj odwołanie do zestawu System.Configuration. Ten zestaw zawiera klasę **System.Configuration.ConfigurationManager**, która jest używana na potrzeby uzyskiwania dostępu do plików konfiguracyjnych, np. App.config.
4. Otwórz plik App.config (dodaj plik do projektu, jeśli nie został dodany domyślnie) i dodaj do pliku sekcję *appSettings*. Ustaw wartości dla nazwy i klucza konta usługi Azure Media Services, jak pokazano w poniższym przykładzie. Aby uzyskać nazwę konta i informacje o kluczu, otwórz klasyczny portal Azure, wybierz konto usługi Media Services, a następnie kliknij przycisk **ZARZĄDZAJ KLUCZAMI**.
   
        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
   
        </configuration>
5. Zastąp istniejące instrukcje **using** na początku pliku Program.cs następującym kodem.
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
6. Utwórz nowy folder w katalogu projektów i skopiuj plik MP4 lub wmv, który ma zostać zakodowany i przesłany strumieniowo lub pobrany progresywnie. W tym przykładzie użyto ścieżki „C:\VideoFiles”.

## <a name="connect-to-the-media-services-account"></a>Nawiązywanie połączenia z kontem usługi Media Services
Podczas korzystania z usługi Media Services z użyciem platformy .NET należy użyć klasy **CloudMediaContext** do większości zadań programowania usługi Media Services, takich jak nawiązywanie połączenia z kontem usługi Media Services oraz tworzenie, aktualizowanie, usuwanie i uzyskiwanie dostępu do następujących obiektów: elementów zawartości, plików elementów zawartości, zadań, zasad dostępu, lokalizatorów itp.

Zastąp domyślną klasę Program poniższym kodem. Kod przedstawia sposób odczytywania wartości połączenia z pliku App.config oraz sposób tworzenia obiektu **CloudMediaContext** na potrzeby połączenia z usługą Media Services. Aby uzyskać więcej informacji na temat nawiązywania połączenia z usługą Media Services, zobacz temat [Nawiązywanie połączenia z usługą Media Services przy użyciu zestawu SDK usługi Media Services dla programu .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

Funkcja **Main** wywołuje metody, które będą zdefiniowane w dalszej części tej sekcji.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Tworzenie nowego elementu zawartości i przekazywanie pliku wideo
Za pomocą usługi Media Services można przekazać (lub pozyskać) pliki cyfrowe do elementu zawartości. Obiekt **Element zawartości** może zawierać pliki wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów (oraz metadane dotyczące tych plików).  Po przekazaniu plików zawartość jest bezpiecznie przechowywana w chmurze, na potrzeby dalszego przetwarzania i przesyłania strumieniowego. Pliki w elementach zawartości są nazywane **plikami elementów zawartości**.

Zdefiniowana poniżej metoda **UploadFile** wywołuje metodę **CreateFromFile** (zdefiniowaną w rozszerzeniach zestawu SDK programu .NET). Metoda **CreateFromFile** tworzy nowy element zawartości, do którego jest przekazywany określony plik źródłowy.

Metoda **CreateFromFile** przyjmuje opcje **AssetCreationOptions**, które pozwalają określić jedną z poniższych opcji tworzenia elementów zawartości:

* **None** — szyfrowanie nie jest stosowane. Jest to wartość domyślna. Należy pamiętać, że w przypadku korzystania z tej opcji zawartość nie jest chroniona w trakcie przesyłania lub przechowywania w magazynie.
  Jeśli planujesz dostarczać zawartość w formacie MP4 przy użyciu pobierania progresywnego, użyj tej opcji.
* **StorageEncrypted** — ta opcja umożliwia lokalne szyfrowanie wyczyszczonej zawartości przy użyciu szyfrowania 256-bitowego Advanced Encryption Standard (AES), a następnie przekazanie jej do usługi Azure Storage, gdzie jest przechowywana w formie zaszyfrowanej. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie odszyfrowywane i umieszczane w systemie szyfrowania plików przed kodowaniem, a także opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Pierwotnym zastosowaniem szyfrowania magazynu jest zabezpieczenie za pomocą silnego szyfrowania wysokiej jakości multimedialnych plików wejściowych przechowywanych na dysku.
* **CommonEncryptionProtected** — za pomocą tej opcji można przekazać zawartość, która jest już zaszyfrowana i chroniona za pomocą wspólnego szyfrowania lub technologii PlayReady DRM (np. pliki Smooth Streaming chronione za pomocą technologii PlayReady DRM).
* **EnvelopeEncryptionProtected** — ta opcja umożliwia przekazanie plików w formacie HLS zaszyfrowanych z użyciem standardu AES. Należy pamiętać, że pliki muszą być zakodowane i zaszyfrowane za pomocą narzędzia Transform Manager.

Metoda **CreateFromFile** pozwala także określić wywołanie zwrotne w celu raportowania postępu przekazywania pliku.

W poniższym przykładzie dla opcji elementu zawartości określono wartość **None**.

Dodaj następującą metodę do klasy Program.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Kodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów
Po pozyskaniu elementów zawartości do usługi Media Services pliki multimedialne przed dostarczeniem do klientów mogą zostać zakodowane, poddane transmultipleksacji, oznaczone znakiem wodnym itp. Te działania są zaplanowane i uruchamiane w wielu wystąpieniach ról w tle, aby zapewnić wysoką wydajność oraz dostępność. Te działania są nazywane zadaniami, a każde zadanie składa się z niepodzielnych podzadań, które wykonują rzeczywistą pracę w pliku elementu zawartości.

Jak wspomniano wcześniej, podczas pracy z usługą Azure Media Services jednym z najbardziej typowych scenariuszy jest dostarczanie do klientów transmisji strumieniowej z adaptacyjną szybkością transmisji bitów. Usługa Media Services, korzystając z funkcji dynamicznego tworzenia pakietów, może utworzyć pakiet zestawu plików MP4 z adaptacyjną szybkością transmisji bitów w jednym z następujących formatów: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH i HDS (tylko dla licencjobiorców Adobe PrimeTime/Access).

Aby skorzystać z funkcji dynamicznego tworzenia pakietów, należy wykonać następujące czynności:

* Zakoduj lub transkoduj plik (źródłowy) mezzanine do zestawu plików MP4 lub Smooth Streaming z adaptacyjną szybkością transmisji bitów.  
* Pobierz co najmniej jedną jednostkę przesyłania strumieniowego dla punktu końcowego przesyłania strumieniowego, z którego planujesz dostarczać zawartość.

Poniższy kod ilustruje sposób przesyłania zadania kodowania. Zadanie zawiera jedno zadanie podrzędne, która określa transkodowanie pliku mezzanine do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów przy użyciu **standardu kodera multimediów**. Kod przesyła zadanie i oczekuje na ukończenie działania.

Po zakończeniu zadania użytkownik będzie mógł przesłać strumieniowo element zawartości lub pobrać progresywnie pliki MP4, które zostały utworzone w wyniku transkodowania.
Należy pamiętać, że do pobierania progresywnego plików MP4 nie potrzeba więcej niż 0 jednostek przesyłania strumieniowego.

Dodaj następującą metodę do klasy Program.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publikowanie elementu zawartości i uzyskiwanie adresów URL na potrzeby pobierania stopniowego oraz przesyłania progresywnego
Aby przesłać strumieniowo lub pobrać element zawartości, należy go najpierw opublikować, tworząc lokalizator. Lokalizatory zapewniają dostęp do plików znajdujących się w elemencie zawartości. Usługa Media Services obsługuje dwa typy lokalizatorów: lokalizatory OnDemandOrigin używane do strumieniowego przesyłania plików multimedialnych (na przykład w formacie MPEG DASH, HLS i Smooth Streaming) oraz lokalizatory sygnatury dostępu współdzielonego (SAS) używane do pobierania plików multimedialnych.

Po utworzeniu lokalizatorów można tworzyć adresy URL, które są używane do strumieniowego przesyłania lub pobierania plików.

Adres URL dla protokołu Smooth Streaming ma następujący format:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Adres URL dla protokołu HLS ma następujący format:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Adres URL dla protokołu MPEG DASH ma następujący format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Adres URL SAS używany do pobierania plików ma następujący format:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Rozszerzenia zestawu .NET SDK usługi Media Services zawierają wygodne metody pomocnicze, które zwracają sformatowane adresy URL dla opublikowanego elementu zawartości.

W poniższym kodzie użyto rozszerzeń zestawu .NET SDK, aby utworzyć lokalizatory i pobrać adresy URL przesyłania strumieniowego oraz pobierania progresywnego. Kod przedstawia również sposób pobierania plików do folderu lokalnego.

Dodaj następującą metodę do klasy Program.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

## <a name="test-by-playing-your-content"></a>Testowanie przez odtwarzanie zawartości
Po uruchomieniu programu zdefiniowanego w poprzedniej sekcji w oknie konsoli zostanie wyświetlony adres URL podobny do poniższego.

Adresy URL przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów:

Smooth Streaming

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

Adresy URL pobierania progresywnego (audio i wideo):

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Do przesyłania strumieniowego zawartości wideo użyj [odtwarzacza usługi Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Aby przetestować pobieranie progresywne, wklej adres URL do przeglądarki (np. Internet Explorer, Chrome lub Safari).

## <a name="next-steps:-media-services-learning-paths"></a>Następne kroki: ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else?"></a>Szukasz czegoś innego?
Jeśli ten temat nie zawiera oczekiwanych treści, brakuje w nim informacji lub w inny sposób nie spełnia Twoich potrzeb, podziel się z nami swoją opinią w wątku Disqus poniżej.

<!-- Anchors. -->


<!-- URLs. -->
[Instalator platformy sieci Web]: http://go.microsoft.com/fwlink/?linkid=255386
[Portal]: http://manage.windowsazure.com/



<!--HONumber=Oct16_HO3-->


