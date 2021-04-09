---
title: Azure Virtual Network とのアプリの統合
description: Azure App Service のアプリを Azure 仮想ネットワークと統合します。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4666f03e529c568177ff56a2db84e226bc906132
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182912"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>アプリを Azure 仮想ネットワークと統合する

この記事では、Azure App Service の VNet 統合機能と、それを [Azure App Service](./overview.md) のアプリで設定する方法について説明します。 [Azure Virtual Network][VNETOverview] (VNet) を使用すると、多くの Azure リソースをインターネットにルーティングできないネットワークに配置できます。 VNet 統合機能を使用すると、アプリは VNet 内のリソースにアクセスするか、VNet を通じてリソースにアクセスできます。 VNet 統合では、アプリにプライベートでアクセスすることはできません。

Azure App Service では、VNet 統合機能に次の 2 つのバリエーションがあります。

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNET 統合を有効にする

1. App Service ポータルで **[ネットワーク]** の UI に移動します。 **[VNet 統合]** の下の **[ここをクリックして構成]** を選択します。

1. **[Add VNet]\(VNet の追加)** を選択します。

   ![Vnet 統合を選択する][1]

1. ドロップダウン リストには、お使いのサブスクリプションで同じリージョンに存在するすべての Azure Resource Manager 仮想ネットワークが含まれます。 その下に、他のすべてのリージョンの Resource Manager 仮想ネットワークの一覧が表示されます。 統合する VNet を選択します。

   ![VNet の選択][2]

   * 同じリージョン内の VNet の場合は、新しいサブネットを作成するか、空の既存のサブネットを選択します。
   * 別のリージョンの VNet を選択するには、ポイント対サイトが有効になっている VNet ゲートウェイがプロビジョニングされている必要があります。
   * クラシック VNet と統合するには、 **[仮想ネットワーク]** ドロップダウン リストを選択するのではなく、 **[クラシック VNet に接続するには、ここをクリックします]** を選択します。 使用するクラシック仮想ネットワークを選択します。 ターゲットの VNet には、ポイント対サイトが有効になっている Virtual Network ゲートウェイがプロビジョニングされている必要があります。

    ![クラシック VNet の選択][3]

統合中にアプリは再起動されます。 統合が完了すると、統合されている VNet の詳細が表示されます。

## <a name="regional-vnet-integration"></a>リージョン VNet 統合

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>リージョン VNET 統合のしくみ

App Service 内のアプリは、worker ロールでホストされます。 Basic 以上の価格プランは、同じ worker 上で他の顧客のワークロードが実行されない専用のホスティング プランです。 リージョン VNet 統合は、委任されたサブネット内のアドレスで仮想インターフェイスをマウントすることによって機能します。 送信元アドレスは VNet 内に存在するため、VNet 内の VM と同様に、VNet 内または VNet 経由でほとんどの場所にアクセスできます。 ネットワークの実装は、VNet で VM を実行する場合とは異なります。 そのため、一部のネットワーク機能はこの機能ではまだ使用できません。

![リージョン VNET 統合のしくみ][5]

リージョン VNet 統合が有効になっているとき、アプリによるインターネットへの送信呼び出しは、通常と同じチャネル経由で行われます。 アプリのプロパティ ポータルに一覧表示される送信アドレスは、引き続きそのアプリによって使用されるアドレスです。 アプリに関する変更は、サービス エンドポイントのセキュリティ保護されたサービスつまり RFC 1918 アドレスへの呼び出しが、VNet に転送されることです。 WEBSITE_VNET_ROUTE_ALL が 1 に設定されている場合、すべての送信トラフィックを VNet 内に送信できます。

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` は、現在 Windows コンテナーではサポートされていません。
> 

この機能では、worker ごとに 1 つの仮想インターフェイスのみがサポートされます。 worker あたり 1 つの仮想インターフェイスは、App Service プランごとに 1 リージョンの VNet 統合があることを意味します。 同じ App Service プラン内のすべてのアプリで、同じ VNet 統合を使用できます。 アプリで追加の VNet に接続する必要がある場合は、別の App Service プランを作成する必要があります。 使用される仮想インターフェイスは、顧客が直接アクセスできるリソースではありません。

このテクノロジが動作する方法の性質のために、VNet 統合で使用されるトラフィックは Azure Network Watcher や NSG フローのログには表示されません。

## <a name="gateway-required-vnet-integration"></a>ゲートウェイが必要な VNet 統合

ゲートウェイが必要な VNet 統合では、別のリージョンの VNet またはクラシック仮想ネットワークへの接続がサポートされています。 ゲートウェイが必要な VNet 統合の特徴は次のとおりです。

* アプリでは一度に 1 つの VNet にのみ接続できます。
* App Service プランで、最大 5 つの VNet と統合できます。
* App Service プランで使用できる総数に影響を与えることなく、App Service プラン内の複数のアプリで同じ VNet を使用できます。 同じ App Service プラン内の同じ VNet を使用しているアプリが 6 つある場合、使用されている VNet は 1 つとしてカウントされます。
* ゲートウェイでの SLA により、99.9% の SLA がサポートされます。
* アプリでは VNet が構成されている DNS を使用できます。
* アプリに接続できるためには、Virtual Network のルート ベースのゲートウェイに、SSTP のポイント対サイト VPN が構成されている必要があります。

ゲートウェイが必要な VNet 統合は次の場合は使用できません。

* Azure ExpressRoute で接続された VNet に対して。
* Linux アプリから。
* [Windows コンテナー](quickstart-custom-container.md)から。
* サービス エンドポイントによって保護されているリソースにアクセスする場合。
* ExpressRoute とポイント対サイトまたはサイト間 VPN の両方をサポートする共存ゲートウェイに対して。

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Azure 仮想ネットワークでゲートウェイを設定する ###

ゲートウェイを作成するには:

1. VNet 内に[ゲートウェイ サブネットを作成][creategatewaysubnet]します。

1. [VPN ゲートウェイを作成][creategateway]します。 VPN の種類はルート ベースを選択します。

1. [ポイント対サイト アドレスを設定][setp2saddresses]します。 ゲートウェイが Basic SKU 内にない場合は、ポイント対サイトの構成で IKEV2 を無効にする必要があり、SSTP を選択する必要があります。 ポイント対サイトのアドレス空間は、RFC 1918 のアドレス ブロック 10.0.0.0/8、172.16.0.0/12、192.168.0.0/16 内に存在する必要があります。

App Service の VNet 統合で使用するゲートウェイを作成する場合は、証明書をアップロードする必要はありません。 ゲートウェイの作成には 30 分かかることがあります。 ゲートウェイがプロビジョニングされるまで、アプリを VNet に統合することはできません。

### <a name="how-gateway-required-vnet-integration-works"></a>ゲートウェイが必要な VNet 統合のしくみ

ゲートウェイが必要な VNet 統合は、ポイント対サイト VPN テクノロジに基づいて構築されています。 ポイント対サイト VPN により、ネットワーク アクセスはアプリがホストされている仮想マシンに制限されます。 アプリは、ハイブリッド接続または VNet 統合を通してのみ、インターネットにトラフィックを送信するように制限されます。 ゲートウェイが必要な VNet 統合を使用するようにアプリをポータルで構成すると、ゲートウェイとアプリケーション側で証明書を作成して割り当てるための複雑なネゴシエーションが、ユーザーの代わりに管理されます。 結果として、アプリをホストするために使用される worker は、選択した VNet 内の仮想ネットワーク ゲートウェイに直接接続できるようになります。

![ゲートウェイが必要な VNet 統合のしくみ][6]

### <a name="access-on-premises-resources"></a>オンプレミスのリソースにアクセスする

アプリは、サイト間接続がある Vnet と統合することで、オンプレミスのリソースにアクセスできます。 ゲートウェイが必要な VNet 統合を使用する場合は、ポイント対サイト アドレス ブロックでオンプレミスの VPN ゲートウェイのルートを更新します。 サイト間 VPN が初めてセットアップされるときには、その構成に使用するスクリプトによって、ルートが正しくセットアップされるはずです。 サイト間 VPN を作成した後にポイント対サイト アドレスを追加する場合は、ルートを手動で更新する必要があります。 その詳しい方法はゲートウェイごとに異なるため、ここでは説明しません。 BGP をサイト間 VPN 接続で構成することはできません。

VNet 経由でオンプレミスのリソースにアクセスするために、リージョン VNet 統合機能に対する追加の構成は必要ありません。 必要なのは、単に ExpressRoute またはサイト間 VPN を使用して VNet をオンプレミスのリソースに接続することだけです。

> [!NOTE]
> ゲートウェイが必要な VNet 統合機能では、アプリは ExpressRoute ゲートウェイを含む VNet とは統合されません。 ExpressRoute ゲートウェイが[共存モード][VPNERCoex]で構成されている場合であっても、VNet 統合は機能しません。 ExpressRoute 接続経由でリソースにアクセスする必要がある場合は、VNet で実行されるリージョン VNet 統合機能または [App Service Environment][ASE] を使用します。
>
>

### <a name="peering"></a>ピアリング

リージョン VNet 統合でピアリングを使用する場合、追加の構成を行う必要はありません。

ゲートウェイが必要な VNet 統合でピアリングを使用する場合は、いくつかの追加の項目を構成する必要があります。 アプリで動作するようにピアリングを構成するには:

1. アプリの接続先の VNet でピアリング接続を追加します。 ピアリング接続を追加するときに、 **[仮想ネットワークアクセスを許可する]** を有効にして、 **[転送されたトラフィックを許可する]** チェック ボックスと **[ゲートウェイ転送を許可する]** チェック ボックスをオンにします。
1. 接続先の VNet とピアリングする VNet にピアリング接続を追加します。 対象の VNet でピアリング接続を追加するときは、 **[仮想ネットワークアクセスを許可する]** を有効にして、 **[転送されたトラフィックを許可する]** と **[Allow remote gateways]\(リモート ゲートウェイを許可する)** を選択します。
1. ポータルで **[App Service プラン]**  >  **[ネットワーク]**  >  **[VNet 統合]** に移動します。 アプリの接続先 VNet を選択します。 ルーティングのセクションで、アプリの接続先の VNet とピアリングされている VNet のアドレス範囲を追加します。

## <a name="manage-vnet-integration"></a>VNet 統合を管理する

VNet との接続および切断は、アプリ レベルで行われます。 複数のアプリで VNet 統合に影響する可能性がある操作は、App Service プラン レベルで行われます。 アプリの **[ネットワーク]**  >  **[VNet 統合]** ポータルから、VNet に関する詳細を取得できます。 **[App Service プラン]**  >  **[ネットワーク]**  >  **[VNet 統合]** ポータルの App Service プラン レベルでも、同様の情報が表示されます。

VNet 統合インスタンスのアプリ ビューで実行できる操作は、現在接続されている VNet からアプリを切断する操作のみです。 VNet からアプリを切断するには、 **[切断]** を選択します。 VNet から切断すると、アプリが再起動されます。 切断によって VNet に変更は生じません。 サブネットやゲートウェイは削除されません。 その後で VNet を削除する必要がある場合は、まず VNet からアプリを切断し、ゲートウェイなどのそこに含まれているリソースを削除します。

App Service プランの VNet 統合の UI には、App Service プラン内のアプリによって使用されているすべての VNet 統合が表示されます。 各 VNet の詳細を表示するには、目的の VNet を選択します。 ここでは、ゲートウェイが必要な VNet 統合に対して実行できる操作が 2 つあります。

* **ネットワークを同期する**: ネットワーク同期操作は、ゲートウェイに依存する VNet 統合機能に対してのみ使用されます。 ネットワーク同期操作を実行すると、証明書とネットワークの情報が確実に同期されるようになります。VNet の DNS を追加または変更する場合は、ネットワーク同期操作を実行する必要があります。 この操作では、この VNet を使用するすべてのアプリが再起動されます。 この操作は、使用しているアプリと VNet が異なるサブスクリプションに属している場合は機能しません。
* **ルートを追加する**: ルートを追加すると、送信トラフィックが VNet に転送されます。

インスタンスに割り当てられたプライベート IP は、環境変数 **WEBSITE_PRIVATE_IP** によって公開されます。 Kudu コンソールの UI には、Web アプリで使用できる環境変数の一覧も表示されます。 この IP は、統合されたサブネットのアドレス範囲から割り当てられます。 リージョン VNet 統合の場合、WEBSITE_PRIVATE_IP の値は委任されたサブネットのアドレス範囲の IP になります。また、ゲートウェイが必要な VNet 統合では、この値は Virtual Network ゲートウェイに構成されているポイント対サイトのアドレス プールのアドレス範囲の IP になります。 これは、Web アプリが Virtual Network を通じてリソースに接続するために使用する IP です。 

> [!NOTE]
> WEBSITE_PRIVATE_IP の値は、必ず変更されます。 ただし、これは統合サブネットまたはポイント対サイトのアドレス範囲内の IP であるため、アドレス範囲全体からのアクセスを許可する必要があります。
>

### <a name="gateway-required-vnet-integration-routing"></a>ゲートウェイが必要な VNET 統合のルーティング
VNet で定義されているルートが、アプリから VNet にトラフィックを転送するために使用されます。 VNet に追加の送信トラフィックを送信するには、ここでそれらのアドレス ブロックを追加します。 この機能は、ゲートウェイが必要な VNet 統合でのみ動作します。 ゲートウェイが必要な VNet 統合を使用すると、リージョン VNet 統合の場合とは異なり、ルート テーブルによるアプリのトラフィックへの影響はありません。

### <a name="gateway-required-vnet-integration-certificates"></a>ゲートウェイが必要な VNet 統合の証明書
ゲートウェイが必要な VNet 統合が有効になっている場合は、接続のセキュリティを確保するために証明書の交換が必要です。 証明書と共に、DNS の構成、ルート、ネットワークについて記述するその他の同様の情報があります。

証明書またはネットワーク情報が変更された場合は、 **[ネットワークの同期]** を選択します。 **[ネットワークの同期]** を選択すると、アプリと VNet の間の接続が短時間途切れます。 アプリが再起動されていない間は、接続の途切れによって、サイトが正常に機能しなくなる可能性があります。

## <a name="pricing-details"></a>価格の詳細
リージョン VNet 統合機能には、App Service プランの価格レベルの料金を超える追加の使用料金はありません。

ゲートウェイが必要な VNet 統合機能の使用には、3 つの料金が関連します。

* **App Service プランの価格レベルの料金**: アプリは、Standard、Premium、PremiumV2、または PremiumV3 の App Service プランに属している必要があります。 コストについて詳しくは、「[App Service の価格][ASPricing]」をご覧ください。
* **データ転送コスト**: VNet が同じデータ センター内にある場合でも、データのエグレスには料金がかかります。 これらの料金については、[データ転送価格の詳細][DataPricing]に関するページをご覧ください。
* **VPN Gateway のコスト**: ポイント対サイト VPN に必要な仮想ネットワーク ゲートウェイに対して費用がかかります。 詳細については、「[VPN Gateway の価格][VNETPricing]」を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

> [!NOTE]
> VNET 統合は、App Service の Docker Compose シナリオではサポートされていません。
> プライベート エンドポイントが存在する場合、Azure Functions のアクセス制限は無視されます。
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>オートメーション

リージョン VNet 統合では CLI がサポートされています。 次のコマンドにアクセスするには、[Azure CLI をインストール][installCLI]します。

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

リージョン VNet 統合では PowerShell のサポートも提供されていますが、サブネットの resourceID のプロパティ配列を使用して汎用のリソースを作成する必要があります

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

#Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

#Creation of the VNet integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```


ゲートウェイが必要な VNet 統合では、PowerShell を使用して App Service を Azure 仮想ネットワークと統合できます。 すぐに使用できるスクリプトについては、「[Azure App Service のアプリを Azure 仮想ネットワークに接続する](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)」をご覧ください。


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli
[privateendpoints]: networking/private-endpoint.md
