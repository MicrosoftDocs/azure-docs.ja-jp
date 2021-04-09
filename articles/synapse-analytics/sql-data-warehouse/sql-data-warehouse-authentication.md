---
title: 専用 SQL プール (旧称 SQL DW) の認証
description: Azure Active Directory (Azure AD) または SQL Server 認証を使用して、Azure Synapse Analytics で専用 SQL プール (旧称 SQL DW) に対して認証する方法について説明します。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 80bc9f6fc6af94ba2a5ade77cc1d53b3fc29f1ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685346"
---
# <a name="authenticate-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics での専用 SQL プール (旧称 SQL DW) に対する認証

Azure Active Directory (Azure AD) または SQL Server 認証を使用して、Azure Synapse で専用 SQL プール (旧称 SQL DW) に対して認証する方法について説明します。

専用 SQL プール (旧称 SQL DW) に接続するには、認証のためにセキュリティ資格情報を渡す必要があります。 接続の確立時に、特定の接続の設定が、クエリ セッションの構築の一環として構成されます。  

セキュリティの詳細と、専用 SQL プール (旧称 SQL DW) への接続を有効にする方法の詳細については、[データベースのセキュリティ保護に関するドキュメント](sql-data-warehouse-overview-manage-security.md)を参照してください。

## <a name="sql-authentication"></a>SQL 認証

専用 SQL プール (旧称 SQL DW) に接続するには、次の情報を指定する必要があります。

* 完全修飾サーバー名
* SQL 認証の指定
* ユーザー名
* Password
* 既定のデータベース (省略可)

既定では、ユーザー データベースではなく、*master* データベースに接続されます。 ユーザー データベースに接続するには、次の 2 つの操作のいずれかを選択します。

* SQL Server オブジェクト エクスプ ローラーで、SSDT、 SSMS またはアプリケーションの接続文字列を使用してサーバーを登録する際に、 既定のデータベースを指定する。 たとえば、ODBC 接続に InitialCatalog パラメーターを含めます。
* SSDT でセッションを作成する前に、ユーザー データベースを強調表示する。

> [!NOTE]
> 接続目的でデータベースを切り替えるための TRANSACT-SQL ステートメント **USE MyDatabase;** はサポートされていません。 SSDT を使用して SQL プールに接続する方法については、[Visual Studio を使用したクエリ](sql-data-warehouse-query-visual-studio.md)に関する記事を参照してください。

## <a name="azure-active-directory-authentication"></a>Azure Active Directory 認証

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 認証は、Azure Active Directory (Azure AD) の ID を使用して SQL プールに接続するメカニズムです。 Azure Active Directory 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。 ID の一元管理では、1 か所で 専用 SQL プール (旧称 SQL DW) ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。

### <a name="benefits"></a>メリット

Azure Active Directory には次のような利点があります。

* SQL Server 認証の代替方法が用意されています。
* サーバー全体でユーザー ID が急増するのを防ぎます
* 1 か所でのパスワードのローテーションを許可します。
* 外部の (Azure AD) グループを使用してデータベースのアクセス許可を管理できます。
* 統合 Windows 認証や、Azure Active Directory でサポートされる他の認証形式を有効にすると、パスワードが保存されません。
* 包含データベース ユーザーを使用して、データベース レベルで ID を認証します。
* SQL プールに接続するアプリケーション向けにトークンベース認証をサポートしています。
* [SQL Server Management Studio](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)、[SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) などのさまざまなツールに対して、Active Directory ユニバーサル認証を介して Multi-Factor Authentication をサポートします。

> [!NOTE]
> Azure Active Directory はまだ比較的新しいため、制限がいくつかあります。 Azure Active Directory を環境に確実に適合させるには、「 [Azure AD の機能と制限事項](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations)」を参照してください。

### <a name="configuration-steps"></a>構成の手順

Azure Active Directory 認証を構成するには、次の手順に従います。

1. Azure Active Directory を作成して設定する
2. 省略可能:Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更する
3. Azure Synapse の Azure Active Directory 管理者を作成する
4. クライアント コンピューターを構成する
5. Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成する
6. Azure AD ID を使用して SQL プールに接続する

現在、Azure Active Directory ユーザーは SSDT のオブジェクト エクスプローラーに表示されません。 回避策として、ユーザーを [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) で表示してください。

### <a name="find-the-details"></a>詳細の確認

* Azure Active Directory 認証を構成および使用する手順は、Azure SQL Database と Azure Synapse の Synapse SQL とではほぼ同じです。 詳細な手順については、[Azure Active Directory 認証を使用して SQL Database または SQL プールに接続する](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)方法に関する記事を参照してください。
* カスタム データベース ロールを作成し、ロールにユーザーを追加します。 その後、ロールに詳細なアクセス許可を付与します。 詳細については、「 [データベース エンジンの権限の概要](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」をご覧ください。

## <a name="next-steps"></a>次のステップ

Visual Studio とその他のアプリケーションでクエリを開始するには、[Visual Studio を使用したクエリ](sql-data-warehouse-query-visual-studio.md)に関する記事を参照してください。
