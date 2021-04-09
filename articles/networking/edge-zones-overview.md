---
title: Azure Edge Zone プレビューについて
description: 'Microsoft の次のエッジ コンピューティング オファリングについて説明します: Azure Edge Zone。'
services: vnf-manager
author: cherylmc
ms.service: vnf-manager
ms.topic: article
ms.date: 01/13/2021
ms.author: cherylmc
ms.openlocfilehash: 04555303d5128db6c183d27a0c5fcb69063fdc28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185412"
---
# <a name="about-azure-edge-zone-preview"></a>Azure Edge Zone プレビューについて

Azure Edge Zone は、ユーザーの近くでデータ処理を可能にする Microsoft Azure のオファリング ファミリです。 アプリケーションの低待機時間と高スループットの要件に対応するために、VM、コンテナー、およびその他の選択した Azure サービスを Edge Zone にデプロイできます。

Edge Zone の一般的なユース ケース シナリオは次のとおりです。

- ロボット工学でのリアルタイムの命令と制御。
- 人工知能と機械学習によるリアルタイムの分析と推論。
- マシン ビジョン。
- 複合現実および VDI のシナリオのためのリモート レンダリング。
- 臨場感あふれるマルチプレイヤー ゲーム。
- メディアのストリーミングとコンテンツの配信。
- 監視とセキュリティ。

3 種類の Azure Edge Zone があります。

- Azure Edge Zone
- Azure Edge Zones with Carrier
- Azure Private Edge Zones

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zone

![Azure Edge Zone](./media/edge-zones-overview/edge-zones.png "Azure Edge Zone")

Azure Edge Zone は、Azure リージョンから遠く離れた人口密集地に配置される、占有領域の小さい Azure の拡張機能です。 Azure Edge Zone では、VM、コンテナー、選択された Azure サービスのセットがサポートされ、待機時間の影響を受けやすくスループットの大きいアプリケーションをエンド ユーザーの近くで実行できます。 Azure Edge Zone は、Microsoft のグローバル ネットワークの一部です。 ユーザーに近いエッジゾーンで実行されるアプリケーション間に、セキュリティで保護された信頼性の高い高帯域幅の接続が提供されます。 Azure Edge Zone は、Microsoft によって所有および運営されています。 同じ Azure ツールのセットおよび同じポータルを使用して、Edge Zone を管理し、Edge Zone にサービスをデプロイすることができます。

一般的なユース ケースは次のとおりです。

- ゲームおよびゲーム ストリーミング。
- メディアのストリーミングとコンテンツの配信。
- 人工知能と機械学習によるリアルタイムの分析と推論。
- 複合現実のためのレンダリング。

Azure Edge Zone は次の都市部で利用することができます。

- ニューヨーク州ニューヨーク
- カリフォルニア州ロサンゼルス
- フロリダ州マイアミ

詳細については、[Edge Zone チームにお問い合わせください](https://aka.ms/EdgeZones)。

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Azure Edge Zones with Carrier

![Edge Zones with Carrier](./media/edge-zones-overview/edge-carrier.png "Edge Zones with Carrier")

Azure Edge Zone with Carrier は、人口密集地にある携帯電話会社のデータセンター内に配置される、占有領域の小さい Azure の拡張機能です。 Azure Edge Zone with Carrier のインフラストラクチャは、携帯電話会社の 5G ネットワークから 1 ホップ離れた場所に配置されます。 この配置により、モバイル デバイスからアプリケーションに対して 10 ミリ秒未満の待機時間が実現されます。

Azure Edge Zone with Carrier は携帯電話会社のデータセンターにデプロイされ、Microsoft のグローバル ネットワークに接続されます。 ユーザーの近くで実行されるアプリケーション間に、セキュリティで保護された信頼性の高い高帯域幅の接続が提供されます。 開発者は使い慣れた同じツール セットを使用してサービスを構築し、Edge Zone にデプロイすることができます。

一般的なユース ケースは次のとおりです。

- ゲームおよびゲーム ストリーミング。
- メディアのストリーミングとコンテンツの配信。
- 人工知能と機械学習によるリアルタイムの分析と推論。
- 複合現実のためのレンダリング。
- コネクテッド カー。
- 遠隔治療。

Edge Zone は次の事業者と連携して提供されます。

- AT&T (アトランタ、ダラス、およびロサンゼルス)

5G ネットワークに接続する最適化されたスケーラブルなアプリケーションを開発している ISV は、超低待機時間のプラットフォーム、モバイル、および接続されたシナリオをビルドして実験するときに、AT&T で Azure Edge Zones の新しいロサンゼルス プレビューの場所を使用できるようになりました。 セキュリティで保護された高帯域幅の接続を利用するには、早期導入者プログラムに登録します。

詳細については、[Edge Zone チームにお問い合わせください](https://aka.ms/EdgeZones)。

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Azure Private Edge Zones

![Private Edge Zones](./media/edge-zones-overview/private-edge.png "Private Edge Zones")

Azure Private Edge Zone は、オンプレミスに配置される、占有領域の小さい Azure の拡張機能です。 Azure Private Edge Zone は、[Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) プラットフォームが基になっています。 これにより、オンプレミスにデプロイされたコンピューティング サービスとストレージ サービスに、短い待機時間でアクセスすることができます。 また、Private Edge Zone を使用すると、ISV のアプリケーションや仮想化されたネットワーク機能 (VNF) を、[Azure マネージド アプリケーション](https://azure.microsoft.com/services/managed-applications/)として、オンプレミスの仮想マシンやコンテナーと共にデプロイすることができます。 このような VNF には、モバイル パケット コア、ルーター、ファイアウォール、SD-WAN アプライアンスなどを含めることができます。 Azure Private Edge Zone には、クラウドネイティブのオーケストレーション ソリューションが付属しており、VNF とアプリケーションのライフサイクルを Azure portal から管理できます。

Azure Private Edge Zone を使用すると、Azure でアプリケーションを構築およびデプロイするときと同じ使い慣れたツールを使用し、オンプレミスでアプリケーションを開発してデプロイすることができます。

次のこともできます。

- プライベート モバイル ネットワーク (プライベート LTE、プライベート 5G) を実行します。
- ファイアウォールのようなセキュリティ機能を実装します。
- 同じ Private Edge Zone アプライアンス上の SD-WAN アプライアンスを使用することで、複数のブランチと Azure にオンプレミスのネットワークを拡張し、Azure から管理します。

一般的なユース ケースは次のとおりです。

- ロボット工学でのリアルタイムの命令と制御。
- 人工知能と機械学習によるリアルタイムの分析と推論。
- マシン ビジョン。
- 複合現実および VDI のシナリオのためのリモート レンダリング。
- 監視とセキュリティ。

Private Edge Zone を使用してエンドツーエンドのソリューションを実現する、VNF ベンダー、ISV、および MSP パートナーからなる豊富なエコシステムが用意されています。 詳細については、[Private Edge Zone チームにお問い合わせください](https://aka.ms/EdgeZonesPartner)。

### <a name="private-edge-zone-partners"></a><a name="private-edge-partners"></a>Private Edge Zone のパートナー

![Private Edge Zone のパートナー](./media/edge-zones-overview/partners.png "Private Edge Zone パートナー")

#### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>仮想化されたネットワーク機能 (VNF)

##### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>モバイル ネットワーク用の仮想化 Evolved Packet Core (vEPC)

- [Affirmed Networks](https://www.affirmednetworks.com/)
- [Celona](https://www.celona.io/azure-edge)
- [Druid Software](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Mavenir](https://mavenir.com/)
- [Metaswitch](https://www.metaswitch.com/)
- [Nokia Digital Automation Cloud](https://www.dac.nokia.com/)

##### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>移動無線パートナー

- [Celona](https://www.celona.io/azure-edge)
- [Commscope Ruckus](https://support.ruckuswireless.com/)

##### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>SD-WAN ベンダー

- [128 Technology](https://www.128technology.com/)
- [NetFoundry](https://netfoundry.io/)
- [Nuage Networks (Nokia)](https://www.nuagenetworks.net/)
- [Versa Networks](https://www.versa-networks.com/)
- [VMware SD-WAN (Velocloud)](https://www.velocloud.com/)

##### <a name="router-vendors"></a><a name="router-vendors"></a>ルーター ベンダー

- [Arista](https://www.arista.com/)

##### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>ファイアウォール ベンダー

- [Palo Alto Networks](https://www.paloaltonetworks.com/)

##### <a name="managed-solutions-providers-mobile-operators-and-global-system-integrators-gsis"></a><a name="msp-mobile"></a>マネージド ソリューション プロバイダー: 携帯電話会社およびグローバル システム インテグレーター (GSI)

| GSI と運営会社 | 携帯電話会社 |
| --- | --- |
| Amdocs                       | Etisalat             |
| American Tower               | NTT Communications   |
| CenturyLink                  | Proximus             |
| Expeto                       | Rogers               |
| Federated Wireless           | SK Telecom           |
| Infosys                      | Telefonica           |
| Tech Mahindra                | Telstra              |
|                              | Vodafone             |

パートナーになる方法については、[Private Edge Zone チームにお問い合わせください](https://aka.ms/EdgeZonesPartner)。

### <a name="private-edge-zone-solutions"></a><a name="solutions-private-edge"></a>Private Edge Zone のソリューション

#### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Private Edge Zone のプライベート モバイル ネットワーク

![Private Edge Zone のプライベート モバイル ネットワーク](./media/edge-zones-overview/mobile-networks.png "Private Edge Zone のプライベート モバイル ネットワーク")

Private Edge Zone にプライベート モバイル ネットワークをデプロイできるようになりました。 プライベート モバイル ネットワークを使用すると、ビジネス クリティカルなアプリケーションに必要な、待機時間が非常に短く高容量で信頼性の高い、セキュリティで保護されたワイヤレス ネットワークを実現できます。

プライベート モバイル ネットワークでは、次のようなシナリオを実現することができます。
- 倉庫内の無人搬送車 (AGV) の命令や制御。
- スマート ファクトリ内のロボット間のリアルタイム通信。
- 拡張現実と仮想現実のエッジ アプリケーション。

仮想化 Evolved Packet Core (vEPC) ネットワーク機能は、プライベート モバイル ネットワークの頭脳になります。 Private Edge Zones で vEPC をデプロイできるようになりました。 Private Edge Zone で利用可能な vEPC パートナーの一覧については、[vEPC の ISV](#vEPC) を参照してください。

Private Edge Zone にプライベート モバイル ネットワーク ソリューションをデプロイするには、モバイル アクセス ポイント、SIM カード、ルーターなどの他の VNF といったコンポーネントが必要です。 プライベート モバイル ネットワークを設定するには、ライセンスされた、またはライセンスされていない周波数帯へのアクセスが重要です。 また、RF 計画、物理的なレイアウト、インストール、サポートに関する支援が必要になる場合があります。 パートナーの一覧については、「[移動無線パートナー](#mobile-radio)」を参照してください。

Microsoft では、このプロセスのあらゆる側面を支援するパートナー エコシステムが提供されています。 パートナーは、ネットワークの計画、必要なデバイスの購入、ハードウェアの設定、Azure からの構成の管理をお手伝いできます。 Microsoft と緊密に統合されている実証済みの一連のパートナーにより、信頼性が高く使いやすいソリューションが保証されます。 お客様は、中核となるシナリオに専念し、他の作業は Microsoft とそのパートナーに任せることができます。

#### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>Private Edge Zones での SD-WAN

![Private Edge Zones での SD-WAN](./media/edge-zones-overview/sd-wan.png "Private Edge Zones での SD-WAN")

SD-WAN を使うと、次のような利点のあるエンタープライズ レベルのワイド エリア ネットワーク (WAN) を作成できます。

- 帯域幅の増加
- クラウドへの高パフォーマンスのアクセス
- サービスの挿入
- [信頼性]
- ポリシー管理
- 広範なネットワークの可視性

SD-WAN により、冗長性のある中央コントローラーからの調整されたシームレスなブランチ オフィス接続が、低い所有コストで実現されます。
Private Edge Zone の SD-WAN を使うと、CapEx 中心モデルから、サービスとしてのソフトウェア (SaaS) モデルに移行し、IT 予算を減らすことができます。 任意の SD-WAN パートナー、オーケストレーター、またはコントローラーを使用して、新しいサービスを有効にし、それらをネットワーク全体に直ちに伝達することができます。

## <a name="next-steps"></a>次のステップ

詳しくは、以下のチームにお問い合わせください。

* [Edge Zone チーム](https://aka.ms/EdgeZones)
* [Private Edge Zone チーム (パートナーになる場合)](https://aka.ms/EdgeZonesPartner)
