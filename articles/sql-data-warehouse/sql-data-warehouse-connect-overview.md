---
title: Nawiązywanie połączenia z usługą Azure SQL Data Warehouse | Microsoft Docs
description: Jak znaleźć nazwę serwera i parametry połączenia dla usługi Azure SQL Data Warehouse
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
ms.date: 09/26/2016
ms.author: sonyama;barbkess

---
# Nawiązywanie połączenia z usługą Azure SQL Data Warehouse
W tym artykule opisano, jak nawiązać połączenie z usługą SQL Data Warehouse.

## Znajdowanie nazwy serwera
Pierwszym krokiem do nawiązania połączenia z usługą SQL Data Warehouse jest wiedza, jak znaleźć nazwę serwera.  Na przykład w poniższym przykładzie nazwa serwera to sample.database.windows.net. Aby znaleźć w pełni kwalifikowaną nazwę serwera:

1. Przejdź do witryny [Azure Portal][Azure Portal].
2. Kliknij pozycję **Bazy danych SQL**. 
3. Kliknij bazę danych, z którą chcesz nawiązać połączenie.
4. Znajdź pełną nazwę serwera.
   
    ![Pełna nazwa serwera][1]

## Obsługiwane sterowniki i parametry połączenia
Usługa Azure SQL Data Warehouse obsługuje sterowniki [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] i [JDBC][JDBC]. Kliknij jeden z powyższych sterowników, aby znaleźć najnowszą wersję i dokumentację. Aby automatycznie wygenerować parametry połączenia dla używanego sterownika z poziomu witryny Azure Portal, można kliknąć pozycję **Pokaż parametry połączenia bazy danych** z poprzedniego przykładu.  Poniżej przedstawiono również przykłady parametrów połączenia dla każdego sterownika.

> [!NOTE]
> Rozważ ustawienie limitu czasu połączenia na wartość 300 sekund, aby połączenie nie zostało zakończone mimo krótkich okresów niedostępności.
> 
> 

### Przykład parametrów połączenia sterownika ADO.NET
```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### Przykład parametrów połączenia sterownika ODBC
```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### Przykład parametrów połączenia sterownika PHP
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### Przykład parametrów połączenia sterownika JDBC
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Ustawienia połączenia
Usługa SQL Data Warehouse standaryzuje niektóre ustawienia podczas tworzenia połączenia i obiektu. Tych ustawień nie można zastąpić i obejmują one:

| Ustawienia bazy danych | Wartość |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |ON |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |ON |
| [DATEFORMAT][DATEFORMAT] |mdy |
| [DATEFIRST][DATEFIRST] |7 |

## Następne kroki
Aby nawiązać połączenie i rozpocząć tworzenie zapytań przy użyciu programu Visual Studio, zobacz artykuł [Query with Visual Studio][Query with Visual Studio] (Wykonywanie zapytań przy użyciu programu Visual Studio). Aby dowiedzieć się więcej na temat opcji uwierzytelniania, zobacz [Authentication to Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] (Uwierzytelnianie w usłudze Azure SQL Data Warehouse).

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure Portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png





<!--HONumber=Sep16_HO4-->


