---
title: クイックスタート - Azure portal で VM に対して Azure Automanage を有効にする
description: Azure portal で新規または既存の VM 上の仮想マシンに対して Automanage を迅速に有効にする方法について説明します。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: jushiman
ms.openlocfilehash: 5f5f1e70d9ae309c90291ccac1e6dd61e7a9d056
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038425"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>クイック スタート:Azure portal で仮想マシンに対して Azure Automanage を有効にする

Azure portal を使用して新規または既存の仮想マシンで自動管理を有効にすることにより、仮想マシンに対して Azure Automanage の使用を開始します。


## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)してください。

> [!NOTE]
> 無料試用版アカウントでは、このチュートリアルで使用されている仮想マシンを利用できません。 従量課金制サブスクリプションにアップグレードしてください。

> [!IMPORTANT]
> 既存の Automanage アカウントを使用して Automanage を有効にするには、VM を含むリソース グループの **共同作成者** ロールが必要です。 新しい Automanage アカウントで Automanage を有効にする場合は、次のアクセス許可が必要です: ご使用のサブスクリプションに対する **所有者** ロール、または **共同作成者** ロールと **ユーザー アクセス管理者** ロールの併用。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://aka.ms/AutomanagePortal-Ignite21) にサインインします。

## <a name="enable-automanage-for-a-single-vm"></a>単一の VM に対して Automanage を有効にする

1. 有効にする仮想マシンを参照します。

2. **[操作]** の下の目次にある **[Automanage (プレビュー)]** エントリをクリックします。

3. **[開始]** を選択します。

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-getstartedbutton.png" alt-text="単一の VM を使用開始する。":::

4. 目的の Automanage 設定 (環境、ユーザー設定、Automanage アカウント) を選択し、 **[有効化]** をクリックします。

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-enablepane.png" alt-text="単一の VM を有効にする。":::

## <a name="enable-automanage-for-multiple-vms"></a>複数の VM に対して Automanage を有効にする

1. 検索バーで、 **[Automanage – Azure machine best practices]\(Automanage - Azure マシンのベスト プラクティスの有効化)** を検索して選択します。

2. **[Enable on existing VM]\(既存の VM で有効にする\)** を選択します。

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="[Enable on existing VM]\(既存の VM で有効にする\)":::

3. **[マシンの選択]** ブレードで次のようにします。
    1. 実際の **サブスクリプション** と **リソース グループ** で VM のリストにフィルターをかけます。
    1. オンボードする各仮想マシンのチェック ボックスをオンにします。
    1. **[選択]** ボタンをクリックします。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="使用可能な VM のリストから既存の VM を選択します。":::

4. **[環境]** で、環境の種類として **[Dev/Test]** または **[運用]** を選択します。 

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="環境を選択します。":::

   環境の相違点を確認するには、 **[環境の詳細を比較]** をクリックします。
    1. ドロップダウンで環境を選択します。テストの場合は *[Dev/Test]* 、運用環境の場合は *[運用]* 。
    1. **[OK]** をクリックします。

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="運用環境を参照する。":::

5. 既定では、構成設定に対して **[Azure のベスト プラクティス]** 設定が選択されています。 これを変更するには、新しい設定を作成するか、既存のものを選択します。 

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="設定を作成する。":::

6. **[有効]** ボタンをクリックします。


## <a name="enable-automanage-for-a-new-vm"></a>新規の VM に対して Automanage を有効にする

Azure portal の[こちら](https://aka.ms/AzureAutomanagePreview)にサインインして、新しい VM を作成し、Automanage を有効にします。

1. **[基本]** タブに VM の詳細を入力します。

> [!NOTE]
> Automanage が[サポートされているリージョン](automanage-virtual-machines.md#supported-regions)と、Automanage がサポートされている [Linux ディストリビューション](automanage-linux.md#supported-linux-distributions-and-versions)および [Windows Server バージョン](automanage-windows-server.md#supported-windows-server-versions)をご確認ください。

2. **[管理]** タブに移動して、ご使用の **Automanage 環境** を選択します。

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="[管理] タブで Automanage を有効にする。":::

3. 残りの既定値はそのままにして、ページの一番下にある **[Review + create] (確認および作成)** ボタンを選択します。

4. 検証が成功したことを示すメッセージが表示されたら、 **[作成]** を選択します。

## <a name="disable-automanage-for-vms"></a>VM の Automanage を無効にする

自動管理を無効にすることで、仮想マシンに対する Azure Automanage の使用をすばやく停止します。

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="仮想マシンでの Automanage の無効化。":::

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

このクイックスタートでは、VM に対して Azure Automanage を有効にしました。

仮想マシンで Automanage を有効にする際に、カスタマイズした基本設定を作成して適用する方法を確認してください。

> [!div class="nextstepaction"]
> [Azure Automanage for VMs - カスタム構成設定](virtual-machines-custom-preferences.md)
