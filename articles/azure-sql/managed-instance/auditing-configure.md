---
title: SQL Managed Instance の監査
description: T-SQL を使って Azure SQL Managed Instance の監査を始める方法について説明します
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: ae0d9696d869b2a260de643482a9f86c34bcc824
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575464"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>Azure SQL Database Managed Instance の監査の概要
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) の監査では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。 また、監査によって以下を行うことができます。

- 規定コンプライアンスの維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。
- コンプライアンスを保証するものではありませんが、標準へのコンプライアンスを強化します。 標準コンプライアンスをサポートする Azure プログラムの詳細については、[Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)をご覧ください。ここから最新のコンプライアンス証明書の一覧を入手できます。

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Azure Storage に対するサーバー監査の設定

以下のセクションでは、マネージド インスタンスの監査の構成について説明します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 監査ログが格納される Azure Storage **コンテナー** を作成します。

   1. 監査ログを格納する Azure ストレージ アカウントに移動します。

      > [!IMPORTANT]
      > - リージョンをまたいで読み取り/書き込みが行われないように、マネージド インスタンスと同じリージョンのストレージ アカウントを使います。 
      > - ストレージ アカウントが仮想ネットワークまたはファイアウォールの内側にある場合は、「[仮想ネットワークからアクセスの許可](../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)」をご覧ください。
      > - リテンション期間を 0 (無制限のリテンション期間) から他の値に変更した場合、リテンション期間は、リテンション期間の値が変更された後に書き込まれたログにのみ適用されることに注意してください (リテンション期間が無制限に設定されている間に書き込まれたログは、リテンション期間が有効になった後も保持されます)。

   1. ストレージ アカウントで **[概要]** に移動し、 **[BLOB]** をクリックします。

      ![Azure BLOB ウィジェット](./media/auditing-configure/1_blobs_widget.png)

   1. 上部のメニューで、 **[+ コンテナー]** をクリックして新しいコンテナーを作成します。

      ![BLOB コンテナーの作成アイコン](./media/auditing-configure/2_create_container_button.png)

   1. コンテナーの **[名前]** を指定し、 **[パブリック アクセス レベル]** を **[プライベート]** に設定して、 **[OK]** をクリックします。

      ![BLOB コンテナー構成の作成アイコン](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > サーバー レベルまたはデータベース レベルの監査イベントに対して不変のログ ストアを構成する顧客は、[Azure Storage で提供される手順](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes)に従う必要があります。 (不変 BLOB ストレージを構成するときに、 **[さらに追加を許可する]** を選択していることを確認してください)。
  
3. 監査ログ用のコンテナーを作成した後、それを監査ログ用のターゲットとして構成するには、[T-SQL を使用する](#blobtsql)方法と [SQL Server Management Studio (SSMS) UI を使用する](#blobssms)方法の 2 つがあります。

   - <a id="blobtsql"></a>**T-SQL を使用して監査ログ用の BLOB ストレージを構成する:**

     1. コンテナーの一覧で新しく作成されたコンテナーをクリックし、 **[コンテナーのプロパティ]** をクリックします。

        ![BLOB コンテナーのプロパティ ボタン](./media/auditing-configure/4_container_properties_button.png)

     1. コピー アイコンをクリックしてコンテナーの URL をコピーし、後で使えるように (メモ帳などに) URL を保存します。 コンテナー URL は、`https://<StorageName>.blob.core.windows.net/<ContainerName>` という形式になっている必要があります。

        ![BLOB コンテナーの URL をコピーする](./media/auditing-configure/5_container_copy_name.png)

     1. マネージド インスタンスの監査アクセス権をストレージ アカウントに付与するための Azure Storage の **SAS トークン** を生成します。

        - 前の手順でコンテナーを作成した Azure ストレージ アカウントに移動します。

        - **[ストレージの設定]** メニューの **[Shared Access Signature]** をクリックします。

          ![[ストレージの設定] メニューの [Shared Access Signature] アイコン](./media/auditing-configure/6_storage_settings_menu.png)

        - 次に示すように SAS を構成します。

          - **使用できるサービス**:BLOB

          - **開始日**: タイム ゾーンに関連する問題を回避するため、前日の日付を使用します

          - **終了日**: この SAS トークンの有効期限が切れる日付を選びます

            > [!NOTE]
            > 監査の失敗を避けるため、トークンの期限が切れたら更新します。

          - **[SAS の生成]** をクリックします。

            ![SAS の構成](./media/auditing-configure/7_sas_configure.png)

        - SAS トークンが下部に表示されます。 コピー アイコンをクリックしてトークンをコピーし、後で使えるように (メモ帳などに) 保存します。

          ![SAS トークンをコピーする](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > トークンの先頭にある疑問符 ("?") を削除します。

     1. SQL Server Management Studio またはサポートされるその他のツールを介してマネージド インスタンスに接続します。

     1. 次の T-SQL ステートメントを実行し、前の手順で作成したコンテナー URL と SAS トークンを使って、**新しい資格情報を作成** します。

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. 次の T-SQL ステートメントを実行し、新しいサーバー監査を作成します (独自の監査名を選び、前の手順で作成したコンテナー URL を使います)。 `RETENTION_DAYS` を指定しないと、既定値の 0 (無制限のリテンション期間) になります。

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

     1. [サーバー監査仕様またはデータベース監査仕様を作成](#createspec)して続行します。

   - <a id="blobssms"></a>**SQL Server Management Studio 18 を使用して監査ログの BLOB ストレージを構成する:**

     1. SQL Server Management Studio UI を使ってマネージド インスタンスに接続します。

     1. オブジェクト エクスプローラーのルート ノードを展開します。

     1. **[セキュリティ]** ノードを展開し、 **[監査]** ノードを右クリックして **[新しい監査]** をクリックします。

        ![[セキュリティ] ノードと [監査] ノードを展開する](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. **[監査の出力先]** で **[URL]** が選択されていることを確認し、 **[参照]** をクリックします。

        ![Azure Storage を参照する](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. (オプション) Azure アカウントにサインインします。

        ![Azure へのサインイン](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. ドロップダウンからサブスクリプション、ストレージ アカウント、および BLOB コンテナーを選択するか、 **[作成]** をクリックして独自のコンテナーを作成します。 完了したら、 **[OK]** をクリックします。

        ![Azure サブスクリプション、ストレージ アカウント、および BLOB コンテナーを選択する](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. **[監査の作成]** ダイアログで **[OK]** をクリックします。
     
     1. <a id="createspec"></a>BLOB コンテナーを監査ログのターゲットとして構成した後、SQL Server の場合と同様に、サーバー監査仕様またはデータベース監査仕様を作成して有効にします。

   - [サーバー監査仕様の作成 T-SQL ガイド](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [データベース監査仕様の作成 T-SQL ガイド](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. 手順 3 で作成したサーバー監査を有効にします。

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

以下の追加情報をご覧ください。

- [Azure SQL Managed Instance と SQL Server のデータベースでの監査の相違点](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>Event Hubs または Azure Monitor ログに対するサーバー監査の設定

マネージド インスタンスからの監査ログは、Azure Event Hubs または Azure Monitor ログに送信できます。 このセクションでは、この動作の構成方法について説明します。

1. [Azure portal](https://portal.azure.com/) 内でマネージド インスタンスに移動します。

2. **[診断設定]** をクリックします。

3. **[Turn on diagnostics]\(診断をオンにする\)** をクリックします。 診断が既に有効になっている場合は、 **[+Add diagnostic setting]\(+ 診断設定を追加する\)** が代わりに表示されます。

4. ログのリストで **[SQLSecurityAuditEvents]** を選択します。

5. 監査イベントの宛先を選択します (イベント ハブ、Azure Monitor ログ、またはその両方)。 必要なパラメーター (Log Analytics ワークスペースなど) をターゲットごとに構成します。

6. **[保存]** をクリックします。

    ![診断設定を構成する](./media/auditing-configure/9_mi_configure_diagnostics.png)

7. **SQL Server Management Studio (SSMS)** またはサポートされるその他のクライアントを使用して、マネージド インスタンスに接続します。

8. サーバー監査を作成するために次の T-SQL ステートメントを実行します。

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. SQL Server の場合と同様に、サーバー監査仕様またはデータベース監査仕様を作成して有効にします。

   - [サーバー監査仕様の作成 T-SQL ガイド](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [データベース監査仕様の作成 T-SQL ガイド](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. 手順 8 で作成したサーバー監査を有効にします。

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>監査ログの使用

### <a name="consume-logs-stored-in-azure-storage"></a>Azure Storage に格納されたログの使用

BLOB 監査ログを表示するには、いくつかの方法が使用できます。

- システム関数 `sys.fn_get_audit_file` (T-SQL) を使って、表形式で監査ログ データを返します。 この関数の使用方法の詳細については、[sys.fn_get_audit_file のドキュメント](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)を参照してください。

- [Azure ストレージ エクスプローラー](https://azure.microsoft.com/features/storage-explorer/)などのツールを使用して監査ログを調査できます。 Azure Storage では、監査ログは、監査ログを格納するために定義されたコンテナー内に BLOB ファイルのコレクションとして保存されます。 ストレージ フォルダーの階層、命名規則、およびログ形式の詳細については、[BLOB 監査ログ形式のリファレンス](../database/audit-log-format.md)を参照してください。

- 監査ログの使い方の完全な一覧については、[Azure SQL Database 監査の使用](../../azure-sql/database/auditing-overview.md)に関するページをご覧ください。

### <a name="consume-logs-stored-in-event-hubs"></a>Event Hubs に格納されているログの使用

Event Hubs の監査ログ データを使用するには、イベントを処理し、そのイベントをターゲットに書き込むようにストリームを設定する必要があります。 詳細については、Azure Event Hubs のドキュメントをご覧ください。

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Azure Monitor ログに格納されているログの使用および分析

監査ログが Azure Monitor ログに書き込まれると、それらの監査ログが Log Analytics ワークスペースで使用可能になります。Log Analytics ワークスペースでは、監査データに対して高度な検索を実行できます。 最初に、Log Analytics ワークスペースに移動します。 **[全般]** セクションで、 **[ログ]** をクリックし、単純なクエリ (例: `search "SQLSecurityAuditEvents"`) を入力して監査ログを表示します。  

Azure Monitor ログにより、統合された検索とカスタム ダッシュボードを使用してオペレーション インサイトがリアルタイムで得られるため、ワークロードやサーバー全体に散在する何百万件のレコードもすぐに分析できます。 Azure Monitor ログの検索言語とコマンドに関する有用な追加情報については、[Azure Monitor ログ検索リファレンス](../../azure-monitor/logs/log-query-overview.md)に関するページをご覧ください。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>Azure SQL Managed Instance のデータベースと SQL Server のデータベースでの監査の相違点

Azure SQL Managed Instance のデータベースと SQL Server のデータベースにおける監査の主な相違点は、次のとおりです。

- Azure SQL Managed Instance では、監査はサーバー レベルで機能し、Azure BLOB Storage に `.xel` ログ ファイルが保存されます。
- SQL Server では、監査はサーバー レベルで機能しますが、イベントはファイル システム/Windows イベント ログに保存されます。

マネージド インスタンスの XEvent 監査では、Azure Blob Storage のターゲットがサポートされます。 ファイル ログと Windows ログは **サポートされていません**。

Azure Blob Storage を監査するための `CREATE AUDIT` 構文の主な相違点は次のとおりです。

- 新しい `TO URL` 構文が用意されています。この構文を使って、`.xel` ファイルを配置する Azure Blob Storage コンテナーの URL を指定できます。
- Event Hubs および Azure Monitor ログ ターゲットを有効にするための新しい構文 `TO EXTERNAL MONITOR` が用意されています。
- Azure SQL Managed Instance は Windows ファイル共有にアクセスできないため、`TO FILE` 構文は **サポートされていません**。
- Shutdown オプションは **サポートされていません**。
- `queue_delay` の値として 0 は **サポートされていません**。

## <a name="next-steps"></a>次のステップ

- 監査ログの使い方の完全な一覧については、[Azure SQL Database 監査の使用](../../azure-sql/database/auditing-overview.md)に関するページをご覧ください。
- 標準コンプライアンスをサポートする Azure プログラムの詳細については、[Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)をご覧ください。ここから最新のコンプライアンス証明書の一覧を入手できます。

<!--Image references-->
