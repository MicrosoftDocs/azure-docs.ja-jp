---
title: リージョン間で Azure リソースを移動するためのツールを選択する
description: リージョン間で Azure リソースを移動するためのオプションとツールを確認する
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 1b233028b52175842c73660ff116ac592d8296e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90603358"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Azure リソースを移動するためのツールを選択する

Azure 内のリソースは、次のように移動できます。

- **リージョン間でリソースを移動する**:リソースを Resource Mover ハブ内またはリソース グループ内から移動します。 
- **リソース グループまたはサブスクリプション間でリソースを移動する**:リソース グループ内から移動します。 
- **Azure クラウド間でリソースを移動する**:Azure Site Recovery サービスを使用して、パブリックおよび政府のクラウド間でリソースを移動します。
- **同じリージョン内の可用性ゾーン間でリソースを移動する**:Azure Site Recovery サービスを使用して、同じ Azure リージョン内の可用性ゾーン間でリソースを移動します。


## <a name="compare-move-tools"></a>移動ツールの比較

**ツール** | **いつ使用するか** | **詳細については、こちらを参照してください**。
--- | --- | ---
**リソース グループ内での移動** | リソースを別のリソース グループ/サブスクリプション、または複数のリージョンにわたって移動するとき。<br/><br/> 複数のリージョンにわたって移動する場合は、移動するリソースをリソース グループ内で選択し、Resource Mover ハブに移動して依存関係を確認し、リソースをターゲット リージョンに移動します。 | [リソースを別のリソース グループ/サブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)。<br/><br/> [リソースをリソース グループから別のリージョンに移動する](move-region-within-resource-group.md)。
**Resource Mover ハブからの移動** | リソースをリージョン間で移動するとき。 <br/><br/> あるターゲット リージョンに移動するか、ターゲット リージョン内の特定の可用性ゾーンまたは可用性セットに移動できます。 | [Resource Mover ハブ内のリージョン間でリソースを移動する]()。
**Site Recovery を使用した VM の移動** | 政府機関クラウドとパブリック クラウドの間で Azure VM を移動する場合に使用します。<br/><br/> 同じリージョン内の可用性ゾーン間で VM を移動する場合に使用します。 |[政府/パブリック クラウドの間でリソースを移動する](../site-recovery/region-move-cross-geos.md)、[同じリージョン内の可用性ゾーンにリソースを移動する](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)。

## <a name="next-steps"></a>次のステップ

Resource Mover コンポーネントおよび移動プロセスについて[詳しく学習する](about-move-process.md)。
