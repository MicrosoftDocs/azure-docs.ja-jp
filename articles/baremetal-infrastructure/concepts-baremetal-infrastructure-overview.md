---
title: Azure での BareMetal インフラストラクチャ プレビューの概要
description: Azure の BareMetal インフラストラクチャの概要。
ms.custom: references_regions
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: 603aa6504531ef8a75fccbc9d9cc6de648b42373
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954634"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>Azure 上の BareMetal インフラストラクチャ プレビューとは

Azure BareMetal インフラストラクチャには、企業のカスタム ワークロードを移行するためのセキュリティで保護されたソリューションが用意されています。 BareMetal インスタンスは、ユーザーに割り当てられた非共有のホストおよびサーバー ハードウェアです。 認定されたハードウェア、ライセンス、およびサポート契約を必要とする専用のワークロードを使用して、オンプレミス ソリューションを移植できるようになります。 インフラストラクチャの監視とメンテナンスは Azure によって処理されますが、ゲスト オペレーティング システム (OS) 内の監視とアプリケーションの監視はユーザーの所有権内に分類されます。

BareMetal インフラストラクチャを使用すると、既存の投資とアーキテクチャを維持しながら、インフラストラクチャのランドスケープを最新化することができます。 BareMetal インフラストラクチャを使用すると、専用のワークロードを Azure に導入できるため、短い待機時間で Azure サービスにアクセスして統合できます。

## <a name="sku-availability-in-azure-regions"></a>Azure リージョンの SKU の可用性
リビジョン 4.2 (Rev 4.2) スタンプに基づく 4 つのリージョンから、専用および汎用のワークロード用の BareMetal インフラストラクチャを使用できます。
- 西ヨーロッパ
- 北ヨーロッパ
- 米国東部 2
- 米国中南部

>[!NOTE]
>**Rev 4.2** は、既存の Rev 4 アーキテクチャを使用した、最新のブランド変更された BareMetal インフラストラクチャです。  Rev 4 では、Azure 仮想マシン (VM) ホストにより近接します。 これにより、Azure VM と Rev 4 のスタンプまたは行にデプロイされた BareMetal インスタンス ユニットの間のネットワーク待ち時間が大幅に改善されます。  Azure portal を使用して BareMetal インスタンスにアクセスし、管理することができます。 

## <a name="support"></a>サポート
BareMetal インフラストラクチャは、ISO 27001、ISO 27017、SOC 1、SOC 2 に準拠しています。  また、次のライセンス持ち込み (BYOL) モデルも使用されます (OS、専用のワークロード、およびサードパーティー製のアプリケーション)。  

ルート アクセスとフル コントロールを受け取るとすぐに、次の責任を負うことになります。
- バックアップと回復のソリューション、高可用性、ディザスター リカバリーの設計と実装
- OS およびサードパーティ製ソフトウェアのライセンス、セキュリティ、およびサポート

Microsoft の責任は次のとおりです。
- 専用のワークロードにハードウェアを提供する 
- OS をプロビジョニングする

:::image type="content" source="media/baremetal-support-model.png" alt-text="BareMetal インフラストラクチャ サポート モデル" border="false":::

## <a name="compute"></a>Compute
BareMetal インフラストラクチャには、専用のワークロード用に複数の SKU が用意されています。 使用できる SKU は、小型の 2 ソケット システムから 24 ソケット システムまでさまざまです。 専用のワークロードには、ワークロード固有の SKU を使用します。

BareMetal インスタンス スタンプ自体は次のコンポーネントを結合します。

- **コンピューティング:** 必要なコンピューティング機能を提供し、専用のワークロードに対して認定された、さまざまな世代の Intel Xeon プロセッサに基づくサーバー。

- **ネットワーク:** コンピューティング、ストレージ、および LAN コンポーネントを相互接続する統合された高速ネットワーク ファブリック。

- **ストレージ:** 統合されたネットワーク ファブリック経由でアクセスされるインフラストラクチャ。

BareMetal スタンプのマルチテナント インフラストラクチャ内では、分離されたテナントとしてお客様の環境がデプロイされます。 テナントのデプロイ時に、Azure 加入契約内で Azure サブスクリプションに名前を付けます。 この Azure サブスクリプションは、BareMetal のインスタンスに対して課金されるものです。

>[!NOTE]
>BareMetal インスタンスにデプロイされたお客様は、テナントに分離されます。 テナントは、ネットワーク、ストレージ、およびコンピューティング レイヤーで他のテナントから分離されます。 別のテナントに割り当てられているストレージ ユニットやコンピューティング ユニットは、BareMetal インスタンスで相互に認識したり通信したりすることはできません。

## <a name="os"></a>OS
BareMetal インスタンスのプロビジョニング時に、コンピューターにインストールする OS を選択できます。 

>[!NOTE]
>BareMetal インフラストラクチャは BYOL モデルであることに注意してください。

使用できる Linux OS のバージョンは次のとおりです。
- Red Hat Enterprise Linux (RHEL) 7.6
- SUSE Linux Enterprise Server (SLES)
   - SLES 12 SP2
   - SLES 12 SP3
   - SLES 12 SP4
   - SLES 12 SP5
   - SLES 15 SP1

## <a name="storage"></a>ストレージ
特定の SKU の種類に基づく BareMetal インスタンスには、特定のワークロードの種類に対応する事前定義された NFS ストレージが付属しています。 BareMetal をプロビジョニングするときに、サポート リクエストを送信することで、予測される成長率に基づいてより多くのストレージをプロビジョニングできます。 すべてのストレージには、NFSv3 および NFSv4 をサポートするリビジョン 4.2 のオールフラッシュ ディスクが付属しています。 新しいリビジョン 4.5 NVMe SSD を使用できるようになる予定です。 ストレージのサイズ設定の詳細については、[BareMetal ワークロードの種類](../virtual-machines/workloads/sap/get-started.md)に関するセクションを参照してください。

>[!NOTE]
>BareMetal に使用されるストレージは、[Federal Information Processing Standard (FIPS) Publication 140-2](/microsoft-365/compliance/offering-fips-140-2) の要件を満たしており、既定で保存時の暗号化を提供します。 データは、セキュリティで保護された状態でディスクに格納されます。

## <a name="networking"></a>ネットワーク
Azure ネットワーク サービスのアーキテクチャは、BareMetal インスタンスに専用のワークロードを正常にデプロイするための重要なコンポーネントです。 すべての IT システムが既に Azure に配置されているとは限りません。 Azure には、オンプレミスのソフトウェア デプロイで Azure を仮想データ センターのように取り扱うためのネットワーク テクノロジが用意されています。 BareMetal インスタンスに必要な Azure ネットワーク機能は次のとおりです。

- Azure 仮想ネットワークは、オンプレミスのネットワーク資産に接続する ExpressRoute 回線に接続します。
- オンプレミスを Azure に接続する ExpressRoute 回線には、1 Gbps 以上の帯域幅が必要です。
- Azure 内の、または Azure で完全に実行されている拡張 Active ディレクトリと DNS。

ExpressRoute を使用すると、接続プロバイダーが提供するプライベート接続を介して、オンプレミスのネットワークを Microsoft クラウドに拡張できます。 **ExpressRoute Premium** を有効にして地政学的な境界を越えて接続を拡張したり、**ExpressRoute Local** を使用して、目的の Azure リージョンの近くにある場所間でコスト効率の高いデータ転送を行うことができます。

BareMetal インスタンスは、Azure VNET サーバーの IP アドレス範囲内でプロビジョニングされます。

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Azure BareMetal インフラストラクチャの図" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

上記のアーキテクチャは、次の 3 つのセクションに分かれています。
- **左:** パートナーまたはローカルのエッジ ルーター (Equinix など) を介して接続し、さまざまなアプリケーションを実行しているお客様のオンプレミス インフラストラクチャを示します。 詳細については、[接続プロバイダー: Azure ExpressRoute](../expressroute/expressroute-locations.md) に関する記事を参照してください。
- **中央:** Azure エッジ ネットワークへの接続を提供する、Azure サブスクリプションを使用してプロビジョニングされた [ExpressRoute](../expressroute/expressroute-introduction.md) を示します。
- **右:** Azure IaaS を示します。この場合、VM を使用して、Azure 仮想ネットワーク内でプロビジョニングされるアプリケーションをホストします。
- **下:** 低遅延の BareMetal 接続のために、[ExpressRoute FastPath](../expressroute/about-fastpath.md) を有効にした ExpressRoute ゲートウェイの使用を示します。   
   >[!TIP]
   >これをサポートするには、ExpressRoute ゲートウェイを UltraPerformance にする必要があります。  詳細については、「[ExpressRoute の仮想ネットワーク ゲートウェイについて](../expressroute/expressroute-about-virtual-network-gateways.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

次のステップでは、Azure portal を使用して BareMetal インスタンス ユニットを識別および操作する方法について説明します。

> [!div class="nextstepaction"]
> [Azure portal を使用して BareMetal インスタンスを管理する](connect-baremetal-infrastructure.md)