---
title: ログインとユーザー アカウントを使用してサーバーおよびデータベースへのアクセスを承認する
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure SQL Database、SQL Managed Instance、Azure Synapse Analytics がログインとユーザー アカウントを使用して、アクセスのためにユーザーを認証する方法について説明します。 また、ログインとユーザーを承認してアクションを実行したりデータを照会できるように、データベース ロールと明示的な権限を付与する方法についても説明します。
keywords: SQL Database のセキュリティ,データベース セキュリティ管理,ログイン セキュリティ,データベース セキュリティ,データベース アクセス
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 03/23/2020
ms.openlocfilehash: d03bce1566d4f56a576c980723571f587296236f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96452418"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>SQL Database、SQL Managed Instance、Azure Synapse Analytics へのデータベース アクセスを承認する
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

この記事では、次の内容について説明します。

- Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics を構成して、ユーザーが管理タスクを実行し、これらのデータベースに格納されているデータにアクセスできるようにするためのオプション。
- 最初に新しいサーバーを作成した後のアクセスと承認の構成。
- マスター データベースとユーザー アカウントにログインとユーザー アカウントを追加し、これらのアカウントに管理アクセス許可を付与する方法。
- ログインに関連付けられているユーザー アカウントまたは包含ユーザー アカウントとして、ユーザー データベースにユーザー アカウントを追加する方法。
- データベース ロールと明示的アクセス許可を使用して、ユーザー データベース内にアクセス許可を持つユーザー アカウントを構成する。

> [!IMPORTANT]
> Azure SQL Database、Azure SQL Managed Instance、Azure Synapse のデータベースは、この記事の残りの部分ではまとめてデータベースと呼ばれています。また、サーバーは、Azure SQL Database と Azure Synapse のデータベースを管理する[サーバー](logical-servers.md)を指しています。

## <a name="authentication-and-authorization"></a>認証と権限承認

[**認証**](security-overview.md#authentication)は、ユーザーが本人の主張どおりの人物であることを証明するプロセスです。 ユーザーは、ユーザー アカウントを使用してデータベースに接続します。
ユーザーは、データベースへの接続を試みるときに、ユーザー アカウントと認証情報を提供します。 ユーザーは、次の 2 つの認証方法のいずれかを使用して認証されます。

- [SQL 認証。](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

  この認証方法では、ユーザーはユーザー アカウント名と、関連付けられたパスワードを送信して接続を確立します。 このパスワードは、ログインにリンクされているユーザー アカウントのマスター データベースに格納されているか、ログインにリンクされて "*いない*" ユーザー アカウントが含まれるデータベースに格納されています。
- [Azure Active Directory 認証](authentication-aad-overview.md)

  この認証方法では、ユーザーはユーザー アカウント名を送信し、サービスでは Azure Active Directory (Azure AD) に格納されている資格情報を使用するように要求します。

**ログインとユーザー**:データベース内のユーザー アカウントをマスター データベースに格納されているログインに関連付けることや、個々のデータベースに格納されているユーザー名をユーザー アカウントにすることができます。

- **ログイン** はマスター データベース内の個々のアカウントであり、それに対して 1 つ以上のデータベース内のユーザー アカウントをリンクできます。 ログインの使用時には、ユーザー アカウントの資格情報はログインと共に格納されます。
- **ユーザー アカウント** はデータベース内の個々のアカウントであり、ログインにリンクされていることもありますが、必ずしもそうする必要はありません。 ログインにリンクされていないユーザー アカウントの使用時には、資格情報はユーザー アカウントと共に格納されます。

データにアクセスし、さまざまなアクションを実行するための [**承認**](security-overview.md#authorization)は、データベース ロールと明示的アクセス許可を使用して管理されます。 承認は、ユーザーに割り当てられるアクセス許可のことで、そのユーザーが実行できる操作を決定するものです。 ユーザー アカウントのデータベースの[ロール メンバーシップ](/sql/relational-databases/security/authentication-access/database-level-roles)と[オブジェクト レベルのアクセス許可](/sql/relational-databases/security/permissions-database-engine)によって制御されます。 ベスト プラクティスとして、必要最低限の特権をユーザーに付与することをお勧めします。

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>新規データベース作成後の既存のログインとユーザー アカウント

Azure SQL を初めてデプロイするときに、管理者ログインと、そのログインに関連付けられているパスワードを指定します。 この管理者アカウントは、**サーバー管理者** と呼ばれます。マスター データベースとユーザー データベースにおける以下のログインとユーザーの構成は、デプロイ時に行われます。

- 管理特権を持つ SQL ログインは、ユーザーが指定したログイン名を使用して作成されます。 [ログイン](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login)は、SQL Database、SQL Managed Instance、および Azure Synapse にログインするための個々のユーザー アカウントです。
- このログインには、[サーバー レベルのプリンシパル](/sql/relational-databases/security/authentication-access/principals-database-engine)としての、すべてのデータベースに対する完全な管理アクセス許可が付与されます。 このログインは使用可能なすべてのアクセス許可を持ち、制限することはできません。 SQL Managed Instance では、このログインは [sysadmin 固定サーバー ロール](/sql/relational-databases/security/authentication-access/server-level-roles)に追加されます (このロールは、Azure SQL Database には存在しません)。
- 各ユーザー データベースのこのログインに対して、`dbo` と呼ばれる[ユーザー アカウント](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users)が作成されます。 [dbo](/sql/relational-databases/security/authentication-access/principals-database-engine) ユーザーは、データベース内のすべてのデータベース アクセス許可を持ち、`db_owner` 固定データベース ロールにマップされます。 追加の固定データベース ロールについては、この記事で後述します。

データベースの管理者アカウントを確認するには、Azure portal を開き、お使いのサーバーやマネージド インスタンスの **[プロパティ]** タブに移動します。

![SQL サーバーの管理者](./media/logins-create-manage/sql-admins.png)

![[プロパティ] メニュー オプションが強調表示されているスクリーンショット。](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> 管理者のログイン名は、作成された後に変更することはできません。 サーバー管理者のパスワードをリセットするには、[Azure portal](https://portal.azure.com) にアクセスし、 **[SQL Server]** をクリックし、一覧からサーバーを選択して、 **[パスワードのリセット]** をクリックします。 SQL Managed Instance のパスワードをリセットするには、Azure portal にアクセスし、インスタンスをクリックして、 **[パスワードのリセット]** をクリックします。 PowerShell または Azure CLI を使用することもできます。

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>追加のログインと管理アクセス許可を持つユーザーを作成する

この時点で、サーバーまたはマネージド インスタンスは、単一の SQL ログインとユーザー アカウントを使用してアクセスするようにのみ構成されています。 完全な管理アクセス許可、またはその一部を持つ追加のログインを作成するために、以下のオプションが用意されています (デプロイ モードによって異なります)。

- **完全な管理アクセス許可を持つ Azure Active Directory 管理者アカウントを作成する**

  Azure Active Directory 認証を有効にして、Azure AD 管理者ログインを作成します。 1 つの Azure Active Directory アカウントを、完全な管理アクセス許可を持つ、Azure SQL デプロイの管理者として構成できます。 このアカウントは、個人のアカウントまたはセキュリティ グループのアカウントのいずれかです。 SQL Database、SQL Managed Instance、または Azure Synapse への接続に Azure AD アカウントを使用する場合は、Azure AD 管理者を構成する **必要があります**。 すべての種類の Azure SQL デプロイに対して Azure AD 認証を有効にすることの詳細については、以下の記事を参照してください。

  - [Azure Active Directory 認証を使用して SQL を認証する](authentication-aad-overview.md)
  - [SQL による Azure Active Directory 認証の構成と管理](authentication-aad-configure.md)

- **SQL Managed Instance で、完全な管理アクセス許可を持つ SQL ログインを作成する**

  - マスター データベース内に追加の SQL ログインを作成します。
  - [ALTER SERVER ROLE](/sql/t-sql/statements/alter-server-role-transact-sql) ステートメントを使用して、[sysadmin 固定サーバー ロール](/sql/relational-databases/security/authentication-access/server-level-roles)にログインを追加します。 このログインは、完全な管理アクセス許可を持つことになります。
  - または、[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 構文を使用して [Azure AD ログイン](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)を作成します。

- **SQL Database で、管理権限が制限された SQL ログインを作成する**

  - マスター データベース内に追加の SQL ログインを作成します。
  - この新しいログインに関連付けられているマスター データベース内に、ユーザー アカウントを作成します。
  - [ALTER ROLE](/sql/t-sql/statements/alter-role-transact-sql) ステートメントを使用して、ユーザー アカウントを `master` データベース内の `dbmanager` ロールと `loginmanager` ロールのいずれかまたは両方に追加します (Azure Synapse の場合は、[sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ステートメントを使用します)。

  > [!NOTE]
  > `dbmanager` ロールと `loginmanager` ロールは、SQL Managed Instance のデプロイには関係 **ありません**。

  Azure SQL Database のためのこれらの[特別なマスター データベース ロール](/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-)のメンバーには、データベースを作成して管理する権限や、ログインを作成して管理する権限があります。 `dbmanager` ロールのメンバーであるユーザーによって作成されたデータベースでは、そのメンバーは `db_owner` 固定データベース ロールにマップされており、`dbo` ユーザー アカウントを使用してそのデータベースにログインし、管理することができます。 これらのロールは、マスター データベースの外では明示的アクセス許可を持ちません。

  > [!IMPORTANT]
  > SQL Database で、完全な管理アクセス許可を持つ追加の SQL ログインを作成することはできません。

## <a name="create-accounts-for-non-administrator-users"></a>管理者以外のユーザーのアカウントを作成する

管理者以外のユーザーのアカウントは、次の 2 つの方法のいずれかを使用して作成できます。

- **ログインを作成する**

  マスター データベース内に SQL ログインを作成します。 次に、そのユーザーがアクセスする必要がある各データベース内にユーザー アカウントを作成し、そのログインにユーザー アカウントを関連付けます。 この方法は、ユーザーが複数のデータベースにアクセスする必要があり、パスワードの同期が維持されるようにしたい場合に推奨されます。 ただしこの方法は、プライマリ サーバーとセカンダリ サーバーの両方にログインを作成する必要があるため、geo レプリケーションと共に使用する場合は複雑になります。 詳細については、「[Azure SQL Database のセキュリティを geo リストアやフェールオーバー用に構成し、管理する](active-geo-replication-security-configure.md)」を参照してください。
- **ユーザー アカウントを作成する**

  ユーザーがアクセスする必要のあるデータベース内にユーザー アカウントを作成します ([包含ユーザー](/sql/relational-databases/security/contained-database-users-making-your-database-portable)とも呼ばれます)。

  - SQL Database では、この種類のユーザー アカウントをいつでも作成できます。
  - [Azure AD サーバー プリンシパル](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)をサポートしている SQL Managed Instance を使用すると、SQL Managed Instance に対して認証を行うユーザー アカウントを作成できます。包含データベースのユーザーとして、データベース ユーザーを作成する必要はありません。

  この方法では、ユーザー認証情報は各データベースに格納され、geo レプリケートされたデータベースに自動的にレプリケートされます。 ただし、同じアカウントが複数のデータベースに存在していて、Azure SQL 認証を使用している場合は、パスワードの同期を手動で維持する必要があります。 さらに、ユーザーが異なるデータベースに、パスワードが異なるアカウントを持っている場合は、それらのパスワードを覚えておくことが問題になる可能性があります。

> [!IMPORTANT]
> Azure AD の ID にマップされた包含ユーザーを作成するには、Azure SQL Database でデータベースの管理者である Azure AD アカウントを使用してログインする必要があります。 SQL Managed Instance では、`sysadmin` アクセス許可を持つ SQL ログインでも、Azure AD のログインやユーザーを作成できます。

ログインとユーザーの作成方法を示す例については、以下を参照してください。

- [Azure SQL Database のログインを作成する](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Azure SQL Managed Instance のログインを作成する](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Azure Synapse のログインを作成する](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [ユーザーの作成](/sql/t-sql/statements/create-user-transact-sql#examples)
- [Azure AD 包含ユーザーの作成](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> Azure SQL Database でのユーザーの作成を含むセキュリティのチュートリアルについては、「[チュートリアル:Azure SQL Database をセキュリティで保護する](secure-database-tutorial.md)」を参照してください。

## <a name="using-fixed-and-custom-database-roles"></a>固定データベース ロールおよびカスタム データベース ロールの使用

データベース内にユーザー アカウントを作成した後、ログインに基づいて、または包含ユーザーとして、そのユーザーがさまざまなアクションを実行し、特定のデータベースのデータにアクセスすることを承認できます。 以下の方法を使用してアクセスを承認できます。

- **固定データベース ロール**

  ユーザー アカウントを[固定データベース ロール](/sql/relational-databases/security/authentication-access/database-level-roles)に追加します。 9 つの固定データベース ロールがあり、それぞれが定義済みのアクセス許可のセットを持っています。 最も一般的な固定データベース ロールは、**db_owner**、**db_ddladmin**、**db_datawriter**、**db_datareader**、**db_denydatawriter**、**db_denydatareader** です。 **db_owner** は、少数のユーザーのみに完全なアクセス許可を付与する際によく使用されます。 他の固定データベース ロールは、開発段階の単純なデータベースをすばやく取得するには便利ですが、運用段階のほとんどのデータベースには推奨されません。 たとえば、**db_datareader** 固定データベース ロールでは、データベース内のすべてのテーブルへの読み取りアクセスが許可されますが、これは必ず必要なレベルを上回っています。

  - 固定データベース ロールにユーザーを追加するには:

    - Azure SQL Database で、[ALTER ROLE](/sql/t-sql/statements/alter-role-transact-sql) ステートメントを使用します。 例については、[ALTER ROLE の例](/sql/t-sql/statements/alter-role-transact-sql#examples)を参照してください
    - Azure Synapse では、[sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ステートメントを使用します。 例については、[sp_addrolemember の例](/sql/t-sql/statements/alter-role-transact-sql)を参照してください。

- **カスタム データベース ロール**

  [CREATE ROLE](/sql/t-sql/statements/create-role-transact-sql) ステートメントを使用して、カスタム データベース ロールを作成します。 カスタム ロールを利用すると、独自のユーザー定義データベース ロールを作成し、各ロールに対して、ビジネス ニーズに応じて必要とされる最小限のアクセス許可を慎重に付与することができます。 その後、カスタム ロールにユーザーを追加できます。 ユーザーが複数のロールのメンバーである場合は、それらのアクセス許可すべてが集約されます。
- **アクセス許可を直接付与する**

  ユーザー アカウントに[アクセス許可](/sql/relational-databases/security/permissions-database-engine)を直接付与します。 SQL Database では、個別に許可または拒否できるアクセス許可が 100 個を超えています。 これらのアクセス許可の多くは、入れ子になっています。 たとえば、スキーマに対する `UPDATE` アクセス許可には、そのスキーマ内の各テーブルに対する `UPDATE` アクセス許可が含まれています。 ほとんどのアクセス許可システムと同様に、アクセス許可の拒否は許可をオーバーライドします。 入れ子になっている性質と、アクセス許可の数により、データベースを正しく保護するのに適切なアクセス許可システムを設計するには、慎重な調査を行う場合があります。 まず「[権限 (データベース エンジン)](/sql/relational-databases/security/permissions-database-engine)」でアクセス許可の一覧を確認してから、アクセス許可の[ポスター サイズの図](/sql/relational-databases/security/media/database-engine-permissions.png)も確認してください。

## <a name="using-groups"></a>グループの使用

効率的なアクセス管理では、個々のユーザーに対してではなく、Active Directory セキュリティ グループや、固定ロールまたはカスタム ロールに割り当てられたアクセス許可を使用します。

- Azure Active Directory 認証を使用する場合は、Azure Active Directory ユーザーを Azure Active Directory セキュリティ グループに所属させます。 そのグループ用に包含データベース ユーザーを作成します。 1 人以上のデータベース ユーザーを、そのユーザー グループにとって適切な特定のアクセス許可を持つカスタムまたは組み込みのデータベース ロールにメンバーとして追加します。

- SQL 認証の使用時は、データベース内に包含データベース ユーザーを作成します。 1 人以上のデータベース ユーザーを、そのユーザー グループにとって適切な特定のアクセス許可を持つカスタム データベース ロールに配置します。

  > [!NOTE]
  > 非包含データベース ユーザーにはグループを使用することもできます。

アクセス許可を制限したり昇格させたりするために使用できる次の機能について理解を深める必要があります。

- [権限借用](/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server)と[モジュール署名](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)を使用すると、安全にアクセス許可を一時的に昇格できます。
- [Row-Level Security](/sql/relational-databases/security/row-level-security) を使用すると、ユーザーがアクセスできる行を制限できます。
- [データのマスキング](dynamic-data-masking-overview.md) を使用すると、機密データの公開を制限できます。
- [ストアド プロシージャ](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) を使用すると、データベースで実行できるアクションを制限できます。

## <a name="next-steps"></a>次のステップ

Azure SQL Database と SQL Managed Instance のすべてのセキュリティ機能の概要については、[セキュリティの概要](security-overview.md)に関するページを参照してください。