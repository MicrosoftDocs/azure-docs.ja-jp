---
title: 回線構成のワークフロー - ExpressRoute:Azure| Microsoft Docs
description: このページでは、ExpressRoute の回線とピアリングを構成するためのワークフローについて説明します。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 3ffcc5ac2193e607573ceb93717258f5349d1f15
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101689"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>回線のプロビジョニングと回線の状態の ExpressRoute ワークフロー
このページでは、サービス プロビジョニングと上位のルーティング構成ワークフローについて段階的に説明します。

![回線のワークフロー](./media/expressroute-workflows/expressroute-circuit-workflow.png)

下の図とそれに対応する手順は、ExpressRoute 回線をエンドツーエンドでプロビジョニングするための作業を示しています。 

1. PowerShell を利用し、ExpressRoute 回線を構成します。 詳細については、 [ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md) に関する記事を参照してください。
2. サービス プロバイダーに接続を要求します。 このプロセスはさまざまです。 接続の要求方法に関する詳細については、接続プロバイダーにお問い合わせください。
3. PowerShell で ExpressRoute のプロビジョニング状態を確認することで、回線が正常にプロビジョニングされていることを確認します。 
4. ルーティング ドメインを構成します。 接続プロバイダーが代わりにレイヤー 3 を管理する場合、接続プロバイダーが回線のルーティングを構成します。 接続プロバイダーがレイヤー 2 サービスのみを提供する場合、[ルーティング要件](expressroute-routing.md)ページと[ルーティング構成](expressroute-howto-routing-classic.md)ページのガイドラインに従い、自分でルーティングを構成する必要があります。
   
   * Azure プライベート ピアリングを有効にする - 仮想ネットワーク内にデプロイされている VM / クラウドに接続するには、このピアリングを有効にします。

   * Microsoft ピアリングを有効にする - Office 365 と Dynamics 365 サービスにアクセスするにはこれを有効にします。 さらに、すべての Azure PaaS サービスは、Microsoft ピアリング経由でアクセスできます。
     
     > [!IMPORTANT]
     > Microsoft に接続するには、インターネットに使用しているプロキシ/エッジとは別のプロキシ/エッジを使用する必要があります。 ExpressRoute とインターネットの両方に同じエッジを使用すると、ルーティングが非同期になり、ネットワークの接続が停止します。
     > 
     > 
     
     ![ワークフローのルーティング](./media/expressroute-workflows/routing-workflow.png)
5. ExpressRoute 回線に仮想ネットワークをリンクする - 仮想ネットワークを ExpressRoute 回線にリンクできます。 指示に従い、回線に [VNet をリンク](expressroute-howto-linkvnet-arm.md) してください。 VNet は ExpressRoute 回線と同じ Azure サブスクリプションに配置するか、別のサブスクリプションに配置できます。

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute 回線のプロビジョニング状態
各 ExpressRoute 回線には 2 つの状態があります。

* サービス プロバイダーのプロビジョニング状態
* Status

状態は Microsoft のプロビジョニングの状態を表します。 Expressroute 回線を作成したとき、このプロパティは "有効" に設定されます。

接続プロバイダーのプロビジョニング状態は接続プロバイダー側の状態を表します。 これは「*プロビジョニングされていません*」、「*プロビジョニングしています*」、「*プロビジョニング済み*」のいずれかになります。 ExpressRoute 回線を使用するには「プロビジョニング済み」状態になっている必要があります。

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute 回線の状態
このセクションでは、ExpressRoute 回線の状態を一覧にまとめています。

**作成時**

PowerShell コマンドレットを実行し、ExpressRoute 回線を作成すると、直後に ExpressRoute 回線の状態が以下のようになります。

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**接続プロバイダーが回線にプロビジョニングしているとき**

サービス キーを接続プロバイダーに渡し、接続プロバイダーがプロビジョニングを開始すると、直後に ExpressRoute 回線の状態が以下のようになります。

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**接続プロバイダーがプロビジョニングを完了したとき**

接続プロバイダーがプロビジョニングを完了すると、直後に ExpressRoute 回線の状態が以下のようになります。

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

回線は「プロビジョニング済み」で「有効」の状態でなければ使用できません。 レイヤー 2 のプロバイダーを使用している場合、この状態にあるときにのみ、回線のルーティングを構成できます。

**接続プロバイダーが回線をプロビジョニング解除しているとき**

サービス プロバイダーに ExpressRoute 回線のプロビジョニング解除を要請した場合、サービス プロバイダーがプロビジョニング解除を完了すると、回線が以下の状態になります。

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


必要に応じて再度有効にしたり、PowerShell コマンドレットを実行して回線を削除したりできます。  

> [!IMPORTANT]
> ServiceProviderProvisioningState が "プロビジョニング中" または "プロビジョニング済み" のときに、回線を削除する PowerShell コマンドレットを実行すると、操作は失敗します。 最初に ExpressRoute 回線のプロビジョニング解除を接続プロバイダーに要請してから、回線を削除してください。 PowerShell コマンドレットを実行して回線を削除するまで、Microsoft は回線に課金を続けます。
> 
> 

## <a name="routing-session-configuration-state"></a>ルーティング セッション構成の状態
BGP プロビジョニング状態からは、Microsoft エッジで BGP セッションが有効になっているかどうかがわかります。 ピアリングを使用するにはこの状態を有効にする必要があります。

特に Microsoft ピアリングの場合、BGP セッションの状態を確認することが重要です。 BGP プロビジョニング状態に加え、「 *アドバタイズされたパブリック プレフィックス*」と呼ばれているもう 1 つの状態があります。 BGP セッションを開始し、ルーティングをエンドツーエンドで動作させるには、「アドバタイズされたパブリック プレフィックス」状態を「 *構成済み* 」にする必要があります。 

「アドバタイズされたパブリック プレフィックス」状態が「*検証必須*」に設定されている場合、BGP セッションは無効になっています。アドバタイズされたプレフィックスがルーティング レジストリの AS 番号に一致しなかったためです。 

> [!IMPORTANT]
> 「アドバタイズされたパブリック プレフィックス」状態が「 *手動検証* 」の場合、 [Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) でサポート チケットを開き、関連自律システム番号と共にアドバタイズされた IP アドレスを所有している証拠を提出する必要があります。
> 
> 

## <a name="next-steps"></a>次の手順
* ExpressRoute 接続を構成します。
  
  * [ExpressRoute 回線の作成](expressroute-howto-circuit-arm.md)
  * [ルーティングの構成](expressroute-howto-routing-arm.md)
  * [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-arm.md)

