---
title: Azure Active Directory 認証
description: Azure Active Directory を使用して Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics の Synapse SQL を認証する方法について説明します
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 04/23/2020
ms.openlocfilehash: a636c0e2a41b636f30ada14d4f16a022f2890b71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454293"
---
# <a name="use-azure-active-directory-authentication"></a>Azure Active Directory 認証を使用する

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Active Directory (Azure AD) 認証は、Azure AD の ID を使用して [Azure SQL Database](sql-database-paas-overview.md)、[Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)、[Azure Synapse Analytics の Synapse SQL](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) に接続するメカニズムです。

> [!NOTE]
> この記事は、Azure SQL Database、SQL Managed Instance、Azure Synapse Analytics に当てはまります。

Azure AD 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。 ID の一元管理では、1 か所でデータベース ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。 次のような利点があります。

- SQL Server 認証の代替方法が用意されています。
- サーバー全体でユーザー ID が急増するのを防ぐために役立ちます。
- 1 か所でのパスワードのローテーションを許可します。
- 顧客は外部の (Azure AD) グループを使用してデータベースのアクセス許可を管理できます。
- 統合 Windows 認証や、Azure Active Directory でサポートされる他の認証形式を有効にすることで、パスワードが保存されないようにすることができます。
- Azure AD 認証では、包含データベース ユーザーを使用して、データベース レベルで ID を認証します。
- Azure AD は、SQL Database と SQL Managed Instance に接続するアプリケーション向けにトークンベース認証をサポートしています。
- Azure AD 認証のサポート対象:
  - Azure AD クラウド専用 ID。
  - 以下をサポートする Azure AD ハイブリッド ID:
    - シームレス シングル サインオン (SSO) と 2 つのオプション (**パススルー** 認証と **パスワード ハッシュ** 認証) を組み合わせたクラウド認証。
    - フェデレーション認証。
  - Azure AD の認証方法と、どれを選択すべきかに関する詳細については、次の記事を参照してください。
    - [Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法を選択する](../../active-directory/hybrid/choose-ad-authn.md)

- Azure AD 認証は、Multi-Factor Authentication を含む Active Directory ユニバーサル認証を使用する SQL Server Management Studio からの接続をサポートします。 Multi-Factor Authentication には、電話、テキスト メッセージ、スマート カードと PIN、モバイル アプリ通知など、簡単な確認オプションによる強力な認証が含まれています。 詳細については、[Azure SQL Database、SQL Managed Instance、Azure Synapse での Azure Multi-Factor Authentication 対応の SSMS サポート](authentication-mfa-ssms-overview.md)に関するページを参照してください

- Azure AD は、Active Directory 対話型認証を使用する SQL Server Data Tools (SSDT) からの同様の接続をサポートしています。 詳細については、「[SQL Server Data Tools (SSDT) での Azure Active Directory のサポート](/sql/ssdt/azure-active-directory)」を参照してください。

> [!NOTE]  
> Azure Active Directory アカウントを使用した Azure 仮想マシン (VM) 上で実行されている SQL Server インスタンスへの接続はサポートされていません。 代わりにドメインの Active Directory アカウントを使用してください。  

構成の手順には、Azure Active Directory 認証を構成して使用する次の手順が含まれます。

1. Azure AD を作成して設定します。
2. 省略可能:Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更します。
3. Azure Active Directory 管理者を作成します。
4. クライアント コンピューターを構成します。
5. Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成します。
6. Azure AD の ID を使用してデータベースに接続します。

> [!NOTE]
> Azure AD を作成して設定した後、Azure SQL Database、SQL Managed Instance、Azure Synapse Analytics の Synapse SQL で Azure AD を構成する方法については、[Azure SQL Database での Azure AD の構成](authentication-aad-configure.md)に関するページを参照してください。

## <a name="trust-architecture"></a>信頼のアーキテクチャ

- Azure AD のネイティブ ユーザー パスワードをサポートする場合は、Azure AD、SQL Database、SQL Managed Instance、Azure Synapse のクラウドの部分のみを考慮します。
- Windows シングル サインオン資格情報 (または Windows 資格情報のユーザーとパスワード) をサポートするには、パススルー認証とパスワード ハッシュ認証のためのシームレスなシングル サインオン用に構成されたフェデレーション ドメインまたはマネージド ドメインから得られる Azure Active Directory 資格情報を使用します。 詳しくは、「[Azure Active Directory シームレス シングル サインオン](../../active-directory/hybrid/how-to-connect-sso.md)」をご覧ください。
- フェデレーション認証 (または Windows 資格情報のユーザー/パスワード) をサポートするには、ADFS ブロックとの通信が必要です。

Azure AD ハイブリッド ID、セットアップ、および同期に関する詳細については、次の記事を参照してください。

- パスワード ハッシュ認証 - 「[Azure AD Connect 同期を使用したパスワード ハッシュ同期の実装](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)」
- パススルー認証 - 「[Azure Active Directory パススルー認証](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)」
- フェデレーション認証 - 「[Azure での Active Directory フェデレーション サービス (AD FS) のデプロイ](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)」および「[Azure AD Connect とフェデレーション](../../active-directory/hybrid/how-to-connect-fed-whatis.md)」

ADFS インフラストラクチャ (または Windows 資格情報のユーザーとパスワード) を使用したサンプルのフェデレーション認証については、以下の図を参照してください。 矢印は通信経路を示します。

![aad auth diagram][1]

次の図は、クライアントがトークンの送信によってデータベースへの接続を許可される、フェデレーション、信頼、およびホスティングの関係を示しています。 トークンは、Azure AD によって認証され、データベースによって信頼されます。 顧客 1 は、Azure Active Directory とネイティブ ユーザーまたは Azure AD とフェデレーション ユーザーを表します。 顧客 2 は、インポートされたユーザーなどの可能性のあるソリューションを表します。この例では、Azure Active Directory および Azure Active Directory と同期された ADFS です。 重要なのは、Azure AD 認証を使用してデータベースにアクセスするには、ホストしているサブスクリプションを Azure AD に関連付ける必要があることを理解することです。 同じサブスクリプションを使用して、Azure SQL Database、SQL Managed Instance、または Azure Synapse リソースを作成する必要があります。

![サブスクリプションの関係性][2]

## <a name="administrator-structure"></a>管理者の構造

Azure AD 認証を使用する場合、元の Azure SQL Server 管理者と Azure AD 管理者の 2 つのサーバー管理者アカウントがあります。 同じ概念が Azure Synapse に適用されます。 ユーザー データベースに最初の Azure AD 包含データベース ユーザーを作成できるのは、Azure AD アカウントに基づく管理者のみです。 Azure AD の管理者ログインには、Azure AD ユーザーまたは Azure AD グループを使用できます。 管理者がグループ アカウントの場合は、サーバー用に複数の Azure AD 管理者を有効にすることで、すべてのグループ メンバーがそのアカウントを使用できます。 グループ アカウントを管理者として使用すると、SQL Database または Azure Synapse でユーザーまたはアクセス許可を変更することなく Azure AD でグループ メンバーを一元的に追加および削除できるため、より管理しやすくなります。 いつでも構成できる Azure AD 管理者 (ユーザーまたはグループ) は 1 つだけです。

![admin structure][3]

## <a name="permissions"></a>アクセス許可

新しいユーザーを作成するには、データベースにおける `ALTER ANY USER` アクセス許可が必要です。 `ALTER ANY USER` アクセス許可は、任意のデータベース ユーザーに付与できます。 `ALTER ANY USER` アクセス許可は、サーバーの管理者アカウント、そのデータベースの `CONTROL ON DATABASE`または `ALTER ON DATABASE` アクセス許可を持つデータベース ユーザー、`db_owner` データベース ロールのメンバーも保持しています。

Azure SQL Database、SQL Managed Instance、または Azure Synapse に包含データベース ユーザーを作成するには、Azure AD ID を使用してデータベースまたはインスタンスに接続する必要があります。 最初の包含データベース ユーザーを作成するには、(データベースの所有者である) Azure AD 管理者を使用してデータベースに接続する必要があります。 これは、[SQL Database または Azure Synapse で Azure Active Directory 認証を構成して管理する](authentication-aad-configure.md)方法に関するページで説明されています。 Azure SQL Database、SQL Managed Instance、または Azure Synapse に対して Azure AD 管理者が作成された場合にのみ、任意の Azure AD 認証が可能です。 Azure Active Directory 管理者がサーバーから削除された場合、SQL Server 内に以前に作成された既存の Azure Active Directory ユーザーは、Azure Active Directory 資格情報を使用してもデータベースにアクセスできなくなります。

## <a name="azure-ad-features-and-limitations"></a>Azure AD の機能と制限事項

- Azure AD の次のメンバーを Azure SQL Database にプロビジョニングできます。

  - ネイティブ メンバー:マネージド ドメインまたは顧客のドメインの Azure AD で作成したメンバー。 詳細については、 [Azure AD への独自のドメイン名の追加](../../active-directory/fundamentals/add-custom-domain.md)に関する記事をご覧ください。
  - Azure Active Directory とフェデレーションされた Active Directory ドメインのメンバー (パススルー認証またはパスワード ハッシュ認証によるシームレスなシングル サインオン用に構成されたマネージド ドメイン上)。 詳細については、[Microsoft Azure による Windows Server Active Directory とのフェデレーションのサポートの実現](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//)に関するページと「[Azure Active Directory シームレス シングル サインオン](../../active-directory/hybrid/how-to-connect-sso.md)」を参照してください。
  - ネイティブ メンバーまたはフェデレーション ドメインのメンバーである別の Azure AD からインポートされたメンバー。
  - セキュリティ グループとして作成された Active Directory グループ。

- `db_owner` サーバー ロールを持つグループに含まれている Azure AD ユーザーは、Azure SQL Database と Azure Synapse に対して **[CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** 構文を使用できません。 次のエラーが表示されます。

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    **CREATE DATABASE SCOPED CREDENTIAL** の問題を軽減するには、`db_owner` ロールを個々の Azure AD ユーザーに直接付与します。

- Azure AD プリンシパル下で実行された場合、以下のシステム関数は NULL 値を返します。

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>SQL Managed Instance

- Azure AD のサーバー プリンシパル (ログイン) とユーザーは、[SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) でサポートされています。
- Azure AD グループにマップされた Azure AD サーバー プリンシパル (ログイン) をデータベース所有者として設定することは、[SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) ではサポートされていません。
  - この拡張機能により、グループが `dbcreator` サーバー ロールの一部として追加されたときに、このグループのユーザーが SQL Managed Instance に接続して新しいデータベースを作成できますが、データベースにアクセスすることはできません。 これは、新しいデータベース所有者が SA であり、Azure AD ユーザーではないためです。 この問題は、`dbcreator` サーバー ロールに個々のユーザーが追加された場合は発生しません。
- SQL Agent の管理とジョブの実行が、Azure AD サーバー プリンシパル (ログイン) に対してサポートされています。
- データベースのバックアップと復元操作は、Azure AD サーバー プリンシパル (ログイン) が実行できます。
- Azure AD サーバー プリンシパル (ログイン) と認証イベントに関連するすべてのステートメントの監査がサポートされています。
- sysadmin サーバー ロールのメンバーである Azure AD サーバー プリンシパル (ログイン) の専用管理者接続がサポートされています。
  - sqlcmd ユーティリティおよび SQL Server Management Studio 経由でサポートされています。
- Azure AD サーバー プリンシパル (ログイン) によるログオン イベントでは、ログオン トリガーがサポートされています。
- Service Broker とデータベース メールは、Azure AD サーバー プリンシパル (ログイン) を使用して設定できます。

## <a name="connect-by-using-azure-ad-identities"></a>Azure AD の ID を使用して接続する

Azure Active Directory 認証では、Azure AD の ID を使用してデータベースに接続する次の方法がサポートされています。

- Azure Active Directory パスワード
- Azure Active Directory 統合
- Multi-Factor Authentication で汎用の Azure Active Directory
- アプリケーション トークン認証を使用する

Azure AD サーバー プリンシパル (ログイン) では、次の認証方法がサポートされています。

- Azure Active Directory パスワード
- Azure Active Directory 統合
- Multi-Factor Authentication で汎用の Azure Active Directory

### <a name="additional-considerations"></a>その他の注意点

- さらに管理しやすくするには、管理者として専用の Azure AD グループをプロビジョニングすることをお勧めします。
- SQL Database または Azure Synapse のサーバー用にいつでも構成できる Azure AD 管理者 (ユーザーまたはグループ) は 1 つだけです。
  - SQL Managed Instance 用の Azure AD サーバー プリンシパル (ログイン) の追加により、`sysadmin` ロールに追加できる複数の Azure AD サーバー プリンシパル (ログイン) を作成できる可能性があります。
- Azure Active Directory アカウントを使用して最初に サーバーまたはマネージド インスタンスに接続できるのは、サーバーの Azure AD 管理者だけです。 Active Directory 管理者は、それ以降の Azure AD のデータベース ユーザーを構成できます。
- 接続のタイムアウトを 30 秒に設定することをお勧めします。
- SQL Server 2016 Management Studio と SQL Server Data Tools for Visual Studio 2015 (バージョン 14.0.60311.1April 2016 以降) では、Azure Active Directory 認証がサポートされています (Azure AD 認証は、 **.NET Framework Data Provider for SqlServer** (.NET Framework 4.6 以降のバージョン) でサポートされています)。 したがって、これらのツールとデータ層アプリケーション (DAC および BACPAC) の最新のバージョンでは、Azure AD 認証を使用できます。
- バージョン 15.0.1 以降では、[sqlcmd ユーティリティ](/sql/tools/sqlcmd-utility)と [bcp ユーティリティ](/sql/tools/bcp-utility)は Multi-Factor Authentication を使用した Active Directory 対話型認証をサポートしています。
- SQL Server Data Tools for Visual Studio 2015 には、April 2016 バージョン以降の Data Tools (バージョン 14.0.60311.1) が必要です。 現在、Azure AD ユーザーは SSDT のオブジェクト エクスプローラーに表示されません。 回避策として、ユーザーを [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql) で表示してください。
- [Microsoft JDBC Driver 6.0 for SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) は、Azure AD 認証をサポートしています。 「 [接続プロパティの設定](/sql/connect/jdbc/setting-the-connection-properties)」もご覧ください。
- PolyBase では Azure AD 認証を使用した認証は行えません。
- Azure SQL Database と Azure Synapse 向けの Azure AD 認証は、Azure Portal の **[データベースのインポート]** ブレードと **[データベースのエクスポート]** ブレードによりサポートされています。 Azure AD 認証を使用したインポートとエクスポートは、PowerShell コマンドでもサポートされています。
- Azure AD 認証は、CLI を使用することによって、SQL Database、SQL Managed Instance、Azure Synapse でサポートされます。 詳細については、[SQL Database または Azure Synapse で Azure AD 認証を構成して管理する](authentication-aad-configure.md)方法に関するページと「[SQL Server - az sql server](/cli/azure/sql/server)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Azure AD インスタンスを作成して設定し、Azure SQL Database、SQL Managed Instance、または Azure Synapse を使用して構成する方法については、[SQL Database、SQL Managed Instance、または Azure Synapse で Azure Active Directory 認証を構成して管理する](authentication-aad-configure.md)方法に関するページを参照してください。
- SQL Managed Instance での Azure AD サーバー プリンシパル (ログイン) の使用のチュートリアルについては、[SQL Managed Instance での Azure AD サーバー プリンシパル (ログイン)](../managed-instance/aad-security-configure-tutorial.md) に関するページを参照してください
- SQL Database のログイン、ユーザー、データベース ロール、アクセス許可の概要については、[ログイン、ユーザー、データベース ロール、およびアクセス許可](logins-create-manage.md)に関するページを参照してください。
- データベース プリンシパルの詳細については、「[プリンシパル](/sql/relational-databases/security/authentication-access/principals-database-engine)」を参照してください。
- データベース ロールの詳細については、[データベース ロール](/sql/relational-databases/security/authentication-access/database-level-roles)に関するページを参照してください。
- SQL Managed Instance 用の Azure AD サーバー プリンシパル (ログイン) の作成の構文については、「[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)」を参照してください。
- SQL Database のファイアウォール規則の詳細については、[SQL Database のファイアウォール規則](firewall-configure.md)に関するページを参照してください。

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
