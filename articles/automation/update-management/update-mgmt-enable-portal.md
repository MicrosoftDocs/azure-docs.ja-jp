---
title: Azure portal から Azure Automation の Update Management を有効にする
description: この記事では、Azure portal から Update Management を有効にする方法について説明します。
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 136a5da8d9956e034532c14b6fce011e4e7ce415
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87563892"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Azure portal から Update Management を有効にする

この記事では、ブラウザーで Azure portal にアクセスして、VM の [Update Management](update-mgmt-overview.md) 機能を有効にする方法を説明します。 Azure VM を大規模に有効にするには、Update Management を使用して既存の VM を有効にする必要があります。

VM の管理に使用できるリソース グループの数は、[Resource Manager のデプロイの上限](../../azure-resource-manager/templates/cross-scope-deployment.md)によって制限されます。 Resource Manager のデプロイ (更新プログラムのデプロイと混同しないでください) は、デプロイあたり 5 つのリソース グループに制限されています。 それらのリソース グループのうちの 2 つは、Log Analytics ワークスペース、Automation アカウント、および関連リソースを構成するために予約されています。 Update Management による管理のために選択できるリソース グループは、これで 3 つ残ります。 この制限は、Automation 機能によって管理できるリソース グループの数ではなく、同時セットアップのみに適用されます。

> [!NOTE]
> Update Management を有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](../how-to/region-mappings.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](../index.yml)。
* [仮想マシン](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

https://portal.azure.com で Azure にサインインします。

## <a name="enable-update-management"></a>Update Management の有効化

1. Azure Portal で**仮想マシン**に移動します。

2. チェックボックスを使用して、Update Management に追加する VM を選択します。 同時に最大 3 つの異なるリソース グループに対してマシンを追加できます。 Azure VM は、ご使用の Automation アカウントの場所に関係なく任意のリージョンに存在できます。

    ![VM の一覧](media/update-mgmt-enable-portal/vmlist.png)

    > [!TIP]
    > フィルター コントロールを使用して、異なるサブスクリプション、場所、およびリソース グループから VM を選択します。 一番上のチェックボックスをオンにすると、一覧のすべての仮想マシンを選択できます。

    [ ![Update Management を有効にする](./media/update-mgmt-enable-portal/onboard-feature.png)](./media/update-mgmt-enable-portal/onboard-feature-expanded.png#lightbox)

3. **[サービス]** を選択し、Update Management 機能に対する **[Update Management]** を選択します。

4. 仮想マシンの一覧はフィルターされ、同じサブスクリプションおよび場所にある仮想マシンのみが表示されます。 お使いの仮想マシンが 4 つ以上のリソース グループにある場合、最初の 3 つのリソース グループが選択されます。

5. 既定では、既存の Log Analytics ワークスペースと Automation アカウントが選択されます。 異なる Log Analytics ワークスペースと Automation アカウントを使用する場合は、 **[カスタム]** を選択して、[カスタム構成] ページから選択します。 Log Analytics ワークスペースを選択すると、それが Automation アカウントにリンクされているかどうかを判断するためのチェックが行われます。 リンクされている Automation アカウントが見つかった場合は、次の画面が表示されます。 操作が完了したら、 **[OK]** をクリックします。

    [![ワークスペースとアカウントの選択](./media/update-mgmt-enable-portal/select-workspace-and-account.png)](./media/update-mgmt-enable-portal/select-workspace-and-account-expanded.png#lightbox)

6. 選択したワークスペースが Automation アカウントにリンクされていない場合は、次の画面が表示されます。 Automation アカウントを選択し、完了したら **[OK]** を選択します。

    ![ワークスペースがありません](media/update-mgmt-enable-portal/no-workspace.png)

7. 有効にしない仮想マシンをオフにします。 有効にできない VM は既に選択解除されています。

8. **[有効にする]** を選択して機能を有効にします。 Update Management を有効にした後、VM から更新プログラムの評価を表示できるようになるまでに、約 15 分かかることがあります。

## <a name="next-steps"></a>次のステップ

* VM の Update Management を使用するには、「[VM の更新プログラムとパッチの管理](update-mgmt-manage-updates-for-vm.md)」を参照してください。
* Update Management の一般的なエラーのトラブルシューティングについては、[Update Management の問題のトラブルシューティング](../troubleshoot/update-management.md)に関する記事を参照してください。
* Windows Update エージェントに関する問題のトラブルシューティングについては、「[Windows Update エージェントの問題をトラブルシューティングする](../troubleshoot/update-agent-issues.md)」を参照してください。
* Linux Update エージェントに関する問題のトラブルシューティングについては、「[Linux Update エージェントに関する問題のトラブルシューティング](../troubleshoot/update-agent-issues-linux.md)」を参照してください。
