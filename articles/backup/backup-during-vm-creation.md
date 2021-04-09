---
title: Azure VM の作成時にバックアップを有効にする
description: Azure Backup を使用した Azure VM の作成時にバックアップを有効にする方法について説明します。
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: ad81300545686d61f42cdd8684e502c937b4fd43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89377337"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Azure VM の作成時にバックアップを有効にする

Azure Virtual Machines (VM) をバックアップするには、Azure Backup サービスを使用します。 バックアップ ポリシーで指定されているスケジュールに従って VM がバックアップされ、バックアップから復旧ポイントが作成されます。 復旧ポイントは、Recovery Services コンテナーに格納されます。

この記事では、Azure portal で仮想マシン (VM) を作成するときにバックアップを有効にする方法について説明します。  

## <a name="before-you-start"></a>開始する前に

- VM の作成時にバックアップを有効にする場合は、どのオペレーティング システムがサポートされているかを[確認](backup-support-matrix-iaas.md#supported-backup-actions)してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

まだアカウントにサインインしていない場合は、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-vm-with-backup-configured"></a>バックアップが構成された VM を作成する

1. Azure Portal で、 **[リソースの作成]** を選択します。

2. Azure Marketplace で **[コンピューティング]** を選択し、VM イメージを選択します。

3. [Windows](../virtual-machines/windows/quick-create-portal.md) または [Linux](../virtual-machines/linux/quick-create-portal.md) の指示に従って、VM を設定します。

4. **[管理]** タブの **[バックアップの有効化]** で **[オン]** を選択します。
5. Azure Backup により、Recovery Services コンテナーにバックアップされます。 既存のコンテナーがない場合は、 **[新規作成]** を選択します。
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

>[!NOTE]
>[SSE と PMK は、Azure VM の既定の暗号化方法です](backup-encryption.md)。 Azure Backup では、これらの Azure VM のバックアップと復元がサポートされます。

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Virtual Machines の Azure Backup リソース グループ

Backup サービスでは、復元ポイント コレクション (RPC) を格納する VM のリソース グループとは異なる、別のリソース グループ (RG) が作成されます。 RPC には、マネージド VM のインスタント復旧ポイントが格納されます。 Backup サービスによって作成されるリソース グループの既定の名前付け形式は `AzureBackupRG_<Geo>_<number>` です。 次に例を示します。*AzureBackupRG_northeurope_1*。 Azure Backup によって作成されたリソース グループ名はカスタマイズできるようになりました。

注意する点:

1. RG の既定の名前を使用することも、会社の要件に従って編集することもできます。
2. VM バックアップ ポリシーの作成時には、入力として RG 名パターンを指定します。 RG 名の形式は `<alpha-numeric string>* n <alpha-numeric string>` にします。 'n' は (1 から始まる) 整数に置き換えられ、最初の RG がいっぱいになった場合はスケールアウトに使用されます。 現在、1 つの RG には、最大 600 の RPC を含めることができます。
              ![ポリシー作成時の名前の選択](./media/backup-during-vm-creation/create-policy.png)
3. このパターンでは、以下の RG の名前付け規則に従う必要があり、合計長は、RG 名の許容最大長を超えてはなりません。
    1. リソース グループ名に使用できるのは、英数字、ピリオド、アンダースコア、ハイフン、かっこのみです。 末尾をピリオドにすることはできません。
    2. リソース グループ名には、RG の名前とサフィックスを含めて、最大 74 文字を使用できます。
4. 最初の `<alpha-numeric-string>` は必須ですが、'n' の後の 2 番目のものは省略可能です。 これが適用されるのは、カスタマイズした名前を指定する場合だけです。 どちらのテキストボックスにも入力しないと、既定の名前が使用されます。
5. 必要が生じた場合、ポリシーを変更することで RG の名前を編集できます。 名前のパターンが変更されると、新しい RG の中に新しい RP が作成されます。 ただし、RP コレクションではリソースの移動がサポートされないため、古い RP は引き続き古い RG 内に存在し、移動されません。 最終的に、ポイントの有効期限が切れたときに RP のガベージ コレクションが実行されます。
![ポリシー変更時の名前の変更](./media/backup-during-vm-creation/modify-policy.png)
6. Backup サービスに使用するために作成されたリソース グループは、ロックしないことをお勧めします。

PowerShell を使用して Virtual Machines の Azure Backup リソース グループを構成するには、[スナップショットのリテンション期間中の Azure Backup リソース グループの作成](backup-azure-vms-automation.md#creating-azure-backup-resource-group-during-snapshot-retention)に関するページを参照してください。

## <a name="start-a-backup-after-creating-the-vm"></a>VM の作成後にバックアップを開始する

VM のバックアップは、バックアップ スケジュールに従って実行されます。 ただし、初回バックアップを実行することをお勧めします。

VM が作成されたら、次の操作を行います。

1. VM のプロパティで、 **[バックアップ]** を選択します。 初回バックアップが実行されるまで、VM の状態は [初回のバックアップが保留中] です。
2. オンデマンド バックアップを実行するには、 **[今すぐバックアップ]** を選択します。

    ![オンデマンド バックアップを実行する](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Resource Manager テンプレートを使用して保護された VM をデプロイする

前の手順では、Azure portal を使用して仮想マシンを作成し、Recovery Services コンテナーにそれを保護する方法について説明しました。 VM をすばやくデプロイし、Recovery Services コンテナーでそれを保護するには、[Windows VM をデプロイしてバックアップを有効にする](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)ためのテンプレートをご覧ください。

## <a name="next-steps"></a>次のステップ

VM が保護されたので、その VM を管理および復元する方法を学習してください。

- [VM の管理と監視](backup-azure-manage-vms.md)
- [VM を復元する](backup-azure-arm-restore-vms.md)

問題が発生した場合は、トラブルシューティング ガイドを[確認して](backup-azure-vms-troubleshoot.md)ください。
