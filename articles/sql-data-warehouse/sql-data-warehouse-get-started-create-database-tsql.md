---
title: Tworzenie bazy danych w usłudze SQL Data Warehouse przy użyciu języka TSQL | Microsoft Docs
description: Dowiedz się, jak utworzyć bazę danych w usłudze Azure SQL Data Warehouse przy użyciu języka TSQL
services: sql-data-warehouse
documentationcenter: NA
author: lodipalm
manager: barbkess
editor: ''
tags: azure-sql-data-warehouse

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/24/2016
ms.author: lodipalm;barbkess;sonyama

---
# Tworzenie bazy danych w usłudze SQL Data Warehouse przy użyciu języka Transact-SQL (TSQL)
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

W tym artykule przedstawiono, jak utworzyć bazę danych w usłudze SQL Data Warehouse przy użyciu języka T-SQL.

## Wymagania wstępne
Aby rozpocząć pracę, potrzebne będą następujące elementy: 

* **Konto platformy Azure**: Aby utworzyć konto, odwiedź witrynę [Bezpłatna wersja próbna platformy Azure][Bezpłatna wersja próbna platformy Azure] lub [Środki na korzystanie z systemu Azure w ramach usługi MSDN][Środki na korzystanie z systemu Azure w ramach usługi MSDN].
* **Serwer Azure SQL**: Aby uzyskać więcej informacji, zobacz temat [Tworzenie serwera logicznego usługi Azure SQL Database przy użyciu witryny Azure Portal][Tworzenie serwera logicznego usługi Azure SQL Database przy użyciu witryny Azure Portal] lub [Tworzenie serwera logicznego usługi Azure SQL Database przy użyciu programu PowerShell][Tworzenie serwera logicznego usługi Azure SQL Database przy użyciu programu PowerShell].
* **Grupa zasobów**: Użyj tej samej grupy zasobów, z której korzysta serwer Azure SQL, lub zobacz, [jak utworzyć grupę zasobów][jak utworzyć grupę zasobów].
* **Środowisko do wykonywania poleceń języka T-SQL**: Do wykonywania poleceń języka T-SQL możesz użyć programu [Visual Studio][Instalowanie programu Visual Studio i narzędzi SSDT], narzędzia [sqlcmd][sqlcmd] lub programu [SSMS][SSMS].

> [!NOTE]
> Utworzenie bazy danych w usłudze SQL Data Warehouse może skutkować powstaniem nowej usługi płatnej.  Zobacz [Cennik usługi SQL Data Warehouse][Cennik usługi SQL Data Warehouse], aby uzyskać więcej informacji o cenach.
> 
> 

## Tworzenie bazy danych przy użyciu programu Visual Studio
Jeśli dopiero zaczynasz korzystać z programu Visual Studio, zobacz [Tworzenie zapytań względem usługi Azure SQL Data Warehouse (Visual Studio)][Tworzenie zapytań względem usługi Azure SQL Data Warehouse (Visual Studio)].  Aby rozpocząć, otwórz Eksplorator obiektów SQL Server w programie Visual Studio i połącz się z serwerem, który będzie hostem bazy danych usługi SQL Data Warehouse.  Po nawiązaniu połączenia można utworzyć bazę danych usługi SQL Data Warehouse, uruchamiając polecenie SQL **master** w odniesieniu do bazy danych.  To polecenie tworzy bazę danych MySqlDwDb z celem usługi wynoszącym DW400 i możliwością wzrostu bazy danych do maksymalnego rozmiaru 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Tworzenie bazy danych przy użyciu narzędzia sqlcmd
Możesz też uruchomić to samo polecenie za pomocą narzędzia sqlcmd w wierszu polecenia w następujący sposób.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Sortowanie domyślne, gdy sortowanie nie jest określone, to COLLATE SQL_Latin1_General_CP1_CI_AS.  Parametr `MAXSIZE` może mieć wartość od 250 GB do 240 TB.  Parametr `SERVICE_OBJECTIVE` może mieć wartość od DW100 do DW2000 jednostek [DWU][DWU].  Lista wszystkich prawidłowych wartości znajduje się w dokumentacji MSDN dotyczącej polecenia [CREATE DATABASE][CREATE DATABASE].  Zarówno parametr MAXSIZE, jak i parametr SERVICE_OBJECTIVE można zmienić za pomocą polecenia języka T-SQL [ALTER DATABASE][ALTER DATABASE].  Po utworzeniu bazy danych nie można zmienić jej sortowania.   W przypadku zmiany parametru SERVICE_OBJECTIVE należy zachować ostrożność, ponieważ zmiana liczby jednostek DWU powoduje ponowne uruchomienie usług, co z kolei anuluje wszystkie bieżące zapytania.  Zmiana parametru MAXSIZE nie powoduje ponownego uruchamiania usług, ponieważ jest to tylko prosta operacja na metadanych.

## Następne kroki
Po zakończeniu aprowizacji bazy danych usługi SQL Data Warehouse możesz przeprowadzić [ładowanie danych przykładowych][ładowanie danych przykładowych] lub poznać sposoby wykonywania takich czynności, jak [opracowywanie][opracowywanie], [ładowanie][ładowanie] lub [migrowanie][migrowanie].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[tworzenie usługi SQL Data Warehouse przy użyciu witryny Azure Portal]: sql-data-warehouse-get-started-provision.md
[Tworzenie zapytań względem usługi Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrowanie]: sql-data-warehouse-overview-migrate.md
[opracowywanie]: sql-data-warehouse-overview-develop.md
[ładowanie]: sql-data-warehouse-overview-load.md
[ładowanie danych przykładowych]: sql-data-warehouse-load-sample-databases.md
[Tworzenie serwera logicznego usługi Azure SQL Database przy użyciu witryny Azure Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Tworzenie serwera logicznego usługi Azure SQL Database przy użyciu programu PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[jak utworzyć grupę zasobów]: ../resource-group-template-deploy-portal.md#create-resource-group
[Instalowanie programu Visual Studio i narzędzi SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[Cennik usługi SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Bezpłatna wersja próbna platformy Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Środki na korzystanie z systemu Azure w ramach usługi MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Sep16_HO3-->


