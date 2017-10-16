---
title: "Azure SQL データ同期の概要 (プレビュー) | Microsoft Docs"
description: "このチュートリアルでは、Azure SQL データ同期 (プレビュー) の概要について説明します。"
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: a295a768-7ff2-4a86-a253-0090281c8efa
ms.service: sql-database
ms.custom: load & move data
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: douglasl
ms.openlocfilehash: 1943e87ffd991d099ab655af5a7d16d7f1608d22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-sql-data-sync-preview"></a>Azure SQL データ同期の概要 (プレビュー)
このチュートリアルでは、Azure SQL Database と SQL Server インスタンスの両方を含むハイブリッド同期グループを作成して、Azure SQL データ同期をセットアップする方法について学習します。 新しい同期グループには必要な構成をすべて行います。このため、新しい同期グループは設定したスケジュールで同期されます。

このチュートリアルは、少なくとも SQL Database および SQL Server をいくらか使用したことがある読者を対象にしています。 

SQL データ同期の概要については、[データの同期](sql-database-sync-data.md)に関するページを参照してください。

SQL データ同期を構成する方法を示す完全な PowerShell の例については、次の記事を参照してください。
-   [PowerShell を使用して複数の Azure SQL Database 間で同期を行う](scripts/sql-database-sync-data-between-sql-databases.md)
-   [PowerShell を使用して Azure SQL Database と SQL Server オンプレミス データベースの間で同期を行う](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!NOTE]
> これまで MSDN に置かれていた Azure SQL データ同期の完全な技術ドキュメント セットは、現在は .PDF ドキュメントとして [こちら](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)でダウンロードできます。

## <a name="step-1---create-sync-group"></a>手順 1 - 同期グループの作成

### <a name="locate-the-data-sync-settings"></a>データ同期設定の検索

1.  ブラウザーで Azure Portal に移動します。

2.  ポータルで、ダッシュボードから、またはツール バーの [SQL データベース] アイコンから、SQL データベースを検索します。

    ![Azure SQL データベースの一覧](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  **[SQL データベース]** ブレードで、データ同期のハブ データベースとして使用する既存の SQL データベースを選択します。[SQL データベース] ブレードが開きます。

4.  選択したデータベースの [SQL データベース] ブレードで、**[別のデータベースに同期]** を選択します。 [データ同期] ブレードが開きます。

    ![[別のデータベースに同期] オプション](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>新しい同期グループの作成

1.  [データ同期] ブレードで **[新しい同期グループ]** を選択します。 **[新しい同期グループ]** ブレードが開きます (手順 1 の **[同期グループの作成]** が強調表示された状態になっています)。 一緒に、**[データ同期グループの作成]** ブレードが開きます。

2.  **[データ同期グループの作成]** ブレードで、以下の操作を行います。

    1.  **[同期グループ名]** フィールドで、新しい同期グループの名前を入力します。

    2.  **[メタデータ データベースの同期]** セクションで、新しいデータベースを作成するか (推奨)、既存のデータベースを使用するかを選択します。

        > [!NOTE]
        > Microsoft では、新しい空のデータベースを作成して同期メタデータ データベースとする方法をお勧めしています。 データ同期は、このデータベースにテーブルを作成し、頻繁に発生するワークロードを実行します。 このデータベースは、選択したリージョンのすべての同期グループが使用する同期メタデータ データベースとして自動的に共有されます。 同期メタデータ データベースやその名前は、削除しないと変更できません。

        **[新しいデータベース]** を選択した場合は、**[新しいデータベースの作成]** を選択します。 **[SQL データベース]** ブレードが開きます。 **[SQL データベース]** ブレードで、新しいデータベースの名前を指定し、構成します。 **[OK]** をクリックします。

        **[既存のデータベースを使用する]** を選択した場合は、一覧からデータベースを選択します。

    3.  **[自動同期]** セクションで、まず、**[オン]** または **[オフ]** を選択します。

        **[オン]** を選択した場合は、**[同期の頻度]** セクションで数値を入力し、[秒]、[分]、[時間]、または [日] を選択します。

        ![同期の頻度の指定](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  **[競合の解決]** セクションで、[ハブ側に合わせる] または [Member wins]\(メンバー側に合わせる\) を選択します。

        ![競合の解決方法の指定](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  **[OK]** を選択し、新しい同期グループが作成されてデプロイされるまで待ちます。

## <a name="step-2---add-sync-members"></a>手順 2 - 同期メンバーの追加

新しい同期グループが作成されてデプロイされると、**[新しい同期グループ]** ブレードで手順 2 の **[同期メンバーの追加]** が強調表示されます。

**[ハブ データベース]** セクションで、ハブ データベースを配置する SQL Database サーバーの既存の資格情報を入力します。 このセクションでは、"*新しい*" 資格情報を入力しないでください。

![ハブ データベースが同期グループに追加された](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

## <a name="add-an-azure-sql-database"></a>Azure SQL Database の追加

**[メンバー データベース]** セクションで、必要に応じて **[Azure データベースを追加]** を選択して、Azure SQL Database を同期グループに追加します。 **[Azure データベースの構成]** ブレードが開きます。

**[Azure データベースの構成]** ブレードで、以下の操作を行います。

1.  **[メンバー名の同期]** フィールドで、新しい同期メンバーの名前を指定します。 この名前は、データベース自体の名前とは異なります。

2.  **[サブスクリプション]** フィールドで、課金のために関連付ける Azure サブスクリプションを選択します。

3.  **[Azure SQL Server]** フィールドで、既存の SQL データベース サーバーを選択します。

4.  **[Azure SQL Database]** フィールドで、既存の SQL データベースを選択します。

5.  **[同期方向]** フィールドで、[双方向の同期]、[ハブへ]、または [ハブから] を選択します。

    ![新しい SQL Database 同期メンバーの追加](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  **[ユーザー名]** と **[パスワード]** フィールドに、メンバー データベースがある SQL Database サーバーの既存の資格情報を入力します。 このセクションでは、"*新しい*" 資格情報を入力しないでください。

7.  **[OK]** を選択し、新しい同期メンバーが作成され、デプロイされるまで待ちます。

    ![新しい SQL Database 同期メンバーが追加された](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

## <a name="add-an-on-premises-sql-server-database"></a>オンプレミス SQL Server データベースの追加

**[メンバー データベース]** セクションで、必要に応じて **[オンプレミス データベースを追加]** を選択して、オンプレミスの SQL Server を同期グループに追加します。 **[オンプレミスの構成]** ブレードが開きます。

**[オンプレミスの構成]** ブレードで、以下の操作を行います。

1.  **[同期エージェント ゲートウェイの選択]** を選択します。 **[同期エージェントの選択]** ブレードが開きます。

    ![同期エージェント ゲートウェイの選択](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  **[同期エージェント ゲートウェイの選択]** ブレードで、既存のエージェントを使用するか、新しいエージェントを作成するかを選択します。

    **[既存のエージェント]** を選択した場合は、一覧から既存のエージェントを選択します。

    **[新しいエージェントを作成します]** を選択した場合は、以下の操作を行います。

    1.  表示されているリンクからクライアント同期エージェント ソフトウェアをダウンロードして、SQL Server があるコンピューターにインストールします。
 
        > [!IMPORTANT]
        > クライアント エージェントがサーバーと通信できるように、ファイアウォールの送信 TCP ポート 1433 を開く必要があります。


    2.  エージェントの名前を入力します。

    3.  **[キーを作成し生成する]** を選択します。

    4.  エージェント キーをクリップボードにコピーします。
        
        ![新しい同期エージェントの作成](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  **[OK]** を選択して、**[同期エージェントの選択]** ブレードを閉じます。

    6.  SQL Server コンピューターでクライアント同期エージェント アプリを探して実行します。

        ![データ同期クライアント エージェント アプリ](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  同期エージェント アプリで **[Submit Agent Key]\(エージェント キーの送信\)** を選択します。 **[Sync Metadata Database Configuration]\(同期メタデータ データベースの構成\)** ダイアログ ボックスが開きます。

    8.  **[Sync Metadata Database Configuration]\(同期メタデータ データベースの構成\)** ダイアログ ボックスで、Azure Portal からコピーしたエージェント キーを貼り付けます。 また、メタデータ データベースが配置されている Azure SQL Database サーバーの既存の資格情報も指定します  (新しいメタデータ データベースを作成した場合、このデータベースはハブ データベースと同じサーバー上にあります)。**[OK]** を選択し、構成が完了するまで待ちます。

        ![エージェント キーとサーバー資格情報の入力](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   この時点でファイアウォールのエラーが発生する場合は、SQL Server コンピューターからの受信トラフィックを許可するように、Azure 上でファイアウォール規則を作成する必要があります。 ポータルで規則を手動で作成することもできますが、SQL Server Management Studio (SSMS) で作成する方が簡単でしょう。 SSMS で、Azure 上のハブ データベースに接続してみてください。 名前として、「\<hub_database_name\>.database.windows.net」を入力します。 ダイアログ ボックスに表示される手順に従って、Azure ファイアウォール規則を構成します。 クライアント同期エージェント アプリに戻ります。

    9.  クライアント同期エージェント アプリで **[登録]** をクリックして、SQL Server データベースをエージェントに登録します。 **[SQL Server の構成]** ダイアログ ボックスが開きます。

        ![SQL Server データベースの追加と構成](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. **[SQL Server の構成]** ダイアログ ボックスで、SQL Server 認証と Windows 認証のどちらを使用して接続するかを選択します。 SQL Server 認証を選択した場合は、既存の資格情報を入力します。 SQL Server 名と、同期するデータベースの名前を指定します。**[接続テスト]** を選択して、設定をテストします。 次に、**[保存]** を選択します。 登録したデータベースが、一覧に表示されます。

        ![SQL Server データベースが登録されました](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. クライアント同期エージェント アプリを閉じることができます。

    12. ポータルの **[オンプレミスの構成]** ブレードで、**[データベースの選択]** を選択します。 **[データベースの選択]** ブレードが開きます。

    13. **[データベースの選択]** ブレードの **[メンバー名の同期]** フィールドで、新しい同期メンバーの名前を指定します。 この名前は、データベース自体の名前とは異なります。 一覧からデータベースを選択します。 **[同期方向]** フィールドで、[双方向の同期]、[ハブへ]、または [ハブから] を選択します。

        ![オンプレミス データベースの選択](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. **[OK]** を選択して、**[データベースの選択]** ブレードを閉じます。 さらに **[OK]** を選択して **[オンプレミスの構成]** ブレードを閉じ、新しい同期メンバーが作成およびデプロイされるまで待ちます。 最後に、**[OK]** をクリックして **[同期メンバーの選択]** ブレードを閉じます。

        ![同期グループに追加されたオンプレミス データベース](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  SQL データ同期およびローカル エージェントに接続するには、ロール `DataSync_Executor` にユーザー名を追加します。 データ同期は、このロールを SQL Server インスタンス上に作成します。

## <a name="step-3---configure-sync-group"></a>手順 3 - 同期グループの構成

新しい同期グループ メンバーが作成およびデプロイされると、**[新しい同期グループ]** ブレードで手順 3 の **[同期グループの構成]** が強調表示されます。

1.  **[テーブル]** ブレードで、同期グループ メンバーの一覧からデータベースを選択し、**[スキーマの更新]** を選択します。

2.  使用可能なテーブルの一覧から、同期するテーブルを選択します。

    ![同期するテーブルの選択](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  既定では、テーブル内のすべての列が選択されます。 同期の必要がない列がある場合は、その列のチェック ボックスをオフにします。主キー列は、選択されたままにしておいてください。

    ![同期するフィールドの選択](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  最後に、**[保存]** を選択します。

## <a name="next-steps"></a>次のステップ
おめでとうございます。 SQL Database インスタンスと SQL Server データベースの両方を含む同期グループを作成しました。

SQL Database および SQL データ同期の詳細については、以下を参照してください。

-   [完全な SQL データ同期テクニカル ドキュメントのダウンロード](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)
-   [SQL データ同期 REST API ドキュメントのダウンロード](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)
-   [SQL Database の概要](sql-database-technical-overview.md)
-   [データベースのライフサイクル管理](https://msdn.microsoft.com/library/jj907294.aspx)
