---
title: "SQL Database の接続ライブラリ | Microsoft Docs"
description: "各種のクライアント プログラミング言語から SQL Server および SQL Database への接続を可能にするモジュールをダウンロードするためのリンクを示します。 モジュールは、コミュニティまたは Microsoft によってリリースされます。"
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: genemi
ms.assetid: 13d899d3-cf46-4e4d-8919-cf4b41ca836d
ms.service: sql-database
ms.custom: develop apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: genemi
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 082abf57b139b9f7d44774dce3a80e20b97f0e3b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="connectivity-libraries-and-frameworks-for-microsoft-sql-server"></a>Microsoft SQL Server の接続ライブラリとフレームワーク

Microsoft の[チュートリアル](http://aka.ms/sqldev)をご覧ください。C#、Java、Node.js、PHP、Python などのプログラミング言語を利用し、Linux または Windows の場合は SQL Server で、macOS の場合は Docker で、アプリの開発をすぐに開始できます。

下の表は、接続ライブラリまたは*ドライバー*を一覧にしたものです。クライアント アプリケーションはさまざまな言語からこれらのライブラリまたはドライバーを利用し、オンプレミス、クラウド、Linux、Windows、Docker で実行されている Microsoft SQL Server に接続し、利用できます。また、Azure SQL Database と Azure SQL Data Warehouse に接続できます。 

| 言語 | Platform | その他のリソース | ダウンロード | 作業の開始 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [SQL Server 用 Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [ダウンロード](https://www.microsoft.com/net/download/) | [作業の開始](https://www.microsoft.com/en-us/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows、Linux、macOS | [SQL Server 用 Microsoft JDBC ドライバー](http://msdn.microsoft.com/library/mt484311.aspx) | [ダウンロード](https://go.microsoft.com/fwlink/?linkid=852460) |  [作業の開始](https://www.microsoft.com/en-us/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows、Linux、macOS| [SQL Server 用 PHP SQL ドライバー](http://msdn.microsoft.com/library/dn865013.aspx) | オペレーティング システム: <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=20098) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [作業の開始](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows、Linux、macOS | [SQL Server 用 Node.js ドライバー](http://msdn.microsoft.com/library/mt652093.aspx) | [インストール](https://msdn.microsoft.com/library/mt652094.aspx) |  [作業の開始](https://www.microsoft.com/en-us/sql-server/developer-get-started/node/ubuntu)
| Python | Windows、Linux、macOS | [Python SQL ドライバー](http://msdn.microsoft.com/library/mt652092.aspx) | インストール オプション:  <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [作業の開始](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows、Linux、macOS | [SQL Server 用 Ruby ドライバー](http://msdn.microsoft.com/library/mt691981.aspx) | [インストール](https://msdn.microsoft.com/library/mt711041.aspx) | [作業の開始](https://www.microsoft.com/en-us/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows、Linux、macOS | [Microsoft ODBC Driver for SQL Server](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) | [ダウンロード](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) |  

下の表は、オブジェクト リレーショナル マッピング (ORM) のフレームワークと Web フレームワークのいくつかの例を一覧にしたものです。クライアント アプリケーションはこれらのフレームワークを、オンプレミス、クラウド、Linux、Windows、Docker で実行されている Microsoft SQL Server で利用できます。また、Azure SQL Database と Azure SQL Data Warehouse で利用できます。 

| 言語 | Platform | ORM |
| :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [Entity Framework](https://docs.microsoft.com/en-us/ef)<br>[Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/index) |
| Java | Windows、Linux、macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows、Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows、Linux、macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows、Linux、macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows、Linux、macOS | [Ruby on Rails](http://rubyonrails.org/) |

## <a name="related-links"></a>関連リンク
- [SQL Server ドライバー](http://msdn.microsoft.com/library/mt654049.aspx) クライアント アプリケーションからの接続用
- [.NET (C#) を使用して SQL Database に接続する](sql-database-connect-query-dotnet.md)
- [PHP を使用して SQL Database に接続する](sql-database-connect-query-php.md)
- [Node.js を使用して SQL Database に接続する](sql-database-connect-query-nodejs.md)
- [Java を使用して SQL Database に接続する](sql-database-connect-query-java.md)
- [Python を使用して SQL Database に接続する](sql-database-connect-query-python.md)
- [Ruby を使用して SQL Database に接続する](sql-database-connect-query-ruby.md)

