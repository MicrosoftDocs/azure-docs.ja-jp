---
title: "Azure Backup: Azure Portal を使用して仮想マシンを復元する | Microsoft Docs"
description: "Azure ポータルを使用して復旧ポイントから Azure 仮想マシンを復元する"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "バックアップの復元, 復元する方法, 回復ポイント"
ms.assetid: 372b87c6-3544-4dc5-bbc9-c742ca502159
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/12/2017
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 1dc2883056eab9764cda674b42fa40c517550ccd
ms.lasthandoff: 03/27/2017


---
# <a name="use-azure-portal-to-restore-virtual-machines"></a>Azure ポータルを使用した仮想マシンの復元
> [!div class="op_single_selector"]
> * [クラシック ポータルでの VM の復元](backup-azure-restore-vms.md)
> * [Azure ポータルでの VM の復元](backup-azure-arm-restore-vms.md)
>
>

定義された間隔でデータのスナップショットを取得してデータを保護します。 これらのスナップショットは復旧ポイントと呼ばれ、Recovery Services コンテナーに格納されます。 VM を修復または再構築する必要がある場合は、保存されている復旧ポイントのいずれかから VM を復元することができます。 復旧ポイントを復元すると、バックアップされた VM のポイントインタイム表現である新しい VM を作成するか、ディスクを復元し、そのディスクに付属するテンプレートを使用して、復元された VM をカスタマイズするか、個別にファイルを回復できます。 この記事では、VM を新しい VM に復元するか、バックアップされたディスクすべてを復元する方法について説明します。 ファイルを個別に回復する方法については、[Azure VM バックアップからのファイルの回復](backup-azure-restore-files-from-vm.md)に関するページをご覧ください

![3-ways-restore-from-vm-backup](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure には、リソースの作成と操作に関して 2 種類のデプロイメント モデルがあります。[Resource Manager デプロイメント モデルとクラシック デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、Resource Manager モデルを使用してデプロイされた VM を復元するための情報および手順を示しています。
>
>

VM バックアップから VM またはすべてのディスクを復元するには、次の 2 つの手順を実行します。

1. 復元を行うための復元ポイントを選択する
2. 復元タイプの選択 - 新しい VM を作成するかディスクを復元し、必要なパラメーターを指定する。 

## <a name="select-restore-point-for-restore"></a>復元を行うための復元ポイントを選択する
1. [Azure ポータル](http://portal.azure.com/)
2. Azure メニューで **[参照]** をクリックし、サービスの一覧で「**Recovery Services**」と入力します。 入力した文字列に合わせて、サービスの一覧の内容が変更されます。 **[Recovery Services コンテナー]**が表示されたら、それを選択します。

    ![Open Recovery Services vault](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    サブスクリプションに含まれるコンテナーの一覧が表示されます。

    ![List of Recovery Services vaults](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. 一覧から、復元する VM に関連付けられているコンテナーを選択します。 コンテナーをクリックすると、ダッシュボードが開きます。

    ![List of Recovery Services vaults](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. コンテナーのダッシュボードが表示されています。 **[バックアップ項目]** タイルの **[Azure Virtual Machines]** をクリックして、コンテナーに関連付けられている VM を表示します。

    ![vault dashboard](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    **[バックアップ項目]** ブレードが開き、Azure の仮想マシンの一覧が表示されます。

    ![list of VMs in vault](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. 一覧から VM を選択して、ダッシュボードを開きます。 VM のダッシュボードが開き、[復元ポイント] タイルを含む [監視] 領域が表示されます。

    ![list of VMs in vault](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. VM のダッシュボードのメニューで、 **[復元]**

    ![list of VMs in vault](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    [復元] ブレードが開きます。

    ![restore blade](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. **[復元]** ブレードで **[復元ポイント]** をクリックして、**[復元ポイントの選択]** ブレードを開きます。

    ![restore blade](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    既定では、ダイアログ ボックスには過去 30 日間のすべての復元ポイントが表示されます。 表示される復元ポイントの時間範囲を変更するには、 **[フィルター]** を使用します。 既定では、すべての整合性の復元ポイントが表示されます。 復元ポイントの特定の整合性を選択するには、 **[すべての復元ポイント]** フィルターを変更します。 各種の復元ポイントの詳細については、[データの整合性](backup-azure-vms-introduction.md#data-consistency)の説明を参照してください。  

   * **[復元ポイントの整合性]** は、次の一覧から選択します。
     * クラッシュ整合性の復元ポイント
     * アプリケーション整合性の復元ポイント
     * ファイル システム整合性の復元ポイント
     * すべての復元ポイント  
8. 復元ポイントを選択し、 **[OK]**をクリックします。

    ![復元ポイントの選択](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    **[復元]** ブレードに、設定した復元ポイントが表示されます。

    ![復元ポイントが設定される](./media/backup-azure-arm-restore-vms/recovery-point-set.png)
9. 復元ポイントを設定すると、**[復元]** ブレードに **[復元の構成]** が自動的に開きます。

## <a name="choosing-a-vm-restore-configuration"></a>VM の復元構成の選択
復元ポイントを選択したので、次に復元 VM の構成を選択します。 復元 VM の構成には、Azure ポータルまたは PowerShell を使用できます。

1. 現在 **[復元]** ブレードを表示していない場合は、表示します。 いずれかの[復元ポイントが選択されている](#select-restore-point-for-restore)状態で、**[復元の構成]** をクリックして **[復元の構成]** ブレードを開きます。

    ![recovery configuration wizard is set](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. **[復元の構成]** ブレードには、次の 2 つの選択肢があります。
   * 完全な仮想マシンの復元
   * バックアップ ディスクの復元

ポータルには、復元される VM の [簡易作成] オプションがあります。 VM 構成をカスタマイズする、または新しい VM 選択肢の作成操作の一環として作成されたリソースの名前をカスタマイズするには、PowerShell またはポータルを使用してバックアップ ディスクを復元し、PowerShell コマンドを使用してそのディスクを VM 構成の選択肢に接続するか、復元ディスクに付属するテンプレートを使用して、復元された VM をカスタマイズします。 複数の NIC を持つ VM またはロード バランサーの VM を復元する方法の詳細については、「[特別なネットワーク構成を持つ VM の復元](#restoring-vms-with-special-network-configurations)」を参照してください。 
 
## <a name="create-a-new-vm-from-restore-point"></a>復元ポイントから新しい VM を作成する
復元ポイントをまだ選択していない場合は、新しい VM を作成する前に、[復元ポイントを選択](#restoring-vms-with-special-network-configurations)しておきます。 復元ポイントが作成されたら、**[復元の構成]** ブレードで、次の各フィールドの値を入力または選択します。

* **[Restore Type (復元の種類)]** - [Create virtual machine (仮想マシンの作成)]。
* **[仮想マシン名]** - VM の名前を指定します。 この名前は、リソース グループ (Resource Manager でデプロイされた VM の場合) またはクラウド サービス (クラシック VM の場合) に対して一意である必要があります。 仮想マシンが既にサブスクリプションに存在する場合、それを置き換えることはできません。
* **[リソース グループ]** - 既存のリソース グループを使用するか、新しいリソース グループを作成します。 クラシック VM を復元する場合は、このフィールドを使用して、新しいクラウド サービスの名前を指定します。 新しいリソース グループまたはクラウド サービスを作成する場合、名前はグローバルに一意である必要があります。 通常、クラウド サービス名は、公開された URL に関連付けられています (例: [cloudservice].cloudapp.net)。 既に使用されているクラウド リソース グループまたはクラウド サービスの名前を指定した場合、Azure はリソース グループまたはクラウド サービスに VM と同じ名前を割り当てます。 一覧には、どのアフィニティ グループにも関連付けられていないリソース グループまたはクラウド サービス、および仮想マシンが表示されます。 詳細については、「 [アフィニティ グループから、リージョン Virtual Network (VNet) に移行する方法](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)」を参照してください。
* **[Virtual Network]** - VM を作成する場合は、Virtual Network (VNET) を選択します。 このフィールドには、サブスクリプションに関連付けられているすべての VNET が表示されます。 VM のリソース グループがかっこ内に表示されます。
* **[サブネット]** - VNET にサブネットがある場合は、最初のサブネットが既定で選択されています。 追加のサブネットがある場合は、必要なサブネットを選択します。
* **[ストレージ アカウント]** - このメニューには、Recovery Services コンテナーと同じ場所にあるストレージ アカウントが表示されます。 ゾーン冗長であるストレージ アカウントはサポートされていません。 Recovery Services コンテナーと同じ場所を共有するストレージ アカウントがない場合は、復元操作を開始する前にアカウントを作成する必要があります。 ストレージ アカウントのレプリケーションの種類がかっこ内に表示されます。

![復元構成ウィザードが設定される](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

> [!NOTE]
> Resource Manager でデプロイされた VM を復元する場合は、仮想ネットワーク (VNET) を特定する必要があります。 クラシック VM の場合には、仮想ネットワーク (VNET) は省略可能です。
>
>

**[復元の構成]** ブレードで **[OK]** をクリックして、復元の構成を完了します。 **[復元]** ブレードで **[復元]** をクリックして、復元操作を開始します。

## <a name="restore-backed-up-disks"></a>バックアップ ディスクの復元
バックアップ ディスクから作成する仮想マシンに、[復元の構成] ブレードに用意されている以外のカスタマイズを加える場合は、**[Restore Type (復元の種類)]** の値として **[Restore disks (ディスクの復元)]** を選択します。 選択すると、バックアップからのディスクのコピー先となるストレージ アカウントを指定するよう求められます。 ストレージ アカウントを選択するときは、Recovery Services コンテナーと同じ場所を共有するアカウントを選択します。 ゾーン冗長であるストレージ アカウントはサポートされていません。 Recovery Services コンテナーと同じ場所を共有するストレージ アカウントがない場合は、復元操作を開始する前にアカウントを作成する必要があります。 ストレージ アカウントのレプリケーションの種類がかっこ内に表示されます。

復元操作が完了すると、次のことができます。
* [テンプレートを使用して、復元された VM をカスタマイズする](#use-templates-to-customize-restore-vm)
* [復元ディスクを使用して、既存の仮想マシンにアタッチする](../virtual-machines/virtual-machines-windows-attach-disk-portal.md)
* [PowerShell を使用して、復元されたディスクから新しい仮想マシンを作成する](./backup-azure-vms-automation.md#restore-an-azure-vm)

**[復元の構成]** ブレードで **[OK]** をクリックして、復元の構成を完了します。 **[復元]** ブレードで **[復元]** をクリックして、復元操作を開始します。

![Recovery configuration completed](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>復元操作を追跡する
復元操作を開始すると、Backup サービスによって、復元操作を追跡するためのジョブが作成されます。 また、通知が作成されて、ポータルの [通知] 領域に一時的に表示されます。 通知が表示されない場合は、いつでも通知アイコンをクリックすることで通知を表示できます。

![Restore triggered](./media/backup-azure-arm-restore-vms/restore-notification.png)

操作を処理中に表示するか、または処理の完了後に表示するには、バックアップ ジョブの一覧を開きます。

1. Azure メニューで **[参照]** をクリックし、サービスの一覧で「**Recovery Services**」と入力します。 入力した文字列に合わせて、サービスの一覧の内容が変更されます。 **[Recovery Services コンテナー]**が表示されたら、それを選択します。

    ![Open Recovery Services vault](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    サブスクリプションに含まれるコンテナーの一覧が表示されます。

    ![List of Recovery Services vaults](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. 一覧から、復元した VM に関連付けられているコンテナーを選択します。 コンテナーをクリックすると、ダッシュボードが開きます。
3. コンテナーのダッシュボードの **[バックアップ ジョブ]** タイルで **[Azure Virtual Machines]** をクリックして、コンテナーに関連付けられているジョブを表示します。

    ![vault dashboard](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    **[バックアップ ジョブ]** ブレードが開き、ジョブの一覧が表示されます。

    ![list of VMs in vault](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-restore-vm"></a>テンプレートを使用して復元 VM をカスタマイズする
[ディスクの復元操作が完了](#Track-the-restore-operation)したら、復元操作の一環として生成されたテンプレートを使用して、バックアップ構成とは異なる構成で新しい VM を作成するか、復元ポイントからの新しい VM の作成時に作成されたリソースの名前をカスタマイズできます。 

> [!NOTE]
> テンプレートは、2017 年 3 月 1 日以降に作成された回復ポイントに対する復元ディスクの一部として追加されます。 こうしたテンプレートは、暗号化されていない管理対象外ディスク VM に適用できます。 暗号化された VM と管理対象ディスク については、今後のリリースでサポートされる予定です。 
>
>

復元ディスクのオプションとして生成されたテンプレートを取得するには

1. 復元ジョブに対応するジョブの詳細に移動します。 
2. テンプレートをダウンロードできるテンプレート URI の一覧が表示されます。 この値の中でコンテナーの名前に注意してください。 

     ![復元ジョブのドリルダウン](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
     
3. 対象のストレージ アカウント名、コンテナー名、BLOB テンプレートの URI をメモしておきます。 "*対象のストレージ アカウント > BLOB を選択 > コンテナー*" のファイルに移動し、名前が *azuredeploy* で始まるファイルをダウンロードします。

    ![download-template-storage-account](./media/backup-azure-arm-restore-vms/download-template.png)
    
   または、[Azure ストレージ エクスプローラー](http://storageexplorer.com/)を使用して、対応するサブスクリプション > 対象のストレージ アカウント > BLOB コンテナーの順に移動し、上記の手順でメモしておいたコンテナー名を選択します。 コンテナー内のファイルが表示されている右側のウィンドウで、名前が *azuredeploy* で始まるファイルをダウンロードします。 
   
   ![download-template-storage-explorer](./media/backup-azure-arm-restore-vms/template-storage-explorer-download.png)
     
テンプレートがダウンロードされたら、テンプレート デプロイを使用して[テンプレートを編集およびデプロイ](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)するか、デプロイする前に[テンプレートを作成](../azure-resource-manager/resource-group-authoring-templates.md)することで、さらにカスタマイズします。 [ファイルの読み込み] を使用すると、上記でダウンロードしたテンプレートをデプロイできます。 

   ![テンプレート デプロイの読み込み](./media/backup-azure-arm-restore-vms/loading-template.png)
   
必要な値を入力し、"*使用条件*" に同意して、**[購入]** をクリックします。

   ![テンプレート デプロイの送信](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>復元後の手順
* Ubuntu など cloud-init ベースの Linux ディストリビューションを使用している場合、セキュリティ上の理由から、復元後にパスワードがブロックされます。 復元した VM は、VMAccess 拡張機能を使用して [パスワードをリセット](../virtual-machines/linux/classic/reset-access.md)してください。 これらのディストリビューションでは、SSH キーを使用して、復元後のパスワード リセットを回避するようお勧めします。
* バックアップの構成の間に存在した拡張機能はインストールされますが、有効にはされません。 問題がある場合、拡張機能を再インストールしてください。 
* バックアップされる VM に静的 IP がある場合は、復元後、復元された VM を作成するときの競合を避けるため、復元される VM には動的 IP が設定されます。 [復元された VM に静的 IP を追加する](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)方法を参照してください。
* 復元された VM には可用性の値が設定されていません。 復元ディスク オプションを使い、復元されたディスクを使って PowerShell またはテンプレートから VM を作成するときに[可用性セットを追加する](../virtual-machines/virtual-machines-windows-create-availability-set.md#use-powershell-to-create-an-availability-set)ことをお勧めします。 

## <a name="backup-for-restored-vms"></a>復元された VM のバックアップ
最初にバックアップされた VM と同じ名前で同じリソース グループに VM を復元した場合、復元後も VM に対するバックアップは引き続き行われます。 別のリソース グループに VM を復元した場合、または復元された VM に別の名前を指定した場合、この VM は新しい VM として扱われるので、復元された VM に対してバックアップをセットアップする必要があります。

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>Azure データ センターにおける障害発生時の VM の復元
VM が稼働しているプライマリ データ センターが被災した場合、Backup コンテナーが geo 冗長に構成されていると、Azure Backup ではバックアップされた VM をペアのデータセンターに復元することができます。 このようなシナリオでは、ペアのデータ センター内に存在するストレージ アカウントを選択する必要があります。これ以外の復元処理は同じとなります。 Azure Backup では、ペアの geo からコンピューティング サービスを使って、復元された仮想マシンを作成します。 詳しくは、[Azure データ センターの回復性に関するページ](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)をご覧ください。

## <a name="restoring-vms-with-special-network-configurations"></a>特別なネットワーク構成を持つ VM の復元
次のような特別なネットワーク構成を持つ VM をバックアップして復元することができます。 ただし、これらの構成では、復元プロセスを実行するときにいくつかの特別な配慮が必要となります。

* ロード バランサー (内部および外部) の対象になっている VM
* 予約済み IP が複数ある VM
* NIC が複数ある VM

> [!IMPORTANT]
> VM に対して特別なネットワーク構成を作成するときは、PowerShell を使用して、復元されたディスクから VM を作成する必要があります。
>
>

ディスクへの復元後に仮想マシンを完全に作成し直すには、次の手順を実行します。

1. [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm) を使用して、Recovery Services コンテナーからディスクを復元します。
2. PowerShell コマンドレットを使用して、ロード バランサー、複数の NIC、複数の予約済み IP に必要な VM 構成を作成し、その構成を使用して、目的の構成の VM を作成します。

   * [内部ロード バランサー ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * [インターネットに接続するロード バランサー](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)に接続する VM を作成する
   * [NIC が複数](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * [予約済み IP が複数](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>次のステップ
これで、VM を復元することができます。VM で発生する一般的なエラーについては、トラブルシューティングの記事を参照してください。 また、VM を使用したタスク管理に関する記事もご覧ください。

* [エラーのトラブルシューティング](backup-azure-vms-troubleshoot.md#restore)
* [仮想マシンの管理](backup-azure-manage-vms.md)

