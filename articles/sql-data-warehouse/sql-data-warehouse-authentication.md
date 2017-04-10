---
title: "Azure SQL Data Warehouse への認証 | Microsoft Docs"
description: "Azure SQL Data Warehouse への Azure Active Directory (AAD) および SQL Server 認証。"
services: sql-data-warehouse
documentationcenter: 
author: byham
manager: jhubbard
editor: 
tags: 
ms.assetid: fefaaa75-2d0c-4e5d-aadb-410342d1ad73
ms.service: sql-data-warehouse
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.custom: security
ms.date: 03/21/2017
ms.author: rickbyh;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 9f38d57029e962d1bbe8adae68232baa8a4c575d
ms.lasthandoff: 03/22/2017


---
# <a name="authentication-to-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse への認証
> [!div class="op_single_selector"]
> * [セキュリティの概要](sql-data-warehouse-overview-manage-security.md)
> * [認証](sql-data-warehouse-authentication.md)
> * [暗号化 (ポータル)](sql-data-warehouse-encryption-tde.md)
> * [暗号化 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

SQL Data Warehouse に接続するには、認証のためのセキュリティ資格情報を渡す必要があります。 接続の確立時に、特定の接続の設定が、クエリ セッションの構築の一環として構成されます。  

セキュリティの詳細、および SQL Data Warehouse への接続を有効にする方法について詳しくは、「[SQL Data Warehouse でのデータベース保護][Secure a database in SQL Data Warehouse]」をご覧ください。

## <a name="sql-authentication"></a>SQL 認証
SQL Data Warehouse に接続するには、次の情報を提供する必要があります。

* 完全修飾サーバー名
* SQL 認証の指定
* ユーザー名
* パスワード
* 既定のデータベース (省略可) 

既定では、ユーザー データベースではなく、 *master* データベースに接続されます。 ユーザー データベースに接続するには、次の 2 つの操作のいずれかを選択します。

* SQL Server オブジェクト エクスプ ローラーで、SSDT、 SSMS またはアプリケーションの接続文字列を使用してサーバーを登録する際に、 既定のデータベースを指定する。 たとえば、ODBC 接続に InitialCatalog パラメーターを含めます。
* SSDT でセッションを作成する前に、ユーザー データベースを強調表示する。

> [!NOTE]
> 接続目的でデータベースを切り替えるための TRANSACT-SQL ステートメント **USE MyDatabase;** はサポートされていません。 SSDT を使用した SQL Data Warehouse への接続に関するガイダンスは、[Visual Studio を使用したクエリ][Query with Visual Studio]に関する記事をご覧ください。
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD) 認証
[Azure Active Directory][What is Azure Active Directory] 認証は、Azure Active Directory (Azure AD) の ID を使用して Microsoft Azure SQL Data Warehouse に接続するメカニズムです。 Azure Active Directory 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。 ID の一元管理では、1 か所で SQL Data Warehouse ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。 

### <a name="benefits"></a>メリット
Azure Active Directory には次のような利点があります。

* SQL Server 認証の代替方法が用意されています。
* データベース サーバー全体でユーザー ID が急増するのを防ぎます。
* 1 か所でのパスワードのローテーションを許可します。
* 外部の (AAD) グループを使用してデータベースのアクセス許可を管理できます。
* 統合 Windows 認証や、Azure Active Directory でサポートされる他の認証形式を有効にすると、パスワードが保存されません。
* 包含データベース ユーザーを使用して、データベース レベルで ID を認証します。
* SQL Data Warehouse に接続するアプリケーション向けにトークンベース認証をサポートしています。
* SQL Server Management Studio の Active Directory ユニバーサル認証を介して Multi-Factor Authentication をサポートします。 Multi-Factor Authentication の説明については、「 [SQL Database と SQL Data Warehouse での Azure AD MFA のための SSMS のサポート](../sql-database/sql-database-ssms-mfa-authentication.md)」を参照してください。

> [!NOTE]
> Azure Active Directory はまだ比較的新しいため、制限がいくつかあります。 Azure Active Directory を環境に確実に適合させるには、「[Azure AD の機能と制限事項][Azure AD features and limitations]」の、特に「追加の考慮事項」をご覧ください。
> 
> 

### <a name="configuration-steps"></a>構成の手順
Azure Active Directory 認証を構成するには、次の手順に従います。

1. Azure Active Directory を作成して設定する
2. 省略可能: Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更する
3. Azure SQL Data Warehouse 用に Azure Active Directory 管理者を作成する
4. クライアント コンピューターを構成する
5. Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成する
6. Azure AD の ID を使用してデータ ウェアハウスに接続する

現在、Azure Active Directory ユーザーは SSDT のオブジェクト エクスプローラーに表示されません。 回避策として、ユーザーを [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx) で表示してください。

### <a name="find-the-details"></a>詳細の確認
* 詳細な手順を実行します。 Azure Active Directory 認証を構成および使用する詳細な手順は、Azure SQL Database と Azure SQL Data Warehouse ではほぼ同じです。 詳細な手順については、「 [Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](../sql-database/sql-database-aad-authentication.md)」を参照してください。
* カスタム データベース ロールを作成し、ロールにユーザーを追加します。 その後、ロールに詳細なアクセス許可を付与します。 詳細については、「 [データベース エンジンの権限の概要](https://msdn.microsoft.com/library/mt667986.aspx)」をご覧ください。

## <a name="next-steps"></a>次のステップ
Visual Studio またはその他のアプリケーションを使用してデータ ウェアハウスに対するクエリを開始するには、[Visual Studio を使用したクエリ][Query with Visual Studio]に関するページをご覧ください。

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations

