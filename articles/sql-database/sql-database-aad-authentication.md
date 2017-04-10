---
title: "Azure Active Directory 認証 - Azure SQL (概要) | Microsoft Docs"
description: "Azure Active Directory を使用して SQL Database と SQL Data Warehouse を認証する方法について説明します"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 7e2508a1-347e-4f15-b060-d46602c5ce7e
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 01294f8f8fdaa65a5f33dbf6d35c0baa342cacc8
ms.lasthandoff: 03/24/2017


---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql-database-or-sql-data-warehouse"></a>Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse を認証する
Azure Active Directory 認証は、Azure Active Directory (Azure AD) の ID を使用して Microsoft Azure SQL Database および [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) に接続するメカニズムです。 Azure AD 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。 ID の一元管理では、1 か所でデータベース ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。 次のような利点があります。

* SQL Server 認証の代替方法が用意されています。
* データベース サーバー全体でユーザー ID が急増するのを防ぎます。
* 1 か所でのパスワードのローテーションを許可します。
* 顧客は外部の (AAD) グループを使用してデータベースのアクセス許可を管理できます。
* 統合 Windows 認証や、Azure Active Directory でサポートされる他の認証形式を有効にすることで、パスワードが保存されないようにすることができます。
* Azure AD 認証では、包含データベース ユーザーを使用して、データベース レベルで ID を認証します。
* Azure AD は、SQL Database に接続するアプリケーション向けにトークンベース認証をサポートしています。
* Azure AD 認証は、ADFS (ドメイン フェデレーション) またはドメインを同期しないローカル Azure Active Directory のネイティブ ユーザー/パスワード認証をサポートします。  
* Azure AD 認証は、Multi-Factor Authentication (MFA) を含む Active Directory ユニバーサル認証を使用する SQL Server Management Studio からの接続をサポートします。  MFA には、電話、テキスト メッセージ、スマート カードと暗証番号 (PIN)、モバイル アプリ通知など、簡単な各種確認オプションによる強力な認証が含まれます。 詳細については、「 [SQL Database と SQL Data Warehouse での Azure AD MFA のための SSMS のサポート](sql-database-ssms-mfa-authentication.md)」を参照してください。

構成の手順には、Azure Active Directory 認証を構成して使用する次の手順が含まれます。

1. Azure AD を作成して設定します。
2. データベースが Azure SQL Database V12 であることを確認します  (SQL Data Warehouse の場合は不要)。
3. 省略可能: Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更します。
4. Azure SQL Server または [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) の Azure Active Directory 管理者を作成します。
5. クライアント コンピューターを構成します。
6. Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成します。
7. Azure AD の ID を使用してデータベースに接続します。

> [!NOTE]
> Azure AD を作成して設定した後、Azure SQL Database と SQL Data Warehouse で Azure AD を構成する方法については、[Azure SQL Database での Azure AD の構成](sql-database-aad-authentication-configure.md)に関するページを参照してください。
>

## <a name="trust-architecture"></a>信頼のアーキテクチャ
次の図は、Azure SQL Database で Azure AD 認証を使用するソリューション アーキテクチャの概要を示しています。 同じ概念が SQL Data Warehouse にも適用されます。 Azure AD のネイティブ ユーザー パスワードをサポートする場合は、クラウドの部分と Azure AD/Azure SQL Database のみを考慮します。 フェデレーション認証 (または Windows 資格情報のユーザー/パスワード) をサポートするには、ADFS ブロックとの通信が必要です。 矢印は通信経路を示します。

![aad auth diagram][1]

次の図は、クライアントがトークンの送信によってデータベースへの接続を許可される、フェデレーション、信頼、およびホスティングの関係を示しています。 トークンは、Azure AD によって認証され、データベースによって信頼されます。 顧客 1 は、Azure Active Directory とネイティブ ユーザーまたは Azure AD とフェデレーション ユーザーを表します。 顧客 2 は、インポートされたユーザーなどの可能性のあるソリューションを表します。この例では、Azure Active Directory および Azure Active Directory と同期された ADFS です。 重要なのは、Azure AD 認証を使用してデータベースにアクセスするには、ホストしているサブスクリプションを Azure AD に関連付ける必要があることを理解することです。 同じサブスクリプションを使用して、Azure SQL Database または SQL Data Warehouse をホストする SQL Server を作成する必要があります。

![サブスクリプションの関係性][2]

## <a name="administrator-structure"></a>管理者の構造
Azure AD 認証を使用すると、SQL Database サーバーの管理者アカウントは、元の SQL Server 管理者と Azure AD 管理者の 2 つになります。 同じ概念が SQL Data Warehouse にも適用されます。 ユーザー データベースに最初の Azure AD 包含データベース ユーザーを作成できるのは、Azure AD アカウントに基づく管理者のみです。 Azure AD の管理者ログインには、Azure AD ユーザーまたは Azure AD グループを使用できます。 管理者がグループ アカウントの場合は、SQL Server インスタンスに複数の Azure AD 管理者を有効にすることで、すべてのグループ メンバーがそのアカウントを使用できます。 グループ アカウントを管理者として使用すると、SQL Database でユーザーまたはアクセス許可を変更することなく Azure AD でグループ メンバーを一元的に追加および削除できるため、より管理しやすくなります。 いつでも構成できる Azure AD 管理者 (ユーザーまたはグループ) は 1 つだけです。

![admin structure][3]

## <a name="permissions"></a>アクセス許可
新しいユーザーを作成するには、データベースにおける `ALTER ANY USER` アクセス許可が必要です。 `ALTER ANY USER` アクセス許可は、任意のデータベース ユーザーに付与できます。 `ALTER ANY USER` アクセス許可は、サーバーの管理者アカウント、そのデータベースの `CONTROL ON DATABASE`または `ALTER ON DATABASE` アクセス許可を持つデータベース ユーザー、`db_owner` データベース ロールのメンバーも保持しています。

Azure SQL Database または SQL Data Warehouse に包含データベース ユーザーを作成するには、Azure AD の ID を使用してデータベースに接続する必要があります。 最初の包含データベース ユーザーを作成するには、(データベースの所有者である) Azure AD 管理者を使用してデータベースに接続する必要があります。 これについては、以下の手順 4. ～ 5. で説明します。 Azure SQL Database または SQL Data Warehouse サーバーに対して Azure AD 管理者が作成された場合にのみ、任意の Azure AD 認証が可能です。 Azure Active Directory 管理者がサーバーから削除された場合、SQL Server 内に以前に作成された既存の Azure Active Directory ユーザーは、Azure Active Directory 資格情報を使用してもデータベースにアクセスできなくなります。

## <a name="azure-ad-features-and-limitations"></a>Azure AD の機能と制限事項
Azure AD の次のメンバーを、Azure SQL Server または SQL Data Warehouse にプロビジョニングできます。

* ネイティブ メンバー: 管理対象ドメインまたは顧客のドメインの Azure AD で作成したメンバー。 詳細については、 [Azure AD への独自のドメイン名の追加](../active-directory/active-directory-add-domain.md)に関する記事をご覧ください。
* フェデレーション ドメインのメンバー: フェデレーション ドメインを使用して Azure AD で作成されたメンバー。 詳細については、「 [Microsoft Azure now supports federation with Windows Server Active Directory (Microsoft Azure による Windows Server Active Directory とのフェデレーションのサポートの実現)](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)」をご覧ください。
* ネイティブ メンバーまたはフェデレーション ドメインのメンバーである別の Azure AD からインポートされたメンバー。
* セキュリティ グループとして作成された Active Directory グループ。

Microsoft アカウント (outlook.com、hotmail.com、live.com など) またはその他のゲスト アカウント (gmail.com、yahoo.com など) はサポートされていません。 アカウントとパスワードを使用して [https://login.live.com](https://login.live.com) にログインできる場合は、Azure SQL Database または Azure SQL Data Warehouse の Azure AD 認証でサポートされていない Microsoft アカウントを使用しています。

## <a name="connecting-using-azure-ad-identities"></a>Azure AD の ID を使用した接続

Azure Active Directory 認証では、Azure AD の ID を使用してデータベースに接続する次の方法がサポートされています。

* 統合 Windows 認証を使用する
* Azure AD のプリンシパル名とパスワードを使用する
* アプリケーション トークン認証を使用する

### <a name="additional-considerations"></a>追加の考慮事項

* さらに管理しやすくするには、管理者として専用の Azure AD グループをプロビジョニングすることをお勧めします。   
* Azure SQL Server または Azure SQL Data Warehouse 用にいつでも構成できる Azure AD 管理者 (ユーザーまたはグループ) は 1 つだけです。   
* Azure Active Directory アカウントを使用して最初に Azure SQL Server または Azure SQL Data Warehouse に接続できるのは、SQL Server の Azure AD 管理者だけです。 Active Directory 管理者は、それ以降の Azure AD のデータベース ユーザーを構成できます。   
* 接続のタイムアウトを 30 秒に設定することをお勧めします。   
* SQL Server 2016 Management Studio と SQL Server Data Tools for Visual Studio 2015 (バージョン 14.0.60311.1April 2016 以降) では、Azure Active Directory 認証がサポートされています  (Azure AD 認証は、**.NET Framework Data Provider for SqlServer** (.NET Framework 4.6 以降のバージョン) でサポートされています)。 したがって、これらのツールとデータ層アプリケーション (DAC および .bacpac) の最新のバージョンでは、Azure AD 認証を使用できます。   
* [ODBC バージョン 13.1](https://www.microsoft.com/download/details.aspx?id=53339) は Azure Active Directory 認証をサポートしていますが、`bcp.exe` では以前の ODBC プロバイダーが使用されているため、Azure Active Directory 認証を使用して接続することはできません。   
* `sqlcmd` では、 [ダウンロード センター](http://go.microsoft.com/fwlink/?LinkID=825643)から入手可能なバージョン 13.1 以降の Azure Active Directory 認証をサポートしています。   
* SQL Server Data Tools for Visual Studio 2015 には、April 2016 バージョン以降の Data Tools (バージョン 14.0.60311.1) が必要です。 現在、Azure AD ユーザーは SSDT のオブジェクト エクスプローラーに表示されません。 回避策として、ユーザーを [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx) で表示してください。   
* [Microsoft JDBC Driver 6.0 for SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) は、Azure AD 認証をサポートしています。 「 [接続プロパティの設定](https://msdn.microsoft.com/library/ms378988.aspx)」もご覧ください。   
* PolyBase では Azure AD 認証を使用した認証は行えません。   
* SQL Database の Azure AD 認証は、Azure Portal の **[データベースのインポート]** ブレードと **[データベースのエクスポート]** ブレードでサポートされています。 Azure AD 認証を使用したインポートとエクスポートは、PowerShell コマンドでもサポートされています。   

## <a name="next-steps"></a>次のステップ
- Azure AD を作成して設定した後、Azure SQL Database で Azure AD を構成する方法については、[Azure SQL Database での Azure AD の構成](sql-database-aad-authentication-configure.md)に関するページを参照してください。
- SQL Database でのアクセスおよび制御の概要については、[SQL Database のアクセスと制御](sql-database-control-access.md)に関するページを参照してください。
- SQL Database のログイン、ユーザー、データベース ロールの概要については、[ログイン、ユーザー、およびデータベース ロール](sql-database-manage-logins.md)に関するページを参照してください。
- データベース プリンシパルの詳細については、「[プリンシパル](https://msdn.microsoft.com/library/ms181127.aspx)」を参照してください。
- データベース ロールの詳細については、[データベース ロール](https://msdn.microsoft.com/library/ms189121.aspx)に関するページを参照してください。
- SQL Database のファイアウォール規則の詳細については、[SQL Database のファイアウォール規則](sql-database-firewall-configure.md)に関するページを参照してください。
- SQL Server 認証の使用に関するチュートリアルについては、[SQL の認証と許可](sql-database-control-access-sql-authentication-get-started.md)に関するページを参照してください。
- Azure Active Directory 認証の使用に関するチュートリアルについては、[Azure AD の認証と許可](sql-database-control-access-aad-authentication-get-started.md)に関するページを参照してください。

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png


