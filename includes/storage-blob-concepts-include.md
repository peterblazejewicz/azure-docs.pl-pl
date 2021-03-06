## Co to jest Magazyn obiektów Blob?
Magazyn obiektów Blob Azure to usługa do przechowywania dużych ilości danych obiektów niestrukturalnych, takich jak dane tekstowe lub binarne, do których można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Magazyn obiektów Blob może być użyty do udostępniania danych publicznie lub do przechowywania danych aplikacji prywatnie.

Najczęstsze zastosowania Magazynu obiektów Blob obejmują:

* Obsługiwanie obrazów i dokumentów bezpośrednio w przeglądarce
* Przechowywanie plików do dostępu rozproszonego
* Przesyłanie strumieniowe audio i wideo
* Zapisywanie danych w celu tworzenia kopii zapasowych, przywracania, odzyskiwania po awarii i archiwizowania
* Przechowywanie danych w celu analizy w usłudze lokalnej lub hostowanej na platformie Azure

## Pojęcia dotyczące usługi Blob
Usługa Blob obejmuje następujące składniki:

![Blob1][Blob1]

* **Konto magazynu:** cały dostęp do usługi Azure Storage odbywa się przez konto magazynu. Konto magazynu może być **kontem magazynu ogólnego przeznaczenia** lub **kontem Magazynu obiektów Blob** służącym do przechowywania obiektów/obiektów blob. Aby uzyskać więcej informacji dotyczących kont magazynu, zobacz temat [Azure storage account](../articles/storage/storage-create-storage-account.md) (Konto magazynu Azure).
* **Kontener:** kontener zawiera grupowanie zestawu obiektów blob. Wszystkie obiekty blob muszą być w kontenerze. Konto może zawierać nieograniczoną liczbę kontenerów. Kontener może przechowywać nieograniczoną liczbę obiektów blob. Pamiętaj, że wszystkie litery w nazwie kontenera muszą być małymi literami.
* **Obiekt blob:** plik dowolnego typu o dowolnym rozmiarze. Usługa Azure Storage udostępnia trzy typy obiektów blob: blokowe, stronicowe i uzupełnialne.
  
    *Blokowe obiekty blob* idealnie nadają się do przechowywania tekstu lub plików binarnych, takich dokumenty czy pliki multimedialne. *Uzupełnialne obiekty blob* są podobne do obiektów blokowych, ponieważ składają się z bloków, ale zoptymalizowane do operacji uzupełnialnych, więc przydatne w scenariuszach logowania. Pojedynczy blokowy lub uzupełnialny obiekt blob może zawierać do 50 000 bloków do 4 MB każdy, których rozmiar całkowity może nieco przekraczać 195 GB (4 MB X 50 000).
  
    *Stronicowe obiekty blob* mogą mieć rozmiar maksymalnie 1 TB i są bardziej efektywne w przypadku operacji częstego odczytu/zapisu. Usługa Azure Virtual Machines używa stronicowych obiektów blob jako systemu operacyjnego lub dysków z danymi.
  
    Aby uzyskać szczegółowe informacje o nazewnictwie kontenerów i obiektów blob, zobacz temat [Naming and Referencing Containers, Blobs, and Metadata](https://msdn.microsoft.com/library/azure/dd135715.aspx) (Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych).

[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg



<!--HONumber=Jun16_HO2-->


