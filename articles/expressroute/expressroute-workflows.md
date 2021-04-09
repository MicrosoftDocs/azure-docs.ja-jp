---
title: 'Azure ExpressRoute: 回線構成のワークフロー'
description: このページでは、ExpressRoute の回線とピアリングを構成するためのワークフローについて説明します
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: duau
ms.custom: contperf-fy21q1
ms.openlocfilehash: 24ad325cae2ee71ad49ee8ee055a83ceb8fa7ef2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721737"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>回線のプロビジョニングと回線の状態の ExpressRoute ワークフロー

この記事では、サービス プロビジョニングおよびルーティング構成のワークフローの概要について説明します。 以下のセクションでは、ExpressRoute 回線をエンド ツー エンドでプロビジョニングするためのタスクの概要を示します。

## <a name="workflow-steps"></a>ワークフローのステップ

### <a name="1-prerequisites"></a>1.前提条件

前提条件が満たされていることを確認します。 完全な一覧については、[前提条件とチェックリスト](expressroute-prerequisites.md)に関する記事を参照してください。

* Azure サブスクリプションが作成されている。
* 物理接続が、ExpressRoute パートナーを使用して確立されているか、ExpressRoute Direct を介して構成されている。 場所を確認し、「[場所とパートナー](expressroute-locations-providers.md#partners)」を参照して、ExpressRoute パートナーと、ピアリングの場所の間の ExpressRoute Direct 接続を確認してください。

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2.接続の注文または ExpressRoute Direct の構成

サービス プロバイダーに接続を注文するか、ExpressRoute Direct を構成します。

#### <a name="expressroute-partner-model"></a>ExpressRoute パートナー モデル

サービス プロバイダーに接続を要求します。 このプロセスはさまざまです。 接続の要求方法に関する詳細については、接続プロバイダーにお問い合わせください。

* ExpressRoute パートナーを選択します
* ピアリングの場所を選択します
* 帯域幅を選択します
* 課金モデルを選択します
* Standard または Premium アドオンを選択します

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct モデル

* ピアリングの場所全体で使用可能な ExpressRoute Direct 容量を表示します。
* お使いの Azure サブスクリプションに ExpressRoute Direct リソースを作成して、ポートを予約します。
* 承認状を要求して受け取り、ピアリングの場所のプロバイダーに物理的な交差接続を注文します。
* 管理状態を有効にし、[Azure Monitor](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics) を使用して、ライト レベルと物理リンクを表示します。

### <a name="3-create-an-expressroute-circuit"></a>3.ExpressRoute 回線の作成

#### <a name="expressroute-partner-model"></a>ExpressRoute パートナー モデル

ExpressRoute パートナーが接続をプロビジョニングする準備ができていることを確認します。 ExpressRoute 回線の課金は、サービス キーが発行されたときから始まります。 [ExpressRoute 回線の作成](expressroute-howto-circuit-portal-resource-manager.md)の手順を使用して、回線を作成します。

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct モデル

物理リンクと管理状態が両方のリンクの間で有効になっていることを確認します。 ガイダンスについては、[ExpressRoute Direct の構成方法](how-to-expressroute-direct-portal.md)に関する記事を参照してください。 ExpressRoute 回線の課金は、サービス キーが発行されたときから始まります。 [ExpressRoute 回線の作成](expressroute-howto-circuit-portal-resource-manager.md)の手順を使用して、回線を作成します。

### <a name="4-service-provider-provisions-connectivity"></a>4.サービス プロバイダーが接続をプロビジョニングする

このセクションは、ExpressRoute パートナー接続モデルにのみ関連します。

* サービス キー (s キー) を接続プロバイダーに提供します。
* 接続プロバイダーが必要とする追加情報 (VPN ID など) を提供します。
* プロバイダーがルーティング構成を管理する場合は、必要な詳細を提供します。

PowerShell、Azure portal、または CLI を使用して ExpressRoute 回線のプロビジョニング状態を確認することで、回線が正常にプロビジョニングされていることを確認できます。

### <a name="5-configure-routing-domains"></a>5.ルーティング ドメインを構成する

ルーティング ドメインを構成します。 接続プロバイダーがレイヤー 3 構成を管理する場合、接続プロバイダーが回線のルーティングを構成します。 接続プロバイダーがレイヤー 2 サービスのみを提供する場合、またはお客様が ExpressRoute Direct を使用している場合は、[ルーティングの要件](expressroute-routing.md)および[ルーティング構成](expressroute-howto-routing-classic.md)に関する記事に記載されているガイドラインに従い、自分でルーティングを構成する必要があります。

#### <a name="for-azure-private-peering"></a>Azure プライベート ピアリングの場合

Azure 仮想ネットワーク内にデプロイされている VM およびクラウド サービスに接続するには、プライベート ピアリングを有効にします。

* IPv4 サブネット:
    * パス 1 のピアリング サブネット (/30)
    * パス 2 のピアリング サブネット (/30)
* IPv6 サブネット (省略可能):
    * パス 1 のピアリング サブネット (/126)
    * パス 2 のピアリング サブネット (/126)
* ピアリングの VLAN ID
* ピアリングの AS 番号
* ExpressRoute の AS 番号 = 12076
* MD5 ハッシュ (省略可能)

#### <a name="for-microsoft-peering"></a>Microsoft ピアリングの場合

Microsoft 365 などの Microsoft オンライン サービスにアクセスするには、これを有効にします。 さらに、すべての Azure PaaS サービスは、Microsoft ピアリング経由でアクセスできます。 Microsoft への接続には、インターネットに使用しているものとは別のプロキシ/エッジを必ず使用する必要があります。 ExpressRoute とインターネットの両方に同じエッジを使用すると、ルーティングが非同期になり、ネットワークの接続が停止します。

* IPv4 サブネット:
    * パス 1 のピアリング サブネット (/30) - パブリック IP である必要があります
    * パス 2 のピアリング サブネット (/30) - パブリック IP である必要があります
* IPv6 サブネット (省略可能):
    * パス 1 のピアリング サブネット (/126) - パブリック IP である必要があります
    * パス 2 のピアリング サブネット (/126) - パブリック IP である必要があります
* ピアリングの VLAN ID
* ピアリングの AS 番号
* アドバタイズされたプレフィックス - パブリック IP プレフィックスである必要があります
* 顧客の AS 番号 (ピアリングの AS 番号と異なる場合は省略可能)
* IP および AS 番号検証の RIR/IRR
* ExpressRoute の AS 番号 = 12076
* MD5 ハッシュ (省略可能)

### <a name="6-start-using-the-expressroute-circuit"></a>6.ExpressRoute 回線の使用を開始する

* Azure 仮想ネットワークを ExpressRoute 回線にリンクして、オンプレミスから Azure 仮想ネットワークへの接続を有効にすることができます。 ガイダンスについては、[回線への VNet のリンク](expressroute-howto-linkvnet-arm.md)に関する記事を参照してください。 VNet は ExpressRoute 回線と同じ Azure サブスクリプションに配置するか、別のサブスクリプションに配置できます。
* Microsoft ピアリングを使用して、Azure サービスと Microsoft クラウド サービスに接続します。

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>ExpressRoute パートナー回線のプロビジョニング状態

次のセクションでは、ExpressRoute パートナー接続モデルのさまざまな ExpressRoute 回線の状態について説明します。
各 ExpressRoute パートナー回線には次の 2 つの状態があります。

* **ServiceProviderProvisioningState** は接続プロバイダー側の状態を表します。 これは「*プロビジョニングされていません*」、「*プロビジョニングしています*」、「*プロビジョニング済み*」のいずれかになります。 ピアリングを構成するためには、ExpressRoute 回線がプロビジョニング済み状態になっている必要があります。 **この状態は、ExpressRoute パートナー回線にのみ関連しており、ExpressRoute Direct 回線のプロパティには表示されません**。

* **Status** は Microsoft のプロビジョニングの状態を表します。 ExpressRoute 回線を作成したとき、このプロパティは Enabled (有効) に設定されます

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute 回線の状態

次のセクションでは、ExpressRoute パートナー接続モデルのもとで作成された ExpressRoute 回線の予想される状態について説明します。

**作成時**

ExpressRoute 回線は、リソースの作成時に次の状態を報告します。

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**接続プロバイダーが回線をプロビジョニングしているとき**

ExpressRoute 回線は、接続プロバイダーが回線のプロビジョニングを処理している間に次の状態を報告します。

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**接続プロバイダーがプロビジョニング プロセスを完了したとき**

ExpressRoute 回線では、接続プロバイダーによって回線が正常にプロビジョニングされると、次の状態が報告されます。

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**接続プロバイダーが回線のプロビジョニングを解除しているとき**

ExpressRoute 回線をプロビジョニング解除する必要がある場合、サービス プロバイダーがプロビジョニング解除プロセスを完了したときに、回線は次の状態を報告します。

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

必要に応じて再度有効にしたり、PowerShell コマンドレットを実行して回線を削除したりできます。  

> [!IMPORTANT]
> ServiceProviderProvisioningState が Provisioning または Provisioned である場合、回線を削除することはできません。 接続プロバイダーを削除するには、その前に回線をプロビジョニング解除する必要があります。 Microsoft は、ExpressRoute 回線リソースが Azure で削除されるまで、引き続き回線の課金を行います。
> 

## <a name="routing-session-configuration-state"></a>ルーティング セッション構成の状態

BGP プロビジョニング状態では、Microsoft Edge で BGP セッションが有効になっているかどうかを報告します。 プライベートまたは Microsoft ピアリングを使用するには、状態を有効にする必要があります。

特に Microsoft ピアリングの場合、BGP セッションの状態を確認することが重要です。 BGP プロビジョニング状態に加え、「 *アドバタイズされたパブリック プレフィックス*」と呼ばれているもう 1 つの状態があります。 BGP セッションを開始し、ルーティングをエンド ツー エンドで動作させるには、「アドバタイズされたパブリック プレフィックス」状態を「*構成済み*」にする必要があります。 

「アドバタイズされたパブリック プレフィックス」状態が「*検証必須*」に設定されている場合、BGP セッションは無効になっています。アドバタイズされたプレフィックスがルーティング レジストリの AS 番号に一致しなかったためです。

> [!IMPORTANT]
> 「アドバタイズされたパブリック プレフィックス」状態が「*手動検証*」の場合、[Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)でサポート チケットを開き、関連自律システム番号と共にアドバタイズされた IP アドレスを所有している証拠を提出する必要があります。
> 
> 

## <a name="next-steps"></a>次のステップ

* ExpressRoute 接続を構成します。
  
  * [ExpressRoute 回線の作成](expressroute-howto-circuit-arm.md)
  * [ルーティングの構成](expressroute-howto-routing-arm.md)
  * [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-arm.md)
