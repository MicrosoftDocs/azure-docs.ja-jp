---
title: バックエンド v1 への接続
description: App Service 環境からバックエンド リソースに安全に接続する方法について説明します。 このドキュメントは、レガシ v1 ASE を使用するお客様にのみ提供されます。
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 9f8e288f771b9d584a0fd3430115f5fa60f68e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961807"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>App Service 環境からバックエンド リソースに安全に接続する
App Service 環境は常に Azure Resource Manager 仮想ネットワーク **または** クラシック デプロイ モデル [仮想ネットワーク][virtualnetwork]の **いずれか** に作成されるため、App Service 環境から他のバックエンド リソースへの送信接続は、仮想ネットワーク経由でのみ行うことができます。 2016 年 6 月の時点で、ASE はパブリック アドレス範囲または RFC1918 アドレス空間 (プライベートアドレス) のいずれかを使用する仮想ネットワークにもデプロイすることができます。  

たとえば、ポート 1433 がロックされている仮想マシンのクラスターで実行されている SQL Server がある場合があります。  このエンドポイントは、同じ仮想ネットワークの他のリソースからのアクセスを許可する目的のみで使用されることがあります。  

別の例として、機密性の高いエンドポイントがオンプレミスで実行されていて、[サイト対サイト][SiteToSite]接続または [Azure ExpressRoute][ExpressRoute] 接続を使用して Azure に接続している場合があります。  その結果、サイト対サイトまたは ExpressRoute トンネルに接続されている仮想ネットワーク内のリソースのみがオンプレミスのエンドポイントにアクセスできます。

これらのすべてのシナリオで、App Service Environment で実行中のアプリが、さまざまなサーバーとリソースに安全に接続できます。 送信トラフィックが、App Service Environment で実行されているアプリから、同じ仮想ネットワーク内または同じ仮想ネットワークに接続されているプライベート エンドポイントへの場合、仮想ネットワーク経由でのみ送信されます。  プライベート エンドポイントへの送信トラフィックがパブリック インターネット経由で送信されることはありません。

App Service Environment から仮想ネットワーク内のエンドポイントへの送信トラフィックには、問題が 1 つあります。 App Service Environment から、App Service Environment と **同じ** サブネットにある仮想マシンのエンドポイントに到達することはできません。 App Service Environment が、App Service Environment 専用として予約されているサブネットにデプロイされている場合、通常、これは問題にはなりません。

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>発信接続と DNS の要件
App Service Environment が正常に機能するためには、さまざまなエンドポイントへの発信アクセスが必要となります。 [ExpressRoute を使用した環境のネットワーク構成](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) の記事の「必要なネットワーク接続」というセクションに、App Service Environment で使用されるすべての外部エンドポイントが掲載されています。

App Service Environment では、仮想ネットワーク用に構成された有効な DNS インフラストラクチャが必要です。  App Service Environment の作成後に DNS の構成が変更された場合、開発者は App Service Environment に、新しい DNS 構成の選択を強制できます。 ポータルの App Service Environment の管理ブレードの上部にある **[再起動]** アイコンを使用して、ローリングする環境の再起動をトリガーします。これにより、環境で新しい DNS 構成が選択されます。

また、App Service Environment を作成する前に、VNet 上のカスタム DNS サーバーを設定しておくことをお勧めします。  App Service Environment の作成中に仮想ネットワークの DNS 構成が変更されると、App Service Environment の作成プロセスは失敗します。 VPN ゲートウェイのもう一方の端に、到達不能または使用できないカスタム DNS サーバーがある場合、App Service Environment の作成プロセスも失敗します。

## <a name="connecting-to-a-sql-server"></a>SQL Server への接続
SQL Server の構成には一般的に、ポート 1433 でリッスンしているエンドポイントがあります。

![SQL Server エンドポイント][SqlServerEndpoint]

このエンドポイントへのトラフィックを制限する方法は 2 つあります。

* [ネットワーク アクセス制御リスト][NetworkAccessControlLists] (ネットワーク ACL)
* [ネットワーク セキュリティ グループ][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>ネットワーク ACL でのアクセスの制限
ポート 1433 は、ネットワーク アクセス制御リストを使用して保護できます。  次の例では、仮想ネットワーク内から送信されたクライアント アドレスを割り当てアクセス許可に追加し、他のすべてのクライアントへのアクセスをブロックします。

![ネットワーク アクセス制御リストの例][NetworkAccessControlListExample]

SQL Server と同じ仮想ネットワーク内の App Service Environment で実行されるアプリケーションは、SQL Server インスタンスに接続できます。 SQL Server 仮想マシンに **VNet 内部** IP アドレスを使用します。  

次の例の接続文字列は、プライベート IP アドレスを使用して SQL Server を参照します。

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

仮想マシンにはパブリック エンドポイントもありますが、ネットワーク ACL によって、パブリック IP アドレスを使用する接続の試行は拒否されます。 

## <a name="restricting-access-with-a-network-security-group"></a>ネットワーク セキュリティ グループを使用したアクセスの制限
アクセスを保護する方法にはほかに、ネットワーク セキュリティ グループを使用する方法があります。  ネットワーク セキュリティ グループは、個々の仮想マシン、または仮想マシンを含むサブネットに適用できます。

まず、ネットワーク セキュリティ グループを作成する必要があります。

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

ネットワーク セキュリティ グループを使用すると、アクセスを VNet の内部トラフィックのみに簡単に制限することができます。  ネットワーク セキュリティ グループの既定の規則では、同じ仮想ネットワーク内の他のネットワーク クライアントからのアクセスのみが許可されます。

その結果、SQL Server へのアクセスを簡単にロックできます。 既定の規則を持つネットワーク セキュリティ グループを、SQL Server を実行している仮想マシン、または仮想マシンを含むサブネットのいずれかに適用するだけです。

次の例では、ネットワーク セキュリティ グループを、このグループを含むサブネットに適用します。

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

最終的な結果は、VNet 内部のアクセスを許可し、外部アクセスをブロックするセキュリティの規則のセットになります。

![既定のネットワーク セキュリティ グループ][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>作業の開始
App Service Environment の使用を開始するには、「[App Service Environment の概要][IntroToAppServiceEnvironment]

App Service Environment への着信トラフィックを制御する方法の詳細については、[App Service Environment への着信トラフィックの制御][ControlInboundASE]に関するページを参照してください。

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png
