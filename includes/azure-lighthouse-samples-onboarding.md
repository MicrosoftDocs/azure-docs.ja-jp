---
title: インクルード ファイル
description: インクルード ファイル
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 07/07/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: d80786bbdfc4f53254dc98d79ac3badcf5dd3b24
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111663"
---
特定のオンボード シナリオに対応するさまざまなテンプレートが用意されています。 最適なオプションを選択し、環境に合わせてパラメーター ファイルを変更してください。 デプロイでこれらのファイルを使用する方法の詳細については、「[Azure Lighthouse への顧客のオンボード](../articles/lighthouse/how-to/onboard-customer.md)」を参照してください。

| **テンプレート** | **説明** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | 顧客のサブスクリプションを Azure Lighthouse にオンボードします。 デプロイは、サブスクリプションごとに個別に実行する必要があります。 |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | 顧客の 1 つまたは複数のリソース グループを Azure Lighthouse にオンボードします。 1 つのリソース グループをオンボードするには、**rgDelegatedResourceManagement**  を使用します。同じサブスクリプション内の複数のリソース グループをオンボードするには、**multipleRgDelegatedResourceManagement** を使用します。 |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | [Azure Marketplace にマネージド サービス オファーを発行](../articles/lighthouse/how-to/publish-managed-services-offers.md)している場合、このオファーを承諾した顧客のリソースをオンボードするためにオプションでこのテンプレートを使用できます。 このパラメーター ファイル内の marketplace 値は、プランの発行時に使用した値と一致している必要があります。 |

通常、オンボードするサブスクリプションごとに個別に展開する必要がありますが、複数のサブスクリプションにテンプレートをデプロイすることもできます。

| **テンプレート** | **説明** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | 複数のサブスクリプションをまたいで Azure Resource Manager テンプレートをデプロイします。 |
