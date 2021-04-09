---
title: データベースの保護
description: このチュートリアルでは、シングル データベースか、プールされたデータベースかを問わず、Azure SQL Database をセキュリティで保護するための手法と機能について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 09/21/2020
ms.custom: seoapril2019 sqldbrb=1
ms.openlocfilehash: 8cdf95dd3c0e801896328136b15e4bd4efe53005
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563814"
---
# <a name="tutorial-secure-a-database-in-azure-sql-database"></a>チュートリアル:Azure SQL Database 内のデータベースをセキュリティで保護する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> - サーバーレベルとデータベースレベルのファイアウォール規則を作成する
> - Azure Active Directory (Azure AD) 管理者を構成する
> - SQL 認証、Azure AD 認証、セキュリティで保護された接続文字列を使用して、ユーザー アクセスを管理する
> - Azure Defender for SQLy、監査、データ マスキング、暗号化などのセキュリティ機能を有効にする

Azure SQL Database は、次のようにしてデータをセキュリティで保護します。

- ファイアウォール規則を使用したアクセス制限
- ID の入力を求める認証メカニズムの使用
- ロールベースのメンバーシップとアクセス許可による認可の使用
- セキュリティ機能の有効化

> [!NOTE]
> Azure SQL Managed Instance は、[Azure SQL Database Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) と[接続アーキテクチャ](../managed-instance/connectivity-architecture-overview.md)に関するページに記載されているように、ネットワーク セキュリティ規則とプライベート エンドポイントを使用してセキュリティで保護されます。

詳細については、[Azure SQL Database のセキュリティの概要](./security-overview.md)と[機能](security-overview.md)に関する記事を参照してください。

> [!TIP]
> 次の Microsoft Learn モジュールは、[Azure SQL Database でデータベースをセキュリティ保護する](/learn/modules/secure-your-azure-sql-database/)方法を無料で学習するのに役立ちます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、前提条件として次のものが必要です。

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- [サーバー](logical-servers.md)と単一データベース
  - [Azure portal](single-database-create-quickstart.md)、[CLI](az-cli-script-samples-content-guide.md)、または [PowerShell](powershell-script-content-guide.md) を使用して作成します

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

このチュートリアルのすべての手順は、[Azure portal](https://portal.azure.com/) にサインインして行います

## <a name="create-firewall-rules"></a>ファイアウォール規則を作成する

SQL Database とデータベースは、Azure 内のファイアウォールによって保護されます。 既定では、サーバーとデータベースに対する接続がすべて拒否されます。 詳細については、[サーバーレベルとデータベースレベルのファイアウォール規則](firewall-configure.md)に関するページを参照してください。

最も安全な構成は、 **[Azure サービスへのアクセスを許可]** を **[オフ]** にしておくことです。 次に、Azure VM やクラウド サービスなど、接続する必要があるリソース用に[予約済み IP (クラシック デプロイ)](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) を作成し、その IP アドレスだけにファイアウォール経由のアクセスを許可します。 [Resource Manager](../../virtual-network/public-ip-addresses.md) デプロイ モデルを使用している場合は、リソースごとに専用のパブリック IP アドレスが必要です。

> [!NOTE]
> SQL Database の通信は、ポート 1433 上で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、管理者によってポート 1433 が開放されない限り、サーバーに接続することはできません。

### <a name="set-up-server-level-firewall-rules"></a>サーバーレベルのファイアウォール規則を設定する

サーバーレベルの IP ファイアウォール規則は、同じサーバー内のすべてのデータベースに適用されます。

サーバーレベルのファイアウォール規則を設定する手順は次のとおりです。

1. Azure portal で、左側のメニューから **[SQL データベース]** を選択し、 **[SQL データベース]** ページで目的のデータベースをクリックします。

    ![サーバーのファイアウォール規則](./media/secure-database-tutorial/server-name.png)

    > [!NOTE]
    > チュートリアルの後の手順で使用するために、完全修飾サーバー名 (*yourserver.database.windows.net* など) をコピーしておいてください。

1. **[概要]** ページで、 **[サーバー ファイアウォールの設定]** を選択します。 サーバーの **[ファイアウォール設定]** ページが開きます。

   1. ツール バーの **[クライアント IP の追加]** を選択し、現在の IP アドレスを新しいファイアウォール規則に追加します。 この規則は、単一の IP アドレスまたは一定範囲の IP アドレスに対して、ポート 1433 を開くことができます。 **[保存]** を選択します。

      ![サーバーのファイアウォール規則の設定](./media/secure-database-tutorial/server-firewall-rule2.png)

   1. **[OK]** を選択し、 **[ファイアウォール設定]** ページを閉じます。

これで、指定した IP アドレスまたは IP アドレスの範囲を使用して、サーバー内の任意のデータベースに接続できるようになりました。

### <a name="setup-database-firewall-rules"></a>データベースのファイアウォール規則の設定

データベースレベルのファイアウォール規則は、個々のデータベースのみに適用されます。 サーバーのフェールオーバーの間、これらの規則はデータベースに保持されます。 データベースレベルのファイアウォール規則は、サーバーレベルのファイアウォール規則を構成した後にのみ、Transact-SQL (T-SQL) ステートメントを使用して構成することができます。

データベースレベルのファイアウォール規則を設定するには:

1. [SQL Server Management Studio](connect-query-ssms.md) などを使用して、データベースに接続します。

1. **オブジェクト エクスプローラー** でデータベースを右クリックし、 **[新しいクエリ]** を選択します。

1. クエリ ウィンドウで次のステートメントを追加し、その IP アドレスをお使いのパブリック IP アドレスに変更します。

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. ツール バー上で **[実行]** を選択して、ファイアウォール規則を作成します。

> [!NOTE]
> [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current) コマンドを使用して、SSMS でサーバーレベルのファイアウォール規則を作成することもできます。ただし、*master* データベースに接続する必要があります。

## <a name="create-an-azure-ad-admin"></a>Azure AD 管理者を作成する

適切な Azure Active Directory (AD) マネージド ドメインを使用していることを確認します。 AD ドメインを選択するには、Azure portal の右上隅を使用します。 このプロセスでは、データベースまたはデータ ウェアハウスをホストしている Azure AD と論理 SQL サーバーの両方に同じサブスクリプションが使用されていることを確認します。

   ![choose-ad](./media/secure-database-tutorial/8choose-ad.png)

Azure AD 管理者を設定する手順は次のとおりです。

1. Azure portal の **[SQL Server]** ページで、 **[Active Directory 管理者]** を選択します。次に、 **[管理者の設定]** を選択します。

    ![Active Directory を選択する](./media/secure-database-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > このタスクを実行するには、"グローバル管理者" である必要があります。

1. **[管理者の追加]** ページで、AD ユーザーまたはグループを探して、 **[選択]** を選択します。 Active Directory のすべてのメンバーとグループが一覧表示されます。グレー表示されているエントリは、Azure AD 管理者としてサポートされていません。 「[Azure AD の機能と制限事項](authentication-aad-overview.md#azure-ad-features-and-limitations)」を参照してください。

    ![管理者の選択](./media/secure-database-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Azure ロールベースのアクセス制御 (Azure RBAC) はポータルにのみ適用され、SQL Server には反映されません。

1. **[Active Directory 管理者]** ページの上部にある **[保存]** を選択します。

    管理者を変更する処理には数分かかる場合があります。 新しい管理者が **[Active Directory 管理者]** ボックスに表示されます。

> [!NOTE]
> Azure AD 管理者を設定する場合、新しい管理者名 (ユーザーまたはグループ) が SQL Server ログインまたはユーザーとして "*master*" データベースに存在していてはなりません。 存在していると、セットアップが失敗して変更がロールバックされ、その管理者名が既に存在していることが示されます。 SQL Server ログインまたはユーザーは Azure AD に属していないため、Azure AD 認証を使用してこのユーザーに接続しようとしても失敗します。

Azure AD の構成の詳細については、次のページを参照してください。

- [オンプレミス ID と Azure AD の統合](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Azure AD への独自のドメイン名の追加](../../active-directory/fundamentals/add-custom-domain.md)
- [Microsoft Azure での Windows Server AD とのフェデレーションのサポート](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Azure AD ディレクトリの管理](../../active-directory/fundamentals/active-directory-whatis.md)
- [PowerShell による Azure AD の管理](/powershell/azure/)
- [ハイブリッド ID で必要なポートとプロトコル](../../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>データベース アクセスを管理する

データベースにユーザーを追加するか、セキュリティで保護された接続文字列でのユーザー アクセスを許可することにより、データベース アクセスを管理します。 接続文字列は、外部アプリケーションの場合に便利です。 詳細については、[ログインとユーザー アカウントの管理](logins-create-manage.md)に関するページと [AD 認証](authentication-aad-overview.md)に関するページを参照してください。

ユーザーを追加するには、データベース認証の種類を選択します。

- **SQL 認証**: ログインにユーザー名とパスワードを使用します。サーバー内の特定のデータベースのコンテキストでのみ有効です。

- **Azure AD 認証**: Azure AD によって管理される ID を使用します。

### <a name="sql-authentication"></a>SQL 認証

SQL 認証を使用するユーザーを追加する手順は次のとおりです。

1. [SQL Server Management Studio](connect-query-ssms.md) などを使用して、データベースに接続します。

1. **オブジェクト エクスプローラー** でデータベースを右クリックし、 **[新しいクエリ]** を選択します。

1. クエリ ウィンドウで、次のコマンドを入力します。

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. ツール バーの **[実行]** を選択して、ユーザーを作成します。

1. 既定では、ユーザーはデータベースに接続できますが、データを読み取ったり書き込んだりするアクセス許可は付与されていません。 これらのアクセス許可を付与するには、新しいクエリ ウィンドウで次のコマンドを実行します。

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> 新規ユーザーの作成などの管理者タスクを実行する必要がない場合は、データベース レベルで非管理者アカウントを作成します。

### <a name="azure-ad-authentication"></a>Azure AD 認証

Azure Active Directory 認証では、データベース ユーザーを包含データベース ユーザーとして作成する必要があります。 包含データベース ユーザーは、データベースに関連付けられている Azure AD ディレクトリの ID にマップされていて、*master* データベース内にログイン情報がありません。 Azure AD の ID は、個々のユーザーまたはグループに対応しています。 詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](/sql/relational-databases/security/contained-database-users-making-your-database-portable)」と、Azure AD を使用して認証する方法に関する [Azure AD チュートリアル](authentication-aad-configure.md)を参照してください。

> [!NOTE]
> Azure portal を使用してデータベース ユーザー (管理者を除く) を作成することはできません。 Azure のロールは、SQL サーバー、データベース、データ ウェアハウスには反映されません。 これらは Azure リソースの管理のみに使用され、データベースのアクセス許可には適用されません。
>
> たとえば、"*SQL Server 共同作成者*" ロールでは、データベースやデータ ウェアハウスに接続するためのアクセス権は付与されません。 このアクセス許可は、T-SQL ステートメントを使用して、データベース内で付与する必要があります。

> [!IMPORTANT]
> コロン `:` やアンパサンド `&` などの特殊文字は、T-SQL の `CREATE LOGIN` ステートメントと `CREATE USER` ステートメントのユーザー名ではサポートされていません。

Azure AD 認証でユーザーを追加する手順は次のとおりです。

1. 少なくとも *ALTER ANY USER* アクセス許可を持つ Azure AD アカウントを使用して、Azure のサーバーに接続します。

1. **オブジェクト エクスプローラー** でデータベースを右クリックし、 **[新しいクエリ]** を選択します。

1. クエリ ウィンドウで、次のコマンドを入力し、`<Azure_AD_principal_name>` を Azure AD ユーザーのプリンシパル名または Azure AD グループの表示名に変更します。

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> データベースのメタデータでは、Azure AD ユーザーはタイプ `E (EXTERNAL_USER)`、Azure AD グループはタイプ `X (EXTERNAL_GROUPS)` でマークされます。 詳細については、「[sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql)」を参照してください。

### <a name="secure-connection-strings"></a>セキュリティで保護された接続文字列

クライアント アプリケーションと SQL データベースの間に暗号化された安全な接続を確立するには、接続文字列を次のように構成する必要があります。

- 暗号化接続を要求する
- サーバー証明書を信頼しない

トランスポート層セキュリティ (TLS) を使用して接続が確立され、中間者攻撃のリスクが軽減されます。 接続文字列はデータベースごとに利用可能で、ADO.NET、JDBC、ODBC、PHP などのクライアント ドライバーをサポートするように事前構成されます。 TLS と接続の詳細については、[TLS に関する考慮事項](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)に関するセクションを参照してください。

セキュリティで保護された接続文字列をコピーする手順は次のとおりです。

1. Azure portal で、左側のメニューから **[SQL データベース]** を選択し、 **[SQL データベース]** ページで目的のデータベースをクリックします。

1. **[概要]** ページで、 **[データベース接続文字列の表示]** を選択します。

1. [ドライバー] タブを選択し、完全な接続文字列をコピーします。

    ![ADO.NET の接続文字列](./media/secure-database-tutorial/connection.png)

## <a name="enable-security-features"></a>セキュリティ機能の有効化

Azure SQL Database は、Azure portal を使用してアクセスされるセキュリティ機能を提供します。 これらの機能は、データベースとサーバーの両方で利用可能です。ただし、データ マスクは例外で、データベースのみで利用可能です。 詳細については、[Azure Defender for SQL](azure-defender-for-sql.md)、[監査](../../azure-sql/database/auditing-overview.md)、[動的データ マスクキング](dynamic-data-masking-overview.md)、[透過的なデータ暗号化](transparent-data-encryption-tde-overview.md)に関するページを参照してください。

### <a name="azure-defender-for-sql"></a>Azure Defender for SQL

Azure Defender for SQL 機能は、潜在的な脅威の発生を検出し、異常なアクティビティがあればセキュリティのアラートを送信します。 ユーザーは、監査機能を使用して、こうした疑わしいイベントを調査し、そのイベントがデータベース内のデータへのアクセス、侵害、悪用かどうかを判断できます。 また、脆弱性評価を含めたセキュリティ概要や、データの検出と分類ツールも提供されます。

> [!NOTE]
> 脅威の一例として SQL インジェクションがあります。これは、攻撃者がアプリケーションの入力に悪意のある SQL を挿入するプロセスです。 これにより、アプリケーションが知らないうちに悪意のある SQL を実行し、攻撃者がデータベース内のデータにアクセスして侵害や変更を行えるようになります。

Azure Defender for SQL を有効にするには、次の手順に従います。

1. Azure portal で、左側のメニューから **[SQL データベース]** を選択し、 **[SQL データベース]** ページで目的のデータベースをクリックします。

1. **[概要]** ページで、 **[サーバー名]** リンクを選択します。 サーバーのページが開きます。

1. **[SQL サーバー]** ページで **[セキュリティ]** セクションを探し、 **[Security Center]** を選択します。

   1. この機能を有効にするには、 **[Azure Defender for SQL]** で **[オン]** を選択します。 脆弱性評価の結果を保存するためのストレージ アカウントを選択します。 次に、 **[保存]** を選択します。

      ![ナビゲーション ウィンドウ](./media/secure-database-tutorial/threat-settings.png)

      電子メールを構成して、セキュリティのアラート、ストレージの詳細、脅威検出の種類を受信することもできます。

1. 目的のデータベースの **[SQL データベース]** ページに戻り、 **[セキュリティ]** セクションの **[Security Center]** を選択します。 ここには、データベースのさまざまなセキュリティ インジケーターがあります。

    ![脅威の状態](./media/secure-database-tutorial/threat-status.png)

異常なアクティビティが検出されると、そのイベントの情報が電子メールで届きます。 これには、アクティビティの性質、データベース、サーバー、イベントの発生時点、考えられる原因、推奨アクションが含まれていて、潜在的な脅威を調査してその影響を軽減することができます。 このような電子メールを受信したら、 **[Azure SQL Auditing Log]\(Azure SQL 監査ログ\)** リンクを選択して Azure portal を起動し、そのイベントの時点に関連する監査レコードを確認します。

   ![脅威の検出の電子メール](./media/secure-database-tutorial/threat-email.png)

### <a name="auditing"></a>監査

監査機能は、データベース イベントを追跡し、Azure Storage の監査ログ、Azure Monitor ログ、またはイベント ハブのいずれかにイベントを書き込みます。 監査により、規制に対するコンプライアンスの維持、データベース アクティビティの把握、セキュリティ侵害の可能性がある差異や異常に対する分析が容易になります。

監査を有効にする手順は次のとおりです。

1. Azure portal で、左側のメニューから **[SQL データベース]** を選択し、 **[SQL データベース]** ページで目的のデータベースをクリックします。

1. **[セキュリティ]** セクションで、 **[監査]** を選択します。

1. **[監査]** の設定で、次の値を設定します。

   1. **[監査]** を **[オン]** に設定します。

   1. **[監査ログの保存先]** として、次のいずれかを選択します。

       - **[ストレージ]** : イベント ログが保存され、 *.xel* ファイルとしてダウンロードできる Azure ストレージ アカウント。

          > [!TIP]
          > 監査レポートのテンプレートを最大限活用するには、すべての監査済みデータベースに同じストレージ アカウントを使用してください。

       - **[Log Analytics]** : クエリや詳細な分析のために、イベントが自動的に保存されます。

           > [!NOTE]
           > 分析、カスタムのアラート ルール、Excel や Power BI のエクスポートなどの高度な機能をサポートするには、**Log Analytics ワークスペース** が必要です。 ワークスペースがない場合は、クエリ エディターのみを使用できます。

       - **[イベント ハブ]** : イベントをルーティングして、他のアプリケーションで使用できるようにします。

   1. **[保存]** を選択します。

      ![監査設定](./media/secure-database-tutorial/audit-settings.png)

1. これで、 **[監査ログの表示]** を選択してデータベース イベントのデータを表示できるようになりました。

    ![監査レコード](./media/secure-database-tutorial/audit-records.png)

> [!IMPORTANT]
> PowerShell や REST API を使用して監査イベントをさらにカスタマイズする方法については、[SQL Database の監査](../../azure-sql/database/auditing-overview.md)に関するページを参照してください。

### <a name="dynamic-data-masking"></a>動的データ マスク

データ マスク機能は、データベース内の機密データを自動的に隠します。

データ マスクを有効にする手順は次のとおりです。

1. Azure portal で、左側のメニューから **[SQL データベース]** を選択し、 **[SQL データベース]** ページで目的のデータベースをクリックします。

1. **[セキュリティ]** セクションで、 **[動的データ マスク]** を選択します。

1. **[動的データ マスク]** の設定で、 **[マスクの追加]** を選択してマスクの規則を追加します。 使用可能なデータベース スキーマ、テーブル、選択元の列は自動的に設定されます。

    ![マスクの設定](./media/secure-database-tutorial/mask-settings.png)

1. **[保存]** を選択します。 これで、選択した情報はプライバシー保護のためマスクされます。

    ![マスクの例](./media/secure-database-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>透過的なデータ暗号化

暗号化機能では、保存データが自動的に暗号化されます。暗号化されたデータベースにアクセスするアプリケーションの変更は必要ありません。 新しいデータベースの場合、暗号化は既定で有効になっています。 SSMS と [Always Encrypted](always-encrypted-certificate-store-configure.md) 機能を使用してデータを暗号化することもできます。

暗号化を有効化または確認する手順は次のとおりです。

1. Azure portal で、左側のメニューから **[SQL データベース]** を選択し、 **[SQL データベース]** ページで目的のデータベースをクリックします。

1. **[セキュリティ]** セクションで、 **[透過的なデータ暗号化]** を選択します。

1. 必要に応じて、 **[データの暗号化]** を **[オン]** に設定します。 **[保存]** を選択します。

    ![透過的なデータ暗号化](./media/secure-database-tutorial/encryption-settings.png)

> [!NOTE]
> 暗号化の状態を確認するには、[SSMS](connect-query-ssms.md) を使用してデータベースに接続し、[sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) ビューの `encryption_state` 列にクエリを実行します。 状態 `3` は、データベースが暗号化されていることを示します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、いくつかの簡単な手順を使ってデータベースのセキュリティを向上させる方法について説明しました。 以下の方法を学習しました。

> [!div class="checklist"]
>
> - サーバーレベルとデータベースレベルのファイアウォール規則を作成する
> - Azure Active Directory (AD) 管理者を構成する
> - SQL 認証、Azure AD 認証、セキュリティで保護された接続文字列を使用して、ユーザー アクセスを管理する
> - Azure Defender for SQLy、監査、データ マスキング、暗号化などのセキュリティ機能を有効にする

次のチュートリアルに進み、地理的分散の実装方法を学習してください。

> [!div class="nextstepaction"]
>[地理的に分散したデータベースの実装](geo-distributed-application-configure-tutorial.md)