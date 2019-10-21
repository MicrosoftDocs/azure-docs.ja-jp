---
title: フェールオーバー VM のネットワーク構成をカスタマイズする | Microsoft Docs
description: Azure Site Recovery を使用して Azure VM のレプリケーションを行うときの、フェールオーバー VM のネットワーク構成のカスタマイズについて概説します。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 1905d6afb023b1bed15f1359fed8477d815acb45
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087705"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>ターゲット Azure VM のネットワーク構成をカスタマイズする

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用してリージョン間で Azure VM をレプリケートおよび復旧するときの、ターゲット Azure VM におけるネットワーク構成のカスタマイズに関するガイダンスを提供します。

## <a name="before-you-start"></a>開始する前に

[このシナリオ](azure-to-azure-architecture.md)に対して、Site Recovery でどのようにディザスター リカバリーを提供するかについて説明します。

## <a name="supported-networking-resources"></a>サポートされているネットワーク リソース

Azure VM のレプリケート中に、フェールオーバー VM に対して次の主要なリソース構成を提供できます。

- [内部ロード バランサー](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [パブリック IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- サブネットと NIC 両方の[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

 > [!IMPORTANT]
  > 現時点では、これらの設定はフェールオーバー操作でのみサポートされており、テスト フェールオーバーではサポートされていません。

## <a name="pre-requisites"></a>前提条件

- 復旧側の構成を事前に計画していることを確認します。
- 事前にネットワーク リソースを作成する必要があります。 これを入力として提供して、Azure Site Recovery サービスがこれらの設定を受け入れ、フェールオーバー VM にこれらの設定が確実に適用されるようにします。

## <a name="steps-to-customize-failover-networking-configurations"></a>フェールオーバー ネットワーク構成をカスタマイズする手順

1. **[レプリケートされたアイテム]** に移動します。 
2. 目的の Azure VM をクリックします。
3. **[コンピューティングとネットワーク]** をクリックし、 **[編集]** をクリックします。 NIC 構成設定のソースに、対応するリソースが含まれていることがわかります。 

     ![カスタマイズ](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. 構成する NIC の近くにある **[編集]** をクリックします。 開いた次のブレードのターゲットで、あらかじめ作成してある、対応するリソースを選択します。

    ![NIC のドリルダウン](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. Click **OK**.

Site Recovery がこれらの設定を受け入れ、フェールオーバー時の VM が、対応する NIC を介して、選択したリソースに接続されるようになります。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="unable-to-view-or-select-a-resource"></a>リソースを表示または選択できない

ネットワーク リソースを選択または表示できない場合は、次のチェック項目と条件を確認してください。

- ネットワーク リソースのターゲット フィールドは、ソース VM に対応する入力がある場合にのみ有効になります。 これは、ディザスター リカバリーのシナリオでは、ソースと同じバージョンまたはスケールダウンされたバージョンのいずれかが必要になるという原則に基づいています。
- 問題の各ネットワーク リソースに対して、ドロップダウンでいくつかのフィルターが適用されます。これにより、フェールオーバー VM が選択されたリソースに自動的にアタッチされ、フェールオーバーの信頼性が維持されます。 これらのフィルターは、ソース VM を構成したときに検証されたのと同じネットワーク条件に基づいています。

内部ロード バランサーの検証:

1. LB とターゲット VM のサブスクリプションおよびリージョンは同じである必要があります。
2. 内部ロード バランサーに関連付けられている仮想ネットワークとターゲット VM の仮想ネットワークは同じである必要があります。
3. ターゲット VM のパブリック IP SKU と内部ロード バランサーの SKU は同じである必要があります。
4. ターゲット VM が可用性ゾーンに配置されるように構成されている場合は、ロード バランサーがゾーン冗長であるか、いずれかの可用性ゾーンの一部であるかどうかを確認します (Basic SKU のロード バランサーはゾーンをサポートしていないため、この場合はドロップダウンに表示されません)。
5. あらかじめ作成してあるバックエンド プールとフロントエンド構成が内部ロード バランサーにあることを確認します。


パブリック IP アドレス:
    
1. パブリック IP とターゲット VM のサブスクリプションおよびリージョンは同じである必要があります。
2. ターゲット VM のパブリック IP SKU と内部ロード バランサーの SKU は同じである必要があります。

ネットワーク セキュリティ グループ:
1. ネットワーク セキュリティ グループとターゲット VM のサブスクリプションおよびリージョンは同じである必要があります。


> [!WARNING]
> ターゲット VM が可用性セットに関連付けられている場合は、可用性セット内の他の VM のパブリック IP または内部ロード バランサーと同じ SKU のパブリック IP または内部ロード バランサーを関連付ける必要があります。 そうしないと、フェールオーバーに失敗する可能性があります。
