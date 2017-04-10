---
title: "Azure Backup エージェントを使用したファイルとフォルダーのバックアップ | Microsoft Docs"
description: "Microsoft Azure Backup エージェントを使用して、Windows のファイルとフォルダーを Azure にバックアップします。 Recovery Services コンテナーの作成、Backup エージェントのインストール、バックアップ ポリシーの定義、ファイルとフォルダーの初回バックアップを行います。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "バックアップ コンテナー; Windows サーバーのバックアップ; Windows のバックアップ;"
ms.assetid: 7f5b1943-b3c1-4ddb-8fb7-3560533c68d5
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/23/2017
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: b188affca609dd5ff3aa0d2cba3ec81c1c91888f
ms.openlocfilehash: 3528294d944fd71fc98a30e2736e1245e50c3be6
ms.lasthandoff: 02/24/2017


---
# <a name="back-up-a-windows-server-or-client-to-azure-using-the-resource-manager-deployment-model"></a>Resource Manager デプロイメント モデルで Windows Server または Windows クライアントを Azure にバックアップする
> [!div class="op_single_selector"]
> * [Azure ポータル](backup-configure-vault.md)
> * [クラシック ポータル](backup-configure-vault-classic.md)
>
>

この記事では、Resource Manager デプロイメント モデルを使用して、Azure Backup で Windows Server または Windows クライアントのファイルやフォルダーを Azure にバックアップする方法について説明します。

[!INCLUDE [learn-about-deployment-models](../../includes/backup-deployment-models.md)]

![バックアップ プロセスの手順](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>開始する前に
サーバー (またはクライアント) を Azure にバックアップするには、Azure アカウントが必要です。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/free/) を数分で作成できます。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する
Recovery Services コンテナーは、経時的に作成されたすべてのバックアップと回復ポイントを格納するエンティティです。 Recovery Services コンテナーには、保護対象のファイルとフォルダーに適用されるバックアップ ポリシーも含まれます。 Recovery Services コンテナーを作成するときは、適切なストレージ冗長オプションも選択することをお勧めします。

### <a name="to-create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成するには
1. まだサインインしていない場合は、Azure サブスクリプションを使用して [Azure ポータル](https://portal.azure.com/) にサインインします。
2. ハブ メニューの **[その他のサービス]** をクリックし、リソースの一覧で「**Recovery Services**」と入力して、**[Recovery Services コンテナー]** をクリックします。

    ![Create Recovery Services Vault step 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    サブスクリプションに Recovery Services コンテナーがある場合は、そのコンテナーが一覧表示されます。

3. **[Recovery Services コンテナー]** メニューの **[追加]** をクリックします。

    ![Create Recovery Services Vault step 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    [Recovery Services コンテナー] ブレードが開き、**[名前]**、**[サブスクリプション]**、**[リソース グループ]**、**[場所]** を指定するよう求められます。

    ![Recovery Services コンテナーの作成手順 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. **[名前]**ボックスに、コンテナーを識別する表示名を入力します。 名前は Azure サブスクリプションに対して一意である必要があります。 2 ～ 50 文字の名前を入力します。 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。

5. **[サブスクリプション]** セクションで、ドロップダウン メニューを使用して Azure サブスクリプションを選択します。 サブスクリプションが&1; つのみの場合は、そのサブスクリプションが表示されるので、次の手順に進んでください。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (または推奨) のサブスクリプションを使用してください。 組織のアカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。

6. **[リソース グループ]** セクションで、次のことを行います。

    * 新しいリソース グループを作成する場合は、**[新規作成]** を選択します。
    または
    * 使用可能なリソース グループの一覧を表示するには、**[既存のものを使用]** を選択し、ドロップダウン メニューをクリックします。

  リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。

7. **[場所]** をクリックして、コンテナーの地理的リージョンを選択します。 この選択により、バックアップ データの送信先となるリージョンが決まります。

8. [Recovery Services コンテナー] ブレードの下部にある **[作成]** をクリックします。

  Recovery Services コンテナーの作成には数分かかることがあります。 ポータルの右上の領域で、状態の通知を監視します。 コンテナーが作成されると、Recovery Services コンテナーの一覧に表示されます。 数分経過してもコンテナーが表示されない場合は、**[最新の情報に更新]** をクリックしてください。

  ![[最新の情報に更新] ボタンをクリックする](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

  Recovery Services コンテナーの一覧にコンテナーが表示されたら、ストレージ冗長性を設定する準備が整いました。


### <a name="set-storage-redundancy"></a>ストレージ冗長性の設定
初めて Recovery Services コンテナーを作成するときに、ストレージのレプリケーション方法を決定します。

1. **[Recovery Services コンテナー]** ブレードで、新しいコンテナーをクリックします。

    ![Recovery Services コンテナーの一覧から新しいコンテナーを選択する](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    コンテナーを選択すると、**[Recovery Services コンテナー]** ブレードが縮小され、"*上部にコンテナー名が表示された*" 設定ブレードとコンテナーの詳細ブレードが開きます。

    ![新しいコンテナーのストレージ構成を表示する](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)

2. 新しいコンテナーの設定ブレードで、垂直スライドを使って下へスクロールして [管理] セクションに移動し、**[バックアップ インフラストラクチャ]** をクリックします。

  [バックアップ インフラストラクチャ] ブレードが開きます。

3. [バックアップ インフラストラクチャ] ブレードで、**[バックアップ構成]** をクリックして **[バックアップ構成]** ブレードを開きます。

  ![新しいコンテナーのストレージ構成を設定する](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)

4. コンテナーの適切なストレージ レプリケーション オプションを選択します。

  ![ストレージ構成の選択](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

  既定では、コンテナーには geo 冗長ストレージがあります。 プライマリ バックアップ ストレージ エンドポイントとして Azure を使用する場合は、引き続き **[geo 冗長]** を使用します。 プライマリ バックアップ ストレージ エンドポイントとして Azure を使用しない場合、**[ローカル冗長]** を選択します。これにより、Azure Storage のコストを削減できます。 [geo 冗長](../storage/storage-redundancy.md#geo-redundant-storage)ストレージと[ローカル冗長](../storage/storage-redundancy.md#locally-redundant-storage)ストレージのオプションの詳細については、こちらの[ストレージ冗長性の概要](../storage/storage-redundancy.md)を参照してください。

コンテナーを作成したら、Microsoft Azure Recovery Services エージェントをダウンロードしてインストールし、コンテナーの資格情報をダウンロードし、その資格情報を使用してエージェントをコンテナーに登録して、ファイルとフォルダーをバックアップするインフラストラクチャを準備します。

## <a name="configure-the-vault"></a>コンテナーの構成

1. Recovery Services コンテナー (先ほど作成したコンテナー) のブレードの [作業の開始] セクションで **[バックアップ]** をクリックし、**[バックアップ作業の開始]** ブレードで、**[バックアップの目標]** を選択します。

  ![Open backup goal blade](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

  **[バックアップの目標]** ブレードが開きます。 Recovery Services コンテナーが既に構成されている場合は、[Recovery Services コンテナー] ブレードの **[バックアップ]** をクリックすると、**[バックアップの目標]** が開きます。

  ![Open backup goal blade](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. **[ワークロードはどこで実行されていますか?]** ボックスの一覧の **[オンプレミス]** を選択します。

  Windows Server または Windows コンピューターが Azure にない物理マシンであるため、**[オンプレミス]** を選択します。

3. **[何をバックアップしますか?]** メニューの **[ファイルとフォルダー]** を選択し、**[OK]** をクリックします。

  ![ファイルとフォルダーの構成](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

  [OK] をクリックすると、**[バックアップの目標]** の横にチェックマークが表示され、**[インフラストラクチャの準備]** ブレードが開きます。

  ![バックアップの目標の構成完了、次はインフラストラクチャの準備](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. **[インフラストラクチャの準備]** ブレードで、**[Windows Server または Windows クライアント用エージェントのダウンロード]** をクリックします。

  ![[Download Agent for Windows Server or Windows Client]](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

  Windows Server Essentials を使用している場合は、Windows Server Essentials 用エージェントのダウンロードを選択します。 MARSAgentInstaller.exe を実行するか、保存するかをたずねるポップアップ メニューが表示されます。

  ![MARSAgentInstaller ダイアログ](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. ダウンロードのポップアップ メニューで、**[保存]** をクリックします。

  既定では、 **MARSagentinstaller.exe** ファイルがダウンロード フォルダーに保存されます。 インストーラーのダウンロードが完了すると、インストーラーを実行するかフォルダーを開くかをたずねるポップアップが表示されます。

  ![[Download Agent for Windows Server or Windows Client]](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

  まだ、エージェントをインストールする必要はありません。 エージェントはコンテナー資格情報をダウンロードした後にインストールできます。

6. **[インフラストラクチャの準備]** ブレードで、**[ダウンロード]** をクリックします。

  ![コンテナー資格情報のダウンロード](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

  コンテナー資格情報は、ダウンロード フォルダーにダウンロードされます。 コンテナー資格情報のダウンロードが完了すると、資格情報を開くか保存するかをたずねるポップアップが表示されます。 **[保存]**をクリックします。 誤って **[開く]** をクリックすると、コンテナー資格情報を開こうとして失敗します。 コンテナー資格情報を開くことはできません。 次の手順に進みます。 コンテナー資格情報はダウンロード フォルダーにあります。   

  ![コンテナー資格情報のダウンロード完了](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)

## <a name="install-and-register-the-agent"></a>エージェントをインストールして登録する

> [!NOTE]
> Azure Portal を使用してバックアップを有効にすることはまだできません。 Microsoft Azure Recovery Services エージェントを使用して、ファイルとフォルダーをバックアップしてください。
>

1. ダウンロード フォルダー (または他の保存場所) で **MARSagentinstaller.exe** を見つけて、ダブルクリックします。

  インストーラーは一連のメッセージを表示しながら、Recovery Services エージェントの抽出、インストール、登録を実行します。

  ![Recovery Services エージェントのインストーラーの実行](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Microsoft Azure Recovery Services エージェント セットアップ ウィザードを実行します。 ウィザードでは以下のことを行う必要があります。

  * インストールとキャッシュ フォルダーの場所を選択します。
  * プロキシ サーバーを使用してインターネットに接続する場合は、プロキシ サーバーの情報を指定します。
  * 認証済みのプロキシを使用する場合は、ユーザー名とパスワードの詳細を入力します。
  * ダウンロードしたコンテナーの資格情報を指定します。
  * 暗号化パスフレーズを安全な場所に保存します。

  > [!NOTE]
  > パスフレーズを紛失または忘れた場合、Microsoft はバックアップ データの回復を支援することはできません。 安全な場所にファイルを保存してください。 バックアップを復元するために必要です。
  >
  >

エージェントがインストールされ、コンピューターがコンテナーに登録されました。 バックアップを構成してスケジュールする準備ができました。


## <a name="create-the-backup-policy"></a>バックアップ ポリシーの作成
バックアップ ポリシーとは、回復ポイントを取得するスケジュールと、回復ポイントが保持される期間の長さです。 Microsoft Azure Backup エージェントを使用して、ファイルとフォルダーのバックアップ ポリシーを作成します。

### <a name="to-create-a-backup-schedule"></a>バックアップ スケジュールを作成するには
1. Microsoft Azure Backup エージェントを開きます  エージェントは、コンピューターで **Microsoft Azure Backup**を検索すると見つかります。

    ![Launch the Azure Backup agent](./media/backup-configure-vault/snap-in-search.png)
2. Backup エージェントの **[アクション]** ウィンドウで、**[バックアップのスケジュール]** をクリックしてバックアップのスケジュール ウィザードを起動します。

    ![Schedule a Windows Server backup](./media/backup-configure-vault/schedule-first-backup.png)

3. バックアップのスケジュール ウィザードの **[作業の開始]** ページで、**[次へ]** をクリックします。
4. **[バックアップする項目の選択]** ページで、**[項目の追加]** をクリックします。

  [項目の選択] ダイアログが開きます。

5. 保護するファイルとフォルダーを選択し、**[OK]** をクリックします。
6. **[バックアップする項目の選択]** ページで、**[次へ]** をクリックします。
7. **[バックアップ スケジュールの選択]** ページでバックアップ スケジュールを指定し、**[次へ]** をクリックします。

    毎日 (1 日に最大&3; 回) または毎週のバックアップをスケジュールすることができます。

    ![Windows Server のバックアップ項目](./media/backup-configure-vault/specify-backup-schedule-close.png)

   > [!NOTE]
   > バックアップ スケジュールを指定する方法の詳細については、「 [Azure Backup を使用してテープのインフラストラクチャを置換する](backup-azure-backup-cloud-as-tape.md)」を参照してください。
   >
   >

8. **[保持ポリシーの選択]** ページで、バックアップ コピーのための特定の保持ポリシーを選択して、**[次へ]** をクリックします。

    保持ポリシーは、バックアップを保存する期間を指定します。 すべてのバックアップ ポイントに "同じポリシー" を指定するのでなく、バックアップが実行されるタイミングに基づいて異なる保持ポリシーを指定できます。 必要に応じて、日、週、月、および年単位で保有ポリシーを変更できます。
9. [初期バックアップの種類の選択] ページで、初期バックアップの種類を選択します。 **[自動でネットワーク経由]** オプションが選択された状態のままにし、**[次へ]** をクリックします。

    ネットワーク経由で自動的にバックアップことも、オフラインでバックアップすることもできます。 この記事の残りの部分では、自動バックアップのプロセスについて説明します。 オフライン バックアップを実行する場合は、「 [Azure Backup でのオフライン バックアップのワークフロー](backup-azure-backup-import-export.md) 」で詳細を確認してください。
10. [確認] ページで情報を確認し、 **[完了]**をクリックします。
11. ウィザードでバックアップ スケジュールの作成が完了したら、 **[閉じる]**をクリックします。

### <a name="enable-network-throttling"></a>ネットワーク調整の有効化
Microsoft Azure Backup エージェントは、ネットワーク調整を提供します。 調整では、データ転送中にネットワーク帯域幅をどのように使用するかを制御します。 データのバックアップを業務時間中に行う必要があり、バックアップ処理が他のインターネット トラフィックに影響を与えないようにする場合などに、この制御は便利です。 調整はバックアップと復元のアクティビティに適用されます。

> [!NOTE]
> ネットワーク スロットルは、Windows Server 2008 R2 SP1、Windows Server 2008 SP2、Windows 7 (Service Pack を含む) では使用できません。 Azure Backup のネットワーク スロットル機能は、ローカル オペレーティング システムのサービス品質 (QoS) を利用します。 Azure Backup で上記のオペレーティング システムを保護することはできますが、これらのプラットフォームで使用できる QoS のバージョンでは、Azure Backup のネットワーク スロットルが機能しません。 ネットワーク スロットルは、上記以外の [サポートされているすべてのオペレーティング システム](backup-azure-backup-faq.md)で使用できます。
>
>

**ネットワーク調整を有効にするには**

1. Microsoft Azure Backup エージェントで、**[プロパティの変更]** をクリックします。

    ![[プロパティの変更]](./media/backup-configure-vault/change-properties.png)
2. **[調整]** タブで、**[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** チェック ボックスをオンにします。

    ![ネットワークのスロットル](./media/backup-configure-vault/throttling-dialog.png)
3. スロットルを有効にした後、**[作業時間]** と **[作業時間外]** で、バックアップ データ転送のために許可される帯域幅を指定します。

    帯域幅の値は、512 キロバイト/秒 (Kbps) から始まり、最大で 1023 メガバイト/秒 (Mbps) まで指定できます。 また、 **[作業時間]**の開始および終了時刻や、作業日と見なされる曜日も指定できます。 指定した作業時間以外の時間は、作業時間外と見なされます。
4. **[OK]**をクリックします。

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>初回のファイルとフォルダーをバックアップするには
1. Backup エージェントで **[今すぐバックアップ]** をクリックして、ネットワーク経由での最初のシード処理を完了します。

    ![Windows Server を今すぐバックアップする](./media/backup-configure-vault/backup-now.png)
2. [確認] ページで、今すぐバックアップ ウィザードによってコンピューターのバックアップに使用される設定を確認します。 次に、 **[バックアップ]**をクリックします。
3. **[閉じる]** をクリックしてウィザードを閉じます。 バックアップ プロセスが完了する前にウィザードを閉じても、ウィザードはバックグラウンドで引き続き実行されます。

初回バックアップが完了すると、 **[ジョブは完了しました]** 状態が Backup コンソールに表示されます。

![IR complete](./media/backup-configure-vault/ircomplete.png)

## <a name="questions"></a>疑問がある場合
ご不明な点がある場合や今後搭載を希望する機能がある場合は、 [フィードバックをお送りください](http://aka.ms/azurebackup_feedback)。

## <a name="next-steps"></a>次のステップ
VM や他のワークロードのバックアップの詳細については、以下を参照してください。

* ファイルとフォルダーをバックアップしたので、 [コンテナーとサーバーを管理](backup-azure-manage-windows-server.md)できます。
* バックアップを復元する必要がある場合は、 [Windows コンピューターへのファイルの復元](backup-azure-restore-windows-server.md)に関する記事を参照してください。

