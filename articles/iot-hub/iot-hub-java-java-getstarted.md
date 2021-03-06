---
title: Usługa Azure IoT Hub dla środowiska Java — wprowadzenie | Microsoft Docs
description: Samouczek z wprowadzeniem do usługi Azure IoT Hub dla środowiska Java. Użycie usługi Azure IoT Hub i środowiska Java z zestawami SDK Microsoft Azure IoT w celu zaimplementowania rozwiązania Internetu rzeczy.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2016
ms.author: dobett

---
# Usługa Azure IoT Hub dla środowiska Java — wprowadzenie
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Na końcu tego samouczka będziesz mieć trzy aplikacje konsolowe Java:

* **create-device-identity** tworzy tożsamość urządzenia i skojarzony klucz zabezpieczeń do podłączenia urządzenia symulowanego.
* **read-d2c-messages** powoduje wyświetlenie telemetrii wysyłanej przez urządzenie symulowane.
* **simulated-device** łączy się z centrum IoT przy użyciu tożsamości urządzenia utworzonej wcześniej i wysyła komunikat telemetrii co sekundę przy użyciu protokołu AMQPS.

> [!NOTE]
> Artykuł [IoT Hub SDKs](Zestawy SDK usługi IoT Hub.md)[lnk-hub-sdks] zawiera informacje dotyczące różnych zestawów SDK, których można użyć do utworzenia aplikacji zarówno do uruchamiania na urządzaniach, jak i w zapleczu rozwiązania.
> 
> 

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Java SE 8. <br/> W artykule [Prepare your development environment](Przygotowanie środowiska projektowego.md)[lnk-dev-setup] opisano, jak zainstalować środowisko Java na potrzeby tego samouczka w systemie Windows lub Linux.
* Maven 3.  <br/> W artykule [Prepare your development environment](Przygotowanie środowiska projektowego.md)[lnk-dev-setup] opisano, jak zainstalować narzędzie Maven na potrzeby tego samouczka w systemie Windows lub Linux.
* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure][lnk_free_trial].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

W ostatnim kroku zanotuj wartość **Klucz podstawowy**, a następnie kliknij przycisk **Komunikaty**. W bloku **Obsługa wiadomości** zanotuj **nazwę zgodną z centrum zdarzeń** oraz **punkt końcowy zgodny z centrum zdarzeń**. Te trzy wartości będą potrzebne podczas tworzenia aplikacji **read-d2c-messages**.

![Blok komunikatów usługi IoT Hub witrynie Azure Portal][6]

Utworzono centrum IoT Hub i masz nazwę hosta centrum IoT Hub, jego parametry połączenia, klucz podstawowy, nazwę zgodną z usługą Event Hubs i punkt końcowy zgodny z usługą Event Hubs, potrzebne do wykonania tego samouczka.

## Tworzenie tożsamości urządzenia
W tej sekcji utworzysz aplikację konsolową Java, która tworzy nową tożsamość urządzenia w rejestrze tożsamości w centrum IoT Hub. Urządzenie nie może połączyć się z centrum IoT, jeśli nie ma wpisu w rejestrze tożsamości urządzeń. Więcej informacji znajduje się w sekcji **Device Identity Registry** (Rejestr tożsamości urządzenia) w artykule [IoT Hub Developer Guide](Usługa IoT Hub — przewodnik dewelopera.md)[lnk-devguide-identity]. Po uruchomieniu ta aplikacja konsolowa generuje unikatowy identyfikator i klucz urządzenia, których urządzenie może użyć do zidentyfikowania się podczas wysyłania komunikatów do chmury do usługi IoT Hub.

1. Utwórz nowy pusty folder o nazwie iot-java-get-started. W folderze iot-java-get-started utwórz nowy projekt narzędzia Maven o nazwie **create-device-identity** za pomocą następującego polecenia w wierszu polecenia. Zwróć uwagę, że jest to jedno długie polecenie:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. W wierszu polecenia przejdź do nowo utworzonego folderu create-device-identity. 
3. Za pomocą edytora tekstów otwórz plik pom.xml w folderze create-device-identity i dodaj następującą zależność do węzła **zależności**. Dzięki temu możesz użyć w aplikacji pakietu iothub-service-sdk:
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
4. Zapisz i zamknij plik pom.xml.
5. Za pomocą edytora tekstów otwórz plik create-device-identity\src\main\java\com\mycompany\app\App.java.
6. Dodaj do pliku następujące instrukcje **importowania**:
   
    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;
   
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```
7. Dodaj następujące zmienne na poziomie klasy do klasy **App**, zastępując ciąg **{yourhubconnectionstring}** zanotowaną wcześniej wartością:
   
    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
   
    ```
8. Zmodyfikuj podpis metody **main**, aby uwzględnić poniższe wyjątki:
   
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
9. Dodaj następujący kod jako treść metody **głównej**. Ten kod tworzy urządzenie o nazwie *javadevice* w rejestrze tożsamości usługi IoT Hub, o ile takie już nie istnieje. Następnie wyświetla identyfikator i klucz urządzenia, które będą potrzebne później:
   
    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);
   
    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```
10. Zapisz i zamknij plik App.java.
11. Aby utworzyć aplikację **create-device-identity** przy użyciu narzędzia Maven, wykonaj następujące polecenie w wierszu polecenia w folderze create-device-identity:
    
    ```
    mvn clean package -DskipTests
    ```
12. Aby uruchomić aplikację **create-device-identity** przy użyciu narzędzia Maven, wykonaj następujące polecenie w wierszu polecenia w folderze create-device-identity:
    
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
13. Zanotuj **identyfikator urządzenia** i **klucz urządzenia**. Będą potrzebne później podczas tworzenia aplikacji, która łączy się z usługą IoT Hub jako urządzenie.

> [!NOTE]
> Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia tylko po to, aby umożliwić bezpieczny dostęp do centrum. Przechowuje identyfikatory i klucze urządzeń, które będą używane jako poświadczenia zabezpieczeń, oraz flagę włączone/wyłączone, która umożliwia wyłączenie dostępu do poszczególnych urządzeń. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Więcej informacji znajduje się w artykule [IoT Hub Developer Guide](Usługa IoT Hub — przewodnik dewelopera.md)[lnk-devguide-identity].
> 
> 

## Odbieranie komunikatów z urządzenia do chmury
W tej sekcji opisano tworzenie aplikacji konsolowej Java, która odczytuje komunikaty z urządzenia do chmury z usługi IoT Hub. Usługa IoT Hub udostępnia punkt końcowy zgodny z [centrum zdarzeń][lnk-event-hubs-overview], aby umożliwić odczytywanie komunikatów z urządzenia do chmury. W celu uproszczenia informacji instrukcje w samouczku dotyczą tworzenia czytnika podstawowego, który nie jest odpowiedni do wdrożenia z użyciem dużej przepustowości. W samouczku [Process device-to-cloud messages](Przetwarzanie komunikatów z urządzenia do chmury.md)[lnk-process-d2c-tutorial] przedstawiono sposób przetwarzania komunikatów z urządzenia do chmury na dużą skalę. Samouczek [Rozpoczynanie pracy z usługą Event Hubs][lnk-eventhubs-tutorial] zawiera dalsze informacje dotyczące sposobu przetwarzania komunikatów z usługi Event Hubs i dotyczy punktów końcowych usługi IoT Hub zgodnych z usługą Event Hubs.

> [!NOTE]
> Punkt końcowy zgodny z usługą Event Hubs do odczytywania komunikatów z urządzenia do chmury zawsze korzysta z protokołu AMQPS.
> 
> 

1. W folderze iot-java-get-started utworzonym w sekcji *Tworzenie tożsamości urządzenia* utwórz nowy projekt narzędzia Maven o nazwie **read-d2c-messages** przy użyciu następującego polecenia w wierszu polecenia. Zwróć uwagę, że jest to jedno długie polecenie:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. W wierszu polecenia przejdź do nowego folderu read-d2c-messages.
3. Za pomocą edytora tekstów otwórz plik pom.xml w folderze read-d2c-messages i dodaj następującą zależność do węzła **zależności**. Pozwala to na użycie pakietu eventhubs-client w aplikacji do odczytywania z punktu końcowego zgodnego z usługą Event Hubs:
   
    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```
4. Zapisz i zamknij plik pom.xml.
5. Za pomocą edytora tekstów otwórz plik read-d2c-messages\src\main\java\com\mycompany\app\App.java.
6. Dodaj do pliku następujące instrukcje **importowania**:
   
    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
   
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```
7. Dodaj następujące zmienne na poziomie klasy do klasy **App**. Zastąp ciągi **{youriothubkey}**, **{youreventhubcompatibleendpoint}** i **{youreventhubcompatiblename}** wartościami zanotowanymi wcześniej:
   
    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```
8. Dodaj następującą metodę **receiveMessages** do klasy **App**. Ta metoda tworzy wystąpienie **EventHubClient** do nawiązywania połączenia z punktem końcowym zgodnym z usługą Event Hubs, a następnie asynchronicznie tworzy wystąpienie **PartitionReceiver** do odczytywania z partycji centrum zdarzeń. Działa w sposób ciągły w pętli i drukuje szczegóły komunikatu aż do zakończenia aplikacji.
   
    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```
   
   > [!NOTE]
   > Ta metoda używa filtru podczas tworzenia odbiornika, więc odbiornik odczytuje tylko komunikaty wysyłane do usługi IoT Hub po uruchomieniu odbiornika. Jest to przydatne w środowisku testowym, gdyż dzięki temu jest wyświetlany bieżący zestaw komunikatów. W środowisku produkcyjnym kod powinien sprawdzać, czy są przetwarzane wszystkie komunikaty — więcej informacji znajduje się w samouczku [How to process IoT Hub device-to-cloud messages][lnk-process-d2c-tutorial] (Jak przetwarzać komunikaty z urządzenia do chmury w usłudze IoT Hub).
   > 
   > 
9. Zmodyfikuj podpis metody **main**, aby uwzględnić poniższy wyjątek:
   
    ```
    public static void main( String[] args ) throws IOException
    ```
10. Dodaj następujący kod do metody **głównej** w klasie **App**. Ten kod tworzy dwa wystąpienia **EventHubClient** i **PartitionReceiver** wystąpień i umożliwia zamknięcie aplikacji po zakończeniu przetwarzania komunikatów:
    
    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```
    
    > [!NOTE]
    > W przypadku tego kodu przyjmuje się założenie, że usługa IoT Hub została utworzona w (bezpłatnej) warstwie F1. Bezpłatne centrum IoT ma dwie partycje o nazwie „0” i „1”.
    > 
    > 
11. Zapisz i zamknij plik App.java.
12. Aby utworzyć aplikację **read-d2c-messages** przy użyciu narzędzia Maven, wykonaj następujące polecenie w wierszu polecenia w folderze read-d2c-messages:
    
    ```
    mvn clean package -DskipTests
    ```

## Tworzenie aplikacji symulowanego urządzenia
Ta sekcja zawiera instrukcje dotyczące tworzenia aplikacji konsolowej Java, która symuluje urządzenie wysyłające komunikaty z urządzenia do chmury do usługi IoT Hub.

1. W folderze iot-java-get-started utworzonym w sekcji *Tworzenie tożsamości urządzenia* utwórz nowy projekt narzędzia Maven o nazwie **simulated-device** przy użyciu następującego polecenia w wierszu polecenia. Zwróć uwagę, że jest to jedno długie polecenie:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. W wierszu polecenia przejdź do nowego folderu simulated-device.
3. Za pomocą edytora tekstów otwórz plik pom.xml w folderze simulated-device i dodaj następujące zależności do węzła **zależności**. Pozwala to na użycie pakietu iothub-java-client w aplikacji do komunikowania się z centrum IoT i serializacji obiektów Java do formatu JSON:
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```
4. Zapisz i zamknij plik pom.xml.
5. Za pomocą edytora tekstów otwórz plik simulated-device\src\main\java\com\mycompany\app\App.java.
6. Dodaj do pliku następujące instrukcje **importowania**:
   
    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```
7. Dodaj następujące zmienne na poziomie klasy do klasy **App**, zastępując ciąg **{youriothubname}** nazwą centrum IoT Hub oraz ciąg **{yourdevicekey}** wartością klucza urządzenia wygenerowaną w sekcji *Tworzenie tożsamości urządzenia*:
   
    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    Ta przykładowa aplikacja używa zmiennej **protocol** podczas tworzenia wystąpienia obiektu **DeviceClient**. Do komunikacji z usługą IoT Hub możesz użyć protokołu HTTPS lub AMQPS.
8. Dodaj następującą zagnieżdżoną klasę **TelemetryDataPoint** do wnętrza klasy **App**, aby określić dane telemetryczne wysyłane przez urządzenie do centrum IoT:
   
    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
   
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```
9. Dodaj następującą zagnieżdżoną klasę **EventCallback** do klasy **App**, aby wyświetlić stan potwierdzenia zwracany przez centrum IoT podczas przetwarzania komunikatu z symulowanego urządzenia. Ta metoda powiadamia również wątek główny w aplikacji o przetworzeniu komunikatu:
   
    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
   
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```
10. Dodaj następującą zagnieżdżoną klasę **MessageSender** do klasy **App**. Metoda **run** w tej klasie generuje przykładowe dane telemetrii do wysłania do centrum IoT i oczekuje na potwierdzenie przed wysłaniem nowego komunikatu:
    
    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
    
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
    
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
    
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
    
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```
    
    Ta metoda wysyła nowy komunikat z urządzenia do chmury sekundę po potwierdzeniu poprzedniego komunikatu przez centrum IoT. Komunikat zawiera obiekt serializacji JSON z identyfikatorem urządzenia i generowanym losowo numerem do symulacji czujnika prędkości wiatru.
11. Zastąp metodę **main** następującym kodem, który tworzy wątek do wysyłania komunikatów z urządzenia do chmury do centrum IoT:
    
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();
    
      MessageSender sender = new MessageSender();
    
      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```
12. Zapisz i zamknij plik App.java.
13. Aby utworzyć aplikację **simulated-device** przy użyciu narzędzia Maven, wykonaj następujące polecenie w wierszu polecenia w folderze simulated-device:
    
    ```
    mvn clean package -DskipTests
    ```

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią w artykule z witryny MSDN [Transient Fault Handling](Obsługa przejściowego błędu.md)[lnk-transient-faults].
> 
> 

## Uruchamianie aplikacji
Teraz można uruchomić aplikacje.

1. Z poziomu wiersza polecenia w folderze read-d2c uruchom następujące polecenie, aby rozpocząć monitorowanie pierwszej partycji centrum IoT:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![Aplikacja kliencka usługi IoT Hub dla środowiska Java do monitorowania komunikatów między urządzeniem i chmurą][7]
2. Z poziomu wiersza polecenia w folderze simulateddevice uruchom następujące polecenie, aby rozpocząć wysyłanie danych telemetrycznych do centrum IoT:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![Aplikacja kliencka urządzeń usługi IoT Hub dla środowiska Java do wysyłania komunikatów między urządzeniem i chmurą][8]
3. Na kafelku **Użycie** w [portalu Azure][lnk-portal] wyświetlana jest liczba komunikatów wysłanych do centrum:
   
    ![Kafelek Użycie witryny Azure Portal przedstawiający liczbę komunikatów wysłanych do usługi IoT Hub][43]

## Następne kroki
W tym samouczku opisano konfigurowanie nowego centrum IoT w portalu, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Tożsamość urządzenia została użyta, aby włączyć w aplikacji symulowanego urządzenia funkcję wysyłania komunikatów z urządzenia do chmury do centrum. Utworzono również aplikację, która wyświetla komunikaty odbierane przez centrum. 

Aby kontynuować wprowadzenie do usługi IoT Hub i eksplorowanie innych scenariuszy IoT, zobacz:

* [Łączenie urządzenia][lnk-connect-device]
* [Wprowadzenie do zarządzania urządzeniami][lnk-device-management]
* [Getting started with the Gateway SDK][lnk-gateway-SDK] (Wprowadzenie do korzystania z zestawu SDK bramy)

W samouczku [Process device-to-cloud messages][lnk-process-d2c-tutorial] (Przetwarzanie komunikatów z urządzenia do chmury) przedstawiono, jak rozszerzyć rozwiązanie IoT i przetwarzać komunikaty z urządzenia do chmury na dużą skalę.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/


<!--HONumber=Oct16_HO3-->


