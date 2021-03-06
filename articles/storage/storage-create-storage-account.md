---
title: Sposoby tworzenia, zarządzania i usuwania konta magazynu w Portalu Azure | Microsoft Docs
description: Utwórz nowe konto magazynu, zarządzaj kluczami dostępu do konta lub usuń konto magazynu w Portalu Azure. Więcej informacji na temat kont magazynu (warstwy Standardowa i Premium).
services: storage
documentationcenter: ''
author: robinsh
manager: carmonm
editor: tysonn

ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/26/2016
ms.author: micurd;robinsh

---
# Informacje o kontach magazynu Azure
[!INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

[!INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## Omówienie
Konto usługi Azure Storage zapewnia unikatową przestrzeń nazw do przechowywania i umożliwiania dostępu do obiektów danych usługi Azure Storage. Wszystkie obiekty w koncie magazynu są rozliczane wspólnie jako grupa. Domyślnie dane na Twoim koncie są dostępne tylko dla Ciebie, tj. właściciela konta.

[!INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## Rozliczanie konta usługi Storage
[!INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [!NOTE]
> Kiedy tworzysz maszynę wirtualną platformy Azure, konto magazynu jest tworzone automatycznie w lokalizacji wdrożenia, jeśli jeszcze nie masz konta magazynu w tej lokalizacji. Dlatego też nie musisz wykonywać poniższych kroków, aby utworzyć konto magazynu dla dysków maszyny wirtualnej. Nazwa konta magazynu będzie opierać się na nazwie maszyny wirtualnej. Zobacz [dokumentację usługi Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/), aby uzyskać więcej szczegółów.
> 
> 

## Punkty końcowe konta usługi Storage
Każdy obiekt, który jest przechowywany w usłudze Azure Storage, ma unikatowy adres URL. Nazwa konta magazynu tworzy poddomenę tego adresu. Kombinacja nazw poddomeny i domeny, która jest specyficzna dla poszczególnych usług, tworzy *punkt końcowy* konta magazynu.

Jeśli na przykład konto magazynu ma nazwę *mojekontomagazynu*, domyślnymi punktami końcowymi konta magazynu są:

* Usługa Blob: http://*mojekontomagazynu*.blob.core.windows.net
* Usługa Table service: http://*mojekontomagazynu*.table.core.windows.net
* Usługa kolejki: http://*mojekontomagazynu*.queue.core.windows.net
* Usługa plików http://*mojekontomagazynu*.file.core.windows.net

> [!NOTE]
> Konto Magazynu obiektów Blob przedstawia tylko punkt końcowy usługi Blob.
> 
> 

Adres URL dostępu do obiektu w koncie magazynu jest tworzony przez dodanie lokalizacji obiektu na koncie magazynu do punktu końcowego. Przykładowo adres obiektu Blob może mieć następujący format: http://*mojekontomagazynu*.blob.core.windows.net/*mojkontener*/*mojblob*.

Możesz również skonfigurować niestandardową nazwę domeny do użycia ze swoim kontem magazynu. Aby uzyskać więcej szczegółów na temat klasycznych kont magazynu, zobacz temat [Configure a custom domain Name for your Blob Storage Endpoint](storage-custom-domain-name.md) (Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi Blob Storage). W przypadku kont magazynu usługi Resource Manager ta funkcja nie została jeszcze dodana do witryny [Azure Portal](https://portal.azure.com), ale można skonfigurować ją za pomocą programu PowerShell. Aby uzyskać więcej informacji, zobacz polecenie cmdlet [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx).  

## Tworzenie konta magazynu
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu Centrum wybierz kolejno pozycje **Nowe** -> **Dane i usługa Storage** -> **Konto usługi Storage**.
3. Wprowadź nazwę konta magazynu. Zobacz [Punkty końcowe konta usługi Storage](#storage-account-endpoints), aby uzyskać szczegółowe informacje o tym, w jaki sposób nazwa konta magazynu będzie wykorzystywana do adresowania obiektów w usłudze Azure Storage.
   
   > [!NOTE]
   > Nazwy kont usługi Magazyn muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery.
   > 
   > Nazwa konta magazynu musi być unikatowa w obrębie platformy Azure. Witryna Azure Portal poinformuje, jeśli wybrana nazwa konta magazynu jest już w użyciu.
   > 
   > 
4. Określ model wdrożenia do użycia: **z usługą Resource Manager** lub **Klasyczny**. **Z usługą Resource Manager** jest zalecanym modelem wdrożenia. Aby uzyskać więcej informacji, zobacz [Understanding Resource Manager deployment and classic deployment](../resource-manager-deployment-model.md) (Omówienie wdrożenia z usługą Resource Manager oraz wdrożenia klasycznego).
   
   > [!NOTE]
   > Konta magazynu obiektów Blob można tworzyć wyłącznie w modelu wdrożenia opartym na usłudze Resource Manager.
   > 
   > 
5. Wybierz typ konta magazynu: **Ogólnego przeznaczenia** lub **Blob Storage**. **Ogólnego przeznaczenia** jest wartością domyślną.
   
    Jeśli wybrano typ **Ogólnego przeznaczenia**, określ warstwę wydajności: **Standardowe** lub **Premium**. Wartość domyślna to **Standardowe**. Szczegółowe informacje dotyczące kont magazynu (warstwy Standardowa i Premium) znajdują się w tematach [Wprowadzenie do usługi Microsoft Azure Storage](storage-introduction.md) i [Premium Storage: High-Performance Storage for Azure Virtual Machine Workloads](storage-premium-storage.md) (Premium Storage: usługa Storage o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure).
   
    Jeśli wybrano usługę **Blob Storage**, określ warstwę dostępu: **Gorąca** lub **Chłodna**. Wartość domyślna to **Gorąca**. Zobacz temat [Azure Blob Storage: Cool and Hot tiers](storage-blob-storage-tiers.md) (Azure Blob Storage: warstwy Chłodna i Gorąca), aby uzyskać więcej szczegółów.
6. Wybierz opcję replikacji dla konta magazynu: **LRS**, **GRS**, **RA-GRS** lub **ZRS**. Wartość domyślna to **RA-GRS**. Aby uzyskać więcej szczegółów na temat opcji replikacji usługi Azure Storage, zobacz [Replikacja usługi Azure Storage](storage-redundancy.md).
7. Wybierz subskrypcję, w ramach której chcesz utworzyć nowe konto magazynu.
8. Określ nową grupę zasobów lub wybierz istniejącą grupę zasobów. Więcej informacji na temat grup zasobów znajduje się w temacie [Omówienie usługi Azure Resource Manager](../resource-group-overview.md).
9. Wybierz lokalizację geograficzną dla swojego konta magazynu. Aby uzyskać więcej informacji na temat dostępności usług w poszczególnych regionach, zobacz temat [Regiony świadczenia usługi Azure](https://azure.microsoft.com/regions/#services).
10. Kliknij pozycję **Utwórz**, aby utworzyć konto magazynu.

## Zarządzanie kontem magazynu
### Zmiana konfiguracji konta
Po utworzeniu konta magazynu możesz zmodyfikować jego konfigurację, np. zmienić opcję replikacji używaną wobec konta lub zmienić warstwę dostępu do konta usługi Blob Storage. W witrynie [Azure Portal](https://portal.azure.com) przejdź do swojego konta magazynu, kliknij pozycję **Wszystkie ustawienia**, a następnie kliknij pozycję **Konfiguracja**, aby wyświetlić lub zmienić konfigurację konta.

> [!NOTE]
> W zależności od warstwy wydajności wybranej podczas tworzenia konta magazynu niektóre opcje replikacji mogą być niedostępne.
> 
> 

Zmiana opcji replikacji spowoduje zmianę cen. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

W przypadku kont Magazynu obiektów Blob zmiana warstwy dostępu może spowodować naliczenie opłat za zmianę poza zmianą cennika. Zobacz [Blob storage accounts - Pricing and Billing](storage-blob-storage-tiers.md#pricing-and-billing) (Konta usługi Blob Storage — cennik i rozliczenia), aby uzyskać więcej szczegółów.

### Zarządzanie kluczami dostępu do magazynu
Podczas tworzenia konta magazynu platforma Azure generuje dwa 512-bitowe klucze dostępu do magazynu, które są wykorzystywane do uwierzytelniania podczas uzyskiwania dostępu do konta magazynu. Zapewniając dwa klucze dostępu do magazynu, platforma Azure umożliwia ponowne generowanie kluczy bez zakłóceń w usłudze magazynu lub przerw w dostępie do tej usługi.

> [!NOTE]
> Nie zaleca się udostępniania kluczy dostępu do magazynu innym osobom. Aby zezwolić na dostęp do zasobów magazynu bez przekazywania kluczy dostępu, możesz użyć *sygnatury dostępu współdzielonego*. Sygnatura dostępu współdzielonego zapewnia dostęp do zasobów na koncie przez określony czas i z określonymi uprawnieniami. Zobacz [Using Shared Access Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) (Używanie sygnatur dostępu współdzielonego), aby uzyskać więcej informacji.
> 
> 

#### Wyświetlanie i kopiowanie kluczy dostępu do magazynu
W witrynie [Azure Portal](https://portal.azure.com) przejdź do swojego konta magazynu, kliknij pozycję **Wszystkie ustawienia**, a następnie kliknij pozycję **Klucze dostępu**, aby wyświetlić, skopiować lub ponownie wygenerować klucze dostępu do konta. Blok **Klucze dostępu** zawiera również wstępnie skonfigurowane parametry połączenia wykorzystujące Twoje klucze podstawowe i pomocnicze, które możesz skopiować do wykorzystania w aplikacjach.

#### Ponowne generowanie kluczy dostępu do magazynu
Zalecamy okresowe zmienienie kluczy dostępu do konta magazynu, aby zabezpieczyć połączenia z magazynem. Przydzielone są dwa klucze dostępu, więc możesz utrzymać łączność z kontem magazynu za pomocą jednego klucza dostępu, jednocześnie ponownie generując drugi klucz dostępu.

> [!WARNING]
> Ponowne generowanie kluczy dostępu może mieć wpływ na usługi na platformie Azure oraz Twoje aplikacje, które są zależne od konta magazynu. Wszyscy klienci, którzy używają klucza dostępu do uzyskiwania dostępu do konta magazynu, muszą zostać poinformowani o potrzebie użycia nowego klucza.
> 
> 

**Usługi multimediów** — jeśli masz usługi multimediów, które są zależne od konta magazynu, musisz ponownie zsynchronizować klucze dostępu z usługą multimediów po ponownym wygenerowaniu kluczy.

**Aplikacje** — jeśli masz aplikacje sieci Web lub usługi w chmurze, które korzystają z konta magazynu, w przypadku ponownego generowania kluczy utracisz połączenia, chyba że wdrożysz klucze.

**Eksploratory usługi Storage** — jeśli używasz dowolnej [aplikacji eksploratora magazynu](storage-explorers.md), prawdopodobnie zajdzie potrzeba zaktualizowania klucza magazynu używanego przez te aplikacje.

Oto proces związany z rotacją kluczy dostępu do magazynu:

1. Zaktualizuj parametry połączenia w kodzie aplikacji, wprowadzając odwołanie do pomocniczego klucza dostępu do konta magazynu.
2. Ponownie wygeneruj podstawowy klucz dostępu do konta magazynu. W bloku **Klucze dostępu** kliknij opcję **Generuj ponownie klucz1**, a następnie kliknij przycisk **Tak**, aby potwierdzić wygenerowanie nowego klucza.
3. Zaktualizuj parametry połączenia w kodzie, wprowadzając odwołanie do nowego podstawowego klucza dostępu.
4. W ten sam sposób wygeneruj ponownie pomocniczy klucz dostępu.

## Usuwanie konta magazynu
Aby usunąć konto magazynu, z którego już nie korzystasz, przejdź do konta magazynu w witrynie [Azure Portal](https://portal.azure.com) i kliknij pozycję **Usuń**. Usunięcie konta magazynu powoduje usunięcie całego konta, w tym wszystkich danych na koncie.

> [!WARNING]
> Nie można przywrócić usuniętego konta magazynu ani odzyskać żadnej zawartości znajdującej się na koncie przed usunięciem. Zanim usuniesz konto, wykonaj kopię zapasową wszystkich danych, które chcesz zapisać. Dotyczy to również wszystkich zasobów na koncie — po usunięciu obiektu Blob, tabeli, kolejki lub pliku elementy te są trwale usuwane.
> 
> 

Aby usunąć konto magazynu powiązane z maszyną wirtualną platformy Azure, musisz najpierw upewnić się, że wszystkie dyski maszyny wirtualnej zostały usunięte. Jeśli nie usuniesz najpierw dysków maszyny wirtualnej, podczas próby usunięcia konta magazynu zobaczysz komunikat o błędzie podobny do poniższego:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Jeśli konto magazynu korzysta z modelu wdrożenia klasycznego, można usunąć dysk maszyny wirtualnej, wykonując następujące kroki w witrynie [Azure Portal](https://manage.windowsazure.com):

1. Przejdź do [klasycznej witrynty Azure Portal](https://manage.windowsazure.com).
2. Przejdź do karty Virtual Machines.
3. Kliknij kartę Dyski.
4. Wybierz dysk danych, a następnie kliknij polecenie Usuń dysk.
5. Aby usunąć obrazy dysku, przejdź do karty Obrazy i usuń wszelkie obrazy przechowywane na koncie.

Zobacz [dokumentację maszyn wirtualnych Azure](http://azure.microsoft.com/documentation/services/virtual-machines/), aby uzyskać więcej informacji.

## Następne kroki
* [Azure Blob Storage: warstwy Chłodna i Gorąca](storage-blob-storage-tiers.md)
* [Replikacja usługi Azure Storage](storage-redundancy.md)
* [Konfiguracja parametrów połączenia usługi Azure Storage](storage-configure-connection-string.md)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
* Odwiedź [Blog zespołu odpowiedzialnego za usługę Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).

<!--HONumber=Sep16_HO3-->


