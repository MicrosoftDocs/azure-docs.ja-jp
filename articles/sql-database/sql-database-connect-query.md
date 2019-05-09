---
title: Azure SQL Database の接続とクエリに関するクイック スタート | Microsoft Docs
description: Azure SQL Database の各種クイック スタートでは、Azure SQL データベースに接続してクエリを実行する方法を紹介しています。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 1bf9c0b1393abfcad6646624301c4f131c8790a6
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447975"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>クイック スタート:Azure SQL Database の接続とクエリ

次のドキュメントは、Azure SQL データベースに接続してクエリを実行する例を紹介した Azure ページへのリンク集です。 トランスポート レベルのセキュリティについての推奨事項も提供されています。

## <a name="quickstarts"></a>クイック スタート

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|このクイック スタートでは、SSMS を使って Azure SQL データベースに接続し Transact-SQL ステートメントを使ってデータベース内のデータを照会、挿入、更新、削除する方法について説明します。|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|このクイック スタートでは、Azure Data Studio を使用して Azure SQL データベースに接続した後、Transact-SQL (T-SQL) ステートメントを使用して Azure Data Studio チュートリアルで使用される TutorialDB を作成する方法を示します。|
|[Azure Portal](sql-database-connect-query-portal.md)|このクイック スタートでは、クエリ エディターを使用して SQL Database に接続し、Transact-SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、および削除する方法について説明します。|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|このクイック スタートでは、Visual Studio Code を使って Azure SQL データベースに接続し、Transact-SQL ステートメントを使ってデータベース内のデータを照会、挿入、更新、削除する方法について説明します。|
|[Visual Studio での .NET](sql-database-connect-query-dotnet-visual-studio.md)|このクイック スタートでは、Azure SQL データベースに接続して Transact-SQL ステートメントでデータを照会する C# プログラムを Visual Studio と .NET Framework を使って作成する方法について説明します。|
|[.NET core](sql-database-connect-query-dotnet-core.md)|このクイック スタートでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータを照会する C# プログラムを Windows/Linux/macOS 上の .NET Core を使って作成する方法について説明します。|
|[Go](sql-database-connect-query-go.md)|このクイック スタートでは、Go を使用して Azure SQL データベースに接続する方法を紹介します。 Transact-SQL ステートメントを使用してデータを照会および変更する方法についても説明します。|
|[Java](sql-database-connect-query-java.md)|このクイック スタートでは、Java を使って Azure SQL データベースに接続した後、Transact-SQL ステートメントを使ってデータを照会する方法について説明します。|
|[Node.js](sql-database-connect-query-nodejs.md)|このクイック スタートでは、Azure SQL データベースに接続して Transact-SQL ステートメントでデータを照会するプログラムを Node.js を使って作成する方法について説明します。|
|[PHP](sql-database-connect-query-php.md)|このクイック スタートでは、Azure SQL データベースに接続して Transact-SQL ステートメントでデータを照会するプログラムを PHP を使って作成する方法について説明します。|
|[Python](sql-database-connect-query-python.md)|このクイック スタートでは、Python を使って Azure SQL データベースに接続した後、Transact-SQL ステートメントを使ってデータを照会する方法について説明します。 |
|[Ruby](sql-database-connect-query-ruby.md)|このクイック スタートでは、Azure SQL データベースに接続して Transact-SQL ステートメントでデータを照会するプログラムを Ruby を使って作成する方法について説明します。|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>SQL Database の接続に関する TLS の考慮事項
トランスポート層セキュリティ (TLS) は、Azure SQL Database への接続のために Microsoft が提供またはサポートしているすべてのドライバーによって使われます。 特別な構成は必要ありません。 SQL Server または Azure SQL Database へのすべての接続について、すべてのアプリケーションで次の構成またはそれと同等の構成を設定することをお勧めします。

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

一部のシステムでは、これらの構成キーワードとは異なりますが同等のキーワードが使われています。 これらの構成を指定すると、サーバーから受信した TLS 証明書の ID をクライアント ドライバーが確認することが保証されます。

また、Payment Card Industry - データ セキュリティ基準 (PCI-DSS) に準拠する必要がある場合は、クライアントにおいて TLS 1.1 および 1.0 を無効にすることをお勧めします。

Microsoft 以外のドライバーは、既定では TLS を使用しない場合があります。 これは Azure SQL Database に接続するときに問題になる可能性があります。 埋め込みドライバーを使用するアプリケーションでは、これらの接続設定を制御できない場合があります。 このようなドライバーおよびアプリケーションについては、機密データを操作するシステムで使う前に、そのセキュリティを調べることをお勧めします。

## <a name="libraries"></a>ライブラリ

さまざまなライブラリとフレームワークを使用して、Azure SQL Database に接続できます。 C#、Java、Node.js、PHP、Python などのプログラミング言語を手軽に始めるには、[開始にあたってのチュートリアル](https://aka.ms/sqldev)をご覧ください。 次に Linux、Windows、または macOS 上の Docker で SQL Server を使用してアプリを構築します。

次の表は、クライアント アプリケーションがさまざまな言語から SQL Server への接続に使用できる接続ライブラリや*ドライバー*を示しています。クライアント アプリケーションはオンプレミスおよびクラウドで実行される SQL Server と共にこれらを使用できます。 Linux、Windows、または Docker でこれらを使用して、Azure SQL Database や Azure SQL Data Warehouse に接続できます。 

| 言語 | プラットフォーム | その他のリソース | ダウンロード | 作業開始 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [SQL Server 用 Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [ダウンロード](https://www.microsoft.com/net/download/) | [作業開始](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows、Linux、macOS | [SQL Server 用 Microsoft JDBC ドライバー](https://msdn.microsoft.com/library/mt484311.aspx) | [ダウンロード](https://go.microsoft.com/fwlink/?linkid=852460) |  [作業開始](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows、Linux、macOS| [SQL Server 用 PHP SQL ドライバー](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [ダウンロード](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [作業開始](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows、Linux、macOS | [SQL Server 用 Node.js ドライバー](https://msdn.microsoft.com/library/mt652093.aspx) | [インストール](https://msdn.microsoft.com/library/mt652094.aspx) |  [作業開始](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows、Linux、macOS | [Python SQL ドライバー](https://msdn.microsoft.com/library/mt652092.aspx) | インストール オプション:  <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [作業開始](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows、Linux、macOS | [SQL Server 用 Ruby ドライバー](https://msdn.microsoft.com/library/mt691981.aspx) | [インストール](https://msdn.microsoft.com/library/mt711041.aspx) | [作業開始](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows、Linux、macOS | [SQL Server 用 Microsoft ODBC ドライバー](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [ダウンロード](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

次の表は、クライアント アプリケーションがオンプレミスまたはクラウドで SQL Server と共に使用できる、オブジェクト リレーショナル マッピング (ORM) フレームワークおよび Web フレームワークの例を示しています。 Linux、Windows、または Docker でフレームワークを使用して SQL Database および SQL Data Warehouse に接続できます。 

| 言語 | プラットフォーム | ORM |
| :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows、Linux、macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows、Linux、macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows、Linux、macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows、Linux、macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows、Linux、macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>次の手順

- 接続アーキテクチャについては、「[Azure SQL Database 接続アーキテクチャ](sql-database-connectivity-architecture.md)」をご覧ください。
- クライアント アプリケーションからの接続に使用する [SQL Server 用ドライバー](https://msdn.microsoft.com/library/mt654049.aspx)を探します
- SQL Database への接続:
  - [.NET (C#) を使用して SQL Database に接続する](sql-database-connect-query-dotnet.md) 
  - [PHP を使用して SQL Database に接続する](sql-database-connect-query-php.md) 
  - [Node.js を使用して SQL Database に接続する](sql-database-connect-query-nodejs.md) 
  - [Java を使用して SQL Database に接続する](sql-database-connect-query-java.md) 
  - [Python を使用して SQL Database に接続する](sql-database-connect-query-python.md)
  - [Ruby を使用して SQL Database に接続する](sql-database-connect-query-ruby.md)
- 再試行ロジックのコード例:
  - [ADO.NET を使用して SQL に弾性的に接続する][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [PHP を使用して SQL に弾性的に接続する][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
