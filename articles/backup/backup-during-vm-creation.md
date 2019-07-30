---
title: Azure Backup を使用した Azure VM の作成時にバックアップを有効にする
description: Azure Backup を使用した Azure VM の作成時にバックアップを有効にする方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: raynew
ms.openlocfilehash: a19653f7ae3900fd7999f347ef4d3ef710be1430
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436344"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Azure VM の作成時にバックアップを有効にする

Azure Virtual Machines (VM) をバックアップするには、Azure Backup サービスを使用します。 バックアップ ポリシーで指定されているスケジュールに従って VM がバックアップされ、バックアップから復旧ポイントが作成されます。 復旧ポイントは、Recovery Services コンテナーに格納されます。

この記事では、Azure portal で仮想マシン (VM) を作成するときにバックアップを有効にする方法について説明します。  

## <a name="before-you-start"></a>開始する前に

- VM の作成時にバックアップを有効にする場合は、どのオペレーティング システムがサポートされているかを[確認](backup-support-matrix-iaas.md#supported-backup-actions)してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

まだアカウントにサインインしていない場合は、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-vm-with-backup-configured"></a>バックアップが構成された VM を作成する

1. Azure portal で、 **[リソースの作成]** をクリックします。

2. Azure Marketplace で **[Compute]\(コンピューティング\)** をクリックし、VM イメージを選択します。

3. [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) または [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) の指示に従って、VM を設定します。

4. **[管理]** タブの **[バックアップの有効化]** で **[オン]** をクリックします。
5. Azure Backup により、Recovery Services コンテナーにバックアップされます。 既存のコンテナーがない場合は、 **[新規作成]** をクリックします。
6. 提示されたコンテナー名を採用するか、独自に指定します。
7. コンテナーが配置されるリソース グループを指定するか、作成します。 リソース グループのコンテナーは、VM のリソース グループとは異なる場合があります。

    ![VM のバックアップを有効にする](./media/backup-during-vm-creation/enable-backup.png)

8. 既定のバックアップ ポリシーを採用するか、設定を変更します。
    - バックアップ ポリシーでは、VM のバックアップ スナップショットを取得する頻度と、バックアップ コピーを保持する期間が指定されます。
    - 既定のポリシーでは、1 日に 1 回、VM がバックアップされます。
    - バックアップが毎日または毎週取得されるように、Azure VM の独自のバックアップ ポリシーをカスタマイズできます。
    - Azure VM のバックアップの考慮事項について詳しくは、[こちら](backup-azure-vms-introduction.md#backup-and-restore-considerations)を参照してください。
    - インスタント リストア機能について詳しくは、[こちら](backup-instant-restore-capability.md)を参照してください。

      ![既定のバックアップ ポリシー](./media/backup-during-vm-creation/daily-policy.png)


> [!NOTE]
> Azure Backup サービスでは、スナップショットを格納するために別のリソース グループ (VM のリソース グループ以外) が作成されます。名前の形式は **AzureBackupRG_geography_number** です (例: AzureBackupRG_northeurope_1)。 このリソース グループ内のデータは、Azure Virtual Machine Backup ポリシーの *[Retain instant recovery snapshot]\(インスタント リカバリ スナップショットの保存\)* セクションに指定されている日数の期間保持されます。  このリソース グループにロックを適用すると、バックアップが失敗する可能性があります。<br>
制限ポリシーによってリソース ポイント コレクションの作成がブロックされ、この場合もバックアップが失敗するため、このリソース グループも名前/タグの制限から除外する必要があります。


## <a name="start-a-backup-after-creating-the-vm"></a>VM の作成後にバックアップを開始する

VM のバックアップは、バックアップ スケジュールに従って実行されます。 ただし、初回バックアップを実行することをお勧めします。

VM が作成されたら、次の操作を行います。

1. VM のプロパティで、 **[バックアップ]** をクリックします。 初回バックアップが実行されるまで、VM の状態は [初回のバックアップが保留中] です
2. オンデマンド バックアップを実行するには、 **[今すぐバックアップ]** をクリックします。

    ![オンデマンド バックアップを実行する](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Resource Manager テンプレートを使用して保護された VM をデプロイする

前の手順では、Azure portal を使用して仮想マシンを作成し、Recovery Services コンテナーにそれを保護する方法について説明しました。 VM をすばやくデプロイし、Recovery Services コンテナーでそれを保護するには、[Windows VM をデプロイしてバックアップを有効にする](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)ためのテンプレートをご覧ください。



## <a name="next-steps"></a>次の手順

VM が保護されたので、その VM を管理および復元する方法を学習してください。

- [VM の管理と監視](backup-azure-manage-vms.md)
- [VM を復元する](backup-azure-arm-restore-vms.md)

問題が発生した場合は、トラブルシューティング ガイドを[確認して](backup-azure-vms-troubleshoot.md)ください。
