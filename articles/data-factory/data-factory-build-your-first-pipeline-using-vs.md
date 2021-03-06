---
title: Tworzenie pierwszej fabryki danych (Visual Studio) | Microsoft Docs
description: Ten samouczek zawiera instrukcje tworzenia przykładowego potoku dla usługi Azure Data Factory przy użyciu programu Visual Studio.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/17/2016
ms.author: spelluru

---
# <a name="tutorial:-build-your-azure-first-data-factory-using-microsoft-visual-studio"></a>Samouczek: Tworzenie pierwszej fabryki danych Azure przy użyciu programu Microsoft Visual Studio
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Szablon usługi Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [Interfejs API REST](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
> 

Ten artykuł zawiera instrukcje tworzenia pierwszej fabryki danych Azure za pomocą programu Microsoft Visual Studio.

## <a name="prerequisites"></a>Wymagania wstępne
1. Przeczytaj artykuł [Omówienie samouczka](data-factory-build-your-first-pipeline.md) oraz wykonaj kroki **wymagań wstępnych**.
2. Aby publikować jednostki fabryki danych w usłudze Fabryka danych Azure przy użyciu programu Visual Studio, musisz być **administratorem subskrypcji platformy Azure**.
3. Na komputerze muszą być zainstalowane następujące elementy: 
   * Visual Studio 2013 lub Visual Studio 2015
   * Pobierz zestaw Azure SDK dla programu Visual Studio 2013 lub Visual Studio 2015. Przejdź do [strony plików do pobrania Azure](https://azure.microsoft.com/downloads/) i kliknij pozycję **VS 2013** lub **VS 2015** w sekcji **.NET**.
   * Pobierz najnowszą wtyczkę usługi Azure Data Factory dla programu Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) lub [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Możesz także zaktualizować wtyczkę, wykonując następujące czynności: w menu kliknij kolejno opcje **Narzędzia** -> **Rozszerzenia i aktualizacje** -> **Online** -> **Galeria Visual Studio** -> **Narzędzia usługi Fabryka danych Microsoft Azure dla programu Visual Studio** -> **Aktualizuj**. 

Teraz utworzymy fabrykę danych Azure przy użyciu programu Visual Studio. 

## <a name="create-visual-studio-project"></a>Tworzenie projektu programu Visual Studio
1. Uruchom program **Visual Studio 2013** lub **Visual Studio 2015**. Kliknij pozycję **Plik**, wskaż polecenie **Nowy** i kliknij pozycję **Projekt**. Powinno zostać wyświetlone okno dialogowe **Nowy projekt**.  
2. W oknie dialogowym **Nowy projekt** wybierz szablon **DataFactory** i kliknij pozycję **Pusty projekt usługi Fabryka danych**.   
   
    ![Okno dialogowe Nowy projekt](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)
3. Wprowadź **nazwę** dla projektu, **lokalizację** oraz nazwę **rozwiązania** i kliknij przycisk **OK**.
   
    ![Eksplorator rozwiązań](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

## <a name="create-linked-services"></a>Tworzenie połączonych usług
Fabryka danych może obejmować jeden lub wiele potoków. Potok może obejmować jedno lub wiele działań. Na przykład działanie kopiowania w celu kopiowania danych ze źródła do docelowego magazynu danych oraz działanie programu Hive w usłudze HDInsight w celu uruchamiania skryptu programu Hive służącego do przekształcania danych wejściowych. Artykuł [supported data stores](data-factory-data-movement-activities.md##supported-data-stores-and-formats) (Obsługiwane magazyny danych) zawiera listę wszystkich źródeł i ujść obsługiwanych przez działanie kopiowania. Artykuł [compute linked services](data-factory-compute-linked-services.md) (Obliczanie połączonych usług) zawiera listę usług obliczeniowych obsługiwanych przez usługę Data Factory. 

W tym kroku opisano połączenie konta usługi Azure Storage oraz klastra Azure HDInsight na żądanie z fabryką danych. Konto usługi Azure Storage będzie przechowywać dane wejściowe i wyjściowe dla potoku w tym przykładzie. Połączona usługa HDInsight służy do uruchamiania skryptu programu Hive określonego w działaniu potoku w tym przykładzie. Zidentyfikuj usługi magazynu danych/usługi obliczeniowe, które są używane w tym scenariuszu, oraz połącz te usługi z fabryką danych przez utworzenie połączonych usług.  

Nazwę i ustawienia dla fabryki danych określisz później, gdy będziesz publikować rozwiązanie usługi Data Factory.

#### <a name="create-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage
W tym kroku opisano łączenie konta usługi Azure Storage z fabryką danych. Na potrzeby tego samouczka do przechowywania danych wejściowych/wyjściowych oraz pliku skryptu HQL używa się tego samego konta usługi Azure Storage. 

1. Kliknij prawym przyciskiem myszy pozycję **Połączone usługi** w Eksploratorze rozwiązań, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy element**.      
2. W oknie dialogowym **Dodawanie nowego elementu** wybierz z listy pozycję **Połączona usługa Azure Storage** i kliknij przycisk **Dodaj**. 
3. Zastąp wartości **accountname** i **accountkey** nazwą konta magazynu Azure oraz jego kluczem. Informacje na temat pobierania klucza dostępu do magazynu znajdują się w artykule [Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu do magazynu](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
   
    ![Połączona usługa Azure Storage](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)
4. Zapisz plik **AzureStorageLinkedService1.json**.

#### <a name="create-azure-hdinsight-linked-service"></a>Tworzenie połączonej usługi Azure HDInsight
W tym kroku przedstawiono łączenie klastra usługi HDInsight na żądanie z fabryką danych. Klaster usługi HDInsight jest automatycznie tworzony w czasie wykonywania oraz usuwany po zakończeniu przetwarzania i określonym czasie bezczynności. Możesz użyć własnego klastra usługi HDInsight zamiast klastra usługi HDInsight na żądanie. Szczegółowe informacje znajdują się w artykule [Compute Linked Services](data-factory-compute-linked-services.md) (Połączone usługi obliczeniowe). 

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Połączone usługi**, wskaż polecenie **Dodaj** i kliknij opcję **Nowy element**.
2. Wybierz pozycję **Połączona usługa HDInsight na żądanie** i kliknij przycisk **Dodaj**. 
3. Zastąp kod **JSON** następującym:
   
        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "AzureStorageLinkedService1"
            }
          }
        }
   
    Poniższa tabela zawiera opis właściwości kodu JSON użytych w tym fragmencie kodu:
   
   | Właściwość | Opis |
   | --- | --- |
   |  Wersja |Oznacza, że wersja utworzonej usługi HDInsight to 3.2. |
   |  ClusterSize |Określa rozmiar klastra usługi HDInsight. |
   |  TimeToLive |Określa czas bezczynności, po którym klaster usługi HDInsight zostanie usunięty. |
   |  linkedServiceName |Określa konto magazynu używane do przechowywania dzienników generowanych w usłudze HDInsight. |
   
    Pamiętaj o następujących kwestiach: 
   
   * Fabryka danych tworzy klaster usługi HDInsight **oparty na systemie Windows** za pomocą powyższego kodu JSON. Możliwe jest również utworzenie klastra usługi HDInsight **opartego na systemie Linux**. Szczegółowe informacje znajdują się w artykule [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Połączona usługa HDInsight na żądanie). 
   * Możesz użyć **własnego klastra usługi HDInsight** zamiast klastra usługi HDInsight na żądanie. Szczegółowe informacje znajdują się w artykule [HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) (Połączona usługa HDInsight).
   * Klaster usługi HDInsight tworzy **kontener domyślny** w magazynie obiektów blob określonym w kodzie JSON (**linkedServiceName**). Usługa HDInsight nie powoduje usunięcia tego kontenera w przypadku usunięcia klastra. To zachowanie jest celowe. W przypadku połączonej usługi HDInsight na żądanie klaster usługi HDInsight jest tworzony przy każdym przetwarzaniu wycinka — o ile w tym momencie nie istnieje aktywny klaster (**timeToLive**). Klaster jest automatycznie usuwany po zakończeniu przetwarzania.
     
       Po przetworzeniu większej liczby wycinków w usłudze Azure Blob Storage będzie widocznych wiele kontenerów. Jeśli nie są potrzebne do rozwiązywania problemów z zadaniami, można je usunąć, aby zmniejszyć koszt przechowywania. Nazwy tych kontenerów są zgodne ze wzorcem: „adf**twojanazwafabrykidanych**-**nazwapołączonejusługi**-znacznikdatygodziny”. Aby usunąć kontenery z magazynu obiektów blob Azure, użyj takich narzędzi, jak [Microsoft Storage Explorer](http://storageexplorer.com/).
     
     Szczegółowe informacje znajdują się w artykule [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Połączona usługa HDInsight na żądanie). 
4. Zapisz plik **HDInsightOnDemandLinkedService1.json**.

## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym kroku opisano tworzenie zestawów danych do reprezentowania danych wejściowych i wyjściowych na potrzeby przetwarzania przy użyciu programu Hive. Te zestawy danych dotyczą elementu **AzureStorageLinkedService1** utworzonego wcześniej w ramach tego samouczka. Połączona usługa wskazuje konto usługi Azure Storage, a zestawy danych określają kontener, folder i nazwę pliku w magazynie, w którym przechowywane są dane wejściowe i wyjściowe.   

#### <a name="create-input-dataset"></a>Tworzenie wejściowego zestawu danych
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Tabele**, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy element**. 
2. Wybierz pozycję **Obiekt blob platformy Azure** z listy, zmień nazwę pliku na **InputDataSet.json** i kliknij przycisk **Dodaj**.
3. Zastąp kod **JSON** w edytorze następującym: 
   
    Za pomocą tego fragmentu kodu JSON tworzysz zestaw danych o nazwie **AzureBlobInput**, który reprezentuje dane wejściowe dla działania w potoku. Ponadto określasz, że dane wejściowe znajdują się w kontenerze obiektów blob o nazwie **adfgetstarted** oraz folderze o nazwie **inputdata**
   
        {
            "name": "AzureBlobInput",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "AzureStorageLinkedService1",
                "typeProperties": {
                    "fileName": "input.log",
                    "folderPath": "adfgetstarted/inputdata",
                    "format": {
                        "type": "TextFormat",
                        "columnDelimiter": ","
                    }
                },
                "availability": {
                    "frequency": "Month",
                    "interval": 1
                },
                "external": true,
                "policy": {}
            }
        } 
   
    Poniższa tabela zawiera opis właściwości kodu JSON użytych w tym fragmencie kodu:
   
   | Właściwość | Opis |
   |:--- |:--- |
   | type |Właściwość type jest ustawiona na wartość AzureBlob, ponieważ dane znajdują się w magazynie obiektów blob Azure. |
   | linkedServiceName |Odnosi się do elementu AzureStorageLinkedService1 utworzonego wcześniej. |
   | fileName |Ta właściwość jest opcjonalna. Jeśli tę właściwość pominiesz, zostaną wybrane wszystkie pliki z folderu folderPath. W tym przypadku zostanie przetworzony tylko plik input.log. |
   | type |Pliki dziennika są w formacie tekstowym, więc używana jest wartość TextFormat. |
   | columnDelimiter |Kolumny w plikach dziennika są rozdzielane przecinkami (,) |
   | frequency/interval |Właściwość frequency (częstotliwość) jest ustawiona na wartość Month (Miesiąc), a wartość interwału wynosi 1, co oznacza, że wycinki wejściowe są dostępne co miesiąc. |
   | external |Ta właściwość ma wartość true (prawda), jeśli dane wejściowe nie są generowane przez usługę Fabryka danych. |
4. Zapisz plik **InputDataset.json**. 

#### <a name="create-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Teraz utworzysz wyjściowy zestaw danych do reprezentowania danych wyjściowych przechowywanych w usłudze Azure Blob Storage. 

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Tabele**, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy element**. 
2. Wybierz pozycję **Obiekt blob platformy Azure** z listy, zmień nazwę pliku na **OutputDataset.json** i kliknij przycisk **Dodaj**. 
3. Zastąp kod **JSON** w edytorze następującym: 
   
    Za pomocą tego fragmentu kodu JSON tworzysz zestaw danych o nazwie **AzureBlobOutput** i określasz strukturę danych, które są generowane przy użyciu skryptu programu Hive. Ponadto określasz, że wyniki są przechowywane w kontenerze obiektów blob o nazwie **adfgetstarted** oraz folderze o nazwie **partitioneddata**. W sekcji **availability** (dostępność) określono, że wyjściowy zestaw danych jest generowany co miesiąc.
   
        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
              "folderPath": "adfgetstarted/partitioneddata",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Month",
              "interval": 1
            }
          }
        }
   
    Opisy tych właściwości znajdują się w części **Tworzenie wejściowego zestawu danych**. Dla wyjściowego zestawu danych nie ustawia się właściwości external (zewnętrzne), gdyż zestaw danych jest generowany w usłudze Fabryka danych.
4. Zapisz plik **OutputDataset.json**.

### <a name="create-pipeline"></a>Tworzenie potoku
W tym kroku opisano tworzenie pierwszego potoku za pomocą działania **HDInsightHive**. Wycinek danych wejściowych jest dostępny co miesiąc (frequency: Month, interval: 1), wycinek danych wyjściowych jest generowany co miesiąc, a właściwość scheduler dla działania jest również ustawiona na wartość miesięczną. Ustawienia dla wyjściowego zestawu danych i harmonogramu działania muszą być zgodne. W tym przypadku wyjściowy zestaw danych jest elementem wpływającym na ustawienia harmonogramu, więc musisz utworzyć wyjściowy zestaw danych nawet wtedy, gdy działanie nie generuje żadnych danych wyjściowych. Jeśli w działaniu nie są używane żadne dane wejściowe, możesz pominąć tworzenie zestawu danych wejściowych. Właściwości użyte w poniższym fragmencie kodu JSON zostały opisane na końcu tej sekcji.

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Potoki**, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy element**. 
2. Wybierz pozycję **Potok przekształcenia programu Hive** z listy i kliknij przycisk **Dodaj**. 
3. Zastąp kod **JSON** następującym fragmentem kodu.
   
   > [!IMPORTANT]
   > Zastąp wartość **storageaccountname** nazwą konta magazynu.
   > 
   > 
   
        {
            "name": "MyFirstPipeline",
            "properties": {
                "description": "My first Azure Data Factory pipeline",
                "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                            "scriptLinkedService": "AzureStorageLinkedService1",
                            "defines": {
                                "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                                "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                            }
                        },
                        "inputs": [
                            {
                                "name": "AzureBlobInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "AzureBlobOutput"
                            }
                        ],
                        "policy": {
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Month",
                            "interval": 1
                        },
                        "name": "RunSampleHiveActivity",
                        "linkedServiceName": "HDInsightOnDemandLinkedService"
                    }
                ],
                "start": "2016-04-01T00:00:00Z",
                "end": "2016-04-02T00:00:00Z",
                "isPaused": false
            }
        }
   
    Ten fragment kodu JSON służy do utworzenia potoku obejmującego jedno działanie, które korzysta z programu Hive do przetwarzania danych w klastrze usługi HDInsight.
   
    Ten fragment kodu JSON służy do utworzenia potoku obejmującego jedno działanie, które korzysta z programu Hive do przetwarzania danych w klastrze usługi HDInsight.
   
    Plik skryptu programu Hive **partitionweblogs.hql** jest przechowywany na koncie magazynu Azure (określonym za pomocą elementu scriptLinkedService o nazwie **AzureStorageLinkedService1**) oraz w folderze **script** w kontenerze **adfgetstarted**.
   
    Sekcja **defines** służy do określania ustawień środowiska uruchomieniowego, które są przekazywane do skryptu programu Hive w formie wartości konfiguracyjnych programu Hive (np. ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).
   
    Właściwości **start** i **end** potoku określają aktywny okres potoku.
   
    W kodzie JSON dotyczącym działania określasz, że skrypt programu Hive jest uruchamiany w usłudze obliczeniowej określonej przez właściwość **linkedServiceName** — **HDInsightOnDemandLinkedService**.
   
   > [!NOTE]
   > Artykuł [Anatomy of a Pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline) (Anatomia potoku) zawiera szczegółowe informacje dotyczące właściwości kodu JSON użytych w tym przykładzie. 
   > 
   > 
4. Zapisz plik **HiveActivity1.json**.

### <a name="add-partitionweblogs.hql-and-input.log-as-a-dependency"></a>Dodawanie plików partitionweblogs.hql i input.log jako zależności
1. Kliknij prawym przyciskiem myszy pozycję **Zależności** w oknie **Eksplorator rozwiązań**, wskaż polecenie **Dodaj** i kliknij pozycję **Istniejący element**.  
2. Przejdź do folderu **C:\ADFGettingStarted** i wybierz pliki **partitionweblogs.hql** **input.log**, a następnie kliknij przycisk **Dodaj**. Te dwa pliki zostały utworzone w ramach wymagań wstępnych z [Omówienia samouczka](data-factory-build-your-first-pipeline.md).

Podczas publikowania rozwiązania w następnym kroku plik **partitionweblogs.hql** zostanie przekazany do folderu skryptów w kontenerze obiektów blob **adfgetstarted**.   

### <a name="publish/deploy-data-factory-entities"></a>Publikowanie/wdrażanie jednostek usługi Fabryka danych
1. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, a następnie kliknij polecenie **Publikuj**. 
2. Jeśli zostanie wyświetlone okno dialogowe **Logowanie na koncie Microsoft** wprowadź poświadczenia dla konta z subskrypcją Azure i kliknij przycisk **Zaloguj**.
3. Powinno zostać wyświetlone następujące okno dialogowe:
   
   ![Okno dialogowe publikowania](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
4. Na stronie konfigurowania fabryki danych wykonaj następujące czynności: 
   
   1. Zaznacz opcję **Utwórz nową fabrykę danych**.
   2. Wprowadź unikatową **nazwę** fabryki danych. Na przykład: **FirstDataFactoryUsingVS09152016**. Nazwa musi być unikatowa w skali globalnej.  

        > [AZURE.IMPORTANT] Jeśli podczas publikowania wystąpi błąd **Nazwa fabryki danych „FirstDataFactoryUsingVS” jest niedostępna**, zmień nazwę (np. TwojaNazwaFirstDataFactoryUsingVS). Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.
1. Wybierz odpowiednią subskrypcję w polu **Subskrypcja**.

        > [AZURE.IMPORTANT] Jeśli nie jest widoczna żadna subskrypcja, upewnij się, że do logowania zostało użyte konto o uprawnieniach administratora lub współadministratora subskrypcji.  

    4. Wybierz **grupę zasobów** dla fabryki danych, która ma być utworzona. 
    5. Wybierz **region** dla fabryki danych. 
    6. Kliknij przycisk **Dalej**, aby przejść na stronę **Publikowanie elementów**. (Naciśnij przycisk **TAB**, aby wyjść z pola nazwy, jeśli przycisk **Dalej** jest wyłączony). 
1. Na stronie **Publikowanie elementów** upewnij się, że wszystkie jednostki usługi Fabryka danych zostały wybrane, i kliknij przycisk **Dalej**, aby przejść na stronę **Podsumowanie**.     
2. Przejrzyj podsumowanie i kliknij przycisk **Dalej**, aby rozpocząć proces wdrożenia oraz wyświetlić stronę **Stan wdrożenia**.
3. Na stronie **Stan wdrożenia** powinien zostać wyświetlony stan procesu wdrożenia. Po zakończeniu wdrożenia kliknij przycisk Zakończ. 

Ważne rzeczy, na które należy zwrócić uwagę: 

* Jeśli zostanie wyświetlony komunikat o błędzie: „**Subskrypcja nie jest zarejestrowana w celu używania przestrzeni nazw Microsoft.DataFactory**”, wykonaj jedną z następujących czynności i spróbuj opublikować ponownie: 
  
  * W programie Azure PowerShell uruchom następujące polecenie, aby zarejestrować dostawcę usługi Fabryka danych. 
    
          Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
      Można uruchomić następujące polecenie, aby potwierdzić, że dostawca usługi Data Factory jest zarejestrowany. 
    
          Get-AzureRmResourceProvider
  * Zaloguj się przy użyciu subskrypcji Azure w witrynie [Azure Portal](https://portal.azure.com) i przejdź do bloku Data Factory lub utwórz fabrykę danych w witrynie Azure Portal. Ta akcja powoduje automatyczne zarejestrowanie dostawcy.
* W przyszłości nazwa fabryki danych może zostać zarejestrowana jako nazwa DNS, a wówczas stanie się widoczna publicznie.
* Aby tworzyć wystąpienia Fabryki danych, musisz być administratorem lub współadministratorem subskrypcji platformy Azure

## <a name="monitor-pipeline"></a>Monitorowanie potoku
### <a name="monitor-pipeline-using-diagram-view"></a>Monitorowanie potoku przy użyciu widoku diagramu
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) i wykonaj następujące czynności:
   1. Kliknij kolejno pozycje **Więcej usług** i **Fabryki danych**.
       ![Przeglądanie fabryk danych](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png) 
   2. Wybierz nazwę fabryki danych (na przykład: **FirstDataFactoryUsingVS09152016**) z listy fabryk danych. 
       ![Wybór fabryki danych](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
2. Na stronie głównej fabryki danych kliknij przycisk **Diagram**.
   
    ![Kafelek Diagram](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
3. Na stronie Widok diagramu zostanie wyświetlony przegląd potoków i zestawów danych używanych w tym samouczku.
   
    ![Widok diagramu](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png) 
4. Aby wyświetlić wszystkie działania w potoku, kliknij prawym przyciskiem myszy potok w diagramie i kliknij polecenie Otwórz potok. 
   
    ![Menu Otwórz potok](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
5. Upewnij się, że działanie HDInsightHive jest widoczne w potoku. 
   
    ![Widok Otwórz potok](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)
   
    Aby przejść z powrotem do poprzedniego widoku, kliknij pozycję **Fabryka danych** w menu linków do stron nadrzędnych u góry. 
6. Na stronie **Widok diagramu** kliknij dwukrotnie zestaw danych **AzureBlobInput**. Sprawdź, czy wycinek jest w stanie **Gotowe**. Może potrwać kilka minut, zanim wycinek zostanie wyświetlony ze stanem Gotowe. Jeśli poczekasz jakiś czas i tak się nie stanie, sprawdź, czy plik wejściowy (input.log) znajduje się w odpowiednim kontenerze (adfgetstarted) i folderze (inputdata).
   
   ![Wycinek danych wejściowych w stanie gotowości](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
7. Kliknij przycisk **X**, aby zamknąć blok **AzureBlobInput**. 
8. Na stronie **Widok diagramu** kliknij dwukrotnie zestaw danych **AzureBlobOutput**. Zostanie wyświetlony wycinek, który jest obecnie przetwarzany.
   
   ![Zestaw danych](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Po zakończeniu przetwarzania wycinek będzie mieć stan **Gotowe**.
   
   > [!IMPORTANT]
   > Tworzenie klastra usługi HDInsight na żądanie zwykle trwa trochę czasu (około 20 minut). Dlatego należy oczekiwać, że przetworzenie wycinka przez potok zajmie **około 30 minut**.  
   > 
   > 
   
    ![Zestaw danych](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png) 
10. Gdy wycinek będzie w stanie **Gotowe**, sprawdź folder **partitioneddata** w kontenerze **adfgetstarted** w magazynie obiektów blob pod kątem danych wyjściowych.  
    
    ![Dane wyjściowe](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. Kliknij wycinek, aby wyświetlić szczegółowe informacje na jego temat w bloku **Wycinek danych**.
    
    ![Szczegóły wycinka danych](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Kliknij uruchomienie działania na **liście uruchomień działań**, aby zobaczyć szczegóły dotyczące uruchamiania działania (w tym scenariuszu działanie Hive) w oknie **Szczegóły uruchamiania działania**.   
    ![Szczegóły uruchamiania działania](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)  
    
    W plikach dziennika zobaczysz zapytanie Hive, które zostało wykonane, oraz informacje o stanie. Dzienniki te są przydatne w przypadku rozwiązywania różnych problemów.  

Zobacz artykuł [Monitor datasets and pipeline](data-factory-monitor-manage-pipelines.md) (Monitorowanie zestawów danych i potoku), aby uzyskać instrukcje dotyczące korzystania z witryny Azure Portal do monitorowania potoku i zestawów danych utworzonych przez siebie w ramach tego samouczka.

### <a name="monitor-pipeline-using-monitor-&-manage-app"></a>Monitorowanie potoku przy użyciu aplikacji Monitorowanie i zarządzanie
Do monitorowania potoków danych możesz też użyć aplikacji Monitorowanie i zarządzanie. Szczegółowe informacje dotyczące korzystania z aplikacji znajdują się w artykule [Monitor and manage Azure Data Factory pipelines using Monitoring and Management App](data-factory-monitor-manage-app.md) (Monitorowanie potoków usługi Fabryka danych Azure oraz zarządzanie nimi za pomocą aplikacji Monitorowanie i zarządzanie).

1. Kliknij kafelek Monitorowanie i zarządzanie.
   
    ![Kafelek Monitorowanie i zarządzanie](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png) 
2. Powinna zostać wyświetlona aplikacja Monitorowanie i zarządzanie. Zmień **godzinę rozpoczęcia** i **godzinę zakończenia** na godzinę rozpoczęcia (01-04-2016 12:00) i godzinę zakończenia (02-04-2016 12:00) potoku i kliknij przycisk **Zastosuj**.
   
    ![Aplikacja Monitorowanie i zarządzanie](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png) 
3. Wybierz okno działania z listy okien działania, aby zobaczyć szczegółowe informacje na jego temat. 
    ![Szczegóły okna działania](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)

> [!IMPORTANT]
> Po pomyślnym przetworzeniu wycinka plik wejściowy zostanie usunięty. Tak więc, jeśli chcesz ponownie uruchomić wycinek lub ponownie wykonać instrukcje z tego samouczka, przekaż plik wejściowy (input.log) do folderu inputdata kontenera adfgetstarted.
> 
> 

## <a name="use-server-explorer-to-view-data-factories"></a>Korzystanie z Eksploratora serwera w celu wyświetlania fabryk danych
1. W programie **Visual Studio** kliknij w menu pozycję **Widok**, a następnie kliknij pozycję **Eksplorator serwera**.
2. W oknie Eksploratora serwera rozwiń węzeł **Azure**, a następnie węzeł **Fabryka danych**. Jeśli zostanie wyświetlony monit **Zaloguj się do programu Visual Studio**, wprowadź **konto** skojarzone z subskrypcją Azure i kliknij przycisk **Kontynuuj**. Wprowadź **hasło** i kliknij przycisk **Zaloguj**. Program Visual Studio podejmie próbę uzyskania informacji na temat wszystkich fabryk danych Azure w ramach danej subskrypcji. Stan tej operacji zostanie wyświetlony w oknie **Lista zadań usługi Data Factory**.
   
    ![Eksplorator serwera](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Kliknij prawym przyciskiem myszy fabrykę danych i wybierz opcję **Eksportuj fabrykę danych do nowego projektu**, aby utworzyć projekt w programie Visual Studio na podstawie istniejącej fabryki danych.
   
    ![Eksportowanie fabryki danych](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## <a name="update-data-factory-tools-for-visual-studio"></a>Aktualizacja narzędzi usługi Fabryka danych dla programu Visual Studio
Aby zaktualizować narzędzia usługi Fabryka danych Azure dla programu Visual Studio, wykonaj następujące czynności:

1. W menu kliknij pozycję **Narzędzia** i wybierz pozycję **Rozszerzenia i aktualizacje**.
2. Wybierz pozycję **Aktualizacje** w lewym okienku, a następnie wybierz pozycję **Galeria Visual Studio**.
3. Wybierz pozycję **Narzędzia usługi Fabryka danych Azure dla programu Visual Studio** i kliknij przycisk **Aktualizuj**. Jeśli ta pozycja nie jest wyświetlana, masz już najnowszą wersję narzędzi. 

## <a name="use-configuration-files"></a>Korzystanie z plików konfiguracji
Plików konfiguracji w programie Visual Studio można użyć do konfigurowania właściwości połączonych usług/tabel/potoków w różny sposób dla poszczególnych środowisk. 

Weź pod uwagę poniższą definicję kodu JSON dotyczącą połączonej usługi Azure Storage. Chcesz określić parametr **connectionString** za pomocą różnych wartości elementów accountname i accountkey dla różnych środowisk (tworzenia/testowania/produkcji), w których wdrażasz jednostki usługi Fabryka danych. Możesz uzyskać takie zachowanie, stosując oddzielny plik konfiguracji dla każdego środowiska. 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "description": "",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    } 

### <a name="add-a-configuration-file"></a>Dodawanie pliku konfiguracji
Dodaj plik konfiguracji dla każdego środowiska, wykonując następujące czynności:   

1. Kliknij prawym przyciskiem myszy projekt usługi Fabryka danych w rozwiązaniu Visual Studio, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy element**.
2. Wybierz pozycję **Konfiguracja** z listy zainstalowanych szablonów po lewej stronie, wybierz opcję **Plik konfiguracji**, wprowadź **nazwę** pliku konfiguracji, a następnie kliknij przycisk **Dodaj**.
   
    ![Dodawanie pliku konfiguracji](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Dodaj parametry konfiguracji i ich wartości w formacie pokazanym niżej.
   
        {
            "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
            "AzureStorageLinkedService1": [
                {
                    "name": "$.properties.typeProperties.connectionString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            ],
            "AzureSqlLinkedService1": [
                {
                    "name": "$.properties.typeProperties.connectionString",
                    "value":  "Server=tcp:spsqlserver.database.windows.net,1433;Database=spsqldb;User ID=spelluru;Password=Sowmya123;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            ]
        }
   
    W tym przykładzie opisano konfigurację właściwości connectionString połączonej usługi Azure Storage oraz połączonej usługi SQL Azure. Zwróć uwagę, że do określania nazwy jest używana składnia [JsonPath](http://goessner.net/articles/JsonPath/).   
   
    Jeśli kod JSON zawiera właściwość obejmującą tablicę wartości, jak pokazano poniżej:  
   
        "structure": [
            {
                "name": "FirstName",
                "type": "String"
            },
            {
                "name": "LastName",
                "type": "String"
            }
        ],
   
    Skonfiguruj właściwości, jak pokazano w następującym pliku konfiguracji (użyj indeksowania rozpoczynającego się od zera): 
   
        {
            "name": "$.properties.structure[0].name",
            "value": "FirstName"
        }
        {
            "name": "$.properties.structure[0].type",
            "value": "String"
        }
        {
            "name": "$.properties.structure[1].name",
            "value": "LastName"
        }
        {
            "name": "$.properties.structure[1].type",
            "value": "String"
        }

### <a name="property-names-with-spaces"></a>Nazwy właściwości zawierające spacje
Jeśli nazwa właściwości zawiera spacje, użyj nawiasów kwadratowych, jak pokazano w poniższym przykładzie (Database server name): 

     {
         "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
         "value": "MyAsqlServer.database.windows.net"
     }


### <a name="deploy-solution-using-a-configuration"></a>Wdrożenie rozwiązania przy użyciu konfiguracji
Podczas publikowania jednostek usługi Fabryka danych Azure w programie VS możesz określić konfigurację, której chcesz użyć dla tej operacji publikowania. 

Aby opublikować jednostki w projekcie usługi Fabryka danych Azure przy użyciu pliku konfiguracji:   

1. Kliknij prawym przyciskiem myszy projekt usługi Fabryka danych i kliknij polecenie **Publikuj**, aby wyświetlić okno dialogowe **Publikowanie elementów**. 
2. Wybierz istniejącą fabrykę danych lub określ wartości do tworzenia fabryki danych na stronie **Konfigurowanie fabryki danych** i kliknij przycisk **Dalej**.   
3. Na stronie **Publikowanie elementów** dla pola **Wybierz konfigurację wdrożenia** zostanie wyświetlona lista rozwijana z dostępnymi konfiguracjami.
   
    ![Wybieranie pliku konfiguracji](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Wybierz **plik konfiguracji**, którego chcesz użyć, i kliknij przycisk **Dalej**. 
5. Upewnij się, że nazwa pliku JSON jest wyświetlana na stronie **Podsumowanie**, i kliknij przycisk **Dalej**. 
6. Kliknij przycisk **Zakończ** po zakończeniu operacji wdrożenia. 

Podczas wdrożenia wartości z pliku konfiguracji służą do ustawiania wartości właściwości w plikach JSON dla jednostek fabryki danych przed ich wdrożeniem w usłudze Fabryka danych Azure.   

## <a name="summary"></a>Podsumowanie
W tym samouczku opisano tworzenie fabryki danych Azure do przetwarzania danych przez uruchomienie skryptu programu Hive w klastrze platformy Hadoop w usłudze HDInsight. Użyto Edytora fabryki danych w witrynie Azure Portal, aby:  

1. Tworzenie **fabryki danych** Azure.
2. Utworzyć dwie **połączone usługi**:
   1. Połączoną usługę **Azure Storage** w celu połączenia magazynu obiektów blob Azure, w którym przechowywane są pliki wejściowe/wyjściowe, z fabryką danych.
   2. Połączoną usługę **Azure HDInsight** na żądanie w celu połączenia klastra platformy Hadoop w usłudze HDInsight na żądanie z fabryką danych. Usługa Fabryka danych Azure tworzy klaster just in time platformy Hadoop w usłudze HDInsight, aby przetwarzać dane wejściowe i generować dane wyjściowe. 
3. Utworzyć dwa **zestawy danych** zawierające dane wejściowe i wyjściowe dla działania programu Hive w usłudze HDInsight w potoku. 
4. Utworzyć **potok** za pomocą działania **programu Hive w usłudze HDInsight**.  

## <a name="next-steps"></a>Następne kroki
W tym artykule opisano tworzenie potoku za pomocą działania przekształcenia (działanie usługi HDInsight), które uruchamia skrypt programu Hive w klastrze usługi HDInsight na żądanie. Instrukcje dotyczące korzystania z działania kopiowania w celu kopiowania danych z magazynu obiektów blob Azure do usług SQL Azure znajdują się w artykule [Tutorial: Copy data from an Azure blob to Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Samouczek: kopiowanie danych z magazynu obiektów blob Azure do usług SQL Azure).

## <a name="see-also"></a>Zobacz też
| Temat | Opis |
|:--- |:--- |
| [Działania przekształcania danych](data-factory-data-transformation-activities.md) |Ten artykuł zawiera listę działań przekształcania danych (takich jak przekształcenie programu Hive w usłudze HDInsight używane w tym samouczku) obsługiwanych w usłudze Fabryka danych Azure. |
| [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md) |W tym artykule wyjaśniono aspekty planowania i wykonywania modelu aplikacji usługi Fabryka danych Azure. |
| [Potoki](data-factory-create-pipelines.md) |Ten artykuł ułatwia zapoznanie się z potokami i działaniami w usłudze Azure Data Factory oraz ze sposobem konstruowania za ich pomocą przepływów pracy typu end-to-end opartych na danych na potrzeby scenariusza lub firmy. |
| [Zestawy danych](data-factory-create-datasets.md) |Ten artykuł ułatwia zapoznanie się z zestawami danych w usłudze Azure Data Factory. |
| [Monitorowanie potoków i zarządzanie nimi za pomocą aplikacji do monitorowania](data-factory-monitor-manage-app.md) |Ten artykuł zawiera instrukcje dotyczące monitorowania i debugowania potoków oraz zarządzania nimi przy użyciu aplikacji do monitorowania i zarządzania. |

<!--HONumber=Oct16_HO3-->


