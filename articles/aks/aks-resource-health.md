---
title: AKS クラスターに影響するリソース正常性イベントを確認する (プレビュー)
description: Azure Resource Health を使用して、AKS クラスターの正常性状態を確認します。
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: a409ac944c51d893fc344f82ae83d97559e055ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92070658"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>AKS クラスターに影響するリソース正常性イベントを確認する (プレビュー)


AKS でコンテナー ワークロードを実行する場合は、ワークロードの可用性への影響を最小限に抑えるために、問題が発生するとすぐにトラブルシューティングを行って修正できるようにする必要があります。 [Azure Resource Health](../service-health/resource-health-overview.md) を使用すると、AKS クラスターを使用できなくなる可能性があるさまざまな正常性イベントを可視化できます。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>Resource Health を開く

### <a name="to-access-resource-health-for-your-aks-cluster"></a>AKS クラスターの Resource Health にアクセスするには

- [Azure portal](https://portal.azure.com) でお使いの AKS クラスターに移動します。
- 左側のナビゲーションで、 **[リソース正常性]** を選択します。

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>サブスクリプションのすべてのクラスターの Resource Health にアクセスするには

- [Azure portal](https://portal.azure.com) で **[サービス正常性]** を検索し、そこに移動します。
- 左側のナビゲーションで、 **[リソース正常性]** を選択します。
- サブスクリプションを選択し、リソースの種類を Azure Kubernetes Service (AKS) に設定します。

![AKS クラスターのリソースの正常性が示されているスクリーンショット。](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>正常性状態の確認

Azure Resource Health は、Azure のリソースに影響を及ぼしているサービスの問題を診断したり、サポートを受けたりするために役立ちます。 Resource Health は、リソースの現在と過去の正常性に関するレポートを作成し、ユーザーが開始した操作またはプラットフォーム イベントによって問題が発生しているかどうかを判断するのに役立ちます。

Resource Health は、クラスターの正常性状態を判断するために、管理対象クラスターの信号を受信します。 AKS クラスターの正常性状態を調べ、各正常性シグナルに必要な操作を報告します。 これらのシグナルは、自動解決する問題から、計画された更新、計画されていない正常性イベント、およびユーザーが開始した操作によって発生した使用不可状態にまで及びます。 これらのシグナルは、Azure Resource Health の正常性状態を使用して次のように分類されます。"*使用可能*"、"*使用不可*"、"*不明*"、および "*デグレード*"。

- **[使用可能]** :クラスターの正常性に影響する既知の問題がない場合、クラスターは *[使用可能]* として報告されます。

- **[使用不可]** :クラスターの正常性に影響を与えるプラットフォームまたはプラットフォーム以外のイベントがある場合、クラスターは *[使用不可]* として報告されます。

- **不明**:クラスターの正常性メトリックへの接続が一時的に失われた場合、クラスターは *[不明]* として報告されます。

- **[デグレード]** :ユーザーの操作を必要とする正常性の問題が発生した場合、クラスターは *[デグレード]* として報告されます。

各正常性状態の詳細については、「[Resource Health の概要](../service-health/resource-health-overview.md#health-status)」を参照してください。

### <a name="view-historical-data"></a>履歴データの表示

**[正常性の履歴]** セクションでは、過去 30 日間のリソース正常性情報の履歴を表示することもできます。

## <a name="next-steps"></a>次のステップ

クラスターの確認を実行し、[AKS 診断](./concepts-diagnostics.md)を使用してクラスターの問題のトラブルシューティングを行います。