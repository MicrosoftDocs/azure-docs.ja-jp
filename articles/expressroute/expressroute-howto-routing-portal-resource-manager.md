---
title: 回線のピアリングを構成する - ExpressRoute:Azure | Microsoft Docs
description: この記事では、ExpressRoute 回線のプライベート、パブリックおよび Microsoft ピアリングを作成し、プロビジョニングする手順について説明します。 この記事では、回線のピアリングの状態確認、更新、または削除の方法も示します。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 6d1ce56ef66d224b89f49a00c2883ebbf22a5745
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622027"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>ExpressRoute 回線のピアリングの作成と変更を行う

この記事では、Azure Portal を使って、Resource Manager デプロイ モデルで ExpressRoute 回線のルーティング構成を作成し、管理します。 また、ExpressRoute 回線の状態確認、ピアリングの更新、または削除およびプロビジョニング解除を行うこともできます。 別の方法を使用して回線を操作する場合は、次の一覧から記事を選択してください。

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [ビデオ - プライベート ピアリング](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [ビデオ - パブリック ピアリング](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [ビデオ - Microsoft ピアリング](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (クラシック)](expressroute-howto-routing-classic.md)
> 

ExpressRoute 回線用に 1 つ、2 つ、または 3 つすべてのピアリング (Azure プライベート、Azure パブリックおよび Microsoft) を構成することができます。 ピアリングは任意の順序で構成することができます。 ただし、各ピアリングの構成は必ず一度に 1 つずつ完了するようにしてください。 ルーティング ドメインとピアリングの詳細については、[回線とピアリングについて](expressroute-circuit-peerings.md)の記事を参照してください。

## <a name="configuration-prerequisites"></a>構成の前提条件

* 構成を開始する前に必ず、[前提条件](expressroute-prerequisites.md)ページ、[ルーティングの要件](expressroute-routing.md)ページおよび[ワークフロー](expressroute-workflows.md) ページを確認してください。
* アクティブな ExpressRoute 回線が必要です。 手順に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-portal-resource-manager.md)し、接続プロバイダー経由で回線を有効にしてから続行してください。 以下のセクションのコマンドレットを実行するには、ExpressRoute 回線を、プロビジョニングおよび有効化されている状態にする必要があります。
* 共有キー/MD5 ハッシュを使用する場合は、必ずトンネルの両側でこれを使用し、英数字の文字数を最大 25 文字に制限してください。 特殊文字はサポートされていません。 

次の手順は、サービス プロバイダーが提供するレイヤー 2 接続サービスで作成された回線にのみ適用されます。 サービス プロバイダーが提供する管理対象レイヤー 3 サービス (MPLS など、通常は IP VPN) を使う場合、接続プロバイダーがユーザーに代わってルーティングを構成して管理します。 

> [!IMPORTANT]
> 現在のところ、サービス管理ポータルでは、サービス プロバイダーが構成したピアリングをアドバタイズしていません。 できるだけ早くこの機能を提供できるように取り組んでいます。 BGP ピアリングを構成する前に、ご利用のサービス プロバイダーにお問い合わせください。
> 
> 

## <a name="msft"></a>Microsoft ピアリング

このセクションでは、ExpressRoute 回線用の Microsoft ピアリング構成を作成、取得、更新、および削除します。

> [!IMPORTANT]
> 2017 年 8 月 1 日より前に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが定義されていない場合でも、すべてのサービス プレフィックスが Microsoft ピアリングでアドバタイズされます。 2017 年 8 月 1 日以降に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが回線に接続されるまで、プレフィックスはアドバタイズされません。 詳しくは、「[Configure a route filter for Microsoft peering](how-to-routefilter-powershell.md)」(Microsoft ピアリング用にルート フィルターを構成する) をご覧ください。
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft ピアリングを作成するには

1. ExpressRoute 回線を構成します。 続行する前に、接続プロバイダーが回線を完全にプロビジョニングしていることを確認します。 接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Microsoft ピアリングを有効にするように接続プロバイダーに依頼できます。 その場合は、次のセクションにリストされている手順に従う必要はありません。 ただし、接続プロバイダーがルーティングを管理しない場合は、回線を作成した後、次の手順を使用して、構成を続行します。

   ![Microsoft ピアリングを一覧表示する](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. 回路の Microsoft ピアリングを構成する 続行する前に、次の情報を確認してください。

   * プライマリ リンク用の /30 サブネット。 これは、自分が所有しており、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。 このサブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。
   * セカンダリ リンク用の /30 サブネット。 これは、自分が所有しており、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。 このサブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。 プライマリとセカンダリの両方のリンクに対し、同じ VLAN ID を使用する必要があります。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。
   * アドバタイズするプレフィックス:BGP セッションを介してアドバタイズする予定のすべてのプレフィックスのリストを指定する必要があります。 パブリック IP アドレス プレフィックスのみが受け入れられます。 一連のプレフィックスを送信する場合は、コンマ区切りのリストを送信できます。 これらのプレフィックスは、RIR/IRR に登録する必要があります。
   * **省略可能 -** 顧客 ASN:ピアリング AS 番号に登録されていないプレフィックスをアドバタイズする場合は、そのプレフィックスが登録されている AS 数を指定できます。
   * ルーティング レジストリ名:AS 番号とプレフィックスを登録する RIR/IRR を指定することができます。
   * **省略可能 -** MD5 を使用する場合には、パスワードを準備します。
3. 次の例のように、構成するピアリングを選ぶことができます。 Microsoft ピアリング行を選択します。

   ![Microsoft ピアリング行を選択する](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Microsoft ピアリングを構成します。 次の図は構成例です。

   ![Microsoft ピアリングを構成する](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. すべてのパラメーターを指定したら、構成を保存します。

   回線が検証が必要な状態になった場合 (次の図を参照)、サポート チケットを開き、プレフィックスの所有権を示す証拠をサポート チームに示してください。

   ![Microsoft ピアリング構成を保存する](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

   次の例のように、ポータルから直接サポート チケットを開くことができます。

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. 構成が正常に受け付けられると、次のような画面が表示されます。

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="getmsft"></a>Microsoft ピアリングの詳細を表示するには

ピアリングを選択して、Azure パブリック ピアリングのプロパティを表示することができます。

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="updatemsft"></a>Microsoft ピアリング構成を更新するには

ピアリングの行を選択し、ピアリングのプロパティを変更できます。

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="deletemsft"></a>Microsoft ピアリングを削除するには

ピアリングの構成を削除するには、次の図ように削除アイコンを選びます。

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="private"></a>Azure プライベート ピアリング

このセクションでは、ExpressRoute 回線用の Azure プライベート ピアリング構成を作成、取得、更新、および削除します。

### <a name="to-create-azure-private-peering"></a>Azure プライベート ピアリングを作成するには

1. ExpressRoute 回線を構成します。 続行する前に、接続プロバイダーが回線を完全にプロビジョニングしていることを確認します。 接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Azure プライベート ピアリングを有効にするように接続プロバイダーに依頼できます。 その場合は、次のセクションにリストされている手順に従う必要はありません。 ただし、接続プロバイダーがルーティングを管理しない場合は、回線を作成した後、次の手順を使用して、構成を続行します。

   ![list](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. 回線用に Azure プライベート ピアリングを構成します。 次の手順に進む前に、以下のものがそろっていることを確認します。

   * プライマリ リンク用の /30 サブネット。 サブネットを、仮想ネットワーク用に予約されたアドレス空間の一部にすることはできません。 このサブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。
   * セカンダリ リンク用の /30 サブネット。 サブネットを、仮想ネットワーク用に予約されたアドレス空間の一部にすることはできません。 このサブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。 プライマリとセカンダリの両方のリンクに対し、同じ VLAN ID を使用する必要があります。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。 このピアリングではプライベート AS 番号を使用できます (ただし、65515 ～ 65520 までの番号を除きます)。
   * **省略可能 -** MD5 を使用する場合には、パスワードを準備します。
3. 次の例で示すように、Azure プライベート ピアリング行を選択します。

   ![プライベート](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. プライベート ピアリングを構成します。 次の図は構成例です。

   ![プライベート ピアリングを構成する](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. すべてのパラメーターを指定したら、構成を保存します。 構成が正常に受け付けられると、次のような画面が表示されます。

   ![プライベート ピアリングを保存する](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="getprivate"></a>Azure プライベート ピアリングの詳細を表示するには

ピアリングを選択して、Azure プライベート ピアリングのプロパティを表示することができます。

![プライベート ピアリングを表示する](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="updateprivate"></a>Azure プライベート ピアリングの構成を更新するには

ピアリングの行を選択し、ピアリングのプロパティを変更できます。

![プライベート ピアリングを更新する](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="deleteprivate"></a>Azure プライベート ピアリングを削除するには

ピアリングの構成を削除するには、次の図ように削除アイコンを選びます。

> [!WARNING]
> この例を実行する前に、すべての仮想ネットワークと ExpressRoute Global Reach 接続が削除されていることを確認する必要があります。 
> 
> 

![プライベート ピアリングを削除する](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="public"></a>Azure パブリック ピアリング

このセクションでは、ExpressRoute 回線用の Azure パブリック ピアリング構成を作成、取得、更新、および削除します。

### <a name="to-create-azure-public-peering"></a>Azure パブリック ピアリングを作成するには

1. ExpressRoute 回線を構成します。 続行する前に、接続プロバイダーが回線を完全にプロビジョニングしていることを確認します。 接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Azure パブリック ピアリングを有効にするように接続プロバイダーに依頼できます。 その場合は、次のセクションにリストされている手順に従う必要はありません。 ただし、接続プロバイダーがルーティングを管理しない場合は、回線を作成した後、次の手順を使用して、構成を続行します。

   ![パブリック ピアリングを一覧表示する](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. 回線用に Azure パブリック ピアリングを構成します。 次の手順に進む前に、以下のものがそろっていることを確認します。

   * プライマリ リンク用の /30 サブネット。 これは有効なパブリック IPv4 プレフィックスである必要があります。 このサブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。 
   * セカンダリ リンク用の /30 サブネット。 これは有効なパブリック IPv4 プレフィックスである必要があります。 このサブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。 プライマリとセカンダリの両方のリンクに対し、同じ VLAN ID を使用する必要があります。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。
   * **省略可能 -** MD5 を使用する場合には、パスワードを準備します。
3. 次の図ように、Azure パブリック ピアリング行を選びます。

   ![パブリック ピアリング行を選ぶ](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. パブリック ピアリングを構成します。 次の図は構成例です。

   ![パブリック ピアリングを構成する](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. すべてのパラメーターを指定したら、構成を保存します。 構成が正常に受け付けられると、次のような画面が表示されます。

   ![パブリック ピアリングの構成を保存する](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="getpublic"></a>Azure パブリック ピアリングの詳細を表示するには

ピアリングを選択して、Azure パブリック ピアリングのプロパティを表示することができます。

![パブリック ピアリングのプロパティを表示する](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="updatepublic"></a>Azure パブリック ピアリング構成を更新するには

ピアリングの行を選択し、ピアリングのプロパティを変更できます。

![パブリック ピアリング行を選ぶ](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="deletepublic"></a>Azure パブリック ピアリングを削除するには

ピアリングの構成を削除するには、次の例ように削除アイコンを選びます。

![パブリック ピアリングを削除する](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="next-steps"></a>次の手順

次の手順では、 [ExpressRoute 回線に VNet をリンク](expressroute-howto-linkvnet-portal-resource-manager.md)します。
* ExpressRoute ワークフローの詳細については、「 [ExpressRoute ワークフロー](expressroute-workflows.md)」を参照してください。
* 回路ピアリングの詳細については、「 [ExpressRoute 回線とルーティング ドメイン](expressroute-circuit-peerings.md)」を参照してください。
* 仮想ネットワークの詳細については、「 [仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)」を参照してください。
