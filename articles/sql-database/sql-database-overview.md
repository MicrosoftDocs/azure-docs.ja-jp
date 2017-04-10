---
title: "Azure SQL データベースとは | Microsoft Docs"
description: "この記事は、Azure SQL Database の概要を示します。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 06639080dec485395d6821c371a4f792c7c02845
ms.openlocfilehash: 5e3ac3a08ea3e5c72082dcc7faa209d848acf2fe


---
# <a name="azure-sql-database-overview"></a>Azure SQL Database の概要
このトピックは、Azure SQL Database の概要を示します。 Azure SQL 論理サーバーについては、[論理サーバー](sql-database-server-overview.md)に関するページを参照してください。

## <a name="what-is-azure-sql-database"></a>Azure SQL Database とは
Azure SQL Database 内の各データベースは、論理サーバーに関連付けられています。 データベースには以下の種類があります。

- 単一のデータベースとその[独自のリソースのセット](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU)
- [一連のリソースを共有する](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus)[エラスティック プール](sql-database-elastic-pool.md)の一部 (eDTU)
- [シャード化されたデータベースのスケール アウトされたセット](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling)の一部 (単一またはプールされたデータベースのいずれか)
- [マルチテナント SaaS デザイン パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)に参加しているデータベースのセットの一部 (これらのデータベースは、単一またはプールされたデータベースのいずれか (またはその両方)) 

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database"></a>Azure SQL Database に接続して認証するにはどうすればよいですか。

- **認証と承認**: Azure SQL Database は、SQL 認証と Azure Active Directory 認証をサポートしています (いくつかの制限があります。認証については「[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](sql-database-aad-authentication.md)」を参照してください。 Azure SQL Database には、サーバーのマスター データベース経由で、またはユーザー データベースに直接接続して認証できます。 詳細については、[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)に関するページを参照してください。 Windows 認証はサポートされません。 
- **TDS**: Microsoft Azure SQL Database では、表形式データ ストリーム (TDS) プロトコル クライアント バージョン 7.3 以降をサポートしています。
- **TCP/IP**: TCP/IP 接続のみが許可されます。
- **SQL Database ファイアウォール**: データを保護するため、SQL Database ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーまたはそのデータベースへのすべてのアクセスを遮断します。 [ファイアウォール](sql-database-firewall-configure.md)に関する記事を参照してください。

## <a name="what-collations-are-supported"></a>どのような照合順序がサポートされていますか。
Microsoft Azure SQL Database で使用される既定のデータベース照合順序は **SQL_LATIN1_GENERAL_CP1_CI_AS** です。**LATIN1_GENERAL** は英語 (米国)、**CP1** はコード ページ 1252、**CI** は大文字と小文字の区別なし、**AS** はアクセントの区別ありを表しています。 V12 データベースの照合順序は変更できません。 照合順序を設定する方法の詳細については、「[COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx)」を参照してください。

## <a name="what-are-the-naming-requirements-for-database-objects"></a>データベース オブジェクトの名前付け要件とは

すべての新しいオブジェクトの名前は、SQL Server の識別子に関する規則に従う必要があります。 詳細については、「 [データベース識別子](https://msdn.microsoft.com/library/ms175874.aspx)」を参照してください。

## <a name="what-features-are-supported-by-azure-sql-databases"></a>Azure SQL Dtabase では、どのような機能がサポートされていますか。

サポートされている機能については、[機能](sql-database-features.md)に関する記事をご覧ください。 特定の種類の機能についてサポートが不足している理由の背景については、「[Azure SQL Database の Transact-SQL の相違点](sql-database-transact-sql-information.md)」を参照してください。

## <a name="how-do-i-manage-an-azure-sql-database"></a>Azure SQL Database の管理方法を教えてください。

いくつかの方法を使用して Azure SQL Database 論理サーバーを管理できます。
- [Azure ポータル](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [Transact-SQL](sql-database-manage-azure-ssms.md)
- [REST ()](/rest/api/sql/)

## <a name="next-steps"></a>次のステップ

- Azure SQL Database のサービスについては、「[SQL Database とは](sql-database-technical-overview.md)」を参照してください。
- サポートされる機能については、[機能](sql-database-features.md)に関する記事をご覧ください。
- Azure SQL 論理サーバーの概要については、「[SQL Database 論理サーバーの概要](sql-database-server-overview.md)」を参照してください。
- Transact-SQL のサポートおよび違いについては、「[Azure SQL Database の Transact-SQL の相違点](sql-database-transact-sql-information.md)」を参照してください。
- サービス階層に基づく特定のリソース クォータと制限については、**サービス階層**に関する記事を参照してください。 サービス レベルの概要については、「 [SQL Database のサービス階層](sql-database-service-tiers.md)」を参照してください。
- セキュリティの概要については、[Azure SQL Database のセキュリティの概要](sql-database-security-overview.md)に関するページを参照してください。
- SQL Database の利用可能なドライバーとサポートの詳細については、「 [SQL Database および SQL Server の接続ライブラリ](sql-database-libraries.md)」を参照してください。




<!--HONumber=Jan17_HO4-->


