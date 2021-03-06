---
title: Omówienie usługi Access Control w usłudze Data Lake Store | Microsoft Docs
description: Zrozumienie kontroli dostępu w usłudze Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2016
ms.author: nitinme

---
# Kontrola dostępu w usłudze Azure Data Lake Store
Usługa Data Lake Store wdraża model kontroli dostępu pochodzący z systemu plików HDFS oraz z modelu kontroli dostępu POSIX. Ten artykuł zawiera podsumowanie podstaw modelu kontroli dostępu dla usługi Data Lake Store. Aby dowiedzieć się więcej o modelu kontroli dostępu systemu plików HDFS, zobacz [Przewodnik po uprawnieniach systemu plików HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## Listy kontroli dostępu do plików i folderów
Istnieją dwa typy list kontroli dostępu (ACL) — **Listy ACL dostępu** i **Domyślne listy ACL**.

* **Listy ACL dostępu** — listy te kontrolują dostęp do obiektu. Pliki i foldery mają listy ACL dostępu.
* **Domyślne listy ACL** — „szablon” list ACL skojarzonych z folderem, które określają listy ACL dostępu dla wszelkich elementów podrzędnych utworzonych w tym folderze. Pliki nie mają domyślnych list ACL.

![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

Zarówno listy ACL dostępu, jak i domyślne listy ACL mają tę samą strukturę.

![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-2.png)

> [!NOTE]
> Zmiana domyślnej listy ACL w lokalizacji nadrzędnej nie wpływa na listę ACL dostępu lub domyślną listę ACL elementów podrzędnych, które już istnieją.
> 
> 

## Użytkownicy i tożsamości
Każdy plik i folder ma różne uprawnienia do tych tożsamości:

* Użytkownik będący właścicielem
* Grupa będąca właścicielem
* Użytkownicy nazwani
* Grupy nazwane
* Wszyscy pozostali użytkownicy

Tożsamości użytkowników i grup są tożsamościami usługi Azure Active Directory (AAD), dlatego też „użytkownik” w kontekście usługi Data Lake Store może oznaczać użytkownika usługi AAD lub grupę zabezpieczeń usługi AAD, chyba że podano inaczej.

## Uprawnienia
Uprawnienia do obiektu systemu plików to uprawnienia do **odczytu**, **zapisu** i **wykonania**. Mogą być używane wobec plików i folderów, jak pokazano w poniższej tabeli.

|  | Plik | Folder |
| --- | --- | --- |
| **Odczyt (R)** |Może odczytywać zawartości pliku |Wymaga uprawnień do **odczytu** i **wykonania**, aby wyświetlać listę zawartości folderu. |
| **Zapis (W)** |Może zapisywać w pliku lub dołączać do pliku |Wymaga uprawnień do **zapisu i wykonania**, aby tworzyć elementy podrzędne w folderze. |
| **Wykonanie (X)** |Nie oznacza niczego w kontekście usługi Data Lake Store |Wymagane w przypadku przechodzenia między elementami podrzędnymi w folderze. |

### Krótkie formy uprawnień
Skrót **RWX** służy do wskazywania uprawnień do **odczytu, zapisu i wykonania**. Istnieje bardziej skondensowana forma liczbowa, w której uprawnienia do **odczytu = 4**, **zapisu = 2** i **wykonania = 1**, a ich suma reprezentuje uprawnienie. Poniżej przedstawiono kilka przykładów.

| Forma liczbowa | Forma krótka | Co to oznacza |
| --- | --- | --- |
| 7 |RWX |Odczyt (R) + zapis (W) + wykonanie (X) |
| 5 |R-X |Odczyt (R) + wykonanie (X) |
| 4 |R-- |Odczyt |
| 0 |--- |Brak uprawnień |

### Uprawnienia nie są dziedziczone
W modelu w stylu POSIX używanym przez usługę Data Lake Store uprawnienia dla elementu są przechowywane w samym elemencie. Innymi słowy, uprawnienia dla elementu nie mogą być dziedziczone z elementów nadrzędnych.

## Typowe scenariusze dotyczące uprawnień
Poniżej przedstawiono kilka typowych scenariuszy, które umożliwiają zrozumienie, które uprawnienia są wymagane do wykonania danych operacji na koncie usługi Data Lake Store.

### Uprawnienia wymagane do odczytu pliku
![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* W przypadku pliku, który będzie odczytywany, wywołujący musi mieć uprawnienia do **odczytu**
* W przypadku wszystkich folderów w strukturze folderów, które zawierają plik, wywołujący musi mieć uprawnienia do **wykonania**

### Uprawnienia wymagane do dołączania do pliku
![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* W przypadku pliku, do którego będzie dołączana zawartość, wywołujący musi mieć uprawnienia do **zapisu**
* W przypadku wszystkich folderów, które zawierają plik, wywołujący musi mieć uprawnienia do **wykonania**

### Uprawnienia wymagane do usunięcia pliku
![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* W przypadku folderu nadrzędnego wywołujący musi mieć uprawnienia do **zapisu i wykonania**
* W przypadku innych folderów w ścieżce pliku wywołujący musi mieć uprawnienia do **wykonania**

> [!NOTE]
> Uprawnienia do zapisu dla pliku nie są wymagane do usunięcia pliku, jeśli dwa powyższe warunki pozostają spełnione.
> 
> 

### Uprawnienia wymagane do wyliczenia folderu
![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* W przypadku folderu do wyliczenia wywołujący musi mieć uprawnienia do **odczytu i wykonania**
* W przypadku wszystkich folderów nadrzędnych wywołujący musi mieć uprawnienia do **wykonania**

## Wyświetlanie uprawnień w witrynie Azure Portal
W bloku **Eksplorator danych** konta usługi Data Lake Store kliknij przycisk **Dostęp**, aby zobaczyć listy ACL dla pliku lub folderu. Na poniższym ekranie kliknij przycisk Dostęp, aby zobaczyć listy ACL dla folderu **catalog** konta **mydatastore**.

![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

Po wykonaniu tego kroku w bloku **Dostęp** kliknij przycisk **Widok prosty**, aby zobaczyć prostszy widok.

![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Kliknij przycisk **Widok zaawansowany**, aby zobaczyć bardziej zaawansowany widok.

![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## Administrator
Administrator ma najwięcej uprawnień spośród wszystkich użytkowników usługi Data Lake Store. Administrator:

* ma uprawnienia RWX do **wszystkich** plików i folderów;
* może zmieniać uprawnienia do dowolnego pliku lub folderu;
* może zmieniać właściciela lub grupę będącą właścicielem dla dowolnego pliku lub folderu.

Na platformie Azure konto usługi Data Lake Store ma kilka ról platformy Azure:

* Właściciele
* Współautorzy
* Czytelnicy
* Itp.

Wszyscy użytkownicy w roli **Właściciele** dla konta usługi Data Lake Store są automatycznie administratorami dla tego konta. Aby uzyskać więcej informacji na temat kontroli dostępu na podstawie ról na platformie Azure (RBAC), zobacz temat [Role-based acces control](../active-directory/role-based-access-control-configure.md) (Kontrola dostępu na podstawie ról).

## Użytkownik będący właścicielem
Użytkownik, który utworzył element, jest automatycznie właścicielem elementu. Użytkownik będący właścicielem może:

* zmieniać uprawnienia dla pliku, którego jest właścicielem;
* zmieniać grupę będącą właścicielem dla pliku, którego jest właścicielem, jeśli użytkownik będący właścicielem jest również członkiem grupy docelowej.

> [!NOTE]
> Użytkownik będący właścicielem **nie może** zmienić użytkownika będącego właścicielem innego pliku. Tylko administratorzy mogą zmieniać użytkowników będących właścicielami pliku lub folderu.
> 
> 

## Grupa będąca właścicielem
Na listach ACL w modelu POSIX każdy użytkownik jest skojarzony z „grupą główną”. Przykładowo użytkownik „Alicja” może należeć do grupy „Finanse”. Alicja może należeć do wielu grup, ale jedna grupa jest zawsze wyznaczona jako jej grupa główna. W modelu POSIX, gdy Alicja tworzy plik, na grupę będąca właścicielem tego pliku zostaje ustawiona jej grupa główna. W tym przypadku jest to grupa „Finanse”.

Gdy zostaje utworzony nowy element systemu plików, usługa Data Lake Store przypisuje wartość grupie będącej właścicielem. 

* **Przypadek 1** — folder główny „/”. Ten folder jest tworzony wraz z kontem usługi Data Lake Store. W takim przypadku grupa będąca właścicielem jest ustawiana na użytkownika, który utworzył konto.
* **Przypadek 2** (każdy inny przypadek) — gdy tworzony jest nowy element, grupa będąca właścicielem jest kopiowana z folderu nadrzędnego.

Grupę będącą właścicielem może zmienić:

* każdy administrator;
* użytkownik będący właścicielem, jeśli jest on również członkiem grupy docelowej.

## Algorytm kontroli dostępu
Poniższa ilustracja przedstawia algorytm kontroli dostępu dla kont usługi Data Lake Store.

![Algorytm list ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)

## Maska i „czynne uprawnienia”
**Maska** jest wartością RWX używaną do ograniczania dostępu dla **użytkowników nazwanych**, **grupy będącej właścicielem** i **grup nazwanych** w przypadku wykonywania algorytmu kontroli dostępu. Oto kluczowe założenia maski. 

* Maska tworzy „czynne uprawnienia”, tj. modyfikuje uprawnienia w momencie przeprowadzania kontroli dostępu.
* Maskę może edytować bezpośrednio właściciel pliku i dowolny administrator.
* Maska ma możliwość usuwania uprawnień w celu tworzenia czynnych uprawnień. Maska **nie może** dodawać uprawnień do czynnych uprawnień. 

Przyjrzyjmy się kilku przykładom. Poniżej maskę ustawiono na **RWX**, co oznacza, że ta maska nie usuwa żadnych uprawnień. Zwróć uwagę, że czynne uprawnienia dla użytkownika nazwanego, grupy będącej właścicielem i grupy nazwanej nie są modyfikowane podczas kontroli dostępu.

![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

W poniższym przykładzie maska jest ustawiona na **R-X**. Maska **wyłącza uprawnienie do zapisu** dla **użytkownika nazwanego**, **grupy będącej właścicielem** oraz **grupy nazwanej** w momencie kontroli dostępu.

![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Jest to miejsce, w którym maska dla pliku lub folderu pojawia się w witrynie Azure Portal.

![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

> [!NOTE]
> W przypadku nowego konta usługi Data Lake Store maski dla listy ACL dostępu i domyślnej listy ACL folderu głównego („/”) są domyślnie maskami RWX.
> 
> 

## Uprawnienia do nowych plików i folderów
Gdy nowy plik lub folder jest tworzony w istniejącym folderze, domyślna lista ACL w folderze nadrzędnym określa:

* domyślną listę ACL i listę ACL dostępu folderu podrzędnego;
* listę ACL dostępu pliku podrzędnego (pliki nie mają domyślnej listy ACL);

### listę ACL dostępu pliku lub folderu podrzędnego.
Gdy tworzony jest plik lub folder podrzędny, domyślna lista ACL lokalizacji nadrzędnej jest kopiowana jako lista ACL dostępu pliku lub folderu podrzędnego. Ponadto jeśli **inny** użytkownik ma uprawnienia RWX w domyślnej liście ACL lokalizacji nadrzędnej, zostaje całkowicie usunięty z listy ACL dostępu elementu podrzędnego.

![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

W większości przypadków powyższe informacje są wszystkim, co należy wiedzieć o sposobie określania listy ACL dostępu elementu podrzędnego. Niemniej jednak jeśli znasz systemy POSIX i chcesz lepiej zrozumieć sposób działania tej transformacji, zapoznaj się z sekcją [Rola maski umask w tworzeniu listy ACL dostępu do nowych plików i folderów](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) poniżej w tym artykule.

### Domyślna lista ACL folderu podrzędnego
Gdy folder podrzędny jest tworzony w folderze nadrzędnym, domyślna lista ACL folderu nadrzędnego jest kopiowana bez zmian do domyślnej listy ACL folderu podrzędnego.

![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## Zaawansowane tematy związane z listami ACL w usłudze Data Lake Store
Poniżej przedstawiono kilka zaawansowanych tematów, które pomagają zrozumieć, jak listy ACL są określane dla plików lub folderów usługi Data Lake Store.

### Rola maski umask w tworzeniu listy ACL dostępu do nowych plików i folderów
W systemie zgodnym z modelem POSIX ogólnym założeniem jest, że maska umask jest 9-bitową wartością w folderze nadrzędnym używaną do przekształcania uprawnień dla **użytkownika będącego właścicielem**, **grupy będącej właścicielem** i **innych** na liście ACL dostępu nowego pliku lub folderu podrzędnego. Bity mapy umask identyfikują bity, które zostaną wyłączone na liście ACL dostępu elementu podrzędnego. W związku z tym maski używa się selektywnie, aby zapobiegać propagacji uprawnień użytkownika będącego właścicielem, grupy będącej właścicielem i innych.

W systemie plików HDFS maska umask jest typowo opcją konfiguracji obejmującą całą lokację, którą kontrolują administratorzy. Usługa Data Lake Store używa **maski umask obejmującej całe konto**, której nie można zmienić. W poniższej tabeli przedstawiono maskę umask usługi Data Lake Store.

| Grupa użytkowników | Ustawienie | Wpływ na listę ACL dostępu nowego elementu podrzędnego |
| --- | --- | --- |
| Użytkownik będący właścicielem |--- |Brak wpływu |
| Grupa będąca właścicielem |--- |Brak wpływu |
| Inne |RWX |Usuwanie uprawnień do odczytu, zapisu, wykonania |

Poniższa ilustracja przedstawia tę maskę umask w działaniu. Efektem sieciowym jest usunięcie uprawnień do **odczytu, zapisu i wykonania** dla **innego** użytkownika. Ponieważ maska umask nie określiła bitów dla **użytkownika będącego właścicielem** oraz **grupy będącej właścicielem**, uprawnienia te nie są przekształcane.

![Listy ACL usługi Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-umask.png) 

### Atrybut sticky bit
Sticky bit jest bardziej zaawansowaną funkcją systemu plików POSIX. W kontekście usługi Data Lake Store jest mało prawdopodobne, że atrybut sticky bit będzie potrzebny.

W poniższej tabeli przedstawiono sposób działania atrybutu sticky bit w usłudze Data Lake Store.

| Grupa użytkowników | Plik | Folder |
| --- | --- | --- |
| Sticky bit **WYŁ.** |Brak wpływu |Brak wpływu |
| Sticky bit **WŁ.** |Brak wpływu |Uniemożliwia wszystkich użytkownikom poza **administratorami** i **użytkownikiem będącym właścicielem** elementu podrzędnego usunięcie lub zmianę nazwy tego elementu podrzędnego. |

Atrybut sticky bit nie jest wyświetlany w witrynie Azure Portal.

## Często zadawane pytania dotyczące list ACL w usłudze Data Lake Store
Oto kilka często zadawanych pytań w kontekście list ACL w usłudze Data Lake Store.

### Czy muszę włączyć obsługę list ACL?
Nie. Kontrola dostępu za pośrednictwem list ACL jest zawsze włączona dla konta usługi Data Lake Store.

### Jakie uprawnienia są wymagane do rekursywnego usunięcia folderu i jego zawartości?
* Folder nadrzędny musi mieć uprawnienia do **zapisu i wykonania**.
* Folder do usunięcia i każdy folder w tym folderze muszą mieć uprawnienia do **odczytu, zapisu i wykonania**.
  >[AZURE.NOTE] Usuwanie plików w folderach nie wymaga uprawnienia do zapisu dla tych plików. Ponadto nigdy nie można usunąć folderu głównego „/” **nigdy**.

### Kto jest ustawiony jako właściciel pliku lub folderu?
Twórca pliku lub folderu staje się jego właścicielem.

### Jaka grupa zostaje ustawiona jako grupa będąca właścicielem pliku lub folderu w momencie jego tworzenia?
Grupa jest kopiowana z grupy będącej właścicielem folderu nadrzędnego, w którym tworzony jest nowy plik lub folder.

### Jestem właścicielem pliku, ale nie mam wymaganych uprawnień RWX. Co mam zrobić?
Użytkownik będący właścicielem może po prostu zmienić uprawnienia do pliku, aby przydzielić sobie wymagane uprawnienia RWX.

### Czy usługa Data Lake Store obsługuje dziedziczenie list ACL?
Nie.

### Jaka jest różnica między maską i maską umask?
| maska | maska umask |
| --- | --- |
| Właściwość **maski** jest dostępna dla każdego pliku i folderu. |**Umask** jest właściwością konta usługi Data Lake Store. W usłudze Data Lake Store istnieje więc tylko jedna maska umask. |
| Właściwość maski dla pliku lub folderu może zmienić użytkownik będący właścicielem lub grupa będąca właścicielem pliku, a także administrator. |Właściwości maski umask nie może zmodyfikować żaden użytkownik, nawet administrator. Jest to niezmienna, stała wartość. |
| Właściwość maski jest używana podczas wykonywania algorytmu kontroli dostępu w momencie uruchomienia, aby określić, czy użytkownik ma prawo wykonać operację na pliku lub folderze. Rolą maski jest tworzenie „czynnych uprawnień” w momencie przeprowadzania kontroli dostępu. |Właściwość maski umask nie jest używana podczas kontroli dostępu. Maski umask używa się do określania listy ACL dostępu dla nowych elementów podrzędnych w folderze. |
| Maska jest 3-bitową wartością RWX stosowaną do nazwanego użytkownika, nazwanej grupy i użytkownika będącego właścicielem w czasie przeprowadzania kontroli dostępu. |Umask jest 9-bitową wartością stosowaną do użytkownika będącego właścicielem, grupy będącej właścicielem i innych użytkowników nowego elementu podrzędnego. |

### Gdzie można dowiedzieć się więcej na temat modelu kontroli dostępu POSIX?
* [http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html)
* [Przewodnik po uprawnieniach systemu plików HDFS](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) 
* [POSIX — Często zadawane pytania](http://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1e 1997](http://users.suse.com/~agruen/acl/posix/Posix_1003.1e-990310.pdf)
* [Listy ACL modelu POSIX w systemie Linux](http://users.suse.com/~agruen/acl/linux-acls/online/)
* [Listy ACL korzystające z list usługi Access Control w systemie Linux](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## Zobacz też
* [Omówienie usługi Azure Data Lake Store](data-lake-store-overview.md)
* [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

<!--HONumber=Sep16_HO3-->


