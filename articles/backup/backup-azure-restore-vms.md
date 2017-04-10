---
title: "バックアップから Azure 仮想マシンを復元する | Microsoft Docs"
description: "回復ポイントから Azure 仮想マシンを復元する方法について説明します"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "バックアップの復元, 復元する方法, 回復ポイント"
ms.assetid: fed877b3-b496-49fb-99e4-653be7c23e3a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: trinadhk; jimpark;
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 64557a71f30762befe07616c3d274a621f22e235
ms.lasthandoff: 03/27/2017


---
# <a name="restore-virtual-machines-in-azure"></a>Azure での仮想マシンの復元
> [!div class="op_single_selector"]
> * [Azure ポータルでの VM の復元](backup-azure-arm-restore-vms.md)
> * [クラシック ポータルでの VM の復元](backup-azure-restore-vms.md)
> 
> 

Azure Backup コンテナーに格納されているバックアップから新しい VM に仮想マシンを復元するには、以下の手順を実行します。

## <a name="restore-workflow"></a>ワークフローの復元
### <a name="step-1-choose-an-item-to-restore"></a>手順 1. 復元する項目を選択する
1. **[保護された項目]** タブに移動し、新しい VM に復元する仮想マシンを選択します。
   
    ![保護された項目](./media/backup-azure-restore-vms/protected-items.png)
   
    **[保護された項目]** ページの **[回復ポイント]** 列には、仮想マシンの回復ポイント数が表示されます。 **[最新の回復ポイント]** 列には、仮想マシンを復元できる最新のバックアップ時刻が表示されます。
2. **[復元]** をクリックすると **[復元するアイテム]** ウィザードが開きます。
   
    ![[復元するアイテム]](./media/backup-azure-restore-vms/restore-item.png)

### <a name="step-2-pick-a-recovery-point"></a>手順 2. 回復ポイントを選択する
1. **[回復ポイントの選択]** 画面で、最新の回復ポイントまたは以前の特定の時点から復元できます。 ウィザードが開いたときに選択されている既定のオプションは、 *[最新の復旧ポイント]*です。
   
    ![[回復ポイントの選択]](./media/backup-azure-restore-vms/select-recovery-point.png)
2. 以前の特定の時点を選択するには、ドロップダウン リストで **[日付の選択]** オプションを選択し、**カレンダー アイコン**をクリックしてカレンダー コントロールから日付を選択します。 コントロール内の回復ポイントが設定されているすべての日付は、淡い灰色の網掛けで表示され、選択可能です。
   
    ![日付を選択する](./media/backup-azure-restore-vms/select-date.png)
   
    カレンダー コントロールの日付をクリックすると、その日付の使用可能な回復ポイントが下の回復ポイント テーブルに表示されます。 **[時間]** 列は、スナップショットが作成された日時を示します。 **[種類]** 列は、回復ポイントの [整合性](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) を示します。 テーブル ヘッダーのかっこ内は、その日に使用可能な回復ポイントの数を示します。
   
    ![[回復ポイント]](./media/backup-azure-restore-vms/recovery-points.png)
3. **[回復ポイント]** テーブルから回復ポイントを選択し、次へ進む矢印をクリックして次の画面に移動します。

### <a name="step-3-specify-a-destination-location"></a>手順 3. コピー先の場所を指定する
1. **[Select restore instance]** 画面で、仮想マシンを復元する場所の詳細を指定します。
   
   * 仮想マシン名を指定する: 特定のクラウド サービスでは、仮想マシンの名前を一意にする必要があります。 既存の VM の上書きはサポートされていません。 
   * 仮想マシンのクラウド サービスを選択する: これは仮想マシンを作成するために必須です。 既存のクラウド サービスを使用するか、新しいクラウド サービスを作成するかを選択できます。
     
        クラウド サービス名の選択は、グローバルに一意である必要があります。 通常、クラウド サービス名は、[cloudservice].cloudapp.net という形式の公開された URL に関連付けられます。 Azure では、名前が既に使用されている場合に、新しいクラウド サービスを作成することはできません。 新しいクラウド サービスを作成することを選択した場合、その名前は仮想マシンと同じ名前になります。その場合、選択した VM には、関連するクラウド サービスに適用できる程度に一意の名前を付ける必要があります。
     
        復元インスタンス詳細のアフィニティ グループに関連しないクラウド サービスや仮想ネットワークのみが表示されます。 [詳細情報](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)
2. 仮想マシンのストレージ アカウントを選択する: これは仮想マシンを作成するために必須です。 Azure Backup 資格情報コンテナーと同じリージョン内の既存のストレージ アカウントから選択できます。 ゾーン冗長または Premium Storage タイプのストレージ アカウントはサポートされません。
   
    サポートされている構成のストレージ アカウントがない場合は、復元操作を開始する前に、サポートされている構成のストレージ アカウントを作成してください。
   
    ![仮想ネットワークを作成する](./media/backup-azure-restore-vms/restore-sa.png)
3. 仮想ネットワークを選択する: 仮想マシンの作成時に、仮想マシンの仮想ネットワーク (VNET) を選択する必要があります。 復元 UI には、このサブスクリプション内の使用できるすべての VNET が表示されます。 復元された仮想マシンの VNET を選択する必要はありません。VNET が適用されない場合でも、インターネット経由で復元された仮想マシンに接続できます。
   
    選択したクラウド サービスが仮想ネットワークに関連付けられている場合は、仮想ネットワークを変更できません。
   
    ![仮想ネットワークを作成する](./media/backup-azure-restore-vms/restore-cs-vnet.png)
4. サブネットを選択する: VNET にサブネットがある場合、既定では最初のサブネットが選択されます。 ドロップダウン リストのオプションから、任意のサブネットを選択します。 サブネットの詳細については、 [ポータルのホーム ページ](https://manage.windowsazure.com/)の [Networks] 拡張機能から **[Virtual Networks]** に移動し、仮想ネットワークを選択して、[構成] をドリルダウンしてサブネットの詳細を表示します。
   
    ![サブネットを選択する](./media/backup-azure-restore-vms/select-subnet.png)
5. ウィザードの **[送信]** アイコンをクリックして詳細情報を送信し、復元ジョブを作成します。

## <a name="track-the-restore-operation"></a>復元操作を追跡する
復元ウィザードにすべての情報を入力して送信すると、Azure Backup は復元操作を追跡するジョブの作成を試みます。

![復元ジョブの作成](./media/backup-azure-restore-vms/create-restore-job.png)

ジョブの作成に成功すると、ジョブが作成されたことを示すトースト通知が表示されます。 **[ジョブの表示]** ボタンをクリックすると、**[ジョブ]** タブに詳細情報が表示されます。

![作成された復元ジョブ](./media/backup-azure-restore-vms/restore-job-created.png)

復元操作が完了すると、 **[ジョブ]** タブに完了マークが付けられます。

![復元ジョブの完了](./media/backup-azure-restore-vms/restore-job-complete.png)

仮想マシンを復元したら、元の仮想マシンにある拡張機能を再インストールし、Azure ポータルで仮想マシンの [エンドポイントを変更する](../virtual-machines/windows/classic/setup-endpoints.md) 必要がある場合があります。

## <a name="post-restore-steps"></a>復元後の手順
Ubuntu など cloud-init ベースの Linux ディストリビューションを使用している場合、セキュリティ上の理由から、復元後にパスワードがブロックされます。 復元した VM は、VMAccess 拡張機能を使用して [パスワードをリセット](../virtual-machines/linux/classic/reset-access.md)してください。 これらのディストリビューションでは、SSH キーを使用して、復元後のパスワード リセットを回避するようお勧めします。 

## <a name="backup-for-restored-vms"></a>復元された VM のバックアップ
最初にバックアップされた VM と同じ名前で同じクラウド サービスに VM を復元した場合、復元後も VM に対するバックアップは引き続き行われます。 別のクラウド サービスに VM を復元した場合、または復元された VM に別の名前を指定した場合、この VM は新しい VM として扱われるので、復元された VM に対してバックアップをセットアップする必要があります。

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>Azure データ センターにおける障害発生時の VM の復元
VM が稼働しているプライマリ データ センターが被災した場合、Backup コンテナーが geo 冗長に構成されていると、Azure Backup ではバックアップされた VM をペアのデータセンターに復元することができます。 このようなシナリオでは、ペアのデータセンター内に存在するストレージ アカウントを選択する必要があります。これ以外の復元処理は同じとなります。 Azure Backup では、ペアの geo からコンピューティング サービスを使って、復元された仮想マシンを作成します。 詳しくは、[Azure データ センターの回復性に関するページ](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)をご覧ください。

## <a name="restoring-domain-controller-vms"></a>ドメイン コントローラー の VM の復元
ドメイン コントローラー (DC) の仮想マシンのバックアップは、Azure Backup でサポートされているシナリオです。 ただし、この復元プロセスではいくつか注意が必要です。 復元操作は、シングル DC 構成でのドメイン コントローラーの VM と、マルチ DC 構成でのドメイン コントローラーの VM とで大きく異なります。

### <a name="single-dc"></a>シングル DC
VM は、(他の VM と同様に) Azure ポータルから復元するか、または PowerShell を使用して復元することができます。

### <a name="multiple-dcs"></a>マルチ DC
マルチ DC 環境の場合、ドメイン コントローラーによってデータの同期方法が異なります。 *適切な対策を講じずに*以前のバックアップ ポイントを復元すると、USN ロールバック プロセスによってマルチ DC 環境でエラーが生じるおそれがあります。 このような VM を復元する適切な方法は、DSRM モードでの起動です。

ここで、Azure に DSRM モードがないことが問題となります。 したがって、このような VM を復元する場合に Azure ポータルは使用できません。 サポートされている復元メカニズムは、PowerShell を使用したディスクベースの復元のみです。

> [!WARNING]
> マルチ DC 環境のドメイン コントローラーの VM を復元する場合、Azure ポータルは使用しないでください。 PowerShell ベースの復元のみがサポートされています。
> 
> 

詳細については、 [USN ロールバックの問題に関するトピック](https://technet.microsoft.com/library/dd363553) を参照し、問題の解決案をご覧ください。

## <a name="restoring-vms-with-special-network-configurations"></a>特別なネットワーク構成を持つ VM の復元
Azure Backup は、次のように特殊なネットワーク構成の仮想マシンのバックアップをサポートしています。

* ロード バランサー (内部および外部) の対象になっている VM
* 予約済み IP が複数ある VM
* NIC が複数ある VM

このような構成の場合、復元時に次の点を考慮する必要があります。

> [!TIP]
> 復元後に特殊なネットワーク構成の VM を再作成するには、PowerShell ベースの復元フローを使用してください。
> 
> 

### <a name="restoring-from-the-ui"></a>UI からの復元:
UI から復元する場合は、 **常に新しいクラウド サービスを選択してください**。 ポータルの復元フローでは、必須のパラメーターのみが使用されるので、UI を使用して VM を復元した場合、元の特殊なネットワーク構成は失われます。 つまり、復元される VM は、ロード バランサー、複数の NIC、複数の予約済み IP などの構成がない、通常の VM になります。

### <a name="restoring-from-powershell"></a>PowerShell からの復元:
PowerShell には仮想マシンを作成する機能はなく、バックアップから VM ディスクを復元する機能しかありません。 そのため、前述したような特殊なネットワーク構成が必要な仮想マシンを復元するときに役立ちます。

ディスクの復元後に仮想マシンを完全に再作成するには、次の手順を実行します。

1. [Azure Backup PowerShell](backup-azure-vms-classic-automation.md#restore-an-azure-vm) を使用してバックアップ資格情報コンテナーからディスクを復元します。
2. PowerShell コマンドレットを使用して、ロード バランサー、複数の NIC、複数の予約済み IP に必要な VM 構成を作成し、その構成を使用して、目的の構成の VM を作成します。
   
   * [内部ロード バランサー ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * [インターネットに接続するロード バランサー](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)に接続する VM を作成する
   * [NIC が複数](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * [予約済み IP が複数](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>次のステップ
* [エラーのトラブルシューティング](backup-azure-vms-troubleshoot.md#restore)
* [仮想マシンの管理](backup-azure-manage-vms.md)


