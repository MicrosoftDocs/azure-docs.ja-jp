---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906722"
---


| 特徴量 |[従量課金プラン](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premium プラン](../articles/azure-functions/functions-scale.md#premium-plan)|[専用プラン](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[受信 IP の制限とプライベート サイト アクセス](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅はい|✅はい|✅はい|✅はい|✅はい|
|[仮想ネットワークの統合](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌いいえ|✅はい (リージョン)|✅はい (リージョンとゲートウェイ)|✅はい| ✅はい|
|[仮想ネットワーク トリガー (非 HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌いいえ| ✅はい |✅はい|✅はい|✅はい|
|[ハイブリッド接続](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (Windows のみ)|❌いいえ|✅はい|✅はい|✅はい|✅はい|
|[送信 IP の制限](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌いいえ| ✅はい|✅はい|✅はい|✅はい|