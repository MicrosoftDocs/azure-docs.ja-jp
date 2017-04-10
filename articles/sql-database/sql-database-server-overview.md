---
title: "Azure SQL Database 論理サーバーの概要 | Microsoft Docs"
description: "このページは、Azure SQL 論理サーバーを操作するための考慮事項とガイドラインを示します。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 79a9e72d29b5522dc3960b79bae7876f21acb4c5
ms.openlocfilehash: 07181e5d35703cddf8a896badd45e7485c9e07a2


---
# <a name="azure-sql-database-logical-servers"></a>Azure SQL Database 論理サーバー

このトピックは、Azure SQL 論理サーバーを操作するための考慮事項とガイドラインを示します。 Azure SQL Database については、[SQL Database](sql-database-overview.md) に関するページをご覧ください。

## <a name="what-is-an-azure-sql-database-logical-server"></a>Azure SQL Database 論理サーバーとは
Azure SQL Database 論理サーバーは、複数のデータベースの中央管理ポイントとして機能します。 SQL Database におけるサーバーとは、オンプレミスでなじみのある SQL Server インスタンスとは別の論理コンストラクトです。 具体的には、SQL Database サービスは論理サーバーに関連したデータベースの場所について保証しません。また、インスタンス レベルのアクセスまたは機能を公開しません。  

Azure SQL Database 論理サーバーは、

- Azure サブスクリプション内に作成されますが、含まれているリソースとともに別のサブスクリプションに移動できます
- データベース、エラスティック プール、およびデータ ウェアハウスの親リソースです
- データベース、エラスティック プール、およびデータ ウェアハウスの名前空間を提供します
- 強力な有効期間のセマンティクスが含まれる論理コンテナーです。サーバーを削除すると、包含データベース、エラスティック プール、データ ウェアハウスが削除されます
- Azure ロール ベースのアクセス制御 (RBAC): サーバー内のデータベース、エラスティック プールがサーバーからアクセス権を継承します
- Azure のリソース管理目的での、データベースとエラスティック プールの上位要素です (データベースとプールの URL スキーマを参照してください)
- 領域内にリソースを併置します
- データベース アクセスの接続エンドポイント (<serverName>.database.windows.net) を提供します
- マスター データベースに接続することで、含まれているリソースに関するメタデータへの DMV 経由のアクセスを提供します 
- データベースに適用される管理ポリシーの範囲 (ログイン、ファイアウォール、監査、脅威の検出など) を提供します 
- 親サブスクリプション内のクォータによって制限されます (サブスクリプションあたり&6; サーバー - [サブスクリプションの制限についてはここを参照してください](../azure-subscription-service-limits.md))
- 含まれるリソースのデータベースのクォータと DTU クォータの範囲 (V12 では 45000 DTU など) を提供します
- 含まれるリソース (最新バージョンは V12) で有効な機能のバージョン管理の範囲です
- サーバー レベルのプリンシパルのログインによってサーバー上のすべてのデータベースを管理できます
- サーバー上の&1; つまたは複数のデータベースへのアクセスが付与された、オンプレミスの SQL Server のインスタンスでのログインと同様のログインを含めることができます。また、限定された管理者権限を付与できます。 詳細については、[ログイン](sql-database-manage-logins.md)に関する記事を参照してください。

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database-logical-server"></a>Azure SQL Database 論理サーバーに接続して認証するにはどうすればよいですか。

- **認証と承認**: Azure SQL Database は、SQL 認証と Azure Active Directory 認証をサポートしています (いくつかの制限があります。認証については「[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](sql-database-aad-authentication.md)」を参照してください。 Azure SQL Database には、サーバーのマスター データベース経由で、またはユーザー データベースに直接接続して認証できます。 詳細については、[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)に関するページを参照してください。 Windows 認証はサポートされません。 
- **TDS**: Microsoft Azure SQL Database では、表形式データ ストリーム (TDS) プロトコル クライアント バージョン 7.3 以降をサポートしています。
- **TCP/IP**: TCP/IP 接続のみが許可されます。
- **SQL Database ファイアウォール**: データを保護するため、SQL Database ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーまたはそのデータベースへのすべてのアクセスを遮断します。 [ファイアウォール](sql-database-firewall-configure.md)に関する記事を参照してください。

## <a name="what-collations-are-supported"></a>どのような照合順序がサポートされていますか。

(マスター データベースを含む) Microsoft Azure SQL Database で使用される既定のデータベース照合順序は **SQL_LATIN1_GENERAL_CP1_CI_AS** です。**LATIN1_GENERAL** は英語 (米国)、**CP1** はコード ページ 1252、**CI** は大文字と小文字の区別なし、**AS** はアクセントの区別ありを表しています。 V12 データベースの作成後の照合順序の変更はお勧めしません。 照合順序の詳細については、「[COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx)」を参照してください。

## <a name="what-are-the-naming-requirements-for-database-objects"></a>データベース オブジェクトの名前付け要件とは

すべての新しいオブジェクトの名前は、SQL Server の識別子に関する規則に従う必要があります。 詳細については、「 [データベース識別子](https://msdn.microsoft.com/library/ms175874.aspx)」を参照してください。

## <a name="what-features-are-supported"></a>どのような機能がサポートされていますか?

サポートされている機能については、[機能](sql-database-features.md)に関する記事をご覧ください。 特定の種類の機能についてサポートが不足している理由の背景については、「[Azure SQL Database の Transact-SQL の相違点](sql-database-transact-sql-information.md)」を参照してください。

## <a name="how-do-i-manage-a-logical-server"></a>論理サーバーの管理方法について教えてください。

いくつかの方法を使用して Azure SQL Database 論理サーバーを管理できます。
- [Azure ポータル](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [REST ()](/rest/api/sql/)

## <a name="next-steps"></a>次のステップ

- Azure SQL Database のサービスについては、「[SQL Database とは](sql-database-technical-overview.md)」を参照してください。
- サポートされる機能については、[機能](sql-database-features.md)に関する記事をご覧ください。
- Azure SQL Database の概要については、「[SQL Database の概要](sql-database-overview.md)」を参照してください。
- Transact-SQL のサポートおよび違いについては、「[Azure SQL Database の Transact-SQL の相違点](sql-database-transact-sql-information.md)」を参照してください。
- サービス階層に基づく特定のリソース クォータと制限については、**サービス階層**に関する記事を参照してください。 サービス レベルの概要については、「 [SQL Database のサービス階層](sql-database-service-tiers.md)」を参照してください。
- セキュリティの概要については、[Azure SQL Database のセキュリティの概要](sql-database-security-overview.md)に関するページを参照してください。
- SQL Database の利用可能なドライバーとサポートの詳細については、「 [SQL Database および SQL Server の接続ライブラリ](sql-database-libraries.md)」を参照してください。




<!--HONumber=Feb17_HO1-->


