## Co to jest usługa tabel
Usługa Azure Table Storage służy do przechowywania dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który przyjmuje uwierzytelnione wywołania z chmury Azure i spoza niej. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych. Najczęstsze zastosowania usługi tabel obejmują:

* Przechowywanie tabel danych strukturalnych umożliwiających obsługę aplikacji w skali sieci Web
* Zapisywanie zestawów danych, które nie wymagają złożonych sprzężeń, kluczy obcych lub przechowywanych procedur i mogą być nieznormalizowane w celu zapewniania szybkiego dostępu
* Szybkie wykonywanie zapytań o dane przy użyciu indeksu klastrowanego
* Uzyskiwanie dostępu do danych przy użyciu protokołu OData i zapytań LINQ z bibliotekami .NET usługi danych WCF

Usługa tabel służy do przechowywania i wykonywania zapytań dotyczących dużych zestawów strukturalnych danych nierelacyjnych. Tabele mogą być skalowane wraz ze wzrostem wymagań.

## Pojęcia dotyczące usługi tabel
Usługa tabel zawiera następujące składniki:

![Table1][Table1]

* **Format adresu URL:** kod odwołuje się do tabel na koncie przy użyciu następującego formatu adresu:   
  http://`<storage account>`.table.core.windows.net/`<table>`  
  
  Możesz odwołać się do tabel platformy Azure bezpośrednio przy użyciu tego adresu i protokołu OData. Więcej informacji znajduje się na stronie [OData.org][OData.org]
* **Konto magazynu:** cały dostęp do usługi Azure Storage odbywa się przez konto magazynu. Aby uzyskać szczegółowe informacje na temat pojemności konta magazynu, zobacz temat [Cele dotyczące skalowalności i wydajności usługi Azure Storage](../articles/storage/storage-scalability-targets.md).
* **Tabela**: tabela jest kolekcją obiektów. Tabele nie wymuszają schematu na obiektach, co oznacza, że jedna tabela może zawierać obiekty o różnych zestawach właściwości. Liczba tabel, którą może zawierać konto magazynu, jest ograniczona tylko limitem pojemności konta magazynu.
* **Obiekt**: obiekt jest zestawem właściwości podobnym do wiersza bazy danych. Maksymalny rozmiar obiektu wynosi 1 MB.
* **Właściwości**: właściwość to połączenie nazwy i wartości. Każdy obiekt może zawierać maksymalnie 252 właściwości do przechowywania danych. Każdy obiekt ma również 3 właściwości systemowe, które określają klucz partycji, klucz wiersza i znacznik czasu. Obiekty o tym samym kluczu partycji mogą być szybciej badane oraz wstawiane/aktualizowane w operacjach niepodzielnych. Klucz wiersza obiektu jest jego unikatowym identyfikatorem w partycji.

Szczegółowe informacje na temat nazewnictwa tabel i właściwości można znaleźć w temacie [Omówienie modelu danych usługi tabel](https://msdn.microsoft.com/library/azure/dd179338.aspx).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/



<!--HONumber=Jun16_HO2-->


