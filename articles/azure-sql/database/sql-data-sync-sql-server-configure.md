---
title: SQL データ同期の設定
description: このチュートリアルでは、Azure の SQL データ同期を設定する方法を示します
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: d6b5bab1c1b6c8db4821fdf84728eb66eb55b899
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98882231"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>チュートリアル:Azure SQL Database と SQL Server のデータベース間の SQL データ同期を設定する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

このチュートリアルでは、Azure SQL Database と SQL Server の両方のインスタンスを含む同期グループを作成することによって SQL データ同期を設定する方法について説明します。 同期グループはカスタム構成され、設定されたスケジュールで同期します。

このチュートリアルは、SQL Database と SQL Server の使用経験があることを前提としています。

SQL データ同期の概要については、[SQL データ同期を使用したクラウドとオンプレミスのデータベース間でのデータの同期](sql-data-sync-data-sql-server-sql-database.md)に関する記事を参照してください。

PowerShell を使用した SQL データ同期の構成方法の例については、[SQL Database のデータベース間](scripts/sql-data-sync-sync-data-between-sql-databases.md)または[Azure SQL Database と SQL Server のデータベース間](scripts/sql-data-sync-sync-data-between-azure-onprem.md)の同期方法に関する記事をご覧ください

> [!IMPORTANT]
> 現時点では、SQL データ同期で Azure SQL Managed Instance はサポートされて **いません**。

## <a name="create-sync-group"></a>同期グループを作成する

1. [Azure portal](https://portal.azure.com) に移動し、SQL Database 内の目的のデータベースを見つけます。 **SQL データベース** を検索して選択します。

    ![データベースの検索、Microsoft Azure portal](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. データ同期のハブ データベースとして使用するデータベースを選択します。

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/select-sql-database.png" alt-text = "Select from the database list, Microsoft Azure portal":::

    > [!NOTE]
    > ハブ データベースは同期トポロジの中心になるエンドポイントであり、1 つの同期グループには複数のデータベース エンドポイントが含まれます。 同期グループ内にエンドポイントがある他のすべてのメンバー データベースが、ハブ データベースと同期します。

1. 選択したデータベースの **[SQL データベース]** メニューで、 **[別のデータベースに同期]** を選択します。

    :::image type="content" source="./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png" alt-text = "Sync to other databases, Microsoft Azure portal":::

1. **[別のデータベースに同期]** ページで、 **[新しい同期グループ]** を選択します。 **[新しい同期グループ]** ページが開かれ、 **[同期グループの作成] (手順 1)** が表示されます。

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/new-sync-group-private-link.png" alt-text = "Set up new sync group with private link":::

   **[データ同期グループの作成]** ページで、次の設定を変更します。

   | 設定                        | 説明 |
   | ------------------------------ | ------------------------------------------------- |
   | **同期グループ名** | 新しい同期グループの名前を入力します。 この名前は、データベース自体の名前とは異なります。 |
   | **同期メタデータ データベース** | データベースを作成する (推奨) か、既存のデータベースを使用するかを選択します。<br/><br/>**[新しいデータベース]** を選択した場合は、 **[新しいデータベースの作成]** を選択します。 次に、 **[SQL データベース]** ページで、新しいデータベースの名前を指定して構成し、 **[OK]** を選択します。<br/><br/>**[既存のデータベースを使用する]** を選択した場合は、一覧からデータベースを選択します。 |
   | **自動同期** | **[オン]** または **[オフ]** を選択します。<br/><br/>**[オン]** を選択した場合は、 **[同期の頻度]** セクションで数値を入力し、 **[秒]** 、 **[分]** 、 **[時間]** 、または **[日]** を選択します。<br/> 最初の同期は、構成が保存されてから、指定した期間が経過した後に開始されます。|
   | **競合の解決** | **[Hub win]\(ハブ優先\)** または **[Member win]\(メンバー優先\)** を選択します。<br/><br/>**[Hub win]\(ハブ優先\)** とは、競合が発生した場合、ハブ データベースのデータによって、メンバー データベースの競合するデータが上書きされることを意味します。<br/><br/>**[Member win]\(メンバー優先\)** とは、競合が発生した場合、メンバー データベースのデータによって、ハブ データベースの競合するデータが上書きされることを意味します。 |
   | **Private Link を使用する** | サービス マネージド プライベート エンドポイントを選択して、同期サービスとハブ データベースの間にセキュリティで保護された接続を確立します。 |

   > [!NOTE]
   > Microsoft では、**同期メタデータ データベース** として使用する新しい空のデータベースを作成することをお勧めしています。 データ同期は、このデータベースにテーブルを作成し、頻繁に発生するワークロードを実行します。 このデータベースは、選択したリージョンとサブスクリプション内のすべての同期グループの **同期メタデータ データベース** として共有されます。 リージョン内のすべての同期グループと同期エージェントを削除しないと、データベースまたはその名前を変更することはできません。

   **[OK]** を選択し、同期グループが作成されてデプロイされるまで待ちます。
   
1. **[新しい同期グループ]** ページで、 **[Use private link]\(Private Link を使用する\)** を選択した場合は、プライベート エンドポイント接続を承認する必要があります。 情報メッセージ内のリンクを使用すると、プライベート エンドポイント接続のエクスペリエンスに移動し、そこで接続を承認できます。 

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/approve-private-link.png" alt-text = "Approve private link":::

## <a name="add-sync-members"></a>同期メンバーを追加する

新しい同期グループが作成されてデプロイされると、 **[新しい同期グループ]** ページで **[同期メンバーの追加] (手順 2)** が強調表示されます。

**[ハブ データベース]** セクションで、ハブ データベースが配置されているサーバーの既存の資格情報を入力します。 このセクションでは、"*新しい*" 資格情報を入力しないでください。

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo.png" alt-text = "Enter existing credentials for the hub database server":::

### <a name="to-add-a-database-in-azure-sql-database"></a>Azure SQL Database にデータベースを追加するには

**[メンバー データベース]** セクションで、必要に応じて **[Add an Azure SQL Database]\(Azure SQL Database を追加\)** を選択して、Azure SQL Database 内のデータベースを同期グループに追加します。 **[Configure Azure SQL Database]\(Azure SQL Database の構成\)** ページが開きます。
  
   :::image type="content" source="./media/sql-data-sync-sql-server-configure/step-two-configure.png" alt-text = "Add a database to the sync group":::
   
  **[Configure Azure SQL Database]\(Azure SQL Database の構成\)** ページで、次の設定を変更します。

  | 設定                       | 説明 |
  | ----------------------------- | ------------------------------------------------- |
  | **同期メンバー名** | 新しい同期メンバーの名前を指定します。 この名前は、データベース自体の名前とは異なります。 |
  | **サブスクリプション** | 課金のために関連付ける Azure サブスクリプションを選択します。 |
  | **Azure SQL Server** | 既存のサーバーを選択します。 |
  | **Azure SQL Database** | SQL Database 内の既存のデータベースを選択します。 |
  | **同期方向** | **[双方向の同期]** 、 **[ハブへ]** 、または **[ハブから]** を選択します。 |
  | **[ユーザー名]** と **[パスワード]** | メンバー データベースが配置されているサーバーの既存の資格情報を入力します。 このセクションでは、"*新しい*" 資格情報を入力しないでください。 |
  | **Private Link を使用する** | サービス マネージド プライベート エンドポイントを選択して、同期サービスとメンバー データベースの間にセキュリティで保護された接続を確立します。 |

  **[OK]** を選択し、新しい同期メンバーが作成され、デプロイされるまで待ちます。

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>SQL Server データベースを追加するには

**[メンバー データベース]** セクションで、必要に応じて **[オンプレミス データベースを追加]** を選択して、SQL Server データベースを同期グループに追加します。 **[オンプレミスの構成]** ページが開き、次の操作を実行できます。

1. **[同期エージェント ゲートウェイの選択]** を選択します。 **[同期エージェントの選択]** ページが開きます。

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo-agent.png" alt-text = "Creating a sync agent":::

1. **[同期エージェントの選択]** ページで、既存のエージェントを使用するか、エージェントを作成するかを選択します。

   **[既存のエージェント]** を選択した場合は、一覧から既存のエージェントを選択します。

   **[新しいエージェントの作成]** を選択した場合は、次の操作を行います。

   1. 表示されているリンクからデータ同期エージェントをダウンロードし、SQL Server が配置されているコンピューターにインストールします。 このエージェントは、[Azure SQL Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693) のページから直接ダウンロードすることもできます。

      > [!IMPORTANT]
      > クライアント エージェントがサーバーと通信できるように、ファイアウォールの送信 TCP ポート 1433 を開く必要があります。

   1. エージェントの名前を入力します。

   1. **[Create and Generate Key]\(作成とキーの生成\)** を選択し、エージェント キーをクリップボードにコピーします。

   1. **[OK]** を選択して、 **[同期エージェントの選択]** ページを閉じます。

1. SQL Server コンピューターでクライアント同期エージェント アプリを探して実行します。

   ![データ同期クライアント エージェント アプリ](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. 同期エージェント アプリで **[Submit Agent Key]\(エージェント キーの送信\)** を選択します。 **[Sync Metadata Database Configuration]\(同期メタデータ データベースの構成\)** ダイアログ ボックスが開きます。

    1. **[Sync Metadata Database Configuration]\(同期メタデータ データベースの構成\)** ダイアログ ボックスで、Azure Portal からコピーしたエージェント キーを貼り付けます。 また、メタデータ データベースが配置されているサーバーの既存の資格情報も指定します。 (メタデータ データベースを作成した場合、このデータベースはハブ データベースと同じサーバー上にあります)。 **[OK]** を選択し、構成が完了するまで待ちます。

        ![エージェント キーとサーバー資格情報の入力](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > ファイアウォール エラーが発生する場合は、SQL Server コンピューターからの受信トラフィックを許可するファイアウォール規則を Azure 上に作成する必要があります。 この規則は、ポータルまたは SQL Server Management Studio (SSMS) で手動で作成できます。 SSMS で、名前として <hub_database_name>.database.windows.net を入力することで、Azure 上のハブ データベースに接続します。

    1. **[登録]** を選択して、SQL Server データベースをエージェントに登録します。 **[SQL Server の構成]** ダイアログ ボックスが開きます。

        ![SQL Server データベースの追加と構成](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. **[SQL Server の構成]** ダイアログ ボックスで、SQL Server 認証と Windows 認証のどちらを使用して接続するかを選択します。 SQL Server 認証を選択した場合は、既存の資格情報を入力します。 SQL Server の名前と、同期するデータベースの名前を指定し、 **[テスト接続]** を選択して設定をテストします。 その後、 **[保存]** を選択します。登録したデータベースが一覧に表示されます。

        ![SQL Server データベースが登録されました](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. Client Sync Agent アプリを閉じます。

1. ポータルの **[オンプレミスの構成]** ページで、 **[データベースの選択]** を選択します。

1. **[データベースの選択]** ページの **[メンバー名の同期]** フィールドで、新しい同期メンバーの名前を指定します。 この名前は、データベース自体の名前とは異なります。 一覧からデータベースを選択します。 **[同期方向]** フィールドで、 **[双方向の同期]** 、 **[ハブへ]** 、または **[ハブから]** を選択します。

    ![オンプレミス データベースの選択](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. **[OK]** を選択して、 **[データベースの選択]** ページを閉じます。 さらに **[OK]** を選択して **[オンプレミスの構成]** ページを閉じ、新しい同期メンバーが作成およびデプロイされるまで待ちます。 最後に、 **[OK]** を選択して **[同期メンバーの選択]** ページを閉じます。

> [!NOTE]
> SQL データ同期およびローカル エージェントに接続するには、*DataSync_Executor* ロールにユーザー名を追加します。 データ同期は、このロールを SQL Server インスタンス上に作成します。

## <a name="configure-sync-group"></a>同期グループを構成する

新しい同期グループ メンバーが作成されてデプロイされると、 **[新しい同期グループ]** ページで **[同期グループの構成] (手順 3)** が強調表示されます。

![手順 3 の設定](./media/sql-data-sync-sql-server-configure/stepthree.png)

1. **[テーブル]** ページで、同期グループ メンバーの一覧からデータベースを選択し、 **[スキーマの更新]** を選択します。

1. 一覧から、同期するテーブルを選択します。既定では、すべての列が選択されているため、同期しない列のチェック ボックスをオフにします。主キー列は、選択されたままにしておいてください。

1. **[保存]** を選択します。

1. 既定では、スケジュールに従うか手動で実行されるまで、データベースは同期されません。 手動による同期を実行するには、Azure portal で SQL Database 内の対象のデータベースに移動し、 **[別のデータベースに同期]** を選択し、同期グループを選択します。 **[データ同期]** ページが開きます。 **[同期]** を選択します。

    ![手動同期](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>よく寄せられる質問

**SQL データ同期では、テーブル全体が作成されますか?**

同期先データベースに同期スキーマ テーブルがない場合は、ご自分で選択した列を持つテーブルが SQL データ同期によって作成されます。 ただし、以下の理由から、これは完全に忠実なスキーマにはなりません。

- 同期先テーブルには、ご自分で選択した列だけが作成されます。 選択していない列は無視されます。
- 同期先テーブルには、選択した列のインデックスだけが作成されます。 列が選択されていない場合、それらのインデックスは無視されます。
- XML 型の列のインデックスは作成されません。
- CHECK 制約は作成されません。
- 同期元テーブル上のトリガーは作成されません。
- ビューとストアド プロシージャは作成されません。

これらの制限のため、次のことをお勧めします。

- 運用環境では、完全に忠実なスキーマをご自身で作成します。
- サービスを試す場合は、自動プロビジョニング機能を使用します。

**自分で作成していないテーブルがあるのはなぜですか?**

データ同期では、データベース内に変更を追跡するための追加のテーブルが作成されます。 これらを削除しないでください。削除するとデータ同期が動作を停止します。

**同期後にデータは収束しますか?**

必ずしもその必要はありません。 ハブと 3 つのスポーク (A、B、C) を持つ同期グループがあり、同期は ハブから A、ハブから B、ハブから C に対して行われるものとします。ハブから A への同期が実行された "*後*" でデータベース A が変更された場合、その変更は、次の同期タスクが実行されるまで、データベース B またはデータベース C には書き込まれません。

**スキーマの変更を同期グループに反映するにはどうすればよいですか?**

すべてのスキーマの変更を手動で行い、手動で反映してください。

1. ハブおよびすべての同期メンバーに対してスキーマ変更を手動でレプリケートします。
1. 同期スキーマを更新します。

新しいテーブルと列を追加する場合:

新しいテーブルと列は現在の同期に影響しないため、データ同期では、それらが同期スキーマに追加されるまで無視されます。 新しいデータベース オブジェクトを追加する場合は、次のシーケンスに従ってください。

1. 新しいテーブルまたは列を、ハブとすべての同期メンバーに追加します。
1. 新しいテーブルまたは列を、同期スキーマに追加します。
1. 新しいテーブルと列への値の挿入を開始します。

列のデータ型を変更する場合:

既存の列のデータ型を変更した場合、データ同期は新しい値が同期スキーマで定義された元のデータ型に一致する限り動作を続行します。 たとえば、同期元データベース内の型を **int** から **bigint** に変更した場合、**int** データ型としては大きすぎる値を挿入するまで、データ同期は動作を続行します。 変更を完了するには、ハブとすべての同期メンバーに対してスキーマの変更を手動でレプリケートした後、同期スキーマを更新します。

**データ同期でデータベースのエクスポートとインポートを行うにはどうすればよいですか?**

データベースを *.bacpac* ファイルとしてエクスポートし、そのファイルをインポートしてデータベースを作成した後、以下を実行して、新しいデータベースでデータ同期を使用します。

1. [このスクリプト](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql)を使用して、新しいデータベースでデータ同期オブジェクトと追加のテーブルをクリーンアップします。 このスクリプトは、データベースからすべての必要なデータ同期オブジェクトを削除します。
1. 新しいデータベースで同期グループを再作成します。 古い同期グループが必要ない場合は削除します。

**クライアント エージェントに関する情報はどこで見つけることができますか?**

クライアント エージェントについてよく寄せられる質問については、[エージェントに関する FAQ](sql-data-sync-agent-overview.md#agent-faq) のセクションを参照してください。

**プライベート リンクは、使用を開始する前に手動で承認する必要がありますか?**

はい。同期グループのデプロイ中に Azure portal の [プライベート エンドポイント接続] ページで、または PowerShell を使用して、サービス マネージド プライベート エンドポイントを手動で承認する必要があります。

## <a name="next-steps"></a>次のステップ

おめでとうございます。 SQL データベース インスタンスと SQL Server データベースの両方を含む同期グループを作成しました。

SQL データ同期の詳細については、以下を参照してください。

- [Azure SQL データ同期用の Data Sync Agent](sql-data-sync-agent-overview.md)
- [ベスト プラクティス](sql-data-sync-best-practices.md)と [SQL データ同期に関する問題をトラブルシューティングする方法](sql-data-sync-troubleshoot.md)
- [Azure Monitor ログによる SQL データ同期の監視](./monitor-tune-overview.md)
- [Transact-SQL を使用した同期スキーマの更新](sql-data-sync-update-sync-schema.md)または [PowerShell](scripts/update-sync-schema-in-sync-group.md)

SQL Database の詳細については、以下を参照してください。

- [SQL Database の概要](sql-database-paas-overview.md)
- [データベースのライフサイクル管理](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
