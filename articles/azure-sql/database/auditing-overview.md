---
title: Azure SQL Database および Azure Synapse Analytics の Azure SQL 監査
description: Azure SQL Database の監査を使用して、データベースイベントを追跡し、監査ログに書き込みます。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/09/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: 82445ce7c1ebfc365459bbeba7e04d660221eaf2
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551656"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL Database および Azure Synapse Analytics の監査
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md) および [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) の監査では、データベース イベントが追跡され、Azure ストレージ アカウント、Log Analytics ワークスペース、または Event Hubs の監査ログに書き込まれます。

また、監査によって以下を行うことができます。

- 規定コンプライアンスの維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

- コンプライアンスを保証するものではありませんが、標準へのコンプライアンスを強化します。 標準コンプライアンスをサポートする Azure プログラムの詳細については、[Azure セキュリティ センター](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)を参照してください。ここから最新の Azure SQL コンプライアンス証明書の一覧を見つけることができます。

> [!NOTE]
> Azure SQL Managed Instance の監査の詳細については、[SQL Managed Instance の監査の開始](../managed-instance/auditing-configure.md)に関する記事を参照してください。

## <a name="overview"></a><a id="overview"></a>概要

SQL Database 監査を使用して、以下を行うことができます。

- **保持** 。 監査するデータベース活動のカテゴリを定義できます。
- **レポート** 。 事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
- **分析** 。 疑わしいイベント、異常な活動、および傾向を発見できます。

> [!IMPORTANT]
> Azure SQL Database と Azure Synapse の監査は、可用性とパフォーマンスのために最適化されています。 アクティビティ量が非常に多いか、またはネットワークの負荷が高いときに、Azure SQL Database と Azure Synapse では、操作の続行を可能にするために、一部の監査イベントが記録されない場合があります。

### <a name="auditing-limitations"></a>監査の制限事項

- **Premium Storage** は現在 **サポートされていません**。
- **Azure Data Lake Storage Gen2 ストレージ アカウント** 用の **階層型名前空間** は現在 **サポートされていません**。
- 一時停止中の **Azure Synapse** で監査を有効にすることはサポートされていません。 監査を有効にするには、Azure Synapse を再開します。
- **Azure Synapse SQL プール** の監査では、既定の監査アクション グループ **のみ** がサポートされます。


#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>サーバー レベルおよびデータベース レベルの監査ポリシーを定義する

特定のデータベースに対して、または (SQL Database または Azure Synapse をホストする) Azure の既定の[サーバー](logical-servers.md) ポリシーとして、監査ポリシーを定義できます。

- サーバー ポリシーがサーバー上にある既存と新規作成のすべてのデータベースに適用されます。

- "*サーバー監査が有効*" な場合は、"*常にデータベースに適用*" されます。 データベース監査設定に関係なく、データベースが監査されます。

- Log Analytics ワークスペースまたはイベント ハブの保存先に対し、監査ポリシーがデータベース レベルで定義されている場合、次の操作ではソース データベースレベルの監査ポリシーが維持されません。
    - [データベース コピー](database-copy.md)
    - [ポイントインタイム リストア](recovery-using-backups.md)
    - [Geo レプリケーション](active-geo-replication-overview.md) (セカンダリ データベースにはデータベースレベルの監査はありません)

- サーバー上だけでなく、データベース上でも監査を有効にした場合であっても、サーバーの監査の設定がオーバーライドされたり変更されたりすることは "*ありません*"。 どちらの監査も並行して存在します。 つまり、データベースは並行して 2 回監査されることになります (1 回はサーバー ポリシー、もう 1 回はデータベース ポリシーによって監査されます)。

   > [!NOTE]
   > 次の場合を除き、サーバー監査とデータベース BLOB 監査の両方を有効にすることは避けてください。
    >
    > - 特定のデータベースに対して異なる "*ストレージ アカウント*"、"*リテンション期間*"、"*Log Analytics ワークスペース*" を使用する場合。
    > - 特定のデータベースの監査対象とするイベントの種類またはカテゴリが、このサーバー上の他のデータベースの管理対象と異なる場合。 たとえば、特定のデータベースに対してのみ監査が必要なテーブルの挿入がある場合など。
   >
   > これらに該当しない場合は、サーバー レベルの監査のみを有効にし、すべてのデータベースに対してデータベース レベルの監査を無効にすることをお勧めします。

#### <a name="remarks"></a>解説

- 監査ログは Azure サブスクリプションの Azure Blob Storage 内にある **追加 BLOB** に書き込まれます
- 監査ログは .xel 形式であり、[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) を使用して開くことができます。
- サーバー レベルまたはデータベース レベルの監査イベントに対して不変のログ ストアを構成するには、[Azure Storage で提供される手順](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes)に従います。 不変 BLOB ストレージを構成するときに、 **[さらに追加を許可する]** を選択していることを確認します。
- VNet またはファイアウォールの背後にある Azure Storage アカウントに監査ログを書き込むことができます。 具体的な手順については、[VNet とファイアウォールの背後にあるストレージ アカウントに監査を書き込む](audit-write-storage-account-behind-vnet-firewall.md)方法に関する記事を参照してください。
- ログの形式、ストレージ フォルダーの階層、および命名規則の詳細については、[BLOB 監査ログ形式のリファレンス](./audit-log-format.md)に関するドキュメントを参照してください。
- [読み取り専用レプリカ](read-scale-out.md)での監査は自動的に有効になります。 ストレージ フォルダーの階層、命名規則、ログ形式の詳細については、「[SQL Database 監査ログの形式](audit-log-format.md)」を参照してください。
- Azure AD Authentication を使用している場合は、失敗したログインのレコードは SQL 監査ログに表示 "*されません*"。 失敗したログインの監査レコードを表示するには、これらのイベントの詳細をログに記録している [Azure Active Directory ポータル](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)にアクセスする必要があります。
- ログインはゲートウェイによって、データベースが置かれている特定のインスタンスにルーティングされます。  AAD ログインの場合、そのユーザーを使用し、要求されたデータベースへのログインを試行する前に、資格情報が検証されます。  不合格になった場合、要求されたデータベースがアクセスされることはなく、監査は行われません。  SQL ログインの場合、要求されたデータで資格情報が検証されます。そのため、この場合、監査できます。  ログイン成功 (明らかにデータベースにアクセスできる) は、いずれの場合も監査されます。
- 監査設定を構成した後に、新しい脅威の検出機能をオンにし、電子メールを構成してセキュリティの警告を受信します。 脅威の検出を使用すると、セキュリティ上の脅威になる可能性がある異常なデータベース アクティビティに対するプロアクティブ アラートを受信できます。 詳細については、[脅威の検出の概要](threat-detection-overview.md)に関するページを参照してください。

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>サーバーの監査を設定する

既定の監査ポリシーには、すべてのアクションと次のアクション グループのセットが含まれます。これは、データベースに対して実行されたすべてのクエリとストアド プロシージャに加えて、成功および失敗したログインを監査します。
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
[Azure PowerShell を使用した SQL Database の監査の管理](#manage-auditing)に関するセクションで説明されているように、PowerShell を使用してさまざまな種類のアクションおよびアクション グループの監査を構成することができます。

Azure SQL Database および Azure Synapse の監査では、監査レコードの文字フィールドに 4,000 文字のデータを格納します。 監査可能なアクションから返された、**statement** または **data_sensitivity_information** 値に 4,000 を超える文字が含まれる場合、最初の 4,000 文字以降のすべてのデータは、**切り捨てられ、監査されません**。
以下のセクションでは、Azure Portal を使用した監査の構成について説明します。

  > [!NOTE]
  > - 一時停止している専用 SQL プールで監査を有効にすることはできません。 監査を有効にするには、専用 SQL プールの一時停止を解除します。 専用 SQL プールの詳細については、[こちら](../..//synapse-analytics/sql/best-practices-sql-pool.md)を参照してください。
  > - Azure portal または PowerShell コマンドレットを使用して、監査が Log Analytics ワークスペースまたはハブの送信先に対して構成されている場合、[診断設定](../../azure-monitor/essentials/diagnostic-settings.md)は "SQLSecurityAuditEvents" カテゴリが有効の状態で作成されます。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. **[SQL データベース]** ウィンドウまたは **[SQL サーバー]** ウィンドウの [セキュリティ] 見出しの下にある **[監査]** に移動します。
3. サーバーの監査ポリシーを設定する場合は、データベース監査ページの **[サーバー設定を表示]** リンクを選択できます。 そうすると、サーバー監査設定を表示または修正することができます。 サーバー監視ポリシーは、このサーバー上にある既存のデータベースと新規作成されたデータベースのすべてに適用されます。

    ![[データベース監査] ページで強調表示されている [サーバー設定を表示] リンクを示すスクリーンショット。](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. データベース レベルで監査を有効にする場合は、 **[監査]** を  **[ON]\(オン\)** に切り替えます。 サーバーの監査が有効になっている場合、データベース構成監査とサーバー監査が並行して存在します。

5. 監査ログを書き込む場所を構成するときに、複数のオプションから選択できます。 ログは、Azure ストレージ アカウント、Log Analytics ワークスペース (Azure Monitor ログで使用)、イベント ハブ (イベント ハブで使用) に書き込むことができます。 これらのオプションは組み合わせて構成でき、それぞれの場所に監査ログが書き込まれます。
  
   ![ストレージ オプション](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations"></a><a id="auditing-of-microsoft-support-operations"></a>Microsoft サポート操作の監査

Azure SQL Server に対する Microsoft サポートの操作の監査では、サポート リクエスト時に Microsoft サポートのエンジニアがサーバーにアクセスする必要がある場合に、彼らの操作を監査することができます。 監査に伴い、この機能を使用することで、従業員の透明性が増し、異常検出、トレンドの視覚化、データ損失防止が可能になります。

Microsoft サポートの操作の監査を有効にするには、 **[Azure SQL Server]** ウィンドウの [セキュリティ] 見出しの **[監査]** に移動して、 **[Microsoft サポート操作の監査]** を **[オン]** に切り替えます。

  > [!IMPORTANT]
  > Microsoft サポートの操作の監査では、ストレージ アカウントの保存先はサポートされていません。 この機能を有効にするには、Log Analytics ワークスペースまたは Event Hub の保存先を構成する必要があります。

![Microsoft サポートの操作のスクリーンショット](./media/auditing-overview/support-operations.png)

Log Analytics ワークスペースの Microsoft サポート業務の監査ログを確認するには、次のクエリを使用します。

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>ストレージ保存先への監査

ストレージ アカウントへの監査ログの書き込みを構成するには、 **[ストレージ]** を選択し、 **[容量の詳細]** を開きます。 ログを保存する Azure ストレージ アカウントを選択し、リテンション期間を選択します。 次に、 **[OK]** をクリックします 保持期間よりも古いログは削除されます。

- リテンション期間の既定値は 0 (無制限のリテンション期間) です。 この値は、ストレージ アカウントを監査用に構成するときに **[ストレージ設定]** の **[リテンション期間 (日数)]** スライダーを移動して変更できます。
  - リテンション期間を 0 (無制限のリテンション期間) から他の値に変更した場合、リテンション期間は、リテンション期間の値が変更された後に書き込まれたログにのみ適用されることに注意してください (リテンション期間が無制限に設定されている間に書き込まれたログは、リテンション期間が有効になった後も保持されます)。

  ![ストレージ アカウント](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Log Analytics 保存先への監査
  
Log Analytics ワークスペースへの監査ログの書き込みを構成するには、 **[Log Analytics]** を選択して **[Log Analytics の詳細]** を開きます。 ログが書き込まれる Log Analytics ワークスペースを選択または作成し、 **[OK]** をクリックします。

   ![Log Analytics ワークスペース](./media/auditing-overview/auditing_select_oms.png)

Azure Log Analytics ワークスペースの詳細については、「[Azure Monitor ログのデプロイの設計](../../azure-monitor/logs/design-logs-deployment.md)」を参照してください。
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>イベント ハブ保存先への監査

イベント ハブへの監査ログの書き込みを構成するには、 **[イベント ハブ]** を選択して、 **[イベント ハブの詳細]** を開きます。 ログが書き込まれるイベント ハブを選択し、 **[OK]** をクリックします。 イベント ハブがお使いのデータベースおよびサーバーと同じリージョンにあることを確認します。

   ![イベント ハブ](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>監査ログとレポートを分析する

監査ログを Azure Monitor ログに書き込む場合:

- [Azure Portal](https://portal.azure.com) を使用します。 関連するデータベースを開きます。 データベースの **[監査]** ページの上部にある **[監査ログの表示]** を選択します。

    ![監査ログの表示](./media/auditing-overview/auditing-view-audit-logs.png)

- ログを表示するには、2 つの方法があります。

    **[監査レコード]** ページの上部にある **[Log Analytics]** をクリックすると、Log Analytics ワークスペースでログ ビューが開きます。このビューで、時間の範囲と検索クエリをカスタマイズできます。

    ![Log Analytics ワークスペースで開く](./media/auditing-overview/auditing-log-analytics.png)

    **[監査レコード]** ページの上部にある **[ダッシュボードの表示]** をクリックすると、監査ログ情報を表示するダッシュボードが開きます。ここで、セキュリティ分析情報へのドリルダウンや機微なデータへのアクセスなどを実行できます。 このダッシュボードは、データのセキュリティ分析情報を得るのに役立つように設計されています。
    時間の範囲と検索クエリをカスタマイズすることもできます。
    ![Log Analytics ダッシュボードの表示](media/auditing-overview/auditing-view-dashboard.png)

    ![Log Analytics ダッシュボード](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Log Analytics のセキュリティ分析情報](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- また、Log Analytics ブレードから監査ログにアクセスすることもできます。 ご自身の Log Analytics ワークスペースを開いて、 **[全般]** セクションで **[ログ]** をクリックします。 監査ログを表示するには、*search "SQLSecurityAuditEvents"* などの単純なクエリから始めることができます。
    ここから [Azure Monitor ログ](../../azure-monitor/logs/log-query-overview.md) を使用して、監査ログのデータに対して詳細検索を実行することもできます。 Azure Monitor ログにより、統合された検索とカスタム ダッシュボードを使用してオペレーション インサイトがリアルタイムで得られるため、ワークロードやサーバー全体に散在する何百万件のレコードもすぐに分析できます。 Azure Monitor ログの検索言語とコマンドに関する有用な追加情報については、[Azure Monitor ログ検索リファレンス](../../azure-monitor/logs/log-query-overview.md)に関するページをご覧ください。

監査ログをイベント ハブに書き込む場合:

- イベント ハブの監査ログ データを使用するには、イベントを処理し、そのイベントをターゲットに書き込むようにストリームを設定する必要があります。 詳細については、「[Azure Event Hubs のドキュメント](../index.yml)」を参照してください。
- イベント ハブの監査ログは [Apache Avro](https://avro.apache.org/) イベントの本体でキャプチャされ、UTF-8 エンコードの JSON 形式を使用して格納されます。 監査ログを読み取るために、[Avro Tools](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) またはこの形式を処理する同様のツールを使用できます。

監査ログを Azure ストレージ アカウントに書き込むことを選択すると、複数の方法でログを表示できるようになります。

- 監査ログは、設定時に選択したアカウントで集計されます。 [Azure ストレージ エクスプローラー](https://storageexplorer.com/)などのツールを使用して監査ログを調査できます。 Azure Storage では、監査ログは **sqldbauditlogs** という名前のコンテナー内に BLOB ファイルのコレクションとして保存されます。 ストレージ フォルダーの階層、命名規則、ログ形式の詳細については、「[SQL Database 監査ログの形式](./audit-log-format.md)」を参照してください。

- [Azure Portal](https://portal.azure.com) を使用します。  関連するデータベースを開きます。 データベースの **[監査]** ページの上部にある **[監査ログの表示]** をクリックします。

    ![[データベース監査] ページで強調表示されている [監査ログの表示] ボタンを示すスクリーンショット。](./media/auditing-overview/7_auditing_get_started_blob_view_audit_logs.png)

    **[監査レコード]** が開きます。ここからログを参照できます。

  - **[監査レコード]** ページの上部にある **[フィルター]** をクリックすると、特定の日付を表示できます。
  - **[監査対象]** を切り替えると、"*サーバー監視ポリシー*" で作成された監査レコードと "*データベース監査ポリシー*" で作成された監査レコードを切り替えることができます。
  - **[Show only audit records for SQL injections]\(SQL インジェクションの監査レコードのみを表示する\)** チェックボックスをオンにすると、SQL インジェクション関連の監査レコードのみを表示できます。

       ![監査レコードを表示するためのオプションを示すスクリーンショット。]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- システム関数 **sys.fn_get_audit_file** (T-SQL) を使用して、表形式で監査ログ データを返します。 この関数の使用方法の詳細については、[sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql) に関するページをご覧ください。

- SQL Server Management Studio (SSMS 17 以降) で **[監査ファイルの統合]** を使用します。
    1. SSMS のメニューから、 **[ファイル]**  >  **[開く]**  >  **[監査ファイルの統合]** を選択します。

        ![[監査ファイルの統合] メニュー オプショを示すスクリーンショット。](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. **[監査ファイルの追加]** ダイアログ ボックスが表示されます。 **[追加]** オプションのいずれかを選択して、ローカル ディスクから監査ファイルをマージするか、Azure Storage からインポートするかを選択します。 Microsoft Azure Storage の詳細とアカウント キーを提供する必要があります。

    3. 統合するすべてのファイルを追加した後に、 **[OK]** をクリックして統合の操作を完了します。

    4. 統合されたファイルを SSMS で開くと、ファイルを表示および分析し、XEL または CSV ファイルまたはテーブルにエクスポートすることができます。

- Power BI を使用します。 Power BI で監査ログのデータを表示および分析できます。 ダウンロード可能なテンプレートの詳細と、テンプレートへのアクセスについては、[Power BI での監査ログ データの分析](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895)に関するページを参照してください。
- ポータル経由で、あるいは [Azure ストレージ エクスプローラー](https://storageexplorer.com/)などのツールを利用して Azure Storage BLOB コンテナーからログ ファイルをダウンロードします。
  - ログ ファイルをローカルでダウンロードした後に、ファイルをダブルクリックし、SSMS でログを開き、表示し、分析します。
  - また、Azure ストレージ エクスプ ローラーを使用して、同時に複数のファイルをダウンロードすることもできます。 それには、特定のサブフォルダーを右クリックし、 **[名前を付けて保存]** を選択してローカル フォルダーに保存します。

- 他の方法:

  - 複数のファイルまたはログ ファイルが含まれるサブフォルダーをダウンロードした後、前述の SSMS 監査ファイルの統合の指示に従って、ローカルでマージすることができます。
  - BLOB 監査ログをプログラムで表示します。PowerShell を使用して[拡張イベント ファイルにクエリを実行します](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)。

## <a name="production-practices"></a><a id="production-practices"></a>運用方法


### <a name="auditing-geo-replicated-databases"></a>geo レプリケーション対応データベースの監査

Geo レプリケーション データベースでは、プライマリ データベースの監査を有効にすると、セカンダリ データベースにも同一の監査ポリシーが適用されます。 プライマリ データベースとは別に **セカンダリ サーバー** で監査を有効にすることで、セカンダリ データベースに対する監査を設定することもできます。

- サーバー レベル (**推奨**):**プライマリ サーバー** と **セカンダリ サーバー** の両方で監査を有効にします。プライマリ データベースとセカンダリ データベースは、それぞれのサーバーレベル ポリシーに基づいて個別に監査されます。
- データベースレベル:セカンダリ データベースのデータベースレベルの監査は、プライマリ データベースの監査設定から構成する必要があります。
  - 監査は、サーバーではなく "*プライマリ データベース自体*" で有効にする必要があります。
  - プライマリ データベースで監査を有効にすると、セカンダリ データベースでも有効になります。

    > [!IMPORTANT]
    > データベースレベルの監査では、セカンダリ データベースのストレージ設定はプライマリ データベースと同じになるため、リージョンをまたいだトラフィックが発生します。 サーバー レベルの監査のみを有効にし、すべてのデータベースでデータベース レベルの監査を無効なままにしておくことをお勧めします。

### <a name="storage-key-regeneration"></a>ストレージ キーの再生成

運用環境では、ストレージ キーを最新の情報に定期的に更新することが推奨されます。 監査ログを Azure Storage に書き込む場合、ご自身のキーを最新の情報に更新するときに、お使いの監査ポリシーを再度保存する必要があります。 このプロセスは次のとおりです。

1. **[容量の詳細]** を開きます。 **[ストレージ アクセス キー]** ボックスで **[セカンダリ]** をクリックし、 **[OK]** をクリックします。 次に、監査構成ページの上部にある **[保存]** をクリックします。

    ![セカンダリ ストレージのアクセス キーを選択するためのプロセスを示すスクリーンショット。](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. ストレージ構成ページに移動し、プライマリ アクセス キーを再生成します。

    ![ナビゲーション ウィンドウ](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. 監査構成ページに戻り、[ストレージ アクセス キー] を [セカンダリ] から [プライマリ] に切り替え、 **[OK]** をクリックします。 次に、監査構成ページの上部にある **[保存]** をクリックします。
4. ストレージ構成ページに戻り、セカンダリ アクセス キーを (次のキー更新サイクルの準備として) 再生成します。

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Manage Azure SQL Database の監査

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

**PowerShell コマンドレット (WHERE 句のサポートによってフィルタリングを強化)** :

- [データベース監査ポリシーを作成または更新する (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [サーバー監査ポリシーを作成または更新する (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [データベース監査ポリシーを取得する (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [サーバー監査ポリシーを取得する (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [データベース監査ポリシーを削除する (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [サーバー監査ポリシーを削除する (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

スクリプトの例については、[PowerShell を使用した監査と脅威検出の構成](scripts/auditing-threat-detection-powershell-configure.md)に関するページを参照してください。

### <a name="using-rest-api"></a>REST API の使用

**REST API**:

- [データベース監査ポリシーの作成または更新](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [サーバー監査ポリシーの作成または更新](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [データベース監査ポリシーの取得](/rest/api/sql/database%20auditing%20settings/get)
- [サーバー監査ポリシーの取得](/rest/api/sql/server%20auditing%20settings/get)

WHERE 句のサポートによってフィルタリングを強化した拡張ポリシー:

- [データベース "*拡張*" 監査ポリシーの作成または更新](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [サーバー "*拡張*" 監査ポリシーの作成または更新](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [データベース "*拡張*" 監査ポリシーの取得](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [サーバー "*拡張*" 監査ポリシーの取得](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-cli"></a>Azure CLI の使用

- [サーバーの監査ポリシーを管理する](/cli/azure/sql/server/audit-policy)
- [データベースの監査ポリシーを管理する](/cli/azure/sql/db/audit-policy)

### <a name="using-azure-resource-manager-templates"></a>Azure リソース マネージャーのテンプレートを作成する

以下の例で確認できるように、[Azure Resource Manager](../../azure-resource-manager/management/overview.md) テンプレートを使用して Azure SQL Database の監査を管理できます。

- [Azure BLOB ストレージ アカウントに監査ログを書き込むように監査機能を有効にした Azure SQL Database をデプロイする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Log Analytics に監査ログを書き込むように監査機能を有効にした Azure SQL Database をデプロイする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Event Hubs に監査ログを書き込むように監査機能を有効にした Azure SQL Database をデプロイする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> リンクされたサンプルは、外部の公開リポジトリにあり、保証なしに "手を加えず" に提供され、Microsoft サポート プログラム/サービスのサポート対象ではなありません。
