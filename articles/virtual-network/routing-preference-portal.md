---
title: パブリック IP アドレスのルーティング優先設定を構成する - Azure portal
description: インターネット トラフィックのルーティング優先設定を使用してパブリック IP アドレスを作成する方法について説明します
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 7d41893d734037e466ad786641fed98c461adcfc
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231813"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Azure portal を使用してパブリック IP アドレスのルーティング優先設定を構成する

この記事では、パブリック IP アドレスに対して ISP ネットワーク ( **[インターネット]** オプション) による[ルーティング優先設定](https://docs.microsoft.com/azure/virtual-network/routing-preference-overview)を構成する方法について示します。 パブリック IP アドレスを作成した後は、インターネットでの受信および送信トラフィック用に、次の Azure リソースと関連付けることができます。

* 仮想マシン
* 仮想マシン スケール セット
* Azure Kubernetes Service (AKS)
* インターネットに接続するロード バランサー
* Application Gateway
* Azure Firewall

既定では、パブリック IP アドレスに対するルーティング優先設定は、すべての Azure サービスについて Microsoft グローバル ネットワークに設定され、任意の Azure サービスと関連付けることができます。

> [!IMPORTANT]
> 現在、ルーティング優先設定はパブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure サブスクリプションをお持ちでない場合は、ここで[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="register-the-feature-for-your-subscription"></a>サブスクリプションに機能を登録する
現在、ルーティング優先設定の機能はプレビューの段階です。 次のように Azure PowerShell を使用して、サブスクリプションに機能を登録します。
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature -ProviderNamespace Microsoft.Network
```

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>ルーティング優先設定を使用してパブリック IP アドレスを作成する
1. [Azure portal](https://preview.portal.azure.com/) にサインインします。
2. **[リソースの作成]** を選択します。 
3. 検索ボックスに「*パブリック IP アドレス*」と入力します。
3. 検索結果から **[パブリック IP アドレス]** を選択します。 次に、 **[パブリック IP アドレス]** ページで、 **[作成]** を選択します。
3. **[ルーティングの優先順位]** オプションで、 **[インターネット]** を選択します。

      ![パブリック IP アドレスを作成する](./media/routing-preference-portal/pip-new.png)

    > [!NOTE]
    > パブリック IP アドレスは、IPv4 または IPv6 アドレスを使用して作成されます。 ただし、現在、ルーティング優先設定では IPV4 のみがサポートされています。

上で作成したパブリック IP アドレスを、[Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンに関連付けることができます。 チュートリアル ページ「[仮想マシンへのパブリック IP アドレスの関連付け](associate-public-ip-address-vm.md#azure-cli)」の CLI セクションを使用して、パブリック IP アドレスを VM に関連付けます。 上で作成したパブリック IP アドレスを、ロード バランサーの**フロントエンド**構成に割り当てることで、[Azure Load Balancer](../load-balancer/load-balancer-overview.md) と関連付けることもできます。 このパブリック IP アドレスは、負荷分散された仮想 IP アドレス (VIP) として機能します。

## <a name="next-steps"></a>次のステップ
- [ルーティング優先設定が指定されたパブリック IP アドレス](routing-preference-overview.md)について詳しく学習する。
- [VM 用にルーティング優先設定を構成する](tutorial-routing-preference-virtual-machine-portal.md)。
- [PowerShell を使用してパブリック IP アドレスのルーティング優先設定を構成する](routing-preference-powershell.md)。
- Azure の[パブリック IP アドレス](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。
