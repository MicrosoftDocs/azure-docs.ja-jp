---
title: SQL Database における 1433 以外のポート | Microsoft Docs
description: ADO.NET から Azure SQL Database へのクライアント接続では、1433 以外のポートを使用してプロキシをバイパスし、データベースと直接やり取りできます。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 04/03/2019
ms.openlocfilehash: ddb115370c62371e769ef98e0031f7e0379bafbf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916174"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>ADO.NET 4.5 用の 1433 以外のポート

このトピックでは、クライアントで ADO.NET 4.5 以降のバージョンが使用される場合の Azure SQL Database の接続動作について説明します。

> [!IMPORTANT]
> 接続アーキテクチャについては、「[Azure SQL Database connectivity architecture](sql-database-connectivity-architecture.md)」 (Azure SQL データベース接続アーキテクチャ) を参照してください。
>

## <a name="outside-vs-inside"></a>外部と内部

Azure SQL Database への接続では、まずクライアント プログラムが Azure クラウドの境界の*外部*と*内部*のどちらで実行されているかを確認する必要があります。 サブセクションでは、次の 2 つの一般的なシナリオについて説明します。

### <a name="outside-client-runs-on-your-desktop-computer"></a>*外部:* クライアントをデスクトップ コンピューター上で実行

ポート 1433 が、SQL Database クライアント アプリケーションをホストするデスクトップ コンピューターで開く必要がある唯一のポートです。

### <a name="inside-client-runs-on-azure"></a>*内部:* クライアントを Azure 上で実行

Azure クラウド境界内でクライアントを実行している場合、クライアントは、いわゆる *ダイレクト ルート* を使用して SQL Database とやり取りします。 接続が確立した後に、クライアントとデータベース間のやり取りに Azure SQL Database Gateway が関与することはありません。

順序は次のとおりです。

1. ADO.NET 4.5 (またはそれ以降) は、Azure クラウドと簡単なやり取りを開始し、動的に指定されたポート番号を受信します。

   * 動的に特定されるポート番号は、11000 から 11999 の範囲になります。
2. ADO.NET は次に、ミドルウェアによって仲介することなく、SQL Database サーバーと直接接続します。
3. クエリは、データベースに直接送信され、その結果もクライアントに直接返されます。

11000 から 11999 のポート範囲が Azure クライアント マシン上で使用可能なまま残され、SQL Database と ADO.NET 4.5 クライアントのやり取りに使用できることを確認します。

* 具体的には、対象の範囲のポートが他のすべての送信ブロッカーの影響を受けないようにします。
* Azure VM では、 **高度なセキュリティを備えた Windows ファイアウォール** がポート設定を制御します。
  
  * [ファイアウォールのユーザー インターフェイス](https://msdn.microsoft.com/library/cc646023.aspx)を利用し、**TCP** プロトコルと「**11000-11999**」のような構文のポート範囲を指定するルールを追加できます。

## <a name="version-clarifications"></a>バージョンの明確化

このセクションでは、製品バージョンを参照するモニカーを明らかにします。 また、製品間でのバージョンのいくつかの組み合わせも一覧表示します。

### <a name="adonet"></a>ADO.NET

* ADO.NET 4.0 は TDS 7.3 プロトコルをサポートしますが、7.4 はサポートされません。
* ADO.NET 4.5 以降は、TDS 7.4 プロトコルをサポートします。

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 以降

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4.2 以降 (JDBC 4.0 は実際、TDS 7.4 をサポートしていますが、"リダイレクト" を実行しません)

## <a name="related-links"></a>関連リンク

* ADO.NET 4.6 は、2015 年 7 月 20 日にリリースされました。 .NET チームのブログのお知らせは [こちら](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx)からご利用になれます。
* ADO.NET 4.5 は、2012 年 8 月 15 日にリリースされました。 .NET チームのブログのお知らせは [こちら](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx)からご利用になれます。
  * ADO.NET 4.5.1 についてのブログの投稿は、 [こちら](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx)からご利用になれます。

* Microsoft® ODBC Driver 17 for SQL Server® - Windows、Linux、macOS https://www.microsoft.com/download/details.aspx?id=56567

* リダイレクト https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362 を介して Azure SQL Database V12 に接続する

* [TDS プロトコルのバージョンの一覧](http://www.freetds.org/userguide/tdshistory.htm)
* [SQL Database の開発: 概要](sql-database-develop-overview.md)
* [Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)
* [方法:SQL Database でファイアウォール設定を構成する](sql-database-configure-firewall-settings.md)


