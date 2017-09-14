---
title: "Azure Backup Server v2 のインストール | Microsoft Docs"
description: "Azure Backup Server v2 には、VM、ファイルとフォルダー、ワークロードなどを保護するための高度なバックアップ機能があります。 Azure Backup Server v2 をインストールまたはアップグレードする方法を説明します。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: 1bbb16afef7940933b4c3ae23873f212770137e0
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---

# <a name="install-azure-backup-server-v2"></a>Azure Backup Server v2 のインストール

Azure Backup Server では、仮想マシン (VM)、ワークロード、ファイル、フォルダーなどを保護できます。 Azure Backup Server v2 は、Azure Backup Server v1 上にビルドされ、v1 にはない新しい機能を使用できます。 v1 と v2 の機能の比較は、「[Azure Backup Server の保護マトリックス](backup-mabs-protection-matrix.md)」を参照してください。 

Backup Server v2の追加機能は Backup Server v1からのアップグレードです。 ただし、Backup Server v1 は、Backup Server v2 をインストールするための前提条件ではありません。 Backup Server v1 から v2 にアップグレードする場合は、Backup Server v2 を Backup Server 保護サーバーにインストールします。 既存の Backup Server の設定がそのまま残ります。

Backup Server v2 は、Windows Server 2012 R2 または Windows Server 2016 にインストールできます。 System Center 2016 Data Protection Manager Modern Backup Storage のような新機能を利用するには、Backup Server v2 を Windows Server 2016 にインストールする必要があります。 Backup Server v2 にアップグレードまたはこれをインストールする前に、[インストールの前提条件](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)を確認してください。

> [!NOTE]
> Azure Backup Server には System Center Data Protection Manager と同じコード ベースがあります。 Backup Server v1 は、Data Protection Manager 2012 R2 に相当し、Backup Server v2 は、Data Protection Manager 2016 に相当します。 この記事は、しばしば Data Protection Manager のドキュメントを参照します。
>
>

## <a name="upgrade-backup-server-to-v2"></a>Backup Server の v2 へのアップグレード
Backup Server v1 から Backup Server v2 にアップグレードするには、インストール環境に必要な更新プログラムが適用されていることを確認します。

- 保護サーバーで[保護エージェントを更新](backup-mabs-upgrade-to-v2.md#update-the-dpm-protection-agent)します。
- Windows Server 2012 R2 を Windows Server 2016 にアップグレードします。
- すべての実稼働サーバー上で Azure Backup Server Remote Administrator をアップグレードします。
- 実稼働サーバーを再起動せずに続行されるようにバックアップが設定されていることを確認します。


### <a name="upgrade-steps-for-backup-server-v2"></a>Backup Server v2 のアップグレード手順

1. ダウンロード センターで、[アップグレード インストーラーをダウンロード](https://go.microsoft.com/fwlink/?LinkId=626082)します。

2. セットアップ ウィザードを抽出したら、**[Execute setup.exe]**(setup.exe を実行する) が選択されていることを確認してから **[完了]** を選択します。

  ![セットアップ インストーラー - セットアップを実行](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. Microsoft Azure Backup Server ウィザードの **[Install]**(インストール) で **[Microsoft Azure Backup Server]** を選択します。

  ![セットアップ インストーラー - インストールを選択](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. **[Welcome]**(ようこそ) ページで、警告を確認して、**[Next]**(次へ) を選択します。

  ![セットアップ インストーラー - [Welcome](ようこそ) ページ](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. セットアップ ウィザードでは、前提条件の確認を実行し、お客様の環境をアップグレードできるかどうかを確認します。 **[Prerequisite Checks]**(前提条件のチェック) ページで、**[Check]**(チェック) を選択します。

  ![セットアップ インストーラー - [Prerequisite Checks](前提条件のチェック) ページ](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. 環境が前提条件のチェックをパスする必要があります。 環境がチェックに合格しなかった場合は、問題をメモし、修正します。 **[Check Again]**(再チェック) を選択します。 前提条件のチェックに合格してから、**[Next]**(次へ) を選択します。

  ![セットアップ インストーラー - [Check Again](再チェック) ボタン](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. **[SQL Settings]**(SQL 設定) ページで、SQL のインストールに関連するオプションを選択し、**[Check and Install]**(チェックしてインストール) を選択します。

  ![セットアップ インストーラー - [SQL Settings](SQL 設定) ページ](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  このチェックには数分間かかります。 チェックが完了したら、**[Next]**(次へ) をクリックします。

  ![セットアップ インストーラー - [SQL Settings](SQL 設定) の [Check and Install](チェックしてインストール) ボタン](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and fix-settings.png)

8. **[Installation Settings]**(インストール設定) ページで、Backup Server をインストールする場所、またはスクラッチ ロケーションを変更します。 **[次へ]**を選択します。

  ![セットアップ インストーラー - [Installation Settings](インストール設定) ページ](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. セットアップ ウィザードを終了するには、**[Finish]**(完了) を選択します。

  ![セットアップ インストーラー - [Finish](完了)](./media/backup-mabs-upgrade-to-v2/run-setup.png)



## <a name="add-storage-for-modern-backup-storage"></a>Modern Backup Storage のストレージの追加

バックアップ記憶域の効率を向上させるために、Backup Server v2 はボリュームのサポートを追加しています。 Backup Server v1 と同様に、Backup Server v2 はディスクをサポートします。

### <a name="add-volumes-and-disks"></a>ボリュームとディスクの追加
Windows Server 2016 で Backup Server v2 を実行している場合、ボリュームを使用してバックアップ データを格納できます。 ボリュームを使用すると、記憶域を節約でき、バックアップが高速になります。 ボリュームは Backup Server の新機能であるため、これを追加する必要があります。 

Backup Server にボリュームを追加する場合、ボリュームにフレンドリ名を指定できます。 名前を付けるボリュームの **[Friendly Name]**(フレンドリ名) 列をクリックします。 後で名前を変えることもできます。 PowerShell を使用して、ボリュームのフレンドリ名を追加または変更することができます。

管理者コンソールでボリュームを追加するには、次の手順を実行します。

1. Azure Backup Server の管理者コンソールで、**[Management]**(管理) > **[Disk Storage]**(ディスク ストレージ) > **[Add]**(追加) の順に選択します。

    ![[Add Disk Storage](ディスク ストレージの追加) ウィザードを開きます](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

    [Add Disk Storage] \(ディスク ストレージの追加) ウィザードが開きます。

2. **[Add Disk Storage]**(ディスク ストレージの追加) ページの **[Available volumes]**(使用可能なボリューム) ボックスで、ボリュームを選択し、**[Add]**(追加) を選択します。
3. **[Selected volumes]**(選択したボリューム) ボックスに、ボリュームのフレンドリ名を入力し、**[OK]** を選択します。

      ![[ディスク ストレージの追加] ウィザード - ボリュームの追加](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  ディスクを追加する場合は、そのディスクは従来の記憶域を持つ保護グループに属している必要があります。 これらのディスクは、これらの保護グループに対してのみ使用できます。 Backup Server に従来の保護を持つソースない場合、ディスクは表示されません。

  ディスクの追加の詳細については、[ディスクを追加して従来の記憶域を増やす](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage)に関するページを参照してください。 ディスクには、フレンドリ名を割り当てることはできません。


### <a name="assign-workloads-to-volumes"></a>ワークロードをボリュームに割り当てる

Backup Server では、どのワークロードをどのボリュームに割り当てるかを指定します。 たとえば、1 秒あたりの入出力操作 (IOPS) が多い高コストのボリュームには、大量バックアップが頻繁に必要なワークロードのみを保存するように設定できます。 例としては、トランザクション ログを含む SQL Server があります。

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Backup Server の記憶域プールでボリュームのプロパティを更新するには、PowerShell コマンドレット Update-DPMDiskStorage を使用します。

構文:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

PowerShell を使用して行ったすべての変更は、UI に反映されます。


## <a name="protect-data-sources"></a>データ ソースの保護
データ ソースの保護を開始するには、保護グループを作成します。 次の手順では、新しい保護グループ ウィザードに対する変更や追加が強調表示されています。

保護グループを作成する手順は次のとおりです。

1. Backup Server の管理者コンソールで、**[Protection]**(保護) を選択します。

2. ツール リボンで、**[New]**(新規) を選択します。

    新しい保護グループの作成ウィザードが開きます。

  ![新しい保護グループの作成ウィザード](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. **[ようこそ]** ページで **[次へ]** をクリックします。
4. **[保護グループの種類の選択]** ページで、作成する保護グループの種類を選択し、**[次へ]** を選択します。

  ![[保護グループの種類の選択] ページ](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. **[グループ メンバーの選択]** ページの **[利用可能なメンバー]** ウィンドウで、メンバーと保護エージェントの一覧が表示されます。 この例では、ボリューム D:\ と E:\ を選択し、これらを **[Selected members]**(選択済みメンバー) ウィンドウに追加します。 **[次へ]**を選択します。

  ![[グループ メンバーの選択] ページ](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. **[データの保護方法の選択]** ページで、**[保護グループ名]** に名前を入力し、保護方法を選択してから **[次へ]** を選択します。 短期的な保護の場合、**[ディスク]** バックアップ メソッドを選択する必要があります。

  ![[データ保護方法の選択] ページ](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. **[短期的な目標の指定]** ページで、**[リテンション期間]** と **[同期頻度]** の詳細を選択します。 次に、**[次へ]** を選択します。 必要に応じて、復旧ポイントを取得するスケジュールを変更するために、**[変更]** を選択します。

  ![[短期的な目標の指定] ページ](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. **[ディスク ストレージの割り当ての確認]** ページで、選択したデータ ソース、サイズ、およびプロビジョニングする領域やターゲット記憶域のボリュームの各値についての詳細を確認します。

  ![[ディスク ストレージの割り当ての確認] ページ](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  記憶域のボリュームは、(PowerShell を使用して設定される) ワークロードのボリュームの割り当てと使用可能記憶域に基づいています。 ドロップダウン メニューでその他のボリュームを選択して、記憶域ボリュームを変更できます。 **[ターゲット記憶域]** の値を変更する場合、**[使用可能なディスクの記憶域]** の値は動的に変化し、**[空き領域]** と **[Underprovisioned Space]**(過小にプロビジョニングされた領域) の値を反映します。

  データ ソースが計画どおりに増えると、**[使用可能なディスクの記憶域]** の **[Underprovisioned Space]**(過小にプロビジョニングされた領域) の値は、必要な追加記憶域の量を反映します。 この値を使用して、スムーズなバックアップに必要な記憶域のニーズの計画に役立ててください。 値が 0 の場合、近い将来に記憶域に問題が発生する可能性はありません。 値が 0 以外の数値の場合は、保護ポリシーと保護されたメンバーのデータ サイズに基づいた十分な記憶域が割り当てられていません。

  ![過少割り当てされたディスク ストレージ](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

   保護グループの作成を終了するには、ウィザードを完了します。

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>従来の記憶域の Modern Backup Storage への移行
Backup Server v2 にアップグレードまたはこれをインストールし、オペレーティング システムを Windows Server 2016 にアップグレードしたら、 Modern Backup Storage を使用するように保護グループを更新します。 既定では、保護グループは変更されません。 初期のセットアップのとおりに機能します。 

Modern Backup Storage を使用するように保護グループを更新することはオプションです。 保護グループを更新するには、データ保持オプションを使用して、すべてのデータ ソースの保護を停止します。 次に、新しい保護グループにデータ ソースを追加します。

1. 管理者コンソールで、**[Protection]**(保護) 機能を選択します。 **[保護グループ メンバー]** 一覧で、メンバーを右クリックし、**[Stop protection of member]**(メンバーの保護を停止する) を選択します。

  ![メンバーの保護の停止](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. **[グループから削除]** ダイアログ ボックスで、記憶域プールの使用済みディスク領域と利用可能な空き領域を確認します。 既定では、ディスク上の復旧ポイントをそのままにし、関連付けられている保有ポリシーごとの有効期限が切れるようにします。 **[OK]**をクリックします。

  空き記憶域プールに使用済みディスク領域をすぐに返す場合は、**[ディスク上のレプリカを削除]** チェック ボックスを選択し、そのメンバーに関連付けられているバックアップ データ (および復旧ポイント) を削除します。

  ![[グループ] ダイアログ ボックスから削除](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Modern Backup Storage を使用する保護グループを作成します。 保護されていないデータ ソースが含めます。


## <a name="add-disks-to-increase-legacy-storage"></a>ディスクを追加して従来の記憶域を増やす

Backup Server で従来の記憶域を使用する場合、ディスクを追加して従来の記憶域を増やす必要があります。 

ディスク ストレージを追加するには、次の手順を実行します。

1. 管理者コンソールで、**[Management]**(管理) > **[Disk Storage]**(ディスク ストレージ) > **[Add]**(追加) の順に選択します。

    ![[Add Disk Storage](ディスク ストレージの追加) ダイアログ](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. **[Add Disk Storage]**(ディスク ストレージの追加) ダイアログで **[Add disks]**(ディスクの追加) を選択します。

5. 使用可能なディスクの一覧で、追加するディスクを選択し、**[追加]** を選択し、**[OK]** を選択します。

## <a name="update-the-data-protection-manager-protection-agent"></a>Data Protection Manager 保護エージェントの更新

Backup Server は、System Center Data Protection Manager 保護エージェントを使用して更新します。 ネットワークに接続されていない保護エージェントをアップグレードする場合は、Data Protection Manager 管理コンソールを使用して接続エージェントのアップグレードを完了することはできません。 アクティブでないドメイン環境で保護エージェントをアップグレードする必要があります。 クライアント コンピューターがネットワークに接続されるまで、Data Protection Manager 管理者コンソールは、保護エージェントの更新が保留中であることを表示します。

次のセクションでは、接続されているクライアント コンピューターと接続されていないクライアント コンピューターの保護エージェントを更新する方法について説明します。

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>接続されているクライアント コンピューターの保護エージェントの更新

1. Backup Server の管理者コンソールで、**[Management]**(管理) > **[Agents]**(エージェント) の順に選択します。

2. 表示ウィンドウで、保護エージェントを更新するクライアント コンピューターを選択します。

  > [!NOTE]
  > **[Agent Updates]**(エージェントの更新プログラム) 列は、保護されるコンピューターごとに保護エージェントの更新がいつ行われるかを示します。 **[アクション]** ウィンドウでは、**[更新する]** アイコンは、保護されるコンピューターが選択されていて、更新プログラムが利用可能なときのみ表示されます。
  >
  >

3. 選択したコンピューターに更新された保護エージェントをインストールするには、**[アクション]** ウィンドウで、**[更新する]** を選択します。

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>接続されていないクライアント コンピューターの保護エージェントの更新

1. Backup Server の管理者コンソールで、**[Management]**(管理) > **[Agents]**(エージェント) の順に選択します。

2. 表示ウィンドウで、保護エージェントを更新するクライアント コンピューターを選択します。

  > [!NOTE]
   > **[Agent Updates]**(エージェントの更新プログラム) 列は、保護されるコンピューターごとに保護エージェントの更新がいつ行われるかを示します。 **[アクション]** ウィンドウでは、**[更新する]** アイコンは、保護されるコンピューターが選択されていても更新プログラムが利用できないときには使用できません。
  >
  >

3. 選択したコンピューターに更新された保護エージェントをインストールするには、**[更新する]** を選択します。

4. ネットワークに接続されていないクライアント コンピューターでは、コンピューターがネットワークに接続されるまで、**[エージェントの状態]** 列には **[更新保留中]** の状態が表示されます。

  クライアント コンピューターがネットワークに接続された後、クライアント コンピューターの **[Agent Updates]**(エージェントの更新プログラム) 列には、**[更新中]** の状態が表示されます。
  
### <a name="move-legacy-protection-groups-from-old-version-and-sync-the-new-version-with-azure"></a>従来の保護グループを古いバージョンから移動して新しいバージョンを Azure と同期させる

Azure Backup Server と OS の両方を更新したら、Modern Backup Storage を使用して新しいデータ ソースを保護することができます。 ただし、既に保護されているデータ ソースは、Azure Backup Server と同じように従来の方法で引き続き保護されますが、すべての新しい保護では、Modern Backup Storage が使用されます。

以下の手順は、データ ソースを従来の保護モードから Modern Backup Storage に移行する手順を示しています。

• 新しいボリュームを DPM 記憶域プールに追加し、必要に応じてフレンドリ名とデータ ソース タグを割り当てます。
• レガシ モードにある各データ ソースで、そのデータ ソースの保護を停止し、[保護されるデータを保持する] オプションをオフにします。  これにより、移行後に古い回復ポイントを回復できるようになります。

• 新しい PG を作成し、新しい形式で格納されるデータ ソースを選択します。
• DPM によって、従来のバックアップ ストレージのレプリカが Modern Backup Storage ボリュームにローカルにコピーされます。
注: これは回復後の操作ジョブと見なされます。 • 新しく同期された回復ポイントが Modern Backup Storage に保存されます。
• 古い回復ポイントは、期限切れになったときに取り除かれ、最終的にディスク領域が解放されます。
• 従来のボリュームがすべて古いディスクから削除された後、Azure バックアップとシステムからディスクを削除できます。
• Azure DPMDB のバックアップを作成します。

パート 2: -重要な項目> 新しいサーバーには、元の Azure Backup Server と同じ名前を付ける必要があります。 古い記憶域プールと DPMDB を使用して回復ポイントを保持する場合、新しい Azure Backup Server の名前を変更することはできません。- DPMDB の復元が必要になるため、DPMDB のバックアップが必要です。

1) 元の Azure Backup Server をシャットダウンするか、ネットワークから切り離します。
2) Active Directory のコンピューター アカウントをリセットします。
3) 新しいコンピューターに Server 2016 をインストールし、元の Azure Backup Server と同じコンピューター名を付けます。
4) ドメインに参加します。
5) Azure Backup Server V2 をインストールします (DPM 記憶域プールのディスクを古いサーバーから移動してインポートします)。
6) パート 2 の最後に作成した DPMDB を復元します。
7) 元のバックアップ サーバーの記憶域を新しいサーバーにアタッチします。
8) SQL から DPMDB を復元します。
9) 新しいサーバーで、管理コマンド ラインから Microsoft Azure Backup のインストール場所と bin フォルダーに cd します。

パスの例: C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
to Azure backup

10) DPMSYNC -SYNC を実行します。注: DPM 記憶域プールに古いディスクを移動するではなく、新しいディスクを追加している場合は、DPMSYNC -Reallocatereplica を実行します。

## <a name="new-powershell-cmdlets-in-v2"></a>v2 の新しい PowerShell コマンドレット

Azure Backup Server v2 をインストールすると、次の 2 つの新しいコマンドレットを使用できます。 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>次のステップ

サーバーを準備する方法、またはワークロードの保護を開始する方法を説明します。
- [Backup Server ワークロードの準備](backup-azure-microsoft-azure-backup.md)
- [Backup Server を使用した VMware サーバーのバックアップ](backup-azure-backup-server-vmware.md)
- [Backup Server を使用した SQL Server のバックアップ](backup-azure-sql-mabs.md)
- [Backup Server での Modern Backup Storage の使用](backup-mabs-add-storage.md)


