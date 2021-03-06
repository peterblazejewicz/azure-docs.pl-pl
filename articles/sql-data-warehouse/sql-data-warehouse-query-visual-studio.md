---
title: Tworzenie zapytań względem usługi Azure SQL Data Warehouse (Visual Studio) | Microsoft Docs
description: Tworzenie zapytań względem usługi SQL Data Warehouse przy użyciu programu Visual Studio.
services: sql-data-warehouse
documentationcenter: NA
author: sonyam
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/16/2016
ms.author: sonyama;barbkess

---
# Tworzenie zapytań względem usługi Azure SQL Data Warehouse (Visual Studio)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> 
> 

Używając programu Visual Studio, można w kilka minut utworzyć zapytanie do usługi Azure SQL Data Warehouse. Ta metoda bazuje na rozszerzeniu usługi SQL Server Data Tools (SSDT) w programie Visual Studio. 

## Wymagania wstępne
Aby użyć tego samouczka, potrzebne są następujące elementy:

* Istniejący magazyn danych SQL. Aby go utworzyć, zobacz artykuł [Tworzenie bazy danych w usłudze SQL Data Warehouse][Tworzenie bazy danych w usłudze SQL Data Warehouse].
* Rozszerzenie SSDT dla programu Visual Studio. Jeśli masz program Visual Studio, prawdopodobnie masz też to rozszerzenie. Aby uzyskać instrukcje instalacji i informacje na temat dostępnych opcji, zobacz artykuł [Instalowanie programu Visual Studio i narzędzi SSDT][Instalowanie programu Visual Studio i narzędzi SSDT].
* W pełni kwalifikowana nazwa serwera SQL. Aby ją znaleźć, zobacz [Nawiązywanie połączenia z usługą SQL Data Warehouse][Nawiązywanie połączenia z usługą SQL Data Warehouse].

## 1. Nawiązywanie połączenia z usługą SQL Data Warehouse
1. Otwórz program Visual Studio 2013 lub 2015.
2. Otwórz Eksplorator obiektów SQL Server. W tym celu wybierz pozycje **Widok**  >  **Eksplorator obiektów SQL Server**.
   
    ![Eksplorator obiektów SQL Server][1]
3. Kliknij **ikonę dodawania serwera SQL**.
   
    ![Dodawanie serwera SQL][2]
4. Wypełnij pola w oknie łączenia z serwerem.
   
    ![Łączenie z serwerem][3]
   
   * **Nazwa serwera**. Wprowadź wcześniej ustaloną **nazwę serwera**.
   * **Uwierzytelnianie**. Wybierz opcję **Uwierzytelnianie na serwerze SQL Server** lub **Zintegrowane uwierzytelnianie usługi Active Directory**.
   * **Nazwa użytkownika** i **Hasło**. Wprowadź nazwę użytkownika i hasło, jeżeli powyżej wybrano uwierzytelnianie na serwerze SQL Server.
   * Kliknij przycisk **Połącz**.
5. W celach poznawczych rozwiń węzeł serwera Azure SQL. Możesz przejrzeć skojarzone z serwerem bazy danych. Rozwiń węzeł AdventureWorksDW, aby zobaczyć tabele w przykładowej bazie danych.
   
    ![Poznawanie bazy danych AdventureWorksDW][4]

## 2. Uruchamianie przykładowego zapytania
Teraz, po nawiązaniu połączenia z bazą danych, napiszemy zapytanie.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie][5]
3. Skopiuj to zapytanie TSQL do okna zapytania:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Uruchom zapytanie. W tym celu kliknij zieloną strzałkę lub użyj następującego skrótu: `CTRL`+`SHIFT`+`E`.
   
    ![Uruchamianie zapytania][6]
5. Przejrzyj wyniki zapytania. W tym przykładzie tabela FactInternetSales ma 60398 wierszy.
   
    ![Wyniki zapytania][7]

## Następne kroki
Teraz, kiedy umiesz nawiązywać połączenia i wykonywać zapytania, spróbuj wykonać [wizualizację danych przy użyciu usługi Power BI][wizualizację danych przy użyciu usługi Power BI].

Aby skonfigurować środowisko do uwierzytelniania usługi Azure Active Directory, zobacz artykuł [Uwierzytelnianie w usłudze SQL Data Warehouse][Uwierzytelnianie w usłudze SQL Data Warehouse].

<!--Arcticles-->
[Nawiązywanie połączenia z usługą SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Tworzenie bazy danych w usłudze SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Instalowanie programu Visual Studio i narzędzi SSDT]: sql-data-warehouse-install-visual-studio.md
[Uwierzytelnianie w usłudze SQL Data Warehouse]: sql-data-warehouse-authentication.md
[wizualizację danych przy użyciu usługi Power BI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure Portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png



<!--HONumber=sep16_HO1-->


