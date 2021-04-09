---
title: Azure クラシック デプロイ リソースの移動
description: Azure Resource Manager を使用して、クラシック デプロイのリソースを新しいリソース グループまたはサブスクリプションに移動します。
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75476617"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>クラシック デプロイ モデル リソースの移動に関するガイダンス

クラシック モデルを使用してデプロイされるリソースを移動するための手順は、リソースをサブスクリプション内で移動するか、新しいサブスクリプションに移動するかによって異なります。

## <a name="move-in-the-same-subscription"></a>同じサブスクリプション内で移動する

リソースを同じサブスクリプション内のリソース グループ間で移動する場合は、次の制限が適用されます。

* 仮想ネットワーク (クラシック) を移動することはできません。
* Virtual Machines (クラシック) はクラウド サービスで移動する必要があります。
* クラウド サービスは、移動にその仮想マシンがすべて含まれている場合にのみ移動できます。
* 一度に移動できるクラウド サービスは 1 つだけです。
* 一度に移動できるストレージ アカウント (クラシック) は 1 つだけです。
* ストレージ アカウント (クラシック) は、仮想マシンまたはクラウド サービスと同じ操作では移動できません。

クラシック リソースを同じサブスクリプション内の新しリソース グループに移動するには、ポータル、Azure PowerShell、Azure CLI、または REST API による[標準の移動操作](../move-resource-group-and-subscription.md)を使用します。 Resource Manager のリソースの移動に使用した方法と同じ操作を使用します。

## <a name="move-across-subscriptions"></a>サブスクリプション間で移動する

リソースを新しいサブスクリプションに移動する場合は、次の制限が適用されます。

* サブスクリプション内のすべてのクラシック リソースは、同じ操作で移動する必要があります。
* ターゲット サブスクリプションには、他のクラシック リソースを含めないでください。
* クラシック リソースの場合、移動は、別の REST API を通じてのみ要求できます。 標準の Resource Manager の移動コマンドは、クラシック リソースを新しいサブスクリプションに移動する場合は機能しません。

クラシック リソースを新しいサブスクリプションに移動する場合は、クラシック リソース固有の REST 操作を使用してください。 REST を使用するには、次の手順を実行します。

1. 移動元のサブスクリプションがサブスクリプション間の移動に参加できることを確認します。 次の操作を行います。

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     要求本文は次のようになります。

   ```json
   {
    "role": "source"
   }
   ```

     検証操作の応答は次のような形式になります。

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. 移動先のサブスクリプションがサブスクリプション間の移動に参加できることを確認します。 次の操作を行います。

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     要求本文は次のようになります。

   ```json
   {
    "role": "target"
   }
   ```

     応答は移動元のサブスクリプションの検証と同じ形式になります。
1. 両方のサブスクリプションが検証に合格し、すべてのクラシック リソースをあるサブスクリプションから別のサブスクリプションに移動する場合は、次の操作を行います。

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    要求本文は次のようになります。

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

この操作には数分かかる場合があります。

## <a name="next-steps"></a>次のステップ

クラシック リソースの移行がうまくできない場合は、[サポート](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)までお問い合せください。

リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../move-resource-group-and-subscription.md)」を参照してください。
