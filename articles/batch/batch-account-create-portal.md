---
title: Tworzenie konta usługi Azure Batch | Microsoft Docs
description: Dowiedz się, jak utworzyć konto usługi Azure Batch w portalu Azure w celu równoległego uruchamiania dużych obciążeń w chmurze
services: batch
documentationcenter: ''
author: mmacy
manager: timlt
editor: ''

ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/21/2016
ms.author: marsma

---
# Tworzenie konta usługi Azure Batch w witrynie Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Dowiedz się, jak utworzyć konto usługi Batch w witrynie [Azure Portal][azure_portal] i gdzie można znaleźć ważne właściwości konta, takie jak klucze dostępu i adresy URL konta. Omówimy również ceny usługi Batch oraz łączenie konta usługi Azure Storage z kotem usługi Batch w celu korzystania z [pakietów aplikacji](batch-application-packages.md) oraz [trwałych danych wyjściowych zadań](batch-task-output.md).

## Tworzenie konta usługi Batch
1. Zaloguj się do [portalu Azure][azure_portal].
2. Kliknij kolejno opcje **Nowy** > **Obliczenia** > **Usługa Batch**.
   
    ![Usługa Batch w witrynie Marketplace][marketplace_portal]
3. Wyświetlony zostanie blok **Nowe konto usługi Batch**. Każdy element bloku opisano poniżej w punktach od *a* do *e*.
   
    ![Tworzenie konta usługi Batch][account_portal]
   
    a. **Nazwa konta**: unikatowa nazwa konta usługi Batch. Nazwa musi być niepowtarzalna w regionie usługi Azure, w którym konto zostanie utworzone (patrz punkt *Lokalizacja* poniżej). Może zawierać tylko małe litery i cyfry oraz mieć od 3 do 24 znaków.
   
    b. **Subskrypcja**: subskrypcja, w której ma zostać utworzone konto usługi Batch. Jeśli masz tylko jedną subskrypcję, jest ona wybrana domyślnie.
   
    c. **Grupa zasobów**: istniejąca grupa zasobów dla nowego konta usługi Batch. Opcjonalnie można utworzyć nową grupę zasobów.
   
    d. **Lokalizacja**: region usługi Azure, w którym ma zostać utworzone konto usługi Batch. Tylko regiony obsługiwane przez subskrypcję i grupę zasobów są wyświetlane jako opcje.
   
    e. **Konto magazynu** (opcjonalne): konto magazynu **Ogólnego przeznaczenia** skojarzone (link) z nowym kontem usługi Batch. Więcej szczegółów zamieszczono w sekcji [Połączone konto usługi Azure Storage](#linked-azure-storage-account) poniżej.
4. Kliknij przycisk **Utwórz**, aby utworzyć konto.
   
   W portalu pojawia się wskazanie, że konto jest **Wdrażane**, a po ukończeniu wdrażania jest wyświetlane powiadomienie **Wdrożenia zakończone pomyślnie** w obszarze *Powiadomienia*.

## Wyświetlanie właściwości konta usługi Batch
Po utworzeniu konta możesz otworzyć **Blok konta usługi Batch**, aby uzyskać dostęp do jego ustawień i właściwości. Menu po lewej stronie bloku konta usługi Batch zapewnia dostęp do wszystkich ustawień i właściwości konta.

![Blok konta usługi Batch w witrynie Azure Portal][account_blade]

* **Adres URL konta usługi Batch**: aplikacje tworzone przy użyciu [interfejsów API tworzenia usługi Batch](batch-technical-overview.md#batch-development-apis) wymagają adresu URL konta do zarządzania zasobami i uruchamiania zadań w ramach konta. Adres URL konta usługi Batch ma następujący format:
  
    `https://<account_name>.<region>.batch.azure.com`

![Adres URL konta usługi Batch w portalu][account_url]

* **Klucze dostępu**: aplikacje wymagają także klucza dostępu w przypadku pracy z zasobami w ramach konta usługi Batch. Aby wyświetlić lub ponownie wygenerować klucze dostępu konta usługi Batch, wprowadź wartość `keys` w polu **Wyszukaj** lewego menu w bloku konta usługi Batch, a następnie wybierz opcję **Klucze**.
  
    ![Klucze konta usługi Batch w witrynie Azure Portal][account_keys]

## Cennik
Konta usługi Batch są oferowane tylko w warstwie bezpłatnej, co oznacza brak opłat za samo konto usługi Batch. Opłaty są naliczane za podstawowe zasoby obliczeniowe platformy Azure używane przez rozwiązania usługi Batch oraz za zasoby używane przez inne usługi podczas działania Twoich obciążeń. Są na przykład naliczane opłaty za węzły obliczeniowe w pulach oraz za dane przechowywane w usłudze Azure Storage jako dane wejściowe i wyjściowe zadań. Podobnie jeśli używasz funkcji [pakietów aplikacji](batch-application-packages.md) usługi Batch, opłaty są naliczane za zasoby usługi Azure Storage wykorzystywane do przechowywania pakietów aplikacji. Więcej informacji można znaleźć w temacie [Ceny usługi Batch][batch_pricing].

## Połączone konto usługi Azure Storage
Jak wspomniano wcześniej, możesz (opcjonalnie) połączyć konto usługi Storage **ogólnego przeznaczenia** z kontem usługi Batch. Funkcja [pakietów aplikacji](batch-application-packages.md) usługi Batch korzysta z magazynu obiektów blob w ramach połączonego konta usługi Storage ogólnego przeznaczenia, podobnie jak biblioteka [.NET Batch File Conventions](batch-task-output.md). Te opcjonalne funkcje pomagają we wdrażaniu aplikacji uruchamianych przez zadania usługi Batch oraz utrwalaniu wytwarzanych przez nich danych.

Usługa Batch obsługuje obecnie *tylko* typ konta magazynu **ogólnego przeznaczenia**, zgodnie z opisem w kroku 5 [Tworzenie konta magazynu](../storage/storage-create-storage-account.md#create-a-storage-account) w temacie [About Azure storage accounts](../storage/storage-create-storage-account.md) (Informacje o kontach magazynu Azure). Gdy łączysz konta usługi Azure Storage ze swoim kontem usługi Batch, pamiętaj o połączeniu *tylko* konta magazynu **ogólnego przeznaczenia**.

![Tworzenie konta magazynu „ogólnego przeznaczenia”][storage_account]

Zaleca się utworzenie konta usługi Storage do wyłącznego użytku przez konto usługi Batch.

> [!WARNING]
> Należy zachować ostrożność przy ponownym generowaniu kluczy dostępu połączonego konta usługi Storage. Wygeneruj ponownie tylko jeden klucz konta usługi Storage i kliknij przycisk **Zsynchronizuj klucze** w bloku połączonego konta usługi Storage. Poczekaj 5 minut, aby umożliwić propagację kluczy do węzłów obliczeniowych w swoich pulach, a następnie w razie potrzeby ponownie wygeneruj i zsynchronizuj drugi klucz. Jeśli w tym samym czasie zostaną ponownie wygenerowane oba klucze, węzły obliczeniowe nie będą mogły zsynchronizować żadnego z nich i w ten sposób utracą dostęp do konta usługi Storage.
> 
> 

  ![Ponowne generowanie kluczy konta magazynu][4]

## Limity przydziału i limity usługi Batch
Należy pamiętać, że podobnie jak w przypadku subskrypcji i innych usług Azure, do kont usługi Batch mają zastosowanie określone [limity przydziału i limity](batch-quota-limit.md). Bieżące limity przydziału dla konta usługi Batch są wyświetlane w portalu w obszarze **Właściwości** konta.

![Limity przydziału konta usługi Batch w witrynie Azure Portal][quotas]

Pamiętaj o tych limitach przydziału podczas projektowania i skalowania obciążeń usługi Batch. Jeśli na przykład pula nie osiąga docelowej, określonej liczby węzłów obliczeniowych, być może osiągnięto limit przydziału rdzeni dla konta usługi Batch.

Należy również pamiętać, że subskrypcja platformy Azure nie jest ograniczona do jednego konta usługi Batch. Wiele obciążeń usługi Batch można uruchomić na jednym koncie usługi Batch lub rozdzielić obciążenia pomiędzy konta tej usługi w jednej subskrypcji, ale różnych regionach usługi Azure.

Wiele z tych limitów przydziału można powiększyć po prostu przy użyciu bezpłatnego żądania pomocy technicznej przesłanego w witrynie Azure Portal. Szczegóły dotyczące żądań zwiększenia limitów przydziału zamieszczono w artykule [Quotas and limits for the Azure Batch service](batch-quota-limit.md) (Limity przydziału i limity dla usługi Azure Batch).

## Inne opcje zarządzania kontem usługi Batch
Poza korzystaniem z witryny Azure Portal można również utworzyć konta usługi Batch i zarządzać nimi przy użyciu następujących opcji:

* [Polecenia cmdlet programu PowerShell usługi Batch](batch-powershell-cmdlets-get-started.md)
* [Interfejs wiersza polecenia platformy Azure](../xplat-cli-install.md)
* [Batch Management .NET](batch-management-dotnet.md)

## Następne kroki
* Aby dowiedzieć się więcej o zasadach działania i funkcjach usługi Batch, zobacz temat [Omówienie funkcji usługi Azure Batch](batch-api-basics.md). W artykule omówiono podstawowe zasoby usługi Batch, takie jak pule, węzły obliczeniowe i zadania, oraz opisano funkcje, które umożliwiają wykonywanie obciążeń zasobów obliczeniowych na dużą skalę.
* Poznaj podstawy tworzenia aplikacji wykorzystujących usługę Batch za pomocą biblioteki klienta [Batch .NET](batch-dotnet-get-started.md). [Artykuł wprowadzający](batch-dotnet-get-started.md) zawiera omówienie działającej aplikacji, która korzysta z usługi Batch do wykonywania obciążenia na wielu węzłach obliczeniowych, i opisuje zastosowanie usługi Azure Storage do tymczasowego przechowywania i pobierania pliku obciążenia.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Ponowne generowanie kluczy konta magazynu"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png



<!--HONumber=Sep16_HO3-->


