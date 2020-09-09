---
title: マネージド ID を使用して Azure SQL Database にアクセスする - Azure Stream Analytics
description: この記事では、マネージド ID を使用して、Azure SQL DB の出力に対して Azure Stream Analytics ジョブの認証を行う方法について説明します。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 906311452598d592b73a263ce25d0c8c51cc1cc7
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870189"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>Azure Stream Analytics ジョブからマネージド ID を使用して Azure SQL Database にアクセスする (プレビュー)

Azure Stream Analytics では、Azure SQL Database 出力シンクに対する[マネージド ID 認証](../active-directory/managed-identities-azure-resources/overview.md)がサポートされています。 マネージド ID を使用すると、パスワードの変更や 90 日ごとに発生するユーザー トークンの有効期限切れによる再認証の必要性など、ユーザー ベースの認証方法に伴う制限がなくなります。 手動による認証の必要がなくなると、Stream Analytics のデプロイを完全に自動化できます。

マネージド ID は、特定の Stream Analytics ジョブを表す、Azure Active Directory に登録済みのマネージド アプリケーションです。 マネージド アプリケーションは、対象のリソースに対する認証を行うために使用されます。 この記事では、Azure portal を使用して、Stream Analytics ジョブの Azure SQL Database 出力に対してマネージド ID を有効にする方法を示します。

## <a name="prerequisites"></a>前提条件

この機能を使用するには、次のものが必要です。

- Azure Stream Analytics ジョブ。

- Azure SQL Database リソース。

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

1. Azure SQL Database リソースに移動し、データベースが存在している SQL Server を選択します。 リソース概要ページの *[サーバー名]* の横で SQL Server 名を確認できます。 

1. **[設定]** で **[Active Directory 管理者]** を選択します。 次に、 **[管理者の設定]** を選択します。 

   ![Active Directory 管理者ページ](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. Active Directory 管理者ページで、SQL Server の管理者にするユーザーまたはグループを探して、 **[選択]** をクリックします。

   ![Active Directory 管理者を追加する](./media/sql-db-output-managed-identity/add-admin.png)

   [Active Directory 管理者] ページには、Active Directory のメンバーとグループがすべて表示されます。 淡色表示されているユーザーまたはグループは、Azure AD 管理者としてサポートされていないため選択できません。 「 [Azure Active Directory 認証を使用して SQL Database または Azure Synapse を認証する](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)」の「 **Azure AD の機能と制限事項** 」セクションでサポートされている管理者の一覧を参照してください。 ロール ベースのアクセス制御 (RBAC) はポータルにのみ適用され、SQL Server には反映されません。 また、選択されたユーザーまたはグループは、次のセクションで**包含データベース ユーザー**を作成することができるユーザーです。

1. **[Active Directory 管理者]** ページの **[保存]** を選択します。 管理者を変更するプロセスには数分かかります。

   Azure AD 管理者をセットアップする場合、新しい管理者名 (ユーザーまたはグループ) が SQL Server 認証ユーザーとして仮想マスター データベースに存在していてはいけません。 存在する場合、Azure AD 管理者のセットアップは失敗し、その作成がロールバックされて、管理者 (名前) が既に存在していることが示されます。 SQL Server 認証ユーザーは Azure AD に属していないため、Azure AD 認証を使用してそのユーザーとしてサーバーに接続しようとしても失敗します。 

## <a name="create-a-contained-database-user"></a>包含データベース ユーザーを作成する

次に、Azure Active Directory ID にマップされる SQL Database の包含データベース ユーザーを作成します。 包含データベース ユーザーは、master データベースに対するログインは持っていませんが、データベースに関連付けられているディレクトリ内の ID にマップされています。 Azure Active Directory の ID は、個々のユーザー アカウントでもグループ アカウントでもかまいません。 この場合は、Stream Analytics ジョブに対する包含データベース ユーザーを作成する必要があります。 

1. SQL Server Management Studio を使用して SQL Database に接続します。 **[ユーザー名]** は、**ALTER ANY USER** アクセス許可を持っている Azure Active Directory ユーザーです。 たとえば、SQL Server で設定した管理者です。 **[Azure Active Directory - MFA で汎用]** 認証を使用します。 

   ![SQL Server への接続](./media/sql-db-output-managed-identity/connect-sql-server.png)

   サーバー名 `<SQL Server name>.database.windows.net` は、リージョンによって異なる場合があります。 たとえば、中国リージョンでは `<SQL Server name>.database.chinacloudapi.cn` を使用する必要があります。
 
   **[オプション] > [接続プロパティ] > [データベースへの接続]** に移動することで、特定の SQL Database を指定できます。  

   ![SQL Server の接続プロパティ](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. 初めて接続するときは、次のようなウィンドウが表示される場合があります。

   ![新しいファイアウォール規則ウィンドウ](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. その場合は、Azure portal で SQL Server リソースに移動します。 **[セキュリティ]** セクションで、 **[ファイアウォールと仮想ネットワーク]** ページを開きます。 
   1. 任意の規則名で新しい規則を追加します。
   1. "*開始 IP*" には **[新しいファイアウォール規則]** ウィンドウの "*開始*" IP アドレスを使用します。
   1. "*終了 IP*" には **[新しいファイアウォール規則]** ウィンドウの "*終了*" IP アドレスを使用します。 
   1. **[保存]** を選択し、SQL Server Management Studio から再び接続を試みます。 

1. 接続したら、包含データベース ユーザーを作成します。 次の SQL コマンドを使用して、Stream Analytics ジョブと同じ名前の包含データベース ユーザーを作成します。 *ASA_JOB_NAME* の前後を角かっこで必ず囲みます。 次の T-SQL 構文を使用して、クエリを実行します。 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

1. Stream Analytics ジョブに SQL Database へのアクセス権があるかどうかを Microsoft の Azure Active Directory で確認するには、データベースと通信するためのアクセス許可を Azure Active Directory に付与する必要があります。 これを行うには、Azure portal の [ファイアウォールと仮想ネットワーク] ページに再び移動し、[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する] を有効にします。 

   ![ファイアウォールと仮想ネットワーク](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Stream Analytics ジョブにアクセス許可を付与する

前のセクションで説明したように、包含データベース ユーザーを作成し、ポータルで Azure サービスへのアクセス許可を付与すると、Stream Analytics ジョブには、マネージド ID を介して SQL Database リソースに**接続する**ためのマネージド ID からのアクセス許可が付与されます。 SELECT および INSERT のアクセス許可を Stream Analytics ジョブに付与することをお勧めします。これらは後で Stream Analytics ワークフローで必要になるためです。 **SELECT** のアクセス許可により、ジョブは SQL Database 内のテーブルへの接続をテストできます。 **INSERT** のアクセス許可により、入力と SQL Database 出力を構成した後で、エンドツーエンドの Stream Analytics クエリをテストできます。これらのアクセス許可を Stream Analytics ジョブに付与するには、SQL Server Management Studio を使用します。 詳細については、「GRANT (Transact-SQL)」のリファレンスを参照してください。

データベース内の特定のテーブルまたはオブジェクトに対するアクセス許可のみを付与するには、次の T-SQL 構文を使用してクエリを実行します。 

```sql
GRANT SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME; 
```

または、SQL Server Management Studio でお使いの SQL データベースを右クリックし、 **[プロパティ] > [アクセス許可]** を選択します。 アクセス許可のメニューから、前に追加した Stream Analytics ジョブを確認でき、必要に応じて、手動でアクセス許可を付与または拒否することができます。

## <a name="create-an-azure-sql-database-output"></a>Azure SQL Database の出力を作成する

マネージド ID を構成したので、Stream Analytics ジョブに出力として Azure SQL Database を追加できます。

適切な出力スキーマを使用して SQL Database にテーブルが作成されていることを確認します。 このテーブルの名前は、SQL Database 出力を Stream Analytics ジョブに追加するときに入力する必要がある必須プロパティの 1 つです。 また、接続をテストして Stream Analytics クエリを実行するための **SELECT** および **INSERT** のアクセス許可がジョブにあることを確認します。 まだ行っていない場合は、「[Stream Analytics ジョブにアクセス許可を付与する](#grant-stream-analytics-job-permissions)」セクションを参照してください。 

1. Stream Analytics ジョブに戻り、 **[ジョブ トポロジ]** の下にある **[出力]** ページに移動します。 

1. **[追加] > [SQL Database]** を選択します。 SQL Database 出力シンクの出力プロパティ ウィンドウで、[認証モード] ドロップダウンから **[マネージド ID]** を選択します。

1. 残りのプロパティを入力します。 SQL Database 出力の作成の詳細については、[Stream Analytics での SQL Database 出力の作成](sql-database-output.md)に関する記事を参照してください。 終わったら、 **[保存]** を選択します。 

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics からの出力を理解する](stream-analytics-define-outputs.md)
* [Azure SQL Database への Azure Stream Analytics の出力](stream-analytics-sql-output-perf.md)
