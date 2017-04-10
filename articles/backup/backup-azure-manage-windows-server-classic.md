---
title: "クラシック デプロイ モデルを使用した Azure Backup コンテナーとサーバーの管理 | Microsoft Docs"
description: "このチュートリアルでは、Azure Backup コンテナーとサーバーを管理する方法について説明します。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: f175eb12-0905-437f-91fd-eaee03ab6e81
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: 41d02237708517c782d64aeadcafd053fcd6523d


---
# <a name="manage-azure-backup-vaults-and-servers-using-the-classic-deployment-model"></a>クラシック デプロイメント モデルを使用した Azure Backup コンテナーとサーバーの管理
> [!div class="op_single_selector"]
> * [リソース マネージャー](backup-azure-manage-windows-server.md)
> * [クラシック](backup-azure-manage-windows-server-classic.md)
>
>

この記事では、Azure クラシック ポータルと Microsoft Azure Backup エージェントで使用できるバックアップ管理タスクの概要を説明します。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

## <a name="management-portal-tasks"></a>管理ポータルのタスク
1. [管理ポータル](https://manage.windowsazure.com)にサインインします。
2. **[復旧サービス]**をクリックし、バックアップ資格情報コンテナーの名前をクリックして、[クイック スタート] ページを表示します。

    ![[復旧サービス]](./media/backup-azure-manage-windows-server-classic/rs-left-nav.png)

[クイック スタート] ページの上部にあるオプションを選択すると、利用可能な管理タスクを確認できます。

![管理タブ](./media/backup-azure-manage-windows-server-classic/qs-page.png)

### <a name="dashboard"></a>ダッシュボード
**[ダッシュボード]** を選択すると、サーバーの使用状況の概要が表示されます。 **[使用状況の概要]** には、次の情報が含まれます。

* クラウドに登録されている Windows Server の数
* クラウド内の保護された Azure 仮想マシンの数
* Azure で使用されているストレージの合計
* 最近のジョブの状態

[ダッシュボード] の下部で、次のタスクを実行できます。

* **証明書の管理** - 証明書を使用してサーバーを登録した場合は、このタスクを使用して証明書を更新します。 資格情報コンテナーの資格情報を使用している場合は、 **[証明書の管理]**を使用しないでください。
* **削除** - 現在のバックアップ コンテナーを削除します。 バックアップ コンテナーがもう使用されていない場合は、そのコンテナーを削除してストレージ領域を解放できます。 **[削除]** は、登録されているサーバーすべてが資格情報コンテナーから削除された場合にのみ有効になります。

![バックアップ ダッシュボードのタスク](./media/backup-azure-manage-windows-server-classic/dashboard-tasks.png)

## <a name="registered-items"></a>登録済みの項目
**[登録済みの項目]** を選択すると、このコンテナーに登録されたサーバーの名前が表示されます。

![登録済みの項目](./media/backup-azure-manage-windows-server-classic/registered-items.png)

**[種類]** フィルターは、既定で Azure 仮想マシンに設定されています。 このコンテナーに登録されているサーバーの名前を表示するには、ドロップダウン メニューで **[Windows Server]** を選択します。

ここでは、次のタスクを実行できます。

* **再登録を許可** - サーバーに対してこのオプションが選択されている場合、オンプレミスの Microsoft Azure Backup エージェントで**登録ウィザード**を使用して、サーバーをバックアップ コンテナーにもう一度登録できます。 証明書でエラーが発生したとき、またはサーバーを再構築する必要があった場合などに、再登録が必要になる可能性があります。
* **削除** - バックアップ コンテナーからサーバーを削除します。 サーバーに関連付けられている保存されたデータすべてがすぐに削除されます。

    ![登録済み項目のタスク](./media/backup-azure-manage-windows-server-classic/registered-items-tasks.png)

## <a name="protected-items"></a>保護された項目
**[保護された項目]** を選択すると、サーバーからバックアップされた項目が表示されます。

![保護された項目](./media/backup-azure-manage-windows-server-classic/protected-items.png)

## <a name="configure"></a>構成
**[構成]** タブで、適切なストレージ冗長オプションを選択できます。 ストレージ冗長オプションを選択する最適なタイミングは、コンテナーの作成直後で、コンピューターをコンテナーに登録する前です。

> [!WARNING]
> 項目が資格情報コンテナーに登録されたら、ストレージ冗長オプションはロックされ、変更できなくなります。
>
>

![構成](./media/backup-azure-manage-windows-server-classic/configure.png)

ストレージの冗長性の詳細については、 [こちらの記事](../storage/storage-redundancy.md)をご覧ください。

## <a name="microsoft-azure-backup-agent-tasks"></a>Microsoft Azure Backup エージェントのタスク
### <a name="console"></a>コンソール
**Microsoft Azure Backup エージェント** を開きます (コンピューターで " *Microsoft Azure Backup*" を検索すると見つかります)。

![Backup エージェント](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)

Backup エージェント コンソールの右側にある **[アクション]** から、次の管理タスクを実行します。

* サーバーの登録
* Schedule Backup
* 今すぐバックアップ
* プロパティの変更 

![エージェント コンソールのアクション](./media/backup-azure-manage-windows-server-classic/console-actions.png)

> [!NOTE]
> **データを回復する**方法については、 [Windows サーバーまたは Windows クライアント コンピューターへのファイルの復元](backup-azure-restore-windows-server.md)に関するページをご覧ください。
>
>

### <a name="modify-an-existing-backup"></a>既存のバックアップの変更
1. Microsoft Azure Backup エージェントで、 **[バックアップのスケジュール]**をクリックします。

    ![Windows Server のバックアップ スケジュールを設定します](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
2. **バックアップのスケジュール ウィザード**で、**[バックアップ項目または時刻を変更する]** を選択した状態のまま、**[次へ]** をクリックします。

    ![スケジュールされたバックアップの変更](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
3. 項目を追加または変更する場合は、**[バックアップする項目の選択]** 画面で **[項目の追加]** をクリックします。

    ウィザードのこのページで、 **[除外の設定]** を設定することもできます。 ファイルまたはファイルの種類を除外する場合は、 [除外の設定](#exclusion-settings)を追加する手順をご覧ください。
4. バックアップするファイルとフォルダーを選択し、 **[OK]**をクリックします。

    ![[項目の追加]](./media/backup-azure-manage-windows-server-classic/add-items-modify.png)
5. **バックアップのスケジュール**を指定し、**[次へ]** をクリックします。

    毎日 (1 日に最大 3 回) または毎週のバックアップをスケジュールすることができます。

    ![バックアップ スケジュールの指定](./media/backup-azure-manage-windows-server-classic/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > バックアップ スケジュールの指定の詳細については、こちらの [記事](backup-azure-backup-cloud-as-tape.md)をご覧ください。
   >
   >
6. バックアップ コピーの **[アイテム保持ポリシー]** を選択し、**[次へ]** をクリックします。

    ![リテンション期間ポリシーの選択](./media/backup-azure-manage-windows-server-classic/select-retention-policy-modify.png)
7. **[確認]** 画面で情報を確認し、**[完了]** をクリックします。
8. ウィザードで**バックアップ スケジュール**の作成が完了したら、**[閉じる]** をクリックします。

    保護の変更後、 **[ジョブ]** タブに移動し、バックアップ ジョブに変更が反映されていることを確認することで、バックアップが正しくトリガーされていることを確認できます。

### <a name="enable-network-throttling"></a>ネットワーク調整の有効化
Azure Backup エージェントには、データ転送時のネットワーク帯域幅の使用方法を制御できる [調整] タブがあります。 データのバックアップを業務時間中に行う必要があり、バックアップ処理が他のインターネット トラフィックに影響を与えないようにする場合などに、この制御が便利です。 データ転送のスロットルはバックアップと復元のアクティビティに適用されます。  

調整を有効にするには、次の手順を実行します。

1. **Backup エージェント**で、**[プロパティの変更]** をクリックします。
2. **[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** チェック ボックスをオンにします。

    ![ネットワークのスロットル](./media/backup-azure-manage-windows-server-classic/throttling-dialog.png)
3. スロットルを有効にしたら、**[作業時間]** と **[作業時間外]** で、バックアップ データの転送時に使用できる帯域幅を指定します。

    帯域幅の値は、512 キロバイト/秒 (Kbps) から始まり、最大で 1023 メガバイト/秒 (Mbps) まで指定できます。 また、 **[作業時間]**の開始および終了時刻や、作業日と見なされる曜日も指定できます。 指定した作業時間以外は、作業時間外と見なされます。
4. **[OK]**をクリックします。

## <a name="exclusion-settings"></a>[除外の設定]
1. **Microsoft Azure Backup エージェント** を開きます (コンピューターで " *Microsoft Azure Backup*" を検索すると見つかります)。

    ![Backup エージェントを開く](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)
2. Microsoft Azure Backup エージェントで、 **[バックアップのスケジュール]**をクリックします。

    ![Windows Server のバックアップ スケジュールを設定します](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
3. バックアップのスケジュール ウィザードで、**[バックアップ項目または時刻を変更する]** を選択した状態のまま、**[次へ]** をクリックします。

    ![スケジュールの変更](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
4. **[除外の設定]**をクリックします。

    ![除外する項目の選択](./media/backup-azure-manage-windows-server-classic/exclusion-settings.png)
5. **[除外の追加]**をクリックします。

    ![除外の追加](./media/backup-azure-manage-windows-server-classic/add-exclusion.png)
6. 場所を選択し、 **[OK]**をクリックします。

    ![除外の場所の選択](./media/backup-azure-manage-windows-server-classic/exclusion-location.png)
7. **[ファイルの種類]** フィールドにファイル拡張子を追加します。

    ![ファイルの種類で除外](./media/backup-azure-manage-windows-server-classic/exclude-file-type.png)

    .mp3 拡張子の追加

    ![ファイルの種類の例](./media/backup-azure-manage-windows-server-classic/exclude-mp3.png)

    別の拡張子を追加するには、 **[除外の追加]** をクリックし、別の種類のファイル拡張子を入力します (.jpeg 拡張子を追加)。

    ![他のファイルの種類の例](./media/backup-azure-manage-windows-server-classic/exclude-jpg.png)
8. すべての拡張子を追加したら、 **[OK]**をクリックします。
9. **[確認] ページ**が表示されるまで **[次へ]** をクリックしてバックアップのスケジュール ウィザードを続行し、**[完了]** をクリックします。

    ![除外の確認](./media/backup-azure-manage-windows-server-classic/finish-exclusions.png)

## <a name="next-steps"></a>次のステップ
* [Azure からの Windows Server または Windows クライアントの復元](backup-azure-restore-windows-server.md)
* Azure Backup の詳細については、「 [Azure Backup の概要](backup-introduction-to-azure-backup.md)
*  [Azure Backup フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=290933)



<!--HONumber=Dec16_HO1-->


