---
title: 一般的なデプロイ エラーのトラブルシューティング
titleSuffix: Azure Load Balancer
description: Azure のロード バランサーをデプロイするときに発生する一般的なエラーの解決方法について説明します
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: ae3899a61e942695fed1e3da5fc543b298a42e24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94695556"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Azure Load Balancer での一般的な Azure デプロイ エラーのトラブルシューティング

この記事では、Azure Load Balancer でのデプロイに関する一般的なエラーについて説明し、そのエラーを解決するための情報を提供します。 エラー コードに関する情報を探していて、その情報がこの記事に記載されていない場合は、お知らせください。 このページの最後で、フィードバックを残していただくことができます。 このフィードバックは、GitHub のイシューで追跡されます。

## <a name="error-codes"></a>エラー コード

| エラー コード | 詳細と軽減策 |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| パブリック IP の SKU と Load Balancer の両方の SKU は一致している必要があります。 Azure Load Balancer とパブリック IP の SKU を確実に一致させてください。 運用環境のワークロードには Standard SKU をお勧めします。 [SKU における差異](./skus.md)の詳細を確認してください。  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | SKU が指定されていない場合、または Standard パブリック IP を使用せずにデプロイされている場合、仮想マシン スケール セットは既定で Basic Load Balancer になります。 Standard パブリック IP を使用して個々のインスタンスに仮想マシン スケール セットを再デプロイし、Standard Load Balancer が確実に選択されるようにするか、Azure portal から仮想マシン スケール セットをデプロイするときに Standard LB を選択します。 |
|MaxAvailabilitySetsInLoadBalancerReached | ロード バランサーのバックエンド プールには、最大 150 個の可用性セットを含めることができます。 バックエンド プール内の VM に対して明示的に定義された可用性セットがない場合は、各 VM が専用の可用性セットに配置されます。 したがって、150 台のスタンドアロン VM をデプロイすると、150 個の可用性セットを持つことになり、制限に達します。 回避策として、可用性セットをデプロイし、それに VM を追加することができます。 |
|NetworkInterfaceAndLoadBalancerAreInDifferentAvailabilitySets | Basic SKU ロード バランサーでは、ネットワーク インターフェイスとロード バランサーが同じ可用性セットに含まれている必要があります。 |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| 同じ仮想マシン スケール セットによって参照されている同じバックエンド ポートとプロトコルを使用して、特定のロード バランサーの種類 (内部、パブリック) に対して複数のルールを設定することはできません。 ルールを更新して、この重複するルールの作成を変更します。 |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| 同じ仮想マシン スケール セットによって参照されている同じバックエンド ポートとプロトコルを使用して、特定のロード バランサーの種類 (内部、パブリック) に対して複数のルールを設定することはできません。 ルールのパラメーターを更新して、この重複するルールの作成を変更します。 |
|AnotherInternalLoadBalancerExists| 種類が内部のロード バランサーのバックエンドにある同じ VM またはネットワーク インターフェイスのセットを参照できるのは、その種類の 1 つのロード バランサーだけです。 同じ種類のロード バランサーを 1 つだけ作成するように、デプロイを更新します。 |
|CannotUseInactiveHealthProbe| 仮想マシン スケール セットの正常性に関して構成されたルールで使用されていないプローブは、使用できません。 設定されているプローブがアクティブに使用されていることを確認します。 |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| 同じ種類 (内部、パブリック) のロード バランサーを複数使用することはできません。 最大で 1 つの内部ロード バランサーと 1 つのパブリック ロード バランサーを使用できます。 |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | Basic Load Balancer は、複数配置グループの仮想マシン スケール セットまたは複数の可用性ゾーンにまたがる仮想マシン スケール セットではサポートされていません。 代わりに Standard Load Balancer を使用します。 |
|MarketplacePurchaseEligibilityFailed | サブスクリプションが EA サブスクリプションであるため、購入を有効にするには、適切な管理者アカウントに切り替えます。 詳細については、[こちら](../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase)を参照してください。 |
|ResourceDeploymentFailure| ロード バランサーがエラー状態になっている場合は、次の手順に従ってエラー状態から復帰します。<ol><li>https://resources.azure.com に移動し、Azure portal の資格情報でサインインします。</li><li>**[Read/Write]\(読み取り/書き込み\)** を選択します。</li><li>左側で、 **[Subscriptions]\(サブスクリプション\)** を展開し、更新する Load Balancer を含むサブスクリプションを展開します。</li><li>**[ResourceGroups]** を展開し、更新する Load Balancer を含むリソース グループを展開します。</li><li>**[Microsoft. Network]**  >  **[LoadBalancers]** を選択し、更新する Load Balancer である **[LoadBalancer_1]** を選択します。</li><li>**[LoadBalancer_1]** の表示ページで、 **[GET]\(取得\)**  >  **[Edit]\(編集\)** を選択 します。</li><li>**ProvisioningState** の値を **Failed** から **Succeeded** に更新します。</li><li>**[PUT]** を選択します。</li></ol>|
|  |  |

## <a name="next-steps"></a>次のステップ

* Azure Load Balancer の[SKU 比較表](./skus.md)を確認する
* [Azure Load Balancer の制限事項](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)について詳しく学習する