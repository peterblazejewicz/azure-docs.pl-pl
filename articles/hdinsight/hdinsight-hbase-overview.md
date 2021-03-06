---
title: Czym jest baza danych HBase w usłudze HDInsight? | Microsoft Docs
description: Wprowadzenie do bazy danych Apache HBase w usłudze HDInsight — bazy danych NoSQL opartej na platformie Hadoop. Dowiedz się więcej o przypadkach użycia i porównaj bazę danych HBase z innymi klastrami Hadoop.
keywords: bigtable,nosql,what is hbase
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/14/2016
ms.author: jgao

---
# Czym jest baza danych HBase w usłudze HDInsight: jest to baza danych NoSQL o możliwościach analogicznych do bazy danych BigTable dla platformy Hadoop
Apache HBase jest bazą danych NoSQL typu open source opartą na platformie Hadoop i wzorowaną na bazie danych Google BigTable. Baza danych HBase zapewnia dostęp losowy i wysoki poziom spójności w przypadku dużych ilości danych z częściową strukturą i bez struktury w bezschematowej bazie danych zorganizowanej według rodzin kolumn.

Dane są przechowywane w wierszach tabeli, a dane w obrębie wiersza są zgrupowane według rodziny kolumn. HBase jest bezschematową bazą danych, co oznacza, że ani kolumny, ani typy danych w nich przechowywanych nie muszą być zdefiniowane przed użyciem. Kod typu open source zapewnia skalowanie liniowe, umożliwiając obsługę petabajtów danych na tysiącach węzłów. Baza może wykorzystywać nadmiarowość danych, przetwarzanie wsadowe i inne funkcje, które są dostarczane przez aplikacje rozproszone w ekosystemie Hadoop.

## W jaki sposób baza danych HBase jest zaimplementowana w systemie Azure HDInsight?
Baza danych HBase w usłudze HDInsight jest oferowana jako zarządzany klaster zintegrowany ze środowiskiem Azure. Klastry są skonfigurowane do przechowywania danych bezpośrednio w usłudze Azure Blob Storage, co zapewnia małe opóźnienia i zwiększoną elastyczność w zakresie wydajności i kosztów. Pozwala to klientom tworzyć interaktywne witryny sieci Web pracujące z dużymi zestawami danych, opracowywać usługi przechowywania danych czujników i danych telemetrycznych z milionów punktów końcowych oraz analizować te dane przy użyciu zadań platformy Hadoop. HBase i Hadoop są dobrymi punktami startowymi dla projektów obejmujących dane big data w środowisku Azure. W szczególności mogą one umożliwiać pracę w czasie rzeczywistym aplikacji obsługujących duże zestawy danych.

Implementacja usługi HDInsight wykorzystuje skalowalność architektury HBase, aby zapewnić automatyczne dzielenie tabel na fragmenty, wysoki poziom spójności operacji odczytu i zapisu oraz automatyzację pracy awaryjnej. Wydajność jest zwiększona dzięki buforowaniu w pamięci operacji odczytu i przesyłaniu strumieniowemu o wysokiej przepustowości obejmującemu operacje zapisu. Baza danych HBase w usłudze HDInsight umożliwia również inicjowanie obsługi sieci wirtualnych. Aby uzyskać szczegółowe informacje, zobacz temat [Obsługa administracyjna klastrów HDInsight w sieci Azure Virtual Network][hbase-provision-vnet].

## W jaki sposób zarządzane są dane w bazie danych HBase w usłudze HDInsight?
W bazie danych HBase można zarządzać danymi za pomocą poleceń `create`, `get`, `put` i `scan` z poziomu powłoki HBase. Dane są zapisywane w bazie danych przy użyciu polecenia `put` i odczytywane przy użyciu polecenia `get`. Polecenie `scan` jest używane do uzyskiwania danych z wielu wierszy w tabeli. Danymi można również zarządzać przy użyciu interfejsu API w języku C# bazy danych HBase, który udostępnia bibliotekę klienta ponad interfejsem API REST HBase. Korzystając z programu Hive, można wykonywać zapytania w bazie danych HBase. Aby zapoznać się z wprowadzeniem do tych modeli programowania, zobacz temat [Rozpoczęcie korzystania z bazy danych HBase na platformie Hadoop w usłudze HDInsight][hbase-get-started]. Dostępne są również koprocesory, co umożliwia przetwarzanie danych w węzłach hostujących bazę danych.

## Scenariusze: przypadki użycia bazy danych HBase
Klasycznym przypadkiem użycia, dla którego opracowano bazę danych BigTable (a tym samym bazę danych HBase), jest wyszukiwanie w sieci Web. Aparaty wyszukiwania tworzą indeksy, które mapują terminy na zawierające je strony sieci Web. Istnieje jednak wiele innych przypadków użycia, w których baza danych HBase jest przydatna — niektóre z nich są wymienione w tej sekcji.

* Magazyn wartości klucza
  
    Baza danych HBase może być używana jako magazyn wartości klucza i nadaje się do zarządzania systemami obsługi wiadomości. Facebook używa bazy danych HBase na potrzeby systemu obsługi wiadomości, gdyż takie rozwiązanie idealnie nadaje się do przechowywania wiadomości i zarządzania komunikacją internetową. Usługa WebTable używa bazy danych HBase do wyszukiwania tabel wyodrębnianych ze stron sieci Web oraz zarządzania nimi.
* Dane czujników
  
    Baza danych HBase może służyć do przechwytywania danych gromadzonych przyrostowo z różnych źródeł. Dotyczy to analiz społecznościowych, szeregów czasowych, aktualizowania interaktywnych pulpitów nawigacyjnych przy użyciu trendów i liczników oraz zarządzania systemami dzienników inspekcji. Przykładami mogą być terminale transakcyjne Bloomberg oraz baza danych OpenTSDB (Open Time Series Database), która przechowuje i udostępnia metryki dotyczące kondycji systemów serwerowych.
* Zapytania w czasie rzeczywistym
  
    Baza danych Apache HBase korzysta z aparatu zapytań SQL [Phoenix](http://phoenix.apache.org/). Jest on dostępny jako sterownik JDBC i umożliwia wykonywanie zapytań i zarządzanie tabelami HBase przy użyciu języka SQL.
* HBase jako platforma
  
    Aplikacje mogą działać w bazie danych HBase, wykorzystując ją jako magazyn danych. Przykłady obejmują aplikacje Phoenix, OpenTSDB, Kiji i Titan. Aplikacje można również integrować z bazą danych HBase. Przykładami mogą być aplikacje Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia i Drill.

## <a name="next-steps"></a>Następne kroki
* [Rozpoczęcie korzystania z bazy danych HBase na platformie Hadoop w usłudze HDInsight][hbase-get-started]
* [Obsługa administracyjna klastrów HDInsight w sieci Azure Virtual Network][hbase-provision-vnet].
* [Konfigurowanie replikacji bazy danych HBase w usłudze HDInsight](hdinsight-hbase-geo-replication.md)
* [Analizowanie opinii w serwisie Twitter przy użyciu bazy danych HBase w usłudze HDInsight][hbase-twitter-sentiment]
* [Używanie programu Maven do tworzenia aplikacji Java korzystających z bazy danych HBase w usłudze HDInsight (Hadoop)][hbase-build-java-maven]

## <a name="see-also"></a>Zobacz też
* [Apache HBase](https://hbase.apache.org/)
* [Bigtable: rozproszony systemem przechowywania danych strukturalnych](http://research.google.com/archive/bigtable.html)

[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/



<!--HONumber=Sep16_HO3-->


