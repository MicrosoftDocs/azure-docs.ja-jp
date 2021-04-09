---
title: フェールオーバー VM のネットワーク構成をカスタマイズする | Microsoft Docs
description: Azure Site Recovery を使用して Azure VM のレプリケーションを行うときの、フェールオーバー VM のネットワーク構成のカスタマイズについて概説します。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: sideeksh
ms.openlocfilehash: f63021275574e294fa372357d6e62724f5efe0f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98541192"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>ターゲット Azure VM のネットワーク構成をカスタマイズする

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用してリージョン間で Azure 仮想マシン (VM) をレプリケートおよび復旧するときの、ターゲット Azure VM におけるネットワーク構成のカスタマイズに関するガイダンスを提供します。

## <a name="before-you-start"></a>開始する前に

[このシナリオ](azure-to-azure-architecture.md)に対して、Site Recovery でどのようにディザスター リカバリーを提供するかについて説明します。

## <a name="supported-networking-resources"></a>サポートされているネットワーク リソース

Azure VM のレプリケート中に、フェールオーバー VM に対して次の主要なリソース構成を提供できます。

- [内部ロード バランサー](../load-balancer/load-balancer-overview.md)
- [パブリック IP](../virtual-network/public-ip-addresses.md)
- サブネットと NIC の両方の[ネットワーク セキュリティ グループ](../virtual-network/manage-network-security-group.md)

## <a name="prerequisites"></a>前提条件

- 復旧側の構成を事前に計画していることを確認します。
- 事前にネットワーク リソースを作成します。 これを入力として提供して、Azure Site Recovery サービスがこれらの設定を受け入れ、フェールオーバー VM にこれらの設定が確実に適用されるようにします。

## <a name="customize-failover-and-test-failover-networking-configurations"></a>フェールオーバーおよびテスト フェールオーバーのネットワーク構成をカスタマイズする

1. **[レプリケートされたアイテム]** に移動します。 
2. 目的の Azure VM を選択します。
3. **[コンピューティングとネットワーク]** を選択し、 **[編集]** を選択します。 NIC 構成設定のソースには、対応するリソースが含まれていることがわかります。 

     ![フェールオーバー ネットワーク構成をカスタマイズする](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. テスト フェールオーバーの仮想ネットワークを選択します。
5. 構成する NIC の近くにある **[編集]** を選択します。 開かれる次のブレードで、テスト フェールオーバーまたはフェールオーバーの場所にあらかじめ作成してある、対応するリソースを選択します。

    ![NIC 構成ファイルを編集する](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. **[OK]** を選択します。

Site Recovery がこれらの設定を受け入れ、フェールオーバー時の VM が、対応する NIC を介して、選択したリソースに接続されるようになります。

復旧計画を介してテスト フェールオーバーをトリガーすると、常に Azure 仮想ネットワークをたずねられます。 この仮想ネットワークは、テスト フェールオーバー設定が事前に構成されていないマシンのテスト フェールオーバーに使用されます。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="unable-to-view-or-select-a-resource"></a>リソースを表示または選択できない

ネットワーク リソースを選択または表示できない場合は、次のチェック項目と条件を確認してください。

- ネットワーク リソースのターゲット フィールドは、ソース VM に対応する入力がある場合にのみ有効になります。 これは、ディザスター リカバリーのシナリオでは、ソースと同じバージョンまたはスケールダウンされたバージョンのいずれかが必要になるという原則に基づいています。
- 各ネットワーク リソースに対して、ドロップダウン リストでいくつかのフィルターが適用されます。これにより、フェールオーバー VM が選択されたリソースに自動的にアタッチされ、フェールオーバーの信頼性が確実に維持されます。 これらのフィルターは、ソース VM を構成したときに検証されたのと同じネットワーク条件に基づいています。

内部ロード バランサーの検証:

- ロード バランサーとターゲット VM のサブスクリプションおよびリージョンは同じである必要があります。
- 内部ロード バランサーに関連付けられている仮想ネットワークとターゲット VM の仮想ネットワークは同じである必要があります。
- ターゲット VM のパブリック IP SKU と内部ロード バランサーの SKU は同じである必要があります。
- ターゲット VM が可用性ゾーンに配置されるように構成されている場合は、ロード バランサーがゾーン冗長であるか、いずれかの可用性ゾーンの一部であるかどうかを確認します (Basic SKU のロード バランサーではゾーンがサポートされていないため、この場合はドロップダウン リストに表示されません)。
- 事前に作成済みのバックエンド プールとフロントエンド構成が確実に内部ロード バランサーにあるようにします。

パブリック IP アドレス:

- パブリック IP とターゲット VM のサブスクリプションおよびリージョンは同じである必要があります。
- ターゲット VM のパブリック IP SKU と内部ロード バランサーの SKU は同じである必要があります。

ネットワーク セキュリティ グループ:
- ネットワーク セキュリティ グループとターゲット VM のサブスクリプションおよびリージョンは同じである必要があります。


> [!WARNING]
> ターゲット VM が可用性セットに関連付けられている場合は、可用性セット内の他の VM のパブリック IP および内部ロード バランサーと同じ SKU のパブリック IP および内部ロード バランサーを関連付ける必要があります。 そうしないと、フェールオーバーが失敗する場合があります。
