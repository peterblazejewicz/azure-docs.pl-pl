---
title: Ładowanie danych z programu SQL Server do usługi Azure SQL Data Warehouse (PolyBase) | Microsoft Docs
description: Eksportowanie danych z programu SQL Server do plików prostych przy użyciu programu bcpw, importowanie danych do usługi Azure Blob Storage przy użyciu programu AZCopy oraz pozyskiwanie danych do usługi Azure SQL Data Warehouse przy użyciu programu PolyBase.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/30/2016
ms.author: cakarst;barbkess;sonyama

---
# Ładowanie danych przy użyciu programu PolyBase w usłudze SQL Data Warehouse
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Ten samouczek przedstawia sposób ładowania danych do usługi SQL Data Warehouse przy użyciu programów AzCopy i PolyBase. Po zakończeniu będziesz umieć wykonywać następujące czynności:

* Kopiowanie danych do magazynu obiektów blob platformy Azure przy użyciu programu AzCopy
* Tworzenie obiektów bazy danych do definiowania danych
* Uruchamianie zapytania T-SQL do ładowania danych

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Loading-data-with-PolyBase-in-Azure-SQL-Data-Warehouse/player]
> 
> 

## Wymagania wstępne
Do wykonania kroków opisanych w tym samouczku potrzebne są:

* Baza danych usługi SQL Data Warehouse.
* Konto magazynu platformy Azure typu standardowy magazyn lokalnie nadmiarowy (Standard-LRS), standardowy magazyn geograficznie nadmiarowy (Standard-GRS) lub standardowy magazyn geograficznie nadmiarowy dostępny do odczytu (Standard-RAGRS).
* Narzędzie wiersza polecenia AzCopy. Pobierz i zainstaluj [najnowszą wersję programu AzCopy][najnowszą wersję programu AzCopy], która jest instalowana z narzędziami Microsoft Azure Storage Tools.
  
    ![Narzędzia Azure Storage Tools](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)

## Krok 1: dodawanie przykładowych danych do magazynu obiektów blob platformy Azure
Aby załadować dane, trzeba umieścić trochę przykładowych danych w magazynie obiektów blob platformy Azure. W tym kroku wypełnimy obiekt blob magazynu Azure przykładowymi danymi. Następnie użyjemy aparatu PolyBase, aby załadować te przykładowe dane do bazy danych usługi SQL Data Warehouse.

### A. Przygotowanie przykładowego pliku tekstowego
Aby przygotować przykładowy plik tekstowy:

1. Otwórz program Notatnik i skopiuj następujące wiersze danych do nowego pliku. Zapisz go jako % temp%\DimDate2.txt w lokalnym katalogu tymczasowym.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### B. Znajdowanie punktu końcowego usługi Blob
Aby znaleźć punkt końcowy usługi Blob:

1. W Portalu Azure wybierz opcje **Przeglądaj**  >  **Konta magazynu**.
2. Kliknij konto magazynu, którego chcesz użyć.
3. W bloku Konto magazynu kliknij pozycję Obiekty blob.
   
    ![Klikanie pozycji Obiekty blob](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)
4. Zapisz adres URL punktu końcowego usługi Blob do użycia później.
   
    ![Punkt końcowy usługi Blob](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### C. Znajdowanie klucza magazynu Azure
Aby znaleźć klucz magazynu Azure:

1. W witrynie Azure Portal wybierz opcje **Przeglądaj** > **Konta magazynu**.
2. Kliknij konto magazynu, którego chcesz użyć.
3. Wybierz pozycje **Wszystkie ustawienia**  >  **Klucze dostępu**.
4. Kliknij pole kopiowania, aby skopiować jeden z kluczy dostępu do schowka.
   
    ![Kopiowanie klucza magazynu Azure](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### D. Kopiowanie przykładowego pliku do magazynu obiektów blob platformy Azure
Aby skopiować dane do magazynu obiektów blob platformy Azure:

1. Otwórz wiersz polecenia i zmień katalogi na katalog instalacyjny programu AzCopy. To polecenie powoduje zmianę domyślnego katalogu instalacji na 64-bitowym kliencie systemu Windows.
   
    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```
2. Uruchom następujące polecenie, aby przekazać plik. Określ adres URL punktu końcowego usługi Blob jako wartość <blob service endpoint URL> i klucz konta usługi Azure Storage jako wartość <azure_storage_account_key>.
   
    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Zobacz też [Wprowadzenie do narzędzia wiersza polecenia AzCopy][najnowszą wersję programu AzCopy].

### E. Eksplorowanie kontenera magazynu obiektów blob
Aby zobaczyć plik przekazany do magazynu obiektów blob:

1. Wróć do bloku usługi Blob.
2. W obszarze Kontenery kliknij dwukrotnie pozycję **datacontainer**.
3. Aby wyświetlić ścieżkę do danych, kliknij folder **datedimension**, w którym będzie widoczny przekazany plik **DimDate2.txt**.
4. Aby wyświetlić właściwości, kliknij plik **DimDate2.txt**.
5. Zauważ, że w bloku właściwości obiektu blob można pobrać lub usunąć plik.
   
    ![Wyświetlanie obiektu blob magazynu Azure](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)

## Krok 2: tworzenie tabeli zewnętrznej dla przykładowych danych
W tej sekcji utworzymy tabelę zewnętrzną definiującą przykładowe dane.

Aparat PolyBase używa tabel zewnętrznych do uzyskiwania dostępu do danych w magazynie obiektów blob platformy Azure. Ponieważ dane nie są przechowywane w usłudze SQL Data Warehouse, aparat PolyBase obsługuje uwierzytelnianie w odniesieniu do danych zewnętrznych przy użyciu poświadczeń o zakresie bazy danych.

W tym kroku przykładu użyto następujących instrukcji języka Transact-SQL do utworzenia tabeli zewnętrznej.

* [Create Master Key (Transact-SQL)][Create Master Key (Transact-SQL)] w celu szyfrowania klucza tajnego poświadczeń o zakresie bazy danych.
* [Create Database Scoped Credential (Transact-SQL)][Create Database Scoped Credential (Transact-SQL)], aby określić dane uwierzytelniania dla konta usługi Azure Storage.
* [Create External Data Source (Transact-SQL)][Create External Data Source (Transact-SQL)], aby określić lokalizację usługi Azure Blob Storage.
* [Create External File Format (Transact-SQL)][Create External File Format (Transact-SQL)], aby określić format danych.
* [Create External Table (Transact-SQL)][Create External Table (Transact-SQL)], aby określić definicję tabeli i lokalizację danych.

Uruchom to zapytanie w bazie danych usługi SQL Data Warehouse. W schemacie dbo zostanie utworzona zewnętrzna tabela o nazwie DimDate2External wskazująca przykładowe dane DimDate2.txt w magazynie obiektów blob platformy Azure.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


W Eksploratorze obiektów SQL Server w programie Visual Studio widoczne będą: format pliku zewnętrznego, zewnętrzne źródło danych oraz tabela DimDate2External.

![Widok tabeli zewnętrznej](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## Krok 3: ładowanie danych do usługi SQL Data Warehouse
Po utworzeniu tabeli zewnętrznej można załadować dane do nowej tabeli lub wstawić je do tabeli istniejącej.

* Aby załadować dane do nowej tabeli, uruchom instrukcję [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)]. Nowa tabela będzie miała kolumny o nazwach podanych w zapytaniu. Typy danych kolumn będą zgodne z typami danych w definicji tabeli zewnętrznej.
* Aby załadować dane do istniejącej tabeli, użyj instrukcji [INSERT...SELECT (Transact-SQL)][INSERT...SELECT (Transact-SQL)].

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## Krok 4: tworzenie statystyk na podstawie nowo załadowanych danych
Usługa SQL Data Warehouse nie tworzy ani nie aktualizuje statystyk w sposób automatyczny. Dlatego, aby uzyskać wysoką wydajność zapytań, ważne jest tworzenie statystyk dotyczących poszczególnych kolumn każdej tabeli po pierwszym załadowaniu. Istotne jest również aktualizowanie statystyk po wprowadzeniu istotnych zmian w danych.

W tym przykładzie zostaną utworzone statystyki dla pojedynczej kolumny w nowej tabeli DimDate2.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Aby dowiedzieć się więcej, zobacz temat [Statystyki][Statystyki]:  

## Następne kroki
Zobacz [Przewodnik po programie PolyBase][Przewodnik po programie PolyBase] w celu uzyskania dalszych informacji przydatnych podczas tworzenia rozwiązań z użyciem aparatu PolyBase.

<!--Image references-->


<!--Article references-->
[Aparat PolyBase w usłudze SQL Data Warehouse — samouczek]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Ładowanie danych za pomocą narzędzia bcp]: ./sql-data-warehouse-load-with-bcp.md
[Statystyki]: ./sql-data-warehouse-tables-statistics.md
[Przewodnik po programie PolyBase]: ./sql-data-warehouse-load-polybase-guide.md
[najnowszą wersję programu AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[obsługiwane źródło/ujście]: https://msdn.microsoft.com/library/dn894007.aspx
[działanie kopiowania]: https://msdn.microsoft.com/library/dn835035.aspx
[Adapter miejsca docelowego programu SQL Server]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx



<!--HONumber=Sep16_HO3-->


