---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "83648823"
---
プライベート サイト アクセスとは、Azure 仮想ネットワークなどのプライベート ネットワークからのみアプリにアクセスできるようにすることです。

* プライベート サイトへのアクセスは、サービス エンドポイントが構成されている場合に [Premium](../articles/azure-functions/functions-premium-plan.md)、[従量課金](../articles/azure-functions/functions-scale.md#consumption-plan)、および [App Service](../articles/azure-functions/functions-scale.md#app-service-plan) の各プランで利用できます。
    * サービス エンドポイントは、 **[プラットフォーム機能]**  >  **[ネットワーク]**  >  **[アクセス制限を構成する]**  >  **[ルールの追加]** から、アプリごとに構成できます。 これで、仮想ネットワークをルールの種類として選択できるようになりました。
    * 詳細については、[仮想ネットワーク サービス エンドポイント](../articles/virtual-network/virtual-network-service-endpoints-overview.md)に関するページを参照してください。
    * サービス エンドポイントがあれば、仮想ネットワーク統合が構成されていても、関数はインターネットへの完全な送信アクセスを引き続き持つことに注意してください。
* プライベート サイトへのアクセスは、内部ロード バランサー (ILB) を使用して構成されている App Service Environment 内でも使用可能です。 詳細については、「[App Service Environment で内部ロード バランサーを作成して使用する](../articles/app-service/environment/create-ilb-ase.md)」を参照してください。

プライベート サイト アクセスを設定する方法については、[Azure Functions のプライベート サイト アクセスの設定](../articles/azure-functions/functions-create-private-site-access.md)に関するページ参照してください。