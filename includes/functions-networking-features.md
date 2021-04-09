---
ms.openlocfilehash: 2e92d150851c74a84f785d1f5f0ebe2e5870a54e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97936764"
---


| 特徴量 |[従量課金プラン](../articles/azure-functions/consumption-plan.md)|[Premium プラン](../articles/azure-functions/functions-premium-plan.md)|[専用プラン](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[受信 IP の制限とプライベート サイト アクセス](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅はい|✅はい|✅はい|✅はい|✅はい|
|[仮想ネットワークの統合](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌いいえ|✅はい (リージョン)|✅はい (リージョンとゲートウェイ)|✅はい| ✅はい|
|[仮想ネットワーク トリガー (非 HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌いいえ| ✅はい |✅はい|✅はい|✅はい|
|[ハイブリッド接続](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (Windows のみ)|❌いいえ|✅はい|✅はい|✅はい|✅はい|
|[送信 IP の制限](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌いいえ| ✅はい|✅はい|✅はい|✅はい|