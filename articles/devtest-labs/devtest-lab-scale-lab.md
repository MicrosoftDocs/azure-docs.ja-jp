---
title: Azure DevTest Labs でのラボのクォータと制限のスケール | Microsoft Docs
description: この記事では、Azure DevTest Labs でラボをスケーリングする方法について説明します。 使用量のクォータと制限を確認し、増加を要求します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2166eaab073e2abc24ca24494ae13eb876db1fcf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87533956"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>DevTest Labs でのクォータと制限のスケール
お気づきかもしれませんが、DevTest Labs での作業時に、一部の Azure リソースに一定の既定の制限があります。これは、DevTest Labs サービスに影響する場合があります。 これらの制限は、**クォータ** と呼ばれます。

> [!NOTE]
> DevTest Labs サービスはクォータを課すものではありません。 発生する可能性のあるクォータは、全体的な Azure サブスクリプションの既定の制約です。

各 Azure リソースは、そのクォータ制限に達するまで使用できます。 各サブスクリプションには個別のクォータが設けられ、サブスクリプションごとに使用状況が追跡されます。

たとえば、各サブスクリプションの既定のクォータが 20 コアだとします。 ラボにそれぞれ 4 コアを備えた VM を作成する場合、作成できる VM の数は 5 つのみです。

Azure リソースの最も一般的な一部のクォータについては、[Azure サブスクリプションとサービスの制限](../azure-resource-manager/management/azure-subscription-service-limits.md)に関する記事をご覧ください。 ラボで最も一般的に使用されるリソース、またクォータが発生する可能性のあるリソースには、VM コア、パブリック IP アドレス、ネットワーク インターフェイス、マネージド ディスク、Azure ロール割り当て、ExpressRoute 回線などがあります。

## <a name="view-your-usage-and-quotas"></a>使用量とクォータの表示
次の手順では、特定の Azure リソースのサブスクリプションの現在のクォータや、使用した各クォータの割合を表示する方法を説明します。

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインする
1. **[その他のサービス]** を選択し、一覧の **[課金]** を選択します。
1. [課金] ブレードでサブスクリプションを選択します。
4. **[使用量 + クォータ]** を選択します。

   ![[使用量 + クォータ] ボタン](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas-new.png)

   [使用量 + クォータ] ブレードが表示され、そのサブスクリプションで使用できるさまざまなリソースと、リソースごとに使用されているクォータの割合が一覧表示されます。

   ![クォータと使用量](./media/devtest-lab-scale-lab/devtestlab-view-quotas-new.png)

## <a name="requesting-more-resources-in-your-subscription"></a>サブスクリプションのリソースの引き上げを要求する
クォータの上限に達した場合、[Azure サブスクリプションとサービスの制限](../azure-resource-manager/management/azure-subscription-service-limits.md)に関する記事の説明に従って、サブスクリプションのリソースの既定の制限を最大限増やすことができます。

[Azure ポータル](https://go.microsoft.com/fwlink/p/?LinkID=525040)を使用してクォータの引き上げを要求するには、次の手順に従います。

1. **[その他のサービス]** 、 **[課金]** 、 **[使用量 + クォータ]** の順に選択します。
1. [使用量 + クォータ] ブレードで、 **[引き上げを依頼する]** ボタンを選択します。

   ![[引き上げを依頼する] ボタン](./media/devtest-lab-scale-lab/devtestlab-request-increase-new.png)

1. 要求を送信して完了するには、 **[新しいサポート要求]** フォームの 3 つすべてのタブの必要な情報を入力します。

   ![[引き上げを依頼する] フォーム](./media/devtest-lab-scale-lab/devtestlab-support-form-new.png)

Azure サポートに問い合わせてクォータの引き上げを要求する方法について詳しくは、「[Understanding Azure Limits and Increases (Azure の制限と増設について)](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)」をご覧ください。



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>次のステップ
* [DevTest Labs Azure Resource Manager のクイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)を検索します。
