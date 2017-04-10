---
title: "SQL Database アプリケーションの開発の概要 | Microsoft Docs"
description: "SQL Database に接続するアプリケーションで使用できる接続ライブラリとベスト プラクティスについて説明します。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: genemi
ms.assetid: 67c02204-d1bd-4622-acce-92115a7cde03
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: cf627b92399856af2b9a58ab155fac6730128f85
ms.openlocfilehash: 59b8e7b6b2e2442c0a961d105ccdbc9336445aa6


---
# <a name="sql-database-application-development-overview"></a>SQL Database アプリケーションの開発の概要
この記事では、Azure SQL Database に接続するコードを記述するときの基本的な考慮事項について説明します。

> [!TIP]
> サーバーの作成、サーバーベースのファイアウォールの作成、サーバー プロパティの表示、SQL Server Management Studio を使用した接続、master データベースに対するクエリ実行、サンプル データベースと空のデータベースの作成、データベース プロパティに対するクエリ実行、SQL Server Management Studio を使用した接続、およびサンプル データベースに対するクエリ実行の方法を示すチュートリアルについては、[入門用チュートリアル](sql-database-get-started.md)を参照してください。
>

## <a name="language-and-platform"></a>言語とプラットフォーム
さまざまなプログラミング言語とプラットフォームで利用できるコード サンプルがあります。 コード サンプルについては、次のリンクをご覧ください。 

* 詳細: [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)

## <a name="tools"></a>ツール 
[cheetah](https://github.com/wunderlist/cheetah)、[sql-cli](https://www.npmjs.com/package/sql-cli)、[VS コード](https://code.visualstudio.com/)などのオープン ソース ツールを活用できます。 さらに、Azure SQL Database は、[Visual Studio](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)、[SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) などの Microsoft ツールと連携しています。  また、Microsoft Azure 管理ポータル、PowerShell、および REST API も使用でき、生産性向上に役立ちます。

## <a name="resource-limitations"></a>リソースの制限事項
Azure SQL Database では、リソース ガバナンスと制限の適用という&2; つの異なるメカニズムを使用して、データベースで使用できるリソースを管理します。

* 詳細: [Azure SQL Database のリソース制限](sql-database-resource-limits.md)

## <a name="security"></a>セキュリティ
Azure SQL Database には、SQL Database に対するアクセスの制限、データの保護、およびアクティビティの監視を行うためのリソースが用意されています。

* 詳細: [SQL Database の保護](sql-database-security-overview.md)

## <a name="authentication"></a>認証
* Azure SQL Database では、SQL Server 認証のユーザーとログインの両方と、 [Azure Active Directory 認証](sql-database-aad-authentication.md) ユーザーとログインがサポートされています。
* 既定の *マスター* データベースではなく、特定のデータベースを明示的に指定する必要があります。
* Transact-SQL の **USE myDatabaseName;** ステートメントを SQL Database に対して使用して別のデータベースに切り替えることはできません。
* 詳細: [SQL Database のセキュリティ: データベースのアクセスとログインのセキュリティの管理](sql-database-manage-logins.md)

## <a name="resiliency"></a>回復性
SQL Database への接続中に一時エラーが発生した場合は、コードで呼び出しを再試行する必要があります。  再試行ロジックでは、複数のクライアントが同時に再試行することで SQL Database に過大な負荷がかかるのを防ぐために、バックオフ ロジックを使用することをお勧めします。

* コード サンプル: 再試行ロジックを示すコード サンプルについては、次の記事で好みの言語用のサンプルをご覧ください: [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)
* 詳細: [SQL Database クライアント プログラムのエラー メッセージ](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>接続の管理
* クライアント接続ロジックの中で、タイムアウトが 30 秒になるように既定値をオーバーライドします。  既定では 15 秒ですが、インターネットに依存する接続の場合、それでは短すぎます。
* [接続プール](http://msdn.microsoft.com/library/8xx3tyca.aspx)を使用している場合は、プログラムで接続をアクティブに使用しておらず、再使用の準備をしていない時間は、接続を必ず閉じてください。

## <a name="network-considerations"></a>ネットワークに関する考慮事項
* クライアント プログラムをホストするコンピューターのファイアウォールで、ポート 1433 での発信 TCP が許可されていることを確認します。  詳細: [Azure ポータルを使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings.md)
* クライアントが Azure 仮想マシン (VM) で実行されているときに、クライアント プログラムが SQL Database V12 に接続する場合、VM で特定のポートの範囲を開く必要があります。 詳細: [ADO.NET 4.5 および SQL Database V12 における 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)
* Azure SQL Database V12 へのクライアント接続はプロキシを使用せずに、データベースに直接やり取りする場合があります。 1433 以外のポートが重要になります。 詳細: [ADO.NET 4.5 および SQL Database V12 における 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>Elastic Scale によるデータ シャーディング
Elastic Scale は、スケール アウト (およびスケール イン) のプロセスを簡略化します。 

* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)
* [Azure SQL Database Elastic Scale プレビューの概要](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>次のステップ
[SQL Database の機能](sql-database-technical-overview.md)



<!--HONumber=Feb17_HO1-->


