---
title: "SQL Database における 1433 以外のポート | Microsoft Docs"
description: "ADO.NET から Azure SQL Database V12 へのクライアント接続では、プロキシを使用せずに、データベースと直接やり取りする場合があります。 1433 以外のポートが重要になります。"
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
ms.assetid: 3f17106a-92fd-4aa4-b6a9-1daa29421f64
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 1df9f3549db8417445a5a012d31ed662977a9990
ms.openlocfilehash: 961b8b4eeeb8543c2adec60ff958a7f420c785b5


---
# <a name="ports-beyond-1433-for-adonet-45"></a>ADO.NET 4.5 用の 1433 以外のポート
このトピックでは、クライアントで ADO.NET 4.5 以降のバージョンが使用される場合の Azure SQL Database の接続動作について説明します。

## <a name="outside-vs-inside"></a>外部と内部
Azure SQL Database への接続では、まずクライアント プログラムが Azure クラウドの境界の*外部*と*内部*のどちらで実行されているかを確認する必要があります。 サブセクションでは、次の&2; つの一般的なシナリオについて説明します。

#### <a name="outside-client-runs-on-your-desktop-computer"></a>*外部:* クライアントをデスクトップ コンピューターで実行
ポート 1433 が、SQL Database クライアント アプリケーションをホストするデスクトップ コンピューターで開く必要がある唯一のポートです。

#### <a name="inside-client-runs-on-azure"></a>*内部:* クライアントを Azure で実行
Azure クラウド境界内でクライアントを実行している場合、クライアントは、いわゆる *ダイレクト ルート* を使用して SQL Database とやり取りします。 接続が確立した後に、クライアントとデータベース間のやり取りにミドルウェア プロキシが関与することはありません。

順序は次のとおりです。

1. ADO.NET 4.5 (またはそれ以降) は、Azure クラウドと簡単なやり取りを開始し、動的に指定されたポート番号を受信します。
   
   * 動的に特定されるポート番号は、11000 から 11999 または 14000 から 14999 の範囲になります。
2. ADO.NET は次に、ミドルウェアによって仲介することなく、SQL Database サーバーと直接接続します。
3. クエリは、データベースに直接送信され、その結果もクライアントに直接返されます。

11000 から 11999 および 14000 から 14999 のポート範囲が Azure クライアント コンピューター上で使用可能なまま残され、SQL Database V12 と ADO.NET 4.5 クライアントのやり取りに使用できることを確認します。

* 具体的には、対象の範囲のポートが他のすべての送信ブロッカーの影響を受けないようにします。
* Azure VM では、 **高度なセキュリティを備えた Windows ファイアウォール** がポート設定を制御します。
  
  * [ファイアウォールのユーザー インターフェイス](http://msdn.microsoft.com/library/cc646023.aspx)を利用し、**TCP** プロトコルと「**11000-11999**」のような構文のポート範囲を指定するルールを追加できます。

## <a name="version-clarifications"></a>バージョンの明確化
このセクションでは、製品バージョンを参照するモニカーを明らかにします。 また、製品間でのバージョンのいくつかの組み合わせも一覧表示します。

#### <a name="adonet"></a>ADO.NET
* ADO.NET 4.0 は TDS 7.3 プロトコルをサポートしますが、7.4 はサポートされません。
* ADO.NET 4.5 以降は、TDS 7.4 プロトコルをサポートします。

## <a name="related-links"></a>関連リンク
* ADO.NET 4.6 は、2015 年 7 月 20 日にリリースされました。 .NET チームのブログのお知らせは [こちら](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx)からご利用になれます。
* ADO.NET 4.5 は、2012 年 8 月 15 日にリリースされました。 .NET チームのブログのお知らせは [こちら](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx)からご利用になれます。
  
  * ADO.NET 4.5.1 についてのブログの投稿は、 [こちら](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx)からご利用になれます。
* [TDS プロトコルのバージョンの一覧](http://www.freetds.org/userguide/tdshistory.htm)
* [SQL Database の開発: 概要](sql-database-develop-overview.md)
* [Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)
* [方法: ファイアウォール設定を構成する (SQL データベース)](sql-database-configure-firewall-settings.md)




<!--HONumber=Jan17_HO5-->


