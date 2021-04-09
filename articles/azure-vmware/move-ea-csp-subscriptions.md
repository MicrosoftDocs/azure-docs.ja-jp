---
title: EA と CSP の Azure VMware Solution サブスクリプションを移動する
description: サブスクリプション間でプライベート クラウドを移動する方法について説明します。 この移動は、課金などのさまざまな理由によって行われる場合があります。
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 608f46dbd84d6bb899a3e7fcd1f8a63b3a5e85fb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555135"
---
# <a name="move-ea-and-csp-azure-vmware-solution-subscriptions"></a>EA と CSP の Azure VMware Solution サブスクリプションを移動する

この記事では、サブスクリプション間でプライベート クラウドを移動する方法について説明します。 この移動は、課金などのさまざまな理由によって行われる場合があります。 

>[!IMPORTANT]
>ソースとターゲットの両方のサブスクリプションに対して、少なくとも共同作成者権限を持っている必要があります。 VNet と VNet ゲートウェイを、あるサブスクリプションから別のサブスクリプションに移動することはできません。 また、サブスクリプションを移動しても、vCenter、NSX、ワークロード仮想マシンなどの管理とワークロードには影響しません。

1. Azure portal にサインインし、移動するプライベート クラウドを選択します。

1. **[サブスクリプション (変更)]** リンクを選択します。

   :::image type="content" source="media/private-cloud-overview-subscription-id.png" alt-text="プライベート クラウドの詳細を示すスクリーンショット。":::

1. **[ターゲット]** のサブスクリプションの詳細を指定し、 **[次へ]** を選択します。

   :::image type="content" source="media/move-resources-subscription-target.png" alt-text="ターゲット リソースのスクリーンショット。" lightbox="media/move-resources-subscription-target.png":::

1. 移動することを選択したリソースの検証を確認し、 **[次へ]** を選択します。 

   :::image type="content" source="media/confirm-move-resources-subscription-target.png" alt-text="移動するリソースを示すスクリーンショット。" lightbox="media/confirm-move-resources-subscription-target.png":::

1. 「関連付けられているツールとスクリプトは、それらを更新して新しいリソース ID を使用するまで機能しない旨を理解している」ことを示すチェックボックスをオンにします。 次に、 **[移動]** を選択します。

   :::image type="content" source="media/review-move-resources-subscription-target.png" alt-text="選択された、移動するリソースの概要を示すスクリーンショット。" lightbox="media/review-move-resources-subscription-target.png":::

   リソースの移動が完了すると、通知が表示されます。 新しいサブスクリプションがプライベート クラウドの概要に表示されます。

   :::image type="content" source="media/moved-subscription-target.png" alt-text="新しいサブスクリプションを示すスクリーンショット。" lightbox="media/moved-subscription-target.png":::

