---
title: Azure ExpressRoute v1 を構成する
description: Azure ExpressRoute を使用した PowerApps 用の App Service Environment のネットワーク構成。 このドキュメントは、レガシ v1 ASE を使用するお客様にのみ提供されます。
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 3fa2677f7c71b118ec3f7da717e29d5879ee982b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961824"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Azure ExpressRoute を使用した PowerApps 用の App Service Environment のネットワーク構成の詳細

顧客は、[Azure ExpressRoute][ExpressRoute] 回線を自分の仮想ネットワーク インフラストラクチャに接続することで、オンプレミスのネットワークを Azure に拡張できます。 App Service Environment は、[仮想ネットワーク][virtualnetwork] インフラストラクチャのサブネットの中に作成されます。 App Service Environment で実行されるアプリは、ExpressRoute 接続でのみアクセスできる、バックエンド リソースに対する安全な接続を確立します。  

App Service Environment は以下のシナリオで作成できます。
- Azure Resource Manager の仮想ネットワーク。
- クラシック デプロイ モデルの仮想ネットワーク。
- パブリック アドレス範囲または RFC1918 アドレス空間 (つまりプライベート アドレス) を使用する仮想ネットワーク。 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>必要なネットワーク接続

App Service Environment には、ExpressRoute に接続された仮想ネットワークでは最初は満たされていない場合があるネットワーク接続要件があります。

App Service Environment が正常に機能するには、次のネットワーク接続設定が必要です。

* 世界各国の Azure Storage エンドポイントに対するポート 80 とポート 443 での発信ネットワーク接続。 これらのエンドポイントは、App Service Environment と同じリージョンにあり、その他の Azure リージョンにもあります。 Azure Storage エンドポイントは、次の DNS ドメインで解決されます: table.core.windows.net、blob.core.windows.net、queue.core.windows.net、file.core.windows.net。  

* Azure Files サービスに対するポート 445 での発信ネットワーク接続。

* App Service Environment と同じリージョンにある Azure SQL Database エンドポイントに対する発信ネットワーク接続。 SQL Database エンドポイントは、database.windows.net ドメインで解決されます。これには、ポート 1433、11000 ～ 11999、および 14000 ～ 14999 へのオープン アクセスが必要です。 SQL Database V12 ポートの使用について詳しくは、「[ADO.NET 4.5 用の 1433 以外のポート](../../azure-sql/database/adonet-v12-develop-direct-route-ports.md)」を参照してください。

* Azure 管理プレーン エンドポイント (Azure クラシック デプロイ モデルと Azure Resource Manager の両方のエンドポイント) に対する発信ネットワーク接続。 これらのエンドポイントへの接続には、management.core.windows.net ドメインと management.azure.com ドメインが含まれます。 

* ocsp.msocsp.com、mscrl.microsoft.com、crl.microsoft.com の各ドメインメッセージに対する発信ネットワーク接続。 これらのドメインへの接続は、TLS 機能をサポートするために必要です。

* 仮想ネットワークの DNS 構成は、この記事の前述のすべてのエンドポイントとドメインを解決できるようにする必要があります。 エンドポイントを解決できない場合は、App Service Environment の作成が失敗します。 既存の App Service Environment は異常とマークされています。

* DNS サーバーとの通信には、ポート 53 での発信アクセスが必要です。

* カスタム DNS サーバーが VPN ゲートウェイの相手側にある場合、DNS サーバーは App Service Environment を含むサブネットから到達できる必要があります。 

* 発信ネットワーク パスは、社内プロキシを経由したり、オンプレミスに強制的にトンネリングしたりすることができません。 これらのアクションにより、App Service Environment からの発信ネットワーク トラフィックの実質的な NAT アドレスが変わります。 App Service Environment の発信ネットワーク トラフィックの NAT アドレスを変更すると、多数のエンドポイントに対して接続エラーが発生します。 App Service Environment の作成は失敗します。 既存の App Service Environment は異常とマークされています。

* App Service Environment に必要なポートへの受信ネットワーク アクセスを許可する必要があります。 詳細については、「[App Service Environment への受信トラフィックを制御する方法][requiredports]」を参照してください。

DNS 要件を満たすには、仮想ネットワークに対して有効な DNS インフラストラクチャが構成され保守されていることを確認します。 App Service Environment の作成後に DNS 構成が変わった場合、開発者は強制的に App Service Environment から新しい DNS 構成を選択することができます。 [Azure portal][NewPortal] の App Service Environment の管理にある **[再起動]** アイコンを使用することで、ローリングする環境の再起動をトリガーできます。 再起動により、環境から新しい DNS 構成が選択されます。

受信ネットワーク アクセスの要件を満たすには、App Service Environment のサブネットに対して[ネットワーク セキュリティ グループ (NSG)][NetworkSecurityGroups] を構成します。 NSG は、[App Service Environment への受信トラフィックを制御する][requiredports]ために必要なアクセスを許可します。

## <a name="outbound-network-connectivity"></a>発信ネットワーク接続

既定では、新しく作成された ExpressRoute 回線は、発信インターネット接続を許可する既定のルートをアドバタイズします。 App Service Environment は、この構成を使用して他の Azure エンドポイントに接続できます。

顧客の一般的な構成では、発信インターネット トラフィックを強制的にオンプレミスにフローさせる独自の既定のルート (0.0.0.0/0) を定義しています。 このトラフィック フローでは、App Service Environment の接続が必ず切断されます。 発信トラフィックはオンプレミスでブロックされるか、Azure エンドポイントではもはや有効ではない、認識できないアドレス セットに NAT 処理されます。

解決策は、App Service Environment を含むサブネットに、1 つ (または複数) のユーザー定義ルート (UDR) を定義することです。 UDR は、既定のルートに優先するサブネット固有のルートを定義します。

可能な場合、次の構成を使用します。

* ExpressRoute 構成は 0.0.0.0/0 をアドバタイズします。 既定では、この構成はオンプレミスのすべての送信トラフィックを強制的にトンネリングします。
* App Service Environment を含むサブネットに適用される UDR は、次ホップの種類がインターネットである 0.0.0.0/0 を定義します。 この構成の例については、この記事の後の方で説明します。

この構成を組み合わせた結果として、サブネット レベル UDR は ExpressRoute 強制トンネリングよりも優先されます。 App Service Environment からの発信インターネット アクセスは保証されます。

> [!IMPORTANT]
> ExpressRoute 構成でアドバタイズされたルートよりも優先するには、UDR に定義されているルートを詳細にする必要があります。 次のセクションで説明されている例では、0.0.0.0/0 という広いアドレス範囲を使用しています。 この範囲は、より具体的なアドレス範囲を使用するルート アドバタイズによって誤ってオーバーライドされる可能性があります。
> 
> App Service Environment は、パブリック ピアリング パスからプライベート ピアリング パスにルートをクロスアドバタイズした ExpressRoute 構成ではサポートされません。 パブリック ピアリングが構成された ExpressRoute 構成は、大規模な Microsoft Azure の IP アドレス範囲について Microsoft からルート アドバタイズを受信します。 これらのアドレス範囲がプライベートピアリング パスでクロスアドバタイズされた場合、App Service Environment のサブネットからの送信ネットワーク パケットはすべて、顧客のオンプレミスのネットワーク インフラストラクチャに強制的にトンネリングされます。 このネットワーク フローは App Service Environment では現在サポートされていません。 1 つの解決策として、パブリックピアリング パスからプライベートピアリング パスへのクロスアドバタイズ ルートの停止があります。
> 
> 

ユーザー定義のルートの背景情報については、「[仮想ネットワーク トラフィック ルーティング][UDROverview]」を参照してください。  

ユーザー定義のルートの作成および構成方法については、「[PowerShell を使用してルート テーブルでネットワーク トラフィックをルーティングする][UDRHowTo]」を参照してください。

## <a name="udr-configuration"></a>UDR の構成

このセクションでは、App Service Environment の UDR 構成例を示します。

### <a name="prerequisites"></a>前提条件

* [Azure ダウンロード ページ][AzureDownloads]から Azure PowerShell をインストールします。 2015 年 6 月以降の日付のダウンロードを選択してください。 **[コマンド ライン ツール]**  >  **[Windows Powershell]** で、 **[インストール]** を選択して最新の PowerShell コマンドレットをインストールします。

* App Service Environment が独占的に使用する一意のサブネットを作成します。 一意のサブネットにより、サブネットに適用される UDR で、App Service Environment 用の発信トラフィックのみが開くことが保証されます。

> [!IMPORTANT]
> 構成手順が完了したら、App Service Environment のみをデプロイしてください。 この手順により、App Service Environment のデプロイを試みる前に、発信ネットワーク接続を使用できることを確認できます。

### <a name="step-1-create-a-route-table"></a>手順 1:ルート テーブルの作成

次のスニペットに示されているように、Azure の米国西部リージョンに **DirectInternetRouteTable** という名前のルート テーブルを作成します。

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>手順 2:テーブル内にルートを作成する

発信インターネット アクセスを有効にするために、ルート テーブルにルートを追加します。  

インターネットへの発信アクセスを構成します。 次のスニペットに示されているように、0.0.0.0/0 のルートを定義します。

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 は広いアドレス範囲です。 この範囲は、より具体的な ExpressRoute からアドバタイズされたアドレス範囲によってオーバーライドされます。 0\.0.0.0/0 ルートの UDR は、0.0.0.0/0 のみをアドバタイズする ExressRoute 構成と組み合わせて使用する必要があります。 

または、Azure で使用されている CIDR 範囲の最新の全一覧をダウンロードしてください。 すべての Azure IP アドレス範囲の XML ファイルは、[Microsoft ダウンロード センター][DownloadCenterAddressRanges]で入手できます。  

> [!NOTE]
>
> Azure の IP アドレス範囲は時間が経つと変更されます。 ユーザー定義のルートは、定期的に手動で更新して同期状態を保つ必要があります。
>
> 1 つの UDR の既定の上限は 100 ルートです。 100 ルートの上限に収まるように Azure の IP アドレス範囲を "まとめる" 必要があります。 UDR 定義のルートは、ExpressRoute 接続によってアドバタイズされたルートよりも具体的である必要があります。
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>手順 3:テーブルをサブネットに関連付ける

ルート テーブルを、App Service Environment をデプロイする予定のサブネットに関連付けます。 次のコマンドは、**DirectInternetRouteTable** テーブルを、App Service Environment を含む **ASESubnet** に関連付けます。

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>手順 4:ルートをテストして確認する

ルート テーブルをサブネットにバインドした後、ルートをテストして確認します。

仮想マシンをサブネットにデプロイし、以下の条件を確認します。

* この記事に記載されている Azure エンドポイントと Azure 以外のエンドポイントに対する発信トラフィックは、ExpressRoute 回線を **フローしません**。 サブネットからの発信トラフィックがオンプレミスで強制トンネリングされると、App Service Environment の作成は常に失敗します。
* この記事に記載されているエンドポイントの DNS 参照は、すべて正しく解決されます。 

構成手順を完了してルートを確認したら、仮想マシンを削除します。 サブネットは、App Service Environment の作成時には "空" である必要があります。

これで、App Service Environment をデプロイする準備が整いました。

## <a name="next-steps"></a>次のステップ

PowerApps 用の App Service Environment の使用を開始するには、[App Service Environment の概要][IntroToAppServiceEnvironment]に関する記事を参照してください。

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[UDROverview]: ../../virtual-network/virtual-networks-udr-overview.md
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: ../../virtual-network/tutorial-create-route-table-powershell.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->
