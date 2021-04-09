---
title: マネージド ID を使用して Azure SQL Database または Azure Synapse Analytics にアクセスする - Azure Stream Analytics
description: この記事では、マネージド ID を使用して、Azure SQL Database または Azure Synapse Analytics の出力に対して Azure Stream Analytics ジョブの認証を行う方法について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: e491c421f4af256b2e74fa61eb442d269bdb9e34
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102487918"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>マネージド ID を使用して Azure Stream Analytics ジョブから Azure SQL Database または Azure Synapse Analytics にアクセスする (プレビュー)

Azure Stream Analytics は、Azure SQL Database および Azure Synapse Analytics の出力シンクに対して[マネージド ID 認証](../active-directory/managed-identities-azure-resources/overview.md)をサポートしています。 マネージド ID を使用すると、パスワードの変更や 90 日ごとに発生するユーザー トークンの有効期限切れによる再認証の必要性など、ユーザー ベースの認証方法に伴う制限がなくなります。 手動による認証の必要がなくなると、Stream Analytics のデプロイを完全に自動化できます。

マネージド ID は、特定の Stream Analytics ジョブを表す、Azure Active Directory に登録済みのマネージド アプリケーションです。 マネージド アプリケーションは、対象のリソースに対する認証を行うために使用されます。 この記事では、Azure portal を使用して、Stream Analytics ジョブの Azure SQL Database または Azure Synapse Analytics の出力に対してマネージド ID を有効にする方法を示します。

## <a name="overview"></a>概要

この記事では、マネージド ID 認証モードを使用して Stream Analytics ジョブを Azure SQL Database または Azure Synapse Analytics SQL プールに接続するために必要な手順について説明します。 

- まず、Stream Analytics ジョブに対するシステム割り当てのマネージド ID を作成します。 これは、Azure Active Directory のジョブの ID です。  

- SQL サーバーまたは Synapse ワークスペースに Active Directory 管理者を追加します。これにより、そのリソースに対して Azure AD (マネージド ID) 認証が有効になります。

- 次に、データベース内の Stream Analytics ジョブの ID を表す包含ユーザーを作成します。 Stream Analytics ジョブは、SQL DB または Synapse SQL DB リソースと対話するときは常に、この ID を参照して、Stream Analytics ジョブにどのようなアクセス許可があるかを確認します。

- SQL Database または Synapse SQL プールにアクセスするための許可を Stream Analytics ジョブに付与します。

- 最後に、Azure SQL Database または Azure Synapse Analytics を出力として Stream Analytics ジョブに追加します。

## <a name="prerequisites"></a>前提条件

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

この機能を使用するには、次のものが必要です。

- Azure Stream Analytics ジョブ。

- Azure SQL Database リソース。

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

この機能を使用するには、次のものが必要です。

- Azure Stream Analytics ジョブ。

- Azure Synapse Analytics SQL プール。

- [Stream Analytics ジョブに対して構成されている](azure-synapse-analytics-output.md) Azure Storage アカウント。

- 注: Synapse SQL MSI と統合された Stream Analytics アカウント ストレージ MSI は現在使用できません。

---

## <a name="create-a-managed-identity"></a>マネージド ID の作成

まず、Azure Stream Analytics ジョブに対するマネージド ID を作成します。

1. [Azure portal](https://portal.azure.com) で、Azure Stream Analytics ジョブを開きます。

1. 左側のナビゲーション メニューから、 **[構成]** の下にある **[マネージド ID]** を選択します。 次に、 **[システム割り当てマネージド ID を使用]** のチェック ボックスをオンにして、 **[保存]** を選択します。

   ![システム割り当てマネージド ID を選択する](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)

   Azure Active Directory に Stream Analytics ジョブの ID 用のサービス プリンシパルが作成されます。 新しく作成された ID のライフ サイクルは、Azure によって管理されます。 Stream Analytics ジョブが削除されると、関連付けられた ID (つまりサービス プリンシパル) も Azure によって自動的に削除されます。

1. 構成を保存すると、サービス プリンシパルのオブジェクト ID (OID) が、次に示すようにプリンシパル ID として表示されます。 

   ![プリンシパル ID として表示されたオブジェクト ID](./media/sql-db-output-managed-identity/principal-id.png)

   サービス プリンシパルは、Stream Analytics ジョブと同じ名前を持ちます。 たとえば、ジョブの名前が *MyASAJob* であれば、サービス プリンシパルの名前も *MyASAJob* になります。

## <a name="select-an-active-directory-admin"></a>Active Directory 管理者を選択する

マネージド ID を作成した後は、Active Directory 管理者を選択します。

1. Azure SQL Database または Azure Synapse Analytics SQL プール リソースに移動し、リソースがある SQL Server または Synapse ワークスペースをそれぞれ選択します。 これらへのリンクは、リソースの概要ページの *[サーバー名]* または *[ワークスペース名]* の横にあります。

1. SQL Server と Synapse ワークスペースについてそれぞれ、 **[設定]** の下にある **[Active Directory 管理者]** または **[SQL Active Directory 管理者]** を選択します。 次に、 **[管理者の設定]** を選択します。

   ![Active Directory 管理者ページ](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. Active Directory 管理者ページで、SQL Server の管理者にするユーザーまたはグループを探して、 **[選択]** をクリックします。 これは、次のセクションで **包含データベース ユーザー** を作成することができるユーザーです。

   ![Active Directory 管理者を追加する](./media/sql-db-output-managed-identity/add-admin.png)

   [Active Directory 管理者] ページには、Active Directory のメンバーとグループがすべて表示されます。 淡色表示されているユーザーまたはグループは、Azure Active Directory 管理者としてサポートされていないため、選択できません。 「 [Azure Active Directory 認証を使用して SQL Database または Azure Synapse を認証する](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations)」の「 **Azure Active Directory の機能と制限事項** 」セクションでサポートされている管理者の一覧を参照してください。

1. **[Active Directory 管理者]** ページの **[保存]** を選択します。 管理者を変更するプロセスには数分かかります。

## <a name="create-a-contained-database-user"></a>包含データベース ユーザーを作成する

次に、Azure Active Directory ID にマップされる Azure SQL または Azure Synapse データベースの包含データベース ユーザーを作成します。 包含データベース ユーザーは、プライマリ データベースに対するログインは持っていませんが、データベースに関連付けられているディレクトリ内の ID にマップされます。 Azure Active Directory の ID は、個々のユーザー アカウントでもグループ アカウントでもかまいません。 この場合は、Stream Analytics ジョブに対する包含データベース ユーザーを作成する必要があります。 

詳細については、[SQL Database と Azure Synapse Analytics を使用したユニバーサル認証 (SSMS での MFA のサポート)](../azure-sql/database/authentication-mfa-ssms-overview.md) に関する記事で Azure AD 統合の概要についてご確認ください。

1. SQL Server Management Studio を使用して Azure SQL または Azure Synapse データベースに接続します。 **[ユーザー名]** は、**ALTER ANY USER** アクセス許可を持っている Azure Active Directory ユーザーです。 たとえば、SQL Server で設定した管理者です。 **[Azure Active Directory - MFA で汎用]** 認証を使用します。 

   ![SQL Server への接続](./media/sql-db-output-managed-identity/connect-sql-server.png)

   サーバー名 `<SQL Server name>.database.windows.net` は、リージョンによって異なる場合があります。 たとえば、中国リージョンでは `<SQL Server name>.database.chinacloudapi.cn` を使用する必要があります。
 
   **[オプション] > [接続プロパティ] > [データベースへの接続]** に移動することで、特定の Azure SQL または Azure Synapse データベースを指定できます。  

   ![SQL Server の接続プロパティ](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. 初めて接続するときは、次のようなウィンドウが表示される場合があります。

   ![新しいファイアウォール規則ウィンドウ](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. その場合は、Azure portal で SQL Server または Synapse ワークスペース リソースに移動します。 **[セキュリティ]** セクションで、 **[ファイアウォールと仮想ネットワーク] / [ファイアウォール]** ページを開きます。 
   1. 任意の規則名で新しい規則を追加します。
   1. "*開始 IP*" には **[新しいファイアウォール規則]** ウィンドウの "*開始*" IP アドレスを使用します。
   1. "*終了 IP*" には **[新しいファイアウォール規則]** ウィンドウの "*終了*" IP アドレスを使用します。 
   1. **[保存]** を選択し、SQL Server Management Studio から再び接続を試みます。 

1. 接続したら、包含データベース ユーザーを作成します。 次の SQL コマンドを使用して、Stream Analytics ジョブと同じ名前の包含データベース ユーザーを作成します。 *ASA_JOB_NAME* の前後を角かっこで必ず囲みます。 次の T-SQL 構文を使用して、クエリを実行します。 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```
   
    包含データベース ユーザーが正しく追加されたかどうかを確認するには、関連するデータベースにおいて SSMS で次のコマンドを実行して、 *ASA_JOB_NAME* が [名前] 列の下にあるかどうかを確認します。

   ```sql
   SELECT * FROM <SQL_DB_NAME>.sys.database_principals 
   WHERE type_desc = 'EXTERNAL_USER' 
   ```

1. Stream Analytics ジョブに SQL Database へのアクセス権があるかどうかを Microsoft の Azure Active Directory で確認するには、データベースと通信するためのアクセス許可を Azure Active Directory に付与する必要があります。 これを行うには、Azure portal の [ファイアウォールと仮想ネットワーク] / [ファイアウォール] ページに再び移動し、[Azure サービスおよびリソースにこのサーバー/ワークスペースへのアクセスを許可する] を有効にします。

   ![ファイアウォールと仮想ネットワーク](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Stream Analytics ジョブにアクセス許可を付与する

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

前のセクションで説明したように、包含データベース ユーザーを作成し、ポータルで Azure サービスへのアクセス許可を付与すると、Stream Analytics ジョブには、マネージド ID を介して Azure SQL データベース リソースに **接続する** ためのマネージド ID からのアクセス許可が付与されます。 **SELECT** および **INSERT** のアクセス許可を Stream Analytics ジョブに付与することをお勧めします。これらは後で Stream Analytics ワークフローで必要になるためです。 **SELECT** のアクセス許可により、ジョブは Azure SQL データベース内のテーブルへの接続をテストできます。 **INSERT** のアクセス許可により、入力と Azure SQL データベースの出力を構成した後に、エンドツーエンドの Stream Analytics クエリをテストできます。

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

前のセクションで説明したように、包含データベース ユーザーを作成し、ポータルで Azure サービスへのアクセス許可を付与すると、Stream Analytics ジョブには、マネージド ID を介して Azure Synapse データベース リソースに **接続する** ためのマネージド ID からのアクセス許可が付与されます。 **SELECT**、**INSERT**、および **ADMINISTER DATABASE BULK OPERATIONS** のアクセス許可を Stream Analytics ジョブにさらに付与することをお勧めします。これらは後で Stream Analytics ワークフローで必要になるためです。 **SELECT** のアクセス許可により、ジョブは Azure Synapse データベース内のテーブルへの接続をテストできます。 **INSERT** と **ADMINISTER DATABASE BULK OPERATIONS** のアクセス許可により、入力と Azure Synapse データベースの出力を構成した後に、エンドツーエンドの Stream Analytics クエリをテストできます。

**ADMINISTER DATABASE BULK OPERATIONS** アクセス許可を付与するには、[[権限が含まれるデータベース権限]](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest&preserve-view=true#remarks) で **CONTROL** いうラベルが付けられているすべてのアクセス許可を Stream Analytics ジョブに付与する必要があります。 Stream Analytics ジョブによって、[ADMINISTER DATABASE BULK OPERATIONS と INSERT](/sql/t-sql/statements/copy-into-transact-sql) を必要とする **COPY** ステートメントが実行されるため、このアクセス許可が必要になります。

---

SQL Server Management Studio を使用して、それらのアクセス許可を Stream Analytics ジョブに付与できます。 詳細については、「GRANT (Transact-SQL)」のリファレンスを参照してください。

データベース内の特定のテーブルまたはオブジェクトに対するアクセス許可のみを付与するには、次の T-SQL 構文を使用してクエリを実行します。 

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

```sql
GRANT CONNECT, SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT CONNECT, SELECT, INSERT, CONTROL, ADMINISTER DATABASE BULK OPERATIONS OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

または、SQL Server Management Studio でお使いの Azure SQL または Azure Synapse データベースを右クリックし、 **[プロパティ] > [アクセス許可]** を選択します。 アクセス許可のメニューから、前に追加した Stream Analytics ジョブを確認でき、必要に応じて、手動でアクセス許可を付与または拒否することができます。

*ASA_JOB_NAME* ユーザーに追加したすべてのアクセス許可を確認するには、関連する DB において SSMS で次のコマンドを実行します。 

```sql
SELECT dbprin.name, dbprin.type_desc, dbperm.permission_name, dbperm.state_desc, dbperm.class_desc, object_name(dbperm.major_id) 
FROM sys.database_principals dbprin 
LEFT JOIN sys.database_permissions dbperm 
ON dbperm.grantee_principal_id = dbprin.principal_id 
WHERE dbprin.name = '<ASA_JOB_NAME>' 
```

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>Azure SQL Database または Azure Synapse の出力を作成する

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

マネージド ID を構成したので、Stream Analytics ジョブに Azure SQL Database または Azure Synapse の出力を追加できます。

適切な出力スキーマを使用して SQL Database にテーブルが作成されていることを確認します。 このテーブルの名前は、SQL Database 出力を Stream Analytics ジョブに追加するときに入力する必要がある必須プロパティの 1 つです。 また、接続をテストして Stream Analytics クエリを実行するための **SELECT** および **INSERT** のアクセス許可がジョブにあることを確認します。 まだ行っていない場合は、「[Stream Analytics ジョブにアクセス許可を付与する](#grant-stream-analytics-job-permissions)」セクションを参照してください。 

1. Stream Analytics ジョブに戻り、 **[ジョブ トポロジ]** の下にある **[出力]** ページに移動します。 

1. **[追加] > [SQL Database]** を選択します。 SQL Database 出力シンクの出力プロパティ ウィンドウで、[認証モード] ドロップダウンから **[マネージド ID]** を選択します。

1. 残りのプロパティを入力します。 SQL Database 出力の作成の詳細については、[Stream Analytics での SQL Database 出力の作成](sql-database-output.md)に関する記事を参照してください。 終わったら、 **[保存]** を選択します。

1. **[保存]** をクリックすると、リソースへの接続テストが自動的にトリガーされます。 正常に完了したら、マネージド ID 認証モードを使用して Azure SQL Database または Synapse SQL Database に接続するように Stream Analytics ジョブが正常に構成されています。 

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

マネージド ID およびストレージ アカウントを構成したので、Stream Analytics ジョブに Azure SQL Database または Azure Synapse の出力を追加できます。

適切な出力スキーマを使用して Azure Synapse データベースでテーブルを作成したことを確認します。 このテーブルの名前は、Azure Synapse 出力を Stream Analytics ジョブに追加するときに入力する必要がある必須プロパティの 1 つです。 また、接続をテストして Stream Analytics クエリを実行するための **SELECT** および **INSERT** のアクセス許可がジョブにあることを確認します。 まだ行っていない場合は、「[Stream Analytics ジョブにアクセス許可を付与する](#grant-stream-analytics-job-permissions)」セクションを参照してください。

1. Stream Analytics ジョブに戻り、 **[ジョブ トポロジ]** の下にある **[出力]** ページに移動します。

1. **[追加] > [Azure Synapse Analytics]** を選択します。 SQL Database 出力シンクの出力プロパティ ウィンドウで、[認証モード] ドロップダウンから **[マネージド ID]** を選択します。

1. 残りのプロパティを入力します。 Azure Synapse 出力の作成の詳細については、「[Azure Stream Analytics からの Azure Synapse Analytics 出力](azure-synapse-analytics-output.md)」を参照してください。 終わったら、 **[保存]** を選択します。

1. **[保存]** をクリックすると、リソースへの接続テストが自動的にトリガーされます。 正常に完了すると、Stream Analytics で Azure Synapse Analytics リソースに対してマネージド ID を使用する準備が整っています。 

---

## <a name="remove-managed-identity"></a>マネージド ID を削除する

Stream Analytics ジョブに対して作成されたマネージド ID は、ジョブが削除されたときにのみ削除されます。 ジョブを削除せずにマネージド ID を削除することはできません。 マネージド ID を使用する必要がなくなった場合は、出力の認証方法を変更できます。 マネージド ID は、ジョブが削除されるまで存在し続け、マネージド ID の認証を再度使用する場合に使用されます。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics からの出力を理解する](stream-analytics-define-outputs.md)
* [Azure SQL Database への Azure Stream Analytics の出力](stream-analytics-sql-output-perf.md)
* [Azure Stream Analytics からの Azure Synapse Analytics 出力](azure-synapse-analytics-output.md)
