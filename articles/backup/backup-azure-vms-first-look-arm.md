---
title: Azure Backup サービスで VM の設定から Azure VM をバックアップする
description: Azure Backup サービスを使用して Azure VM をバックアップする方法について説明します
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: raynew
ms.openlocfilehash: 994762098027f7051591b8bf89bfa5cb7c380373
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465276"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>VM の設定から Azure VM をバックアップする

この記事では、[Azure Backup](backup-overview.md) サービスを使用して Azure VM をバックアップする方法について説明します。 いくつかの方法を使用して Azure VM をバックアップすることができます。

- 1 つの Azure VM:この記事の手順では、VM の設定から直接 Azure VM をバックアップする方法について説明します。
- 複数の Azure VM:Recovery Services コンテナーを設定して、複数の Azure VM のバックアップを構成することができます。 このシナリオについては、[こちらの記事](backup-azure-arm-vms-prepare.md)の説明に従ってください。



## <a name="before-you-start"></a>開始する前に

1. バックアップのしくみを[学習](backup-architecture.md#how-does-azure-backup-work)し、サポート要件を[確認](backup-support-matrix.md#azure-vm-backup-support)します。
2. Azure VM バックアップの[概要を理解](backup-azure-vms-introduction.md)します。

### <a name="azure-vm-agent-installation"></a>Azure VM エージェントのインストール

Azure VM をバックアップするため、Azure Backup ではマシンで実行されている VM エージェントの拡張機能がインストールされます。 VM が Azure Marketplace のイメージから作成されている場合は、エージェントが実行されます。 カスタム VM を作成するときや、オンプレミスからマシンを移行するときなど、場合によっては、 エージェントの手動インストールが必要になることがあります。

- VM エージェントを手動でインストールする必要がある場合は、[Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) VM または [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) VM の指示に従ってください。
- エージェントがインストールされた後、バックアップを有効にすると、Azure Backup によってエージェントにバックアップ拡張機能がインストールされます。 ユーザーが何もしなくても、拡張機能に更新プログラムと修正プログラムが適用されます。

## <a name="back-up-from-azure-vm-settings"></a>Azure VM の設定からバックアップする


1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. **[すべてのサービス]** をクリックし、[フィルター] に「**Virtual machines**」と入力して、"**Virtual Machines**" をクリックします。
3. VM の一覧から、バックアップする VM を選択します。
4. VM のメニューで、 **[バックアップ]** をクリックします。
5. **[Recovery Services コンテナー]** で次のようにします。
   - コンテナーが既にある場合は、 **[既存のものを選択]** をクリックしてコンテナーを選択します。
   - コンテナーがない場合は、 **[新規作成]** をクリックします。 コンテナーの名前を指定します。 VM と同じリージョンおよびリソース グループに作成されます。 VM の設定からの直接バックアップを有効にするときは、これらの設定を変更できません。

   ![バックアップの有効化ウィザード](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. **[バックアップ ポリシーの選択]** で次のようにします。

   - 既定のポリシーをそのままにします。 これにより、1 日に 1 回指定時刻に VM がバックアップされ、30 日間コンテナーに保持されます。
   - ある場合は、既存のバックアップ ポリシーを選択します。
   - 新しいポリシーを作成し、ポリシーの設定を定義します。  

   ![Select backup policy](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. **[バックアップの有効化]** をクリックします。 これにより、バックアップ ポリシーと VM が関連付けられます。

    ![[バックアップの有効化] ボタン](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. ポータルの通知で構成の進行状況を追跡できます。
9. ジョブが完了した後、VM のメニューで **[バックアップ]** をクリックします。 ページに、VM のバックアップの状態、復旧ポイントに関する情報、実行されているジョブ、発行されたアラートが表示されます。

   ![バックアップの状態](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. バックアップを有効にした後は、初回バックアップが実行されます。 初回バックアップは、すぐに開始することも、バックアップ スケジュールに従って開始されるまで待つこともできます。
    - 初回バックアップが完了するまで、 **[前回のバックアップの状態]** には **[警告 (初回のバックアップが保留中)]** と表示されます。
    - 次のスケジュールされたバックアップがいつ実行されるかを確認するには、バックアップ ポリシー名をクリックします。


> [!NOTE]
> Azure Backup サービスでは、スナップショットを格納するために別のリソース グループ (VM のリソース グループ以外) が作成されます。名前の形式は **AzureBackupRG_geography_number** です (例: AzureBackupRG_northeurope_1)。 このリソース グループ内のデータは、Azure Virtual Machine Backup ポリシーの "インスタント リカバリ スナップショットの保存" セクションに指定されている日数の期間保持されます。 このリソース グループにロックを適用すると、バックアップが失敗する可能性があります。<br>
制限ポリシーによってリソース ポイント コレクションの作成がブロックされ、この場合もバックアップが失敗するため、このリソース グループも名前/タグの制限から除外する必要があります。


## <a name="run-a-backup-immediately"></a>バックアップをすぐに実行する

1. バックアップをすぐに実行するには、VM のメニューで、 **[バックアップ]**  >  **[今すぐバックアップ]** をクリックします。

    ![バックアップを実行する](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. **[今すぐバックアップ]** で、カレンダー コントロールを使用して復旧ポイントを保持する期間を選択し、 **[OK]** をクリックします。

    ![バックアップ リテンション期間の期限](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. ポータルの通知を使用すると、バックアップ ジョブがトリガーされたことを知ることができます。 バックアップの進行状況を監視するには、 **[すべてのジョブの表示]** をクリックします。




## <a name="back-up-from-the-recovery-services-vault"></a>Recovery Services コンテナーからバックアップする

この記事の手順に従って、Azure Backup Recovery Services コンテナーを設定することによって Azure VM のバックアップを有効にし、コンテナーでバックアップを有効にします。

## <a name="next-steps"></a>次の手順

- この記事のいずれかの手順で問題がある場合は、[トラブルシューティング ガイド](backup-azure-vms-troubleshoot.md)に関する記事をご覧ください。
- バックアップの管理について[学習してください](backup-azure-manage-vms.md)。
