---
title: Azure portal から Azure Automation の変更履歴とインベントリを有効にする
description: この記事では、Azure portal から変更履歴とインベントリの機能を有効にする方法について説明します。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 63041e0b1b6e12c765299b12f28aa3637b6a6ccb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99052789"
---
# <a name="enable-change-tracking-and-inventory-from-azure-portal"></a>Azure portal で変更履歴とインベントリを有効にする

この記事では、Azure portal で 1 つ以上の Azure VM に対して[変更履歴とインベントリ](overview.md)機能を有効にする方法について説明します。 Azure VM を大規模に有効にするには、変更履歴とインベントリを使用して既存の VM を有効にする必要があります。

VM の管理に使用できるリソース グループの数は、[Resource Manager のデプロイの上限](../../azure-resource-manager/templates/deploy-to-resource-group.md)によって制限されます。 Resource Manager のデプロイは、デプロイあたり 5 つのリソース グループに制限されています。 それらのリソース グループのうちの 2 つは、Log Analytics ワークスペース、Automation アカウント、および関連リソースを構成するために予約されています。 変更履歴とインベントリを使った管理のために選択できるリソース グループは、これで 3 つ残ります。 この制限は、Automation 機能によって管理できるリソース グループの数ではなく、同時セットアップのみに適用されます。

> [!NOTE]
> 変更履歴とインベントリを有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](../how-to/region-mappings.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](../automation-security-overview.md)。
* [仮想マシン](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

https://portal.azure.com で Azure にサインインします。

## <a name="enable-change-tracking-and-inventory"></a>Change Tracking と Inventory の有効化

1. Azure Portal で **仮想マシン** に移動します。

2. チェック ボックスを使用して、変更履歴とインベントリに追加する VM を選択します。 同時に最大 3 つの異なるリソース グループに対してマシンを追加することができます。 Azure VM は、ご使用の Automation アカウントの場所に関係なく任意のリージョンに存在できます。

    ![VM の一覧](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > フィルター コントロールを使用して、異なるサブスクリプション、場所、およびリソース グループから VM を選択します。 一番上のチェック ボックスをオンにすると、一覧のすべての仮想マシンを選択できます。

3. **[構成管理]** の **[Change tracking]\(変更履歴\)** または **[インベントリ]** を選択します。

4. 仮想マシンの一覧はフィルターされ、同じサブスクリプションおよび場所にある仮想マシンのみが表示されます。 お使いの仮想マシンが 4 つ以上のリソース グループにある場合、最初の 3 つのリソース グループが選択されます。

5. 既定では、既存の Log Analytics ワークスペースと Automation アカウントが選択されます。 異なる Log Analytics ワークスペースと Automation アカウントを使用する場合は、 **[カスタム]** をクリックして、[カスタム構成] ページから選択します。 Log Analytics ワークスペースを選択すると、それが Automation アカウントにリンクされているかどうかを判断するためのチェックが行われます。 リンクされている Automation アカウントが見つかった場合は、次の画面が表示されます。 完了したら、 **[OK]** をクリックします。

    ![ワークスペースとアカウントを選択する](media/enable-from-portal/select-workspace-and-account.png)

6. 選択したワークスペースが Automation アカウントにリンクされていない場合は、次の画面が表示されます。 Automation アカウントを選択し、完了したら **[OK]** をクリックします。

    ![ワークスペースがありません](media/enable-from-portal/no-workspace.png)

7. 有効にしない仮想マシンの横のチェックボックスをオフにします。 有効にできない VM は既に選択解除されています。

8. **[有効にする]** をクリックして、選択した機能を有効にします。 セットアップが完了するまでに最大 15 分かかります。

## <a name="next-steps"></a>次のステップ

* この機能の操作の詳細については、[変更履歴の管理](manage-change-tracking.md)と[インベントリの管理](manage-inventory-vms.md)に関するページをご覧ください。
* この機能に関する一般的な問題のトラブルシューティングについては、「[Change Tracking と Inventory に関する問題のトラブルシューティング](../troubleshoot/change-tracking.md)」を参照してください。