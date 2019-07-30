---
title: Azure NetApp Files のネットワーク計画のガイドライン | Microsoft Docs
description: Azure NetApp Files を使用して、有効なネットワークアーキテクチャを設計するために役立つガイドラインについて説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: 087ecee053069a02e4d4dd6f636d05ea15269e2e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383487"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Azure NetApp Files のネットワーク計画のガイドライン

ネットワーク アーキテクチャの計画は、あらゆるアプリケーション インフラストラクチャ設計の重要な要素です。 この記事は、お客様のワークロードに有効なネットワーク アーキテクチャを設計し、Azure NetApp Files の豊富な機能からベネフィットを得るために役立ちます。

Azure NetApp Files のボリュームは、Azure Virtual Network 内の[委任されたサブネット](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)と呼ばれる特別な目的のサブネットに含まれるように設計されています。 そのため、必要に応じて、お客様の VNet から直接、同じリージョン内のピアリングされた VNet から、またはオンプレミスから、Virtual Network Gateway (ExpressRoute または VPN Gateway) 経由でボリュームにアクセスできます。 サブネットは Azure NetApp Files 専用であり、他の Azure サービスまたはインターネットへの接続はありません。

## <a name="considerations"></a>考慮事項  

Azure NetApp Files ネットワークを計画するときは、いくつかの考慮事項を理解している必要があります。

### <a name="constraints"></a>制約

次の機能は、Azure NetApp Files では現在サポートされていません。 

* 委任されたサブネットに適用されるネットワーク セキュリティ グループ (NSG)
* Azure NetApp Files サブネットとして次ホップを持つユーザー定義ルート (UDR)
* Azure NetApp Files インターフェイス上の (カスタム名前付けポリシーなどの) Azure ポリシー
* Azure NetApp Files トラフィック用のロード バランサー

Azure NetApp Files には、次のネットワーク制限が適用されます。

* Azure NetApp Files で使用される VNet の IP の数は (ピアリング VNet も含めて) 1,000 を超えることはできません。
* 各 Azure Virtual Network (VNet) で、1 つのサブネットだけを Azure NetApp Files に委任できます。


### <a name="supported-network-topologies"></a>サポートされているネットワーク トポロジ

次の表では、Azure NetApp Files でサポートされているネットワーク トポロジについて説明します。  サポートされていないトポロジの回避策についても説明します。 

|    トポロジ    |    サポートされているか    |     対処法    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    ローカル VNet 内のボリュームへの接続    |    はい    |         |
|    ピアリングされた VNet 内のボリュームへの接続 (同じリージョン)    |    はい    |         |
|    ピアリングされた VNet 内のボリュームへの接続 (クロス リージョンまたはグローバル ピアリング)    |    いいえ    |    なし    |
|    ExpressRoute ゲートウェイ経由でのボリュームへの接続    |    はい    |         |
|    ExpressRoute ゲートウェイ経由で、ゲートウェイ トランジットとの VNet ピアリングを使用して、スポーク VNet 内のボリュームにオンプレミスから接続    |    はい    |        |
|    VPN ゲートウェイ経由で、スポーク VNet 内のボリュームにオンプレミスから接続    |    はい    |         |
|    VPN ゲートウェイ経由で、ゲートウェイ トランジットとの VNet ピアリングを使用して、スポーク VNet 内のボリュームにオンプレミスから接続    |    はい    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Azure NetApp Files ボリューム用の仮想ネットワーク

このセクションでは、仮想ネットワークの計画に役立つ概念について説明します。

### <a name="azure-virtual-networks"></a>Azure 仮想ネットワーク

Azure NetApp Files ボリュームをプロビジョニングする前に、Azure 仮想ネットワーク (VNet) を作成するか、サブスクリプションに既に存在するものを使用する必要があります。 VNet はボリュームのネットワーク境界を定義します。  仮想ネットワークの作成について詳しくは、[Azure Virtual Network のドキュメント](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)を参照してください。

### <a name="subnets"></a>サブネット

サブネットは、仮想ネットワークを個別のアドレス空間に分割し、それらの中にある Azure リソースから使用できるようにするものです。  Azure NetApp Files のボリュームは、[委任されたサブネット](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)と呼ばれる特別な目的のサブネットに含まれています。 

サブネットの委任では、サブネットにサービス固有のリソースを作成するための明示的なアクセス許可が Azure NetApp Files サービスに与えられます。  サービスのデプロイには一意識別子を使用します。 この場合、Azure NetApp Files への接続を可能にするためにネットワーク インターフェイスが作成されます。

新しい VNet を使用する場合は、「[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)」の手順に従って、サブネットを作成し、そのサブネットを Azure NetApp Files に委任することができます。 他のサービスにまだ委任されていない、既存の空のサブネットを委任することもできます。

VNet が別の VNet とピアリングされている場合、VNet アドレス空間を拡張できません。 そのため、新しい委任されたサブネットは VNet アドレス空間内に作成する必要があります。 アドレス空間を拡張する必要がある場合、アドレス空間を拡張する前に VNet ピアリングを削除する必要があります。

### <a name="udrs-and-nsgs"></a>UDR と NSG

Azure NetApp Files 用の委任サブネットでは、ユーザー定義ルート (UDR) とネットワーク セキュリティ グループ (NSG) はサポートされていません。

回避策として、Azure NetApp Files の委任されたサブネットとの間のトラフィックを許可または拒否する他のサブネットに NSG を適用することができます。  

## <a name="azure-native-environments"></a>Azure ネイティブ環境

次の図は、Azure ネイティブ環境を示しています。

![Azure ネイティブ ネットワーク環境](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>ローカル VNet

基本的なシナリオでは、同じ VNet 内の仮想マシン (VM) から Azure NetApp Files ボリュームを作成するか、それに接続します。 上図の VNet 2 では、ボリューム 1 は委任されたサブネットに作成され、既定のサブネットの VM 1 にマウントすることができます。

### <a name="vnet-peering"></a>VNET ピアリング

互いのリソースへのアクセスを必要とする追加の VNet が同じリージョンにある場合、[VNet ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)を使用して VNet を接続することで、Azure インフラストラクチャを介した安全な接続を実現できます。 

上図の VNet 2 と VNet 3 について考えます。 VM 2 が VM 3 とボリューム 2 に接続する必要がある場合、または VM 3 が VM 2 またはボリューム 1 に接続する必要がある場合は、VNet 2 と VNet 3 の間で VNet ピアリングを有効にする必要があります。 

さらに、同じリージョンで VNet 1 が VNet 2 とピアリングされており、VNet 2 が VNet 3 とピアリングされているシナリオを考えます。 VNet 1 のリソースは VNet 2 のリソースに接続できますが、VNet 1 と VNet 3 がピアリングされていない限り、VNet 3 のリソースには接続できません。 

上の図で、VM 3 はボリューム 1 に接続できますが、VM 4 はボリューム 2 に接続できません。  この理由は、スポーク VNet がピアリングされておらず、_トランジット ルーティングが VNet ピアリング経由ではサポートされない_からです。

## <a name="hybrid-environments"></a>ハイブリッド環境

次の図は、ハイブリッド環境を示しています。 

![ハイブリッド ネットワーク環境](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

ハイブリッド シナリオでは、オンプレミスのデータセンターのアプリケーションが Azure のリソースにアクセスする必要があります。  これは、データセンターを Azure に拡張したいのか、Azure ネイティブ サービスを使用したいのか、それともディザスター リカバリーが目的なのかに関係なく当てはまります。 サイト間 VPN または ExpressRoute 経由でオンプレミスの複数のリソースを Azure のリソースに接続する方法については、[VPN Gateway の計画オプション](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)に関するページを参照してください。

ハイブリッド ハブスポーク トポロジでは、Azure のハブ VNet は、オンプレミス ネットワークへの主要な接続ポイントとして機能します。 スポークはハブとピアリングされる VNet であり、ワークロードを分離するために使用できます。

構成によっては、オンプレミスのリソースを、ハブおよびスポーク内のリソースに接続できます。

上に示したトポロジでは、オンプレミス ネットワークは Azure のハブ VNet に接続されており、ハブ VNet とピアリングされているのと同じリージョンに 2 つのスポーク VNet があります。  このシナリオで、Azure NetApp Files ボリュームに対してサポートされている接続オプションは次のとおりです。

* オンプレミス リソース VM 1 および VM 2 は、サイト間 VPN または ExpressRoute 回線経由でハブのボリューム 1 に接続できます。 
* オンプレミス リソースの VM 1 と VM 2 を、サイト間 VPN とリージョンの VNet ピアリング経由でハブのボリューム 2 とボリューム 3 に接続できます。
* ハブ VNet 内の VM 3 は、スポーク VNet 1 内のボリューム 2 と、スポーク VNet 2 内のボリューム 3 に接続できます。
* スポーク VNet 1 の VM 4 と、スポーク VNet 2 の VM 5 は、ハブ VNet 内のボリューム 1 に接続できます。

スポーク VNet 1 内の VM 4 は、スポーク VNet 2 内のボリューム 3 に接続できません。 また、スポーク VNet 2 内の VM 5 は、スポーク VNet 1 内のボリューム 2 に接続できません。 これは、スポーク VNet がピアリングされておらず、_トランジット ルーティングは VNet ピアリング経由ではサポートされていない_からです。

## <a name="next-steps"></a>次の手順

[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)
