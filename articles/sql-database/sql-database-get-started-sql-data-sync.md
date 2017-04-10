---
title: "Azure SQL データ同期の概要 (プレビュー) | Microsoft Docs"
description: "このチュートリアルでは、Azure SQL データ同期 (プレビュー) の概要について説明します。"
services: sql-database
documentationcenter: 
author: dearandyxu
manager: jhubbard
editor: 
ms.assetid: a295a768-7ff2-4a86-a253-0090281c8efa
ms.service: sql-database
ms.custom: migrate and move
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2016
ms.author: jhubbard
translationtype: Human Translation
ms.sourcegitcommit: c8e285fc6fb82ab5c929236ac9cb5dc858924e57
ms.openlocfilehash: 6535260a1650a2d3cc665eeb9d3ea33ae2de2650


---
# <a name="getting-started-with-azure-sql-data-sync-preview"></a>Azure SQL データ同期の概要 (プレビュー)
このチュートリアルでは、Azure クラシック ポータルを使用して、Azure SQL データ同期の基礎を学習します。

このチュートリアルは、SQL Server および Azure SQL Database を使用した経験がほとんどない読者を対象に作成されています。 このチュートリアルでは、ハイブリッド (SQL Server および SQL Database インスタンス) 同期グループを作成し、全面的に構成して、設定したスケジュールに従って同期します。

> [!NOTE]
> これまで MSDN に置かれていた Azure SQL データ同期の完全な技術ドキュメント セットは、現在は PDF として [こちら](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)でダウンロードできます。
>
>

## <a name="step-1-connect-to-the-azure-sql-database"></a>手順 1: Azure SQL Database への接続
1. [クラシック ポータル](http://manage.windowsazure.com)にサインインします。
2. 左のウィンドウで、 **[SQL DATABASE]** をクリックします。
3. ページの下部にある **[同期]** をクリックします。 [同期] をクリックすると、追加できる項目である **[新しい同期グループ]** および **[新しい同期エージェント]** が一覧に表示されます。
4. 新しい SQL データ同期エージェント ウィザードを起動するには、 **[新しい同期エージェント]**をクリックします。
5. 以前にエージェントを追加したことがない場合は、 **ここをクリックしてエージェントをダウンロードしてください**。

    ![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)

## <a name="step-2-add-a-client-agent"></a>ステップ 2. クライアント エージェントの追加
この手順が必要になるのは、同期グループにオンプレミス SQL Server データベースを含める場合だけです。
同期グループにあるのが SQL Database のインスタンスのみである場合は、手順 4 をスキップします。

<a id="InstallRequiredSoftware"></a>

### <a name="step-2a-install-the-required-software"></a>ステップ 2a. 必要なソフトウェアのインストール
クライアント エージェントをインストールするコンピューターに次のソフトウェアがインストールされていることを確認します。

* **.NET Framework 4.0**

  .NET Framework 4.0 を [ここ](http://go.microsoft.com/fwlink/?linkid=205836)からインストールしてください。
* **Microsoft SQL Server 2008 R2 SP1 System CLR Types (x86)**

  Microsoft SQL Server 2008 R2 SP1 System CLR Types (x86) は、[ここ](http://www.microsoft.com/download/en/details.aspx?id=26728)
* **Microsoft SQL Server 2008 R2 SP1 Shared Management Objects (x86)**

  Microsoft SQL Server 2008 R2 SP1 Shared Management Objects (x86) は、 [ここ](http://www.microsoft.com/download/en/details.aspx?id=26728)

<a id="InstallClient"></a>

### <a name="step-2b-install-a-new-client-agent"></a>ステップ 2b. 新しいクライアント エージェントのインストール
「 [クライアント エージェントのインストール (SQL データ同期)](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf) 」の指示に従って、エージェントをインストールします。

<a id="RegisterSSDb"></a>

### <a name="step-2c-finish-the-new-sql-data-sync-agent-wizard"></a>ステップ 2c. 新しい SQL データ同期エージェント ウィザードの完了
1. 新しい SQL データ同期エージェント ウィザードに戻ります。
2. わかりやすいエージェント名を付けます。
3. ドロップダウン リストで、このエージェントをホストする **[リージョン]** (データ センター) を選択します。
4. ドロップダウン リストで、このエージェントをホストする **[サブスクリプション]** を選択します。
5. 右矢印をクリックします。

## <a name="step-3-register-a-sql-server-database-with-the-client-agent"></a>ステップ 3. SQL Server データベースのクライアント エージェントへの登録
クライアント エージェントをインストールした後、同期グループに含めるオンプレミス SQL Server データベースをすべてエージェントに登録します。
データベースをエージェントに登録するには、「 [SQL Server データベースをクライアント エージェントに登録する](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)」を参照してください。

## <a name="step-4-create-a-sync-group"></a>ステップ 4. 同期グループの作成
<a id="StartNewSGWizard"></a>

### <a name="step-4a-start-the-new-sync-group-wizard"></a>ステップ 4a. 新しい同期グループ ウィザードの開始
1. [クラシック ポータル](http://manage.windowsazure.com)に戻ります。
2. **[SQL データベース]**をクリックします。
3. ページの下部にある **[同期の追加]** をクリックして、ドロワから [新しい同期グループ] を選択します。

   ![Image2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)

<a id=""></a>

### <a name="step-4b-enter-the-basic-settings"></a>ステップ 4b. 基本設定の入力
1. わかりやすい同期グループ名を入力します。
2. ドロップダウン リストで、この同期グループをホストする **[リージョン]** (データ センター) を選択します。
3. 右矢印をクリックします。

    ![Image3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)

<a id="DefineHubDB"></a>

### <a name="step-4c-define-the-sync-hub"></a>ステップ 4c. 同期ハブの定義
1. ドロップダウン リストで、同期グループのハブとして機能する SQL Database インスタンスを選択します。
2. この SQL Database インスタンスの資格情報を **[ハブ ユーザー名]** および **[ハブ パスワード]** に入力します。
3. SQL データ同期によってユーザー名とパスワードが確認されるのを待ちます。 資格情報が確認されると、[パスワード] ボックスの右側に緑色のチェック マークが表示されます。
4. ドロップダウン リストで、 **[競合の解決]** ポリシーを選択します。

   **[ハブ側に合わせる]** - ハブ データベースに書き込まれた変更内容が参照データベースに書き込まれ、同じ参照データベース レコードの変更内容は上書きされます。 機能的には、ハブに書き込まれた最初の変更内容が他のデータベースに反映されることになります。

 **[クライアント側に合わせる]** – ハブ データベースに書き込まれた変更内容は参照データベースの変更内容によって上書きされます。 機能的には、ハブに書き込まれた最後の変更内容は、他のデータベースに保持され反映されている変更内容ということになります。

1. 右矢印をクリックします。

   ![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<a id="AddRefDB"></a>

### <a name="step-4d-add-a-reference-database"></a>ステップ 4d. 参照データベースの追加
同期グループに追加するデータベースごとに、この手順を繰り返します。

1. ドロップダウン リストで、追加するデータベースを選択します。

    ドロップダウン リストに表示されるデータベースは、エージェントに登録された SQL Server データベースと SQL Database インスタンスの両方を含みます。
2. このデータベースの資格情報を **[ユーザー名]** および **[パスワード]** に入力します。
3. ドロップダウン リストで、このデータベースの **[同期の方向]** を選択します。

   **[双方向]** – 参照データベースの変更内容がハブ データベースに書き込まれ、ハブ データベースの変更内容が参照データベースに書き込まれます。

   **[ハブから同期]** - データベースはハブから更新を受け取ります。 変更内容をハブに送信しません。

   **[ハブに同期]** - データベースからハブに更新を送ります。 ハブの変更内容はこのデータベースに書き込まれません。
4. 同期グループの作成を終了するには、ウィザードの右下にあるチェック マークをクリックします。 SQL データ同期によって資格情報が確認されるのを待ちます。 資格情報が確認されると、緑色のチェック マークが表示されます。
5. チェック マークをもう一度クリックします。 これで [SQL Database] の **[同期]** ページに戻ります。 この同期グループが他の同期グループおよびエージェントと並んで一覧表示されています。

   ![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)

## <a name="step-5-define-the-data-to-sync"></a>ステップ 5. 同期するデータの定義
Azure SQL データ同期では、同期するテーブルと列を選択できます。 また、列をフィルターして特定の値 (年齢 >=&65; など) が含まれた行だけを同期する場合は、Azure の SQL データ同期ポータルを使用し、「同期するテーブル、列、および行の選択」の説明を参照して、同期するデータを定義します。

1. [クラシック ポータル](http://manage.windowsazure.com)に戻ります。
2. **[SQL データベース]**をクリックします。
3. **[同期]** タブをクリックします。
4. この同期グループの名前をクリックします。
5. **[同期規則]** タブをクリックします。
6. 同期グループのスキーマを指定するデータベースを選択します。
7. 右矢印をクリックします。
8. **[スキーマの更新]**をクリックします。
9. データベースの各テーブルで、同期の対象とする列を選択します。
   * サポートされていないデータ型の列は選択できません。
   * デーブルの列が選択されていない場合、そのテーブルは同期グループに含められません。
   * すべてのテーブルの選択および選択解除を行うには、画面の下部にある [選択] をクリックします。
10. **[保存]**をクリックし、同期グループのプロビジョニングが完了するのを待ちます。
11. データ同期のランディング ページに戻るには、画面の左上 (同期グループ名の上) にある左向き矢印をクリックします。

    ![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## <a name="step-6-configure-your-sync-group"></a>ステップ 6. 同期グループの構成
同期グループはいつでも、データ同期のランディング ページの下部にある [同期] をクリックして同期できます。
定期的に同期する場合は、同期グループを構成します。

1. [クラシック ポータル](http://manage.windowsazure.com)に戻ります。
2. **[SQL データベース]**をクリックします。
3. **[同期]** タブをクリックします。
4. この同期グループの名前をクリックします。
5. **[構成]** タブをクリックします。
6. **[自動同期]**
   * 設定した頻度で同期グループを同期するには、 **[オン]**をクリックします。 この場合でも、[同期] をクリックして随時同期することができます。
   * **[オフ]** をクリックすると、[同期] をクリックしたときだけ同期グループが同期されます。
7. **[同期の頻度]**
   * [自動同期] が [オン] の場合は、同期の頻度を設定します。 頻度は 5 分から 1 か月の範囲で指定します。
8. **[保存]**をクリックします。

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

おめでとうございます。 SQL Database インスタンスと SQL Server データベースの両方を含む同期グループを作成しました。

## <a name="next-steps"></a>次のステップ
SQL Database と SQL データ同期の詳細については、以下を参照してください。

* [完全な SQL データ同期テクニカル ドキュメントのダウンロード](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)
* [SQL Database の概要](sql-database-technical-overview.md)
* [データベースのライフサイクル管理](https://msdn.microsoft.com/library/jj907294.aspx)



<!--HONumber=Jan17_HO4-->


