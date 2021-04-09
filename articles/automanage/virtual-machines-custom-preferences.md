---
title: Azure Automanage for VMs でカスタム設定を作成する
description: Azure Automanage で環境構成を調整し、独自の基本設定を指定する方法について説明します。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 584a3503bf736fcf727a169611e6c79e0c374c90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101647933"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Azure Automanage for VMs でカスタム設定を作成する

Azure Automanage for virtual machines のベスト プラクティスには、必要に応じて調整できる既定の環境があります。 この記事では、新規または既存の VM で自動管理を有効にするときに、独自の基本設定を指定する方法について説明します。

現在、[Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) と [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration) のカスタマイズをサポートしています。


> [!NOTE]
> Automanage が有効になっている間は、VM の環境または基本設定を変更することはできません。 その VM の Automanage を無効にしてから、必要な構成環境と基本設定を使用して Automanage を再度有効にする必要があります。


## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)してください。

> [!NOTE]
> 無料試用版アカウントでは、このチュートリアルで使用されている仮想マシンを利用できません。 従量課金制サブスクリプションにアップグレードしてください。

> [!IMPORTANT]
> Automanage を有効にするには、次の Azure RBAC アクセス許可が必要です。**所有者** ロール、または **共同作成者** ロールと **ユーザー アクセス管理者** ロールの併用。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com/) にサインインします。


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>既存の VM で VM に対して Automanage を有効にする

1. 検索バーで、 **[Automanage - Azure machine best practices]\(Automanage - Azure マシンのベスト プラクティスの有効化\)** を検索して選択します。

2. **[Enable on existing VM]\(既存の VM で有効にする\)** を選択します。

3. **[マシンの選択]** ブレードで次のようにします。
    1. 実際の **サブスクリプション** と **リソース グループ** で VM のリストにフィルターをかけます。
    1. オンボードする各仮想マシンのチェック ボックスをオンにします。
    1. **[選択]** ボタンをクリックします。

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="使用可能な VM のリストから既存の VM を選択します。":::

    > [!NOTE]
    > **[Show ineligible machines]\(該当しないマシンを表示する\)** をクリックして、サポートされないマシンの一覧とその理由を確認します。 

4. **[構成]** で、 **[Compare Environments]\(環境の比較\)** をクリックします。

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="[Compare Environments]\(環境の比較\)。":::

5. **[環境の詳細]** ブレードで、ドロップダウン メニューから環境 (テストには *[Dev/Test]* 、運用には *[Prod]\(運用\)* ) を選択し、 **[OK]** をクリックします

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="運用環境を参照する。":::

6. 環境を選択したら、 **[Configuration preferences]\(構成の基本設定\)** を選択できます。 既定では、Azure のベスト プラクティスの設定が使用されます。 この基本設定には、各サービスに推奨される設定が含まれています。 カスタムの基本設定を作成して、これらの設定を変更します。 
    1. **[Create new preferences]\(新しい基本設定の作成\)** をクリックします。
    1. **[Create a configuration preference]\(構成の基本設定の作成\)** ブレードで、[基本] タブに入力します。
        1. サブスクリプション
        1. Resource group
        1. Preference name (基本設定の名前)
        1. Region

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="構成の基本設定を入力します。":::

7. 必要に応じて構成の基本設定を調整します。
        
    > [!NOTE]
    > 環境の構成を変更する場合は、ベスト プラクティスの上限と下限の範囲内に収まる調整のみが許可されます。

8. 構成の詳細を確認します。
9. **[作成]** ボタンをクリックします。

10. **[有効]** ボタンをクリックします。


## <a name="disable-automanage-for-vms"></a>VM の Automanage を無効にする

自動管理を無効にすることで、仮想マシンに対する Azure Automanage の使用をすばやく停止します。

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="仮想マシンでの Automanage の無効化。":::

1. 自動管理されている VM がすべて表示されている **[Automanage – Azure virtual machine best practices]\(Automanage - Azure 仮想マシンのベスト プラクティス)** ページに移動します。
1. 無効にする仮想マシンの横にあるチェック ボックスをオンにします。
1. **[Disable automanagent]\(自動管理の無効化\)** ボタンをクリックします。
1. 結果として表示されたポップアップのメッセージをよく読んでから、 **[Disable]\(無効\)** に同意します。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

新しいリソース グループを作成して、仮想マシンに対して Azure Automanage を試し、不要になった場合は、そのリソース グループを削除できます。 グループを削除すると、そのリソース グループ内の VM およびすべてのリソースも削除されます。

Azure Automanage では、リソースを格納するための既定のリソース グループが作成されます。 すべてのリソースをクリーンアップするために "DefaultResourceGroupRegionName" および "AzureBackupRGRegionName" という名前付け規則を使用しているリソース グループを確認します。

1. **[リソース グループ]** を選択します。
1. リソース グループのページで、 **[削除]** を選択します。
1. プロンプトが表示されたら、リソース グループ名を確認し、 **[削除]** を選択します。


## <a name="next-steps"></a>次の手順 

よく寄せられる質問とその回答については、FAQ を参照してください。 

> [!div class="nextstepaction"]
> [よく寄せられる質問](faq.md)