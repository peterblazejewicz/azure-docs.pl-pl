---
title: Tworzenie zapytań względem indeksu usługi Azure Search przy użyciu interfejsu API REST | Microsoft Docs
description: Konstruowanie zapytania wyszukiwania w usłudze Azure Search oraz filtrowanie i sortowanie wyników wyszukiwania za pomocą parametrów wyszukiwania.
services: search
documentationcenter: ''
author: ashmaka

ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka

---
# Tworzenie zapytań względem indeksu usługi Azure Search przy użyciu interfejsu API REST
> [!div class="op_single_selector"]
> * [Omówienie](search-query-overview.md)
> * [Portal](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

W tym artykule opisano tworzenie zapytań względem indeksu przy użyciu [interfejsu API REST usługi Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Przed rozpoczęciem pracy z tym przewodnikiem należy [utworzyć indeks usługi Azure Search](search-what-is-an-index.md) i [wypełnić go danymi](search-what-is-data-import.md).

## I. Identyfikowanie klucza api-key zapytania usługi Azure Search
Głównym składnikiem każdej operacji wyszukiwania wykonywanej przy użyciu interfejsu API REST usługi Azure Search jest *klucz api-key* wygenerowany dla aprowizowanej usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

1. Aby odnaleźć klucze api-key dla usługi, musisz zalogować się w witrynie [Azure Portal](https://portal.azure.com/)
2. Przejdź do bloku usługi Azure Search
3. Kliknij ikonę „Klucze”

Usługa będzie dysponować *kluczami administratora* i *kluczami zapytań*.

* Za pomocą podstawowego i pomocniczego *klucza administratora* przyznawane są pełne prawa do wszystkich operacji, łącznie z możliwością zarządzania usługą oraz tworzenia i usuwania indeksów, indeksatorów i źródeł danych. Dostępne są dwa klucze, dzięki czemu możesz nadal używać klucza pomocniczego, jeśli zdecydujesz się na ponowne wygenerowanie klucza podstawowego, i na odwrót.
* *Klucze zapytań* umożliwiają dostęp tylko do odczytu do indeksów oraz dokumentów i są zazwyczaj dystrybuowane do aplikacji klienckich, które wysyłają żądania wyszukiwania.

W celu tworzenia zapytań względem indeksu można użyć dowolnego klucza zapytania. Do tworzenia zapytań można również używać kluczy administratora, ale w kodzie aplikacji należy używać klucza zapytania, ponieważ takie podejście jest bardziej zgodne z [zasadą najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## II. Formułowanie zapytania
Istnieją dwie metody [przeszukiwania indeksu przy użyciu interfejsu API REST](https://msdn.microsoft.com/library/azure/dn798927.aspx). Pierwsza z nich polega na wysłaniu żądania HTTP POST, w ramach którego parametry zapytania są definiowane w obiekcie JSON w treści żądania. Druga metoda obejmuje wysłanie żądania HTTP GET, w ramach którego parametry zapytania są definiowane w adresie URL żądania. Warto pamiętać o tym, że w przypadku żądania POST limity dotyczące rozmiaru parametrów zapytania są [luźniejsze](https://msdn.microsoft.com/library/azure/dn798927.aspx) niż dla żądania GET. Z tego powodu zaleca się używanie żądania POST, o ile nie występują specjalne okoliczności, w których korzystanie z żądania GET jest wygodniejsze.

Zarówno dla żądania POST, jak i GET zawartość adresu URL żądania musi obejmować *nazwę usługi*, *nazwę indeksu* oraz odpowiednią *wersję interfejsu API* (w momencie publikowania tego dokumentu aktualna wersja interfejsu API to `2015-02-28`). W przypadku żądania GET parametry zapytania są określane w *ciągu zapytania* na końcu adresu URL. Format adresu URL został przedstawiony poniżej:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

Format dla żądania POST jest taki sam, ale parametry ciągu zapytania zawierają tylko element api-version.

#### Przykładowe zapytania
Poniżej znajduje się kilka przykładowych zapytań względem indeksu o nazwie „hotels”. Zostały one przedstawione zarówno w formacie GET, jak i POST.

Wyszukaj termin „budget” w całym indeksie i zwróć tylko pole `hotelName`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

Zastosuj filtr na indeks, aby znaleźć hotele, w których koszt noclegu jest niższy niż 150 USD, i zwróć pola `hotelId` i `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Przeszukaj cały indeks, uporządkuj wyniki według określonego pola (`lastRenovationDate`) w kolejności malejącej, a następnie wyświetl tylko pola `hotelName` i `lastRenovationDate` dla dwóch pierwszych wyników:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## III. Przesyłanie żądania HTTP
Po sformułowaniu zapytania w ramach adresu URL (w przypadku żądania GET) lub treści (w przypadku żądania POST) żądania HTTP można zdefiniować nagłówki żądania i przesłać zapytanie.

#### Żądanie i nagłówki żądania
Należy zdefiniować dwa nagłówki dla żądania GET lub trzy nagłówki dla żądania POST:

1. Nagłówek `api-key` musi mieć wartość klucza zapytania określonego w kroku I powyżej. Jako nagłówka `api-key` można również używać klucza administratora, ale zaleca się używanie klucza zapytania, ponieważ umożliwia on przyznanie wyłącznego dostępu tylko do odczytu do indeksów i dokumentów.
2. Nagłówek `Accept` musi mieć wartość `application/json`.
3. W przypadku żądań POST nagłówek `Content-Type` również musi mieć wartość `application/json`.

Poniżej znajduje się żądanie HTTP GET umożliwiające przeszukanie indeksu „hotels” za pomocą interfejsu API REST usługi Azure Search, które korzysta z prostego zapytania wyszukującego termin „motel”:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

Poniżej przedstawiono to samo przykładowe zapytanie wykonywane przy użyciu żądania HTTP POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Po pomyślnym wykonaniu zapytania kod stanu przyjmie wartość `200 OK`, a wyniki wyszukiwania zostaną zwrócone jako dane JSON w treści odpowiedzi. Poniżej przedstawiono wyniki dla powyższego zapytania przy założeniu, że indeks „hotels” został wypełniony przykładowymi danymi w ramach instrukcji zawartych w temacie [Data Import in Azure Search using the REST API](search-import-data-rest-api.md) (Importowanie danych w usłudze Azure Search przy użyciu interfejsu API REST) (dane JSON zostały sformatowane, aby były bardziej zrozumiałe).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Aby dowiedzieć się więcej, przejdź do sekcji „Response” (Odpowiedź) w artykule [Search Documents](https://msdn.microsoft.com/library/azure/dn798927.aspx) (Wyszukiwanie dokumentów). Aby uzyskać więcej informacji o innych kodach stanów HTTP, które mogą być zwracane w przypadku niepowodzenia, zobacz [HTTP status codes (Azure Search)](https://msdn.microsoft.com/library/azure/dn798925.aspx) (Usługa Azure Search — kody stanów HTTP).

<!--HONumber=Sep16_HO3-->


