---
title: Azure DevTest Labs でのラボのクォータと制限のスケール | Microsoft Docs
description: Azure DevTest Labs でラボをスケールする方法を説明します
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: ec79f6a9b255d44e66b901a0aae263c8dbbf2863
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149862"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>DevTest Labs でのクォータと制限のスケール
お気づきかもしれませんが、DevTest Labs での作業時に、一部の Azure リソースに一定の既定の制限があります。これは、DevTest Labs サービスに影響する場合があります。 これらの制限は、**クォータ**と呼ばれます。

> [!NOTE]
> DevTest Labs サービスはクォータを課すものではありません。 発生する可能性のあるクォータは、全体的な Azure サブスクリプションの既定の制約です。

各 Azure リソースは、そのクォータ制限に達するまで使用できます。 各サブスクリプションには個別のクォータが設けられ、サブスクリプションごとに使用状況が追跡されます。

たとえば、各サブスクリプションの既定のクォータが 20 コアだとします。 ラボにそれぞれ 4 コアを備えた VM を作成する場合、作成できる VM の数は 5 つのみです。

Azure リソースの最も一般的な一部のクォータについては、[Azure サブスクリプションとサービスの制限](https://docs.microsoft.com/azure/azure-subscription-service-limits)に関する記事をご覧ください。 ラボで最も一般的に使用されるリソース、またクォータが発生する可能性のあるリソースには、VM コア、パブリック IP アドレス、ネットワーク インターフェイス、マネージド ディスク、RBAC ロール割り当て、ExpressRoute 回線などがあります。

## <a name="view-your-usage-and-quotas"></a>使用量とクォータの表示
次の手順では、特定の Azure リソースのサブスクリプションの現在のクォータや、使用した各クォータの割合を表示する方法を説明します。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. **[その他のサービス]** を選択し、一覧の **[課金]** を選択します。
1. [課金] ブレードでサブスクリプションを選択します。
4. **[使用量 + クォータ]** を選択します。

   ![[使用量 + クォータ] ボタン](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   [使用量 + クォータ] ブレードが表示され、そのサブスクリプションで使用できるさまざまなリソースと、リソースごとに使用されているクォータの割合が一覧表示されます。

   ![クォータと使用量](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>サブスクリプションのリソースの引き上げを要求する
クォータの上限に達した場合、[Azure サブスクリプションとサービスの制限](https://docs.microsoft.com/azure/azure-subscription-service-limits)に関する記事の説明に従って、サブスクリプションのリソースの既定の制限を最大限増やすことができます。

[Azure ポータル](https://go.microsoft.com/fwlink/p/?LinkID=525040)を使用してクォータの引き上げを要求するには、次の手順に従います。

1. **[その他のサービス]**、**[課金]**、**[使用量 + クォータ]** の順に選択します。
1. [使用量 + クォータ] ブレードで、**[引き上げを依頼する]** ボタンを選択します。

   ![[引き上げを依頼する] ボタン](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. 要求を送信して完了するには、**[新しいサポート要求]** フォームの 3 つすべてのタブの必要な情報を入力します。

   ![[引き上げを依頼する] フォーム](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

Azure サポートに問い合わせてクォータの引き上げを要求する方法について詳しくは、「[Understanding Azure Limits and Increases (Azure の制限と増設について)](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)」をご覧ください。



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>次の手順
* [DevTest Labs Azure Resource Manager のクイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)を検索します。
