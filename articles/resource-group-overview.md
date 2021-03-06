---
title: Omówienie usługi Azure Resource Manager | Microsoft Docs
description: Opis wdrażania zasobów na platformie Azure, kontrolowania dostępu do tych zasobów oraz zarządzania nimi za pomocą usługi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2016
ms.author: tomfitz

---
# Omówienie usługi Azure Resource Manager
Infrastruktura aplikacji zwykle obejmuje wiele składników — może to być maszyna wirtualna, konto magazynu i sieć wirtualna albo aplikacja sieci Web, baza danych, serwer bazy danych i usługi zewnętrzne. Te składniki nie są widoczne jako osobne jednostki, tylko jako powiązane i zależne od siebie nawzajem części jednej całości. Dlatego najlepiej wdrażać i monitorować je oraz zarządzać nimi grupowo. Usługa Azure Resource Manager umożliwia pracę z zasobami tworzącymi rozwiązanie w formie grupy. Wszystkie zasoby danego rozwiązania można wdrożyć, zaktualizować lub usunąć w ramach jednej skoordynowanej operacji. Wdrażanie wykonuje się przy użyciu szablonu, którego można następnie używać w różnych środowiskach (testowanie, etap przejściowy i produkcja). Usługa Resource Manager zapewnia funkcje zabezpieczeń, inspekcji i tagowania ułatwiające zarządzanie zasobami po wdrożeniu. 

## Terminologia
Jeśli dopiero zaczynasz korzystać z usługi Azure Resource Manager, oto kilka terminów, których możesz nie znać.

* **Zasób** — dostępny za pośrednictwem platformy Azure element, którym można zarządzać. Niektóre typowe zasoby to: maszyna wirtualna, konto magazynu, aplikacja sieci Web czy sieć wirtualna. Istnieje ich jednak wiele więcej.
* **Grupa zasobów** — kontener, który zawiera powiązane zasoby rozwiązania dla platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji. Zobacz [Grupy zasobów](#resource-groups).
* **Dostawca zasobów** — usługa dostarczająca zasoby, które można wdrażać i którymi można zarządzać za pomocą usługi Resource Manager. Każdy dostawca zasobów udostępnia operacje do pracy z wdrażanymi zasobami. Typowi dostawcy zasobów to: Microsoft.Compute dostarczający zasób maszyny wirtualnej, Microsoft.Storage dostarczający zasób konta magazynu i Microsoft.Web dostarczający zasoby dotyczące aplikacji sieci Web. Zobacz [Dostawcy zasobów](#resource-providers).
* **Szablon usługi Resource Manager** — plik w formacie JavaScript Object Notation (JSON) definiujący jeden lub większą liczbę zasobów, które mają zostać wdrożone w grupie zasobów. Definiuje również zależności między wdrożonymi zasobami. Szablon może służyć do spójnego i wielokrotnego wdrażania zasobów. Zobacz [Wdrażanie na podstawie szablonu](#template-deployment).
* **Składnia deklaratywna** — składnia pozwalająca określić, co zamierzasz utworzyć, bez konieczności pisania w tym celu sekwencji poleceń programistycznych. Przykładem składni deklaratywnej jest szablon usługi Resource Manager. W tym pliku definiuje się właściwości infrastruktury do wdrożenia na platformie Azure. 

## Zalety korzystania z usługi Resource Manager
Usługa Resource Manager zapewnia kilka korzyści:

* Możliwość grupowego wdrożenia i monitorowania wszystkich zasobów w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania zasobów pojedynczo).
* Możliwość wielokrotnego wdrażania rozwiązania w całym cyklu programistycznym z gwarancją spójnego stanu zasobów po każdym wdrożeniu.
* Możliwość zarządzania infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.
* Możliwość definiowania zależności między zasobami, aby wdrażać je w odpowiedniej kolejności.
* Możliwość stosowania kontroli dostępu do wszystkich usług w grupie zasobów dzięki natywnej integracji funkcji kontroli dostępu na podstawie ról z platformą zarządzania.
* Możliwość dodawania tagów do zasobów w celu logicznego uporządkowania wszystkich zasobów w ramach subskrypcji.
* Możliwość wyjaśniania rozliczeń w organizacji przez wyświetlanie kosztów dla grupy zasobów korzystających z tego samego tagu.  

Usługa Resource Manager udostępnia nową metodę wdrażania rozwiązań i zarządzania nimi. Jeśli znasz wcześniejszy model wdrażania i chcesz dowiedzieć się więcej o zmianach, zobacz artykuł [Understanding Resource Manager deployment and classic deployment](resource-manager-deployment-model.md) (Opis wdrażania za pomocą usługi Resource Manager oraz wdrażania klasycznego).

## Wskazówki
Poniższe sugestie pomogą Ci w pełni wykorzystać możliwości usługi Resource Manager w pracy z rozwiązaniami.

1. Definiuj i wdrażaj infrastrukturę za pomocą składni deklaratywnej w szablonach usługi Resource Manager, a nie za pomocą poleceń imperatywnych.
2. Zdefiniuj w szablonie wszystkie etapy wdrażania i konfiguracji. W konfiguracji rozwiązania nie powinno być żadnych etapów ręcznych.
3. Korzystaj z poleceń imperatywnych do zarządzania zasobami, np. do uruchamiania i zatrzymywania aplikacji lub maszyny.
4. Rozmieść zasoby z tym samym cyklem życia w grupie zasobów. We wszystkich pozostałych operacjach związanych z organizacją zasobów używaj tagów.

Aby uzyskać więcej zaleceń, zobacz [Best practices for creating Azure Resource Manager templates](resource-manager-template-best-practices.md) (Najlepsze rozwiązania dotyczące tworzenia szablonów usługi Azure Resource Manager).

## Grupy zasobów
Definiując grupę zasobów, należy wziąć pod uwagę pewne ważne czynniki:

1. Wszystkie zasoby w grupie powinny mieć ten sam cykl życia. Są one wdrażane, aktualizowane i usuwane razem. Jeśli jakiś zasób, na przykład serwer bazy danych, ma mieć inny cykl wdrażania, powinien zostać umieszczony w innej grupie zasobów.
2. Każdy zasób może znajdować się tylko w jednej grupie zasobów.
3. Zasoby w grupie można dodawać i usuwać w dowolnym momencie.
4. Zasoby można przenosić między poszczególnymi grupami. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](resource-group-move-resources.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).
5. Grupa zasobów może zawierać zasoby, które znajdują się w różnych regionach.
6. Grupa zasobów może służyć do określania zakresu kontroli dostępu na potrzeby działań administracyjnych.
7. Zasób może wchodzić w interakcję z zasobami znajdującymi się w innych grupach zasobów. Ta interakcja jest typowa, gdy dwa zasoby są ze sobą powiązane, ale nie mają tego samego cyklu życia (na przykład aplikacje sieci Web łączące się z bazą danych).

Podczas tworzenia grupy zasobów, należy podać lokalizację dla danej grupy zasobów. Być może zastanawiasz się, „Dlaczego grupa zasobów wymaga określenia lokalizacji? Ponadto dlaczego lokalizacja grupy zasobów jest w ogóle istotna, skoro zasoby mogą znajdować się w innej lokalizacji niż grupa zasobów?” Grupa zasobów przechowuje metadane dotyczące zasobów. Z tego powodu określając lokalizację dla grupy zasobów, określasz miejsce, w którym przechowywane są metadane. Dla zachowania zgodności może być konieczne upewnienie się, że dane są przechowywane w odpowiednim regionie.

## Dostawcy zasobów
Każdy dostawca zasobów udostępnia zestaw zasobów i operacji do pracy w obszarze technicznym. Na przykład w celu przechowywania kluczy i kluczy tajnych należy użyć dostawcy zasobów **Microsoft.KeyVault**. Ten dostawca zasobów udostępnia typ zasobu o nazwie **magazyny** do utworzenia magazynu kluczy oraz typ **magazyny/klucze tajne** do utworzenia klucza tajnego w magazynie kluczy. Ponadto udostępnia operacje w postaci [operacji interfejsu REST API usługi Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx). Interfejs REST API można wywoływać bezpośrednio bądź zarządzać magazynem kluczy przy użyciu [poleceń cmdlet programu PowerShell dla usługi Key Vault](https://msdn.microsoft.com/library/dn868052.aspx) oraz [interfejsu wiersza polecenia platformy Azure dla usługi Key Vault](key-vault/key-vault-manage-with-cli.md). Do pracy z większością zasobów można także skorzystać z różnych języków programowania. Aby uzyskać więcej informacji, zobacz [Zestawy SDK i przykłady](#sdks-and-samples). 

Na potrzeby wdrażania i zarządzania infrastrukturą musisz znać szczegółowe informacje o dostawcy zasobów. Należy zapoznać się z jego typami zasobów, numerami wersji dla operacji interfejsu API REST, obsługiwanymi operacjami i schematem używanym do tworzenia zasobów. Aby dowiedzieć się więcej na temat obsługiwanych dostawców zasobów, zobacz [Resource Manager providers, regions, API versions and schemas](resource-manager-supported-services.md) (Dostawcy, regiony, wersje interfejsów API i schematy usługi Resource Manager).

## Wdrażanie na podstawie szablonu
Usługa Resource Manager umożliwia utworzenie szablonu (w formacie JSON) do definiowania wdrażania i konfiguracji aplikacji. Dzięki szablonowi można wielokrotnie wdrażać aplikację w całym jej cyklu życia z gwarancją spójnego stanu zasobów po każdym wdrożeniu. Usługa Azure Resource Manager analizuje zależności i sprawdza, czy zasoby są tworzone we właściwej kolejności. Aby uzyskać więcej informacji, zobacz [Defining dependencies in Azure Resource Manager templates](resource-group-define-dependencies.md) (Definiowanie zależności w szablonach usługi Azure Resource Manager).

W przypadku tworzenia rozwiązania z portalu rozwiązanie automatycznie zawiera szablon wdrożenia. Nie trzeba tworzyć szablonu od początku — można zacząć od szablonu istniejącego rozwiązania i dostosować go do konkretnych potrzeb. Aby uzyskać szablon dla istniejącej grupy zasobów, można wyeksportować bieżący stan grupy lub skorzystać z szablonu użytego do określonego wdrożenia. Przeglądając wyeksportowany szablon, można poznać jego składnię. Aby dowiedzieć się więcej o pracy z wyeksportowanymi szablonami , zobacz [Eksportowanie szablonu usługi Azure Resource Manager z istniejących zasobów](resource-manager-export-template.md).

Nie trzeba definiować całej infrastruktury w jednym szablonie. Często dobrym rozwiązaniem jest podział wymagań dotyczących wdrożenia na szablony przeznaczone do określonego celu. Te szablony mogą bez problemu być używane wielokrotnie w różnych rozwiązaniach. Aby wdrożyć dane rozwiązanie, należy utworzyć szablon wzorcowy połączony ze wszystkimi wymaganymi szablonami. Aby uzyskać więcej informacji, zobacz [Using linked templates with Azure Resource Manager](resource-group-linked-templates.md) (Używanie szablonów połączonych w usłudze Azure Resource Manager).

Szablonu można także używać w celu aktualizacji infrastruktury. Można na przykład dodać zasób do rozwiązania lub dodać reguły konfiguracji dla już wdrożonych zasobów. Jeśli szablon służy do utworzenia zasobu, ale ten zasób już istnieje, usługa Azure Resource Manager przeprowadzi aktualizację, zamiast tworzyć nowy element zawartości. Usługa Azure Resource Manager zaktualizuje istniejący zasób do stanu określonego dla nowego zasobu. Można także polecić usłudze Resource Manager usunięcie wszystkich zasobów, które nie są zdefiniowane w szablonie. Aby poznać różne opcje wdrażania, zobacz [Deploy an application with Azure Resource Manager template](resource-group-template-deploy.md) (Wdrażanie aplikacji za pomocą szablonu usługi Azure Resource Manager). 

Szablon może mieć parametry umożliwiające dostosowywanie i zapewnienie elastyczności wdrożenia. Można na przykład podać wartości parametrów dostosowujące wdrożenie do danego środowiska testowego. Określając parametry, można użyć tego samego szablonu do wdrożenia rozwiązania w różnych środowiskach.

Usługa Resource Manager zapewnia rozszerzenia na potrzeby sytuacji, gdy potrzebne są dodatkowe operacje, które nie są uwzględnione w konfiguracji (np. zainstalowanie konkretnego oprogramowania). Jeśli używasz już usługi do zarządzania konfiguracją, takiej jak DSC, Chef lub Puppet, dzięki rozszerzeniom możesz z nią dalej bez przeszkód pracować.

Ponadto szablon staje się częścią kodu źródłowego aplikacji. Można go zaewidencjonować w repozytorium kodu źródłowego i aktualizować w miarę rozwijania aplikacji. Do edycji szablonu można używać programu Visual Studio.

Aby uzyskać więcej informacji na temat definiowania szablonu, zobacz [Authoring Azure Resource Manager Templates](resource-group-authoring-templates.md) (Tworzenie szablonów usługi Azure Resource Manager).

Aby uzyskać szczegółowe instrukcje dotyczące tworzenia szablonu, zobacz [Przewodnik po szablonie usługi Resource Manager](resource-manager-template-walkthrough.md).

Aby uzyskać wskazówki dotyczące wdrażania rozwiązania w różnych środowiskach, zobacz [Development and test environments in Microsoft Azure](solution-dev-test-environments.md) (Środowiska projektowe i testowe na platformie Microsoft Azure). 

## Tagi
Usługa Resource Manager udostępnia funkcję tagowania umożliwiającą dzielenie zasobów na kategorie zgodnie z wymaganiami zarządzania lub rozliczeń. Tagi są przydatne w przypadku złożonych kolekcji grup zasobów i zasobów, które trzeba uporządkować wizualnie w możliwie logicznej formie. Można na przykład oznaczyć tagami zasoby, które pełnią podobną rolę w organizacji lub należą do tego samego działu. Bez użycia tagów użytkownicy w organizacji mogą tworzyć wiele zasobów, które będą później bardzo trudne do znalezienia i zarządzania. Na przykład możesz chcieć usunąć wszystkie zasoby dla określonego projektu. Jeśli te zasoby nie są opatrzone tagiem dla projektu, trzeba je znaleźć ręcznie. Tagowanie może być istotnym sposobem na zredukowanie niepotrzebnych kosztów w ramach subskrypcji. 

Zasoby mogą być oznaczone tym samym tagiem, nawet jeśli nie znajdują się w tej samej grupie zasobów. Można utworzyć własną taksonomię tagów, aby mieć pewność, że wszyscy użytkownicy w organizacji używają tych samych tagów. Dzięki temu uniknie się pomyłek wynikających z użycia podobnych tagów (na przykład „wydział” zamiast „dział”).

Aby uzyskać więcej informacji na temat tagów, zobacz [Using tags to organize your Azure resources](resource-group-using-tags.md) (Porządkowanie zasobów na platformie Azure za pomocą tagów). Można także utworzyć [dostosowane zasady](#manage-resources-with-customized-policies) wymagające dodawania tagów do zasobów podczas wdrażania.

## Kontrola dostępu
Usługa Resource Manager pozwala kontrolować, kto może wykonywać określone czynności w organizacji. Zapewnia ona natywną integrację usługi OAuth i funkcji kontroli dostępu na podstawie ról z platformą zarządzania, umożliwiając stosowanie kontroli dostępu do wszystkich usług w grupie zasobów. Wstępnie zdefiniowane role dotyczące platform i zasobów można przypisywać użytkownikom, a następnie stosować je do subskrypcji, grup zasobów lub pojedynczych zasobów w celu zapewnienia kontroli dostępu. Można na przykład skorzystać ze wstępnie zdefiniowanej roli „Współautor bazy danych SQL” umożliwiającej użytkownikom zarządzanie bazami danych, ale nie serwerami baz danych ani zasadami zabezpieczeń. Wystarczy dodać użytkowników w organizacji, którzy potrzebują tego typu dostępu, do roli „Współautor bazy danych SQL” i zastosować rolę do subskrypcji, grupy zasobów lub zasobu.

Usługa Resource Manager automatycznie rejestruje czynności użytkownika na potrzeby inspekcji. Aby uzyskać informacje dotyczące pracy z dziennikami inspekcji, zobacz [Audit operations with Resource Manager](resource-group-audit.md) (Operacje inspekcji w usłudze Resource Manager).

Aby uzyskać więcej informacji na temat kontroli dostępu na podstawie ról, zobacz temat [Azure Role-Based Access Control](active-directory/role-based-access-control-configure.md) (Kontrola dostępu na podstawie ról na platformie Azure). Listę ról wbudowanych i dozwolonych w ramach nich czynności można znaleźć w artykule [RBAC: Built in Roles](active-directory/role-based-access-built-in-roles.md) (Kontrola dostępu na podstawie ról: role wbudowane). Role wbudowane obejmują m.in. role ogólne (Właściciel, Czytelnik czy Współautor) oraz role związane z konkretnymi usługami (Współautor·maszyny·wirtualnej, Współautor·usługi Virtual Network czy Menedżer·zabezpieczeń·SQL).

Można również jawnie zablokować dostęp do kluczowych zasobów, aby uniemożliwić użytkownikom ich usuwanie i modyfikowanie. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](resource-group-lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

Najlepsze rozwiązania można znaleźć w artykule [Security considerations for Azure Resource Manager](best-practices-resource-manager-security.md) (Zagadnienia dotyczące zabezpieczeń w usłudze Azure Resource Manager).

## Zarządzanie zasobami za pomocą zasad niestandardowych
Usługa Resource Manager umożliwia tworzenie zasad niestandardowych na potrzeby zarządzania zasobami. Typy tworzonych zasad mogą obejmować różne scenariusze. Można wymusić konwencję nazewnictwa zasobów, ograniczyć typy i wystąpienia zasobów, które można wdrożyć, lub wprowadzić ograniczenia dotyczące regionów, które mogą hostować dany typ zasobu. Można wymagać wartości tagu dla zasobów w celu organizowania rozliczania według działów. Tworzenie zasad umożliwia obniżenie kosztów i zachowanie spójności w ramach subskrypcji. Aby uzyskać więcej informacji, zobacz [Use Policy to manage resources and control access](resource-manager-policy.md) (Zarządzanie zasobami i kontrola dostępu przy użyciu zasad).

## Spójna warstwa zarządzania
Usługa Resource Manager umożliwia wykonywanie zgodnych operacji za pomocą programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows, witryny Azure Portal i interfejsu API REST. Można korzystać z interfejsu, który najlepiej sprawdza się w danej sytuacji, oraz szybko zmieniać interfejsy bez obaw o zgodność.

Aby uzyskać informacje dotyczące programu PowerShell, zobacz [Using Azure PowerShell with Resource Manager](powershell-azure-resource-manager.md) (Używanie programu Azure PowerShell z usługą Resource Manager) i [Azure Resource Manager Cmdlets](https://msdn.microsoft.com/library/azure/dn757692.aspx) (Polecenia cmdlet usługi Azure Resource Manager).

Aby uzyskać informacje dotyczące interfejsu wiersza polecenia platformy Azure, zobacz [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Manager](xplat-cli-azure-resource-manager.md) (Używanie interfejsu wiersza polecenia platformy Azure na komputerach Mac i komputerach z systemem Linux oraz Windows z usługą Azure Resource Manager).

Aby uzyskać informacje dotyczące interfejsu API REST, zobacz [Azure Resource Manager REST API Reference](https://msdn.microsoft.com/library/azure/dn790568.aspx) (Dokumentacja interfejsu API REST usługi Azure Resource Manager). Aby wyświetlić operacje REST dla wdrożonych zasobów, zobacz [Use Azure Resource Explorer to view and modify resources](resource-manager-resource-explorer.md) (Wyświetlanie i modyfikowanie zasobów za pomocą Eksploratora zasobów Azure).

Aby uzyskać informacje o korzystaniu z portalu, zobacz [Deploy resources with Resource Manager templates and Azure portal](resource-group-template-deploy-portal.md) (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure Portal).

Usługa Azure Resource Manager obsługuje mechanizm współużytkowania zasobów między źródłami (cross-origin resource sharing — CORS). Mechanizm ten umożliwia wywołanie interfejsu API REST usługi Resource Manager lub interfejsu API REST usługi platformy Azure z poziomu aplikacji sieci Web znajdującej się w innej domenie. Bez obsługi mechanizmu CORS przeglądarki internetowe blokowałyby aplikacji w jednej domenie dostęp do zasobów w innej domenie. Usługa Resource Manager obsługuje mechanizm CORS dla wszystkich żądań z prawidłowymi poświadczeniami uwierzytelniania.

## Zestawy SDK
Zestawy Azure SDK są dostępne dla wielu języków i platform.
Implementacje dla poszczególnych języków są dostępne za pośrednictwem menedżera pakietów danego ekosystemu oraz w usłudze GitHub.

Kod w każdym z tych zestawów SDK jest generowany na podstawie specyfikacji interfejsu RESTful API platformy Azure.
To specyfikacje typu open source, oparte na specyfikacji Swagger 2.0.
Kod zestawu SDK jest generowany za pośrednictwem narzędzia typu open source o nazwie AutoRest.
Narzędzie AutoRest przekształca te specyfikacje interfejsu RESTful API na biblioteki klienckie w wielu językach.
Jeśli chcesz ulepszyć jakiekolwiek aspekty kodu generowanego w zestawach SDK, do dyspozycji masz cały zestaw narzędzi służących do tworzenia zestawów SDK. Są one otwarte, dostępne bezpłatnie i bazują na powszechnie przyjętym formacie specyfikacji interfejsu API.

Oto nasze repozytoria zestawów SDK typu open source. Zachęcamy do wysyłania opinii, zgłaszania problemów i przesyłania żądań ściągnięcia.

[.NET](https://github.com/Azure/azure-sdk-for-net) | [Java](https://github.com/Azure/azure-sdk-for-java) | [Node.js](https://github.com/Azure/azure-sdk-for-node) | [PHP](https://github.com/Azure/azure-sdk-for-php) | [Python](https://github.com/Azure/azure-sdk-for-python) | [Ruby](https://github.com/Azure/azure-sdk-ruby)

> [!NOTE]
> Jeśli dany zestaw SDK nie udostępnia wymaganych funkcji, możesz również bezpośrednio wywołać [interfejs API REST platformy Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx).
> 
> 

## Przykłady
### .NET
* [Zarządzanie zasobami i grupami zasobów platformy Azure](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
* [Wdrażanie maszyny wirtualnej z obsługą protokołu SSH przy użyciu szablonu](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)

### Java
* [Zarządzanie zasobami platformy Azure](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource/)
* [Zarządzanie grupami zasobów platformy Azure](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
* [Wdrażanie maszyny wirtualnej z obsługą protokołu SSH przy użyciu szablonu](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)

### Node.js
* [Zarządzanie zasobami i grupami zasobów platformy Azure](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
* [Wdrażanie maszyny wirtualnej z obsługą protokołu SSH przy użyciu szablonu](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)

### Python
* [Zarządzanie zasobami i grupami zasobów platformy Azure](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
* [Wdrażanie maszyny wirtualnej z obsługą protokołu SSH przy użyciu szablonu](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)

### Ruby
* [Zarządzanie zasobami i grupami zasobów platformy Azure](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)
* [Wdrażanie maszyny wirtualnej z obsługą protokołu SSH przy użyciu szablonu](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)

Dodatkowe przykłady możesz wyszukać w galerii.

[.NET](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=dotnet) | [Java](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=java) | [Node.js](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=nodejs) | [Python](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=python) | [Ruby](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=ruby)

## Następne kroki
* Artykuł [Eksportowanie szablonu usługi Azure Resource Manager z istniejących zasobów](resource-manager-export-template.md) zawiera proste instrukcje dotyczące pracy z szablonami.
* Bardziej szczegółowe instrukcje dotyczące tworzenia szablonu zawiera artykuł [Przewodnik po szablonie usługi Resource Manager](resource-manager-template-walkthrough.md).
* Aby poznać funkcje, których można użyć w szablonie, zobacz [Template functions](resource-group-template-functions.md) (Funkcje szablonu).
* Aby uzyskać informacje dotyczące korzystania z programu Visual Studio w połączeniu z usługą Resource Manager, zobacz [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
* Aby uzyskać informacje dotyczące korzystania z programu VS Code w połączeniu z usługą Resource Manager, zobacz [Praca z szablonami usługi Azure Resource Manager w programie Visual Studio Code](resource-manager-vs-code.md).

Oto film z omówieniem tego zagadnienia:

[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Azure-Resource-Manager-Overview/player]




<!--HONumber=Sep16_HO3-->


