---
title: Azure Bastion で VM と NSG を使用する | Microsoft Docs
description: この記事では、Azure Bastion に NSG アクセスを組み込む方法を説明します
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: e7210b2b1be072f5326070d768d5fe12c386ee0b
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191270"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>NSG アクセスと Azure Bastion を使用する (プレビュー)

Azure Bastion の使用時にネットワーク セキュリティ グループ (NSG) を使用できます。 詳細については、「[セキュリティ グループ](../virtual-network/security-overview.md)」を参照してください。 

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
>

![アーキテクチャ](./media/bastion-nsg/nsg_architecture.png)

この図の内容は次のとおりです。

* Azure Bastion ホストは仮想ネットワーク内にデプロイされています。
* ユーザーは任意の HTML5 ブラウザーを使用して Azure portal に接続します。
* ユーザーは、接続先の仮想マシンを選択します。
* 1 回クリックすると、ブラウザーで RDP または SSH セッションが開きます。
* Azure VM ではパブリック IP が必要ありません。

## <a name="nsg"></a>ネットワーク セキュリティ グループ

* **AzureBastionSubnet:** Azure Bastion は、特定の AzureBastionSubnet にデプロイされます。  
    * **パブリック インターネットからのイグレス トラフィック:** Azure Bastion によってパブリック IP が作成されます。このパブリック IP では、イグレス トラフィック用にポート 443 が有効になっている必要があります。 AzureBastionSubnet でポート 3389/22 が開かれている必要はありません。
    * **ターゲット VM へのエグレス トラフィック:** Azure Bastion は、プライベート IP 経由でターゲット VM にリーチします。 NSG では、他のターゲット VM サブネットへのエグレス トラフィックを許可する必要があります。
* **ターゲット VM サブネット:** これは、RDP/SSH で接続するターゲット仮想マシンを含むサブネットです。
    * **Azure Bastion からのイグレス トラフィック:** Azure Bastion は、プライベート IP 経由でターゲット VM にリーチします。 プライベート IP 経由のターゲット VM 側で RDP/SSH ポート (それぞれポート 3389、22) が開かれている必要があります。

## <a name="apply"></a>AzureBastionSubnet への NSG の適用

**AzureBastionSubnet** に NSG を適用する場合、Azure コントロール プレーンおよびインフラストラクチャに対して次の 2 つサービス タグを許可します。

* **GatewayManager (Resource Manager のみ)** :このタグは、Azure Gateway Manager サービスのアドレス プレフィックスを表します。 値として GatewayManager を指定した場合、GatewayManager へのトラフィックが許可または拒否されます。

* **AzureCloud (Resource Manager のみ)** :このタグは、すべてのデータセンターのパブリック IP アドレスを含む Azure の IP アドレス空間を表します。 値として AzureCloud を指定した場合、Azure パブリック IP アドレスへのトラフィックが許可または拒否されます。 特定のリージョンの AzureCloud に対するアクセスのみを許可する場合は、リージョンを指定することができます。 たとえば、米国東部リージョンの Azure AzureCloud へのアクセスのみを許可する場合は、サービス タグとして AzureCloud.EastUS と指定できます。

## <a name="next-steps"></a>次の手順

Azure Bastion の詳細については、[FAQ](bastion-faq.md) をご覧ください。