---
title: Geo 分散スケール
description: Traffic Manager および App Service Environment による geo 分散を使用してアプリを水平方向にスケールする方法について説明します。
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions
ms.openlocfilehash: 004b32118521f72c5b59ad7bab2d4e41244b85c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85833606"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>App Service Environment を使用した geo 分散スケール
## <a name="overview"></a>概要

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

きわめて大規模なスケーリングを必要とするアプリケーション シナリオでは、単一のアプリ デプロイメントで使用できるコンピューティング リソース容量では足りないことがあります。  投票アプリケーション、スポーツ イベント、テレビ放送される娯楽イベントは、いずれも非常に高いスケールを必要とするシナリオの例です。 高スケールの要件には、アプリを水平方向にスケールアウトすることで対応できます。 極端な負荷要件に対応できるように、1 つのリージョン内および複数のリージョン内で、多数のアプリのデプロイを行うことができます。

App Service Environment は、水平方向のスケールアウトに最適なプラットフォームとなっています。既知の要求率をサポートできる App Service Environment 構成を選択していれば、開発者は追加の Azure App Service Environment を "ひな型" 方式でデプロイして、必要なピーク時負荷容量を確保できます。

たとえば、App Service Environment の構成で実行されるアプリが、20K RPS (1 秒あたりの要求数) を処理することがテストでわかっているとします。  必要なピーク時負荷容量が 100K RPS である場合、5 つの App Service Environment を作成して、予想される最大負荷をアプリケーションで処理できるように構成できます。

顧客は通常、カスタム ドメイン (またはバニティ ドメイン) を使用してアクセスするため、開発者は App Service Environment のすべてのインスタンスにアプリの要求を分散する方法が必要になります。  この目的を実現する優れた方法が、[Azure Traffic Manager プロファイル][AzureTrafficManagerProfile]を使用したカスタム ドメインの解決です。  Traffic Manager プロファイルは、個々の App Service Environment をすべてポイントするように構成できます。  Traffic Manager は、Traffic Manager プロファイルの負荷分散設定に基づいて、すべての Azure App Service Environment 間での顧客の分散を自動的に処理します。  このアプローチは、すべての App Service Environment が単一の Azure リージョンにデプロイされている場合はもちろん、世界の複数の Azure リージョンに展開されている場合でも、問題なく機能します。

さらに、顧客はバニティ ドメインを使用してアプリにアクセスするため、アプリを実行する App Service Environment の数を意識することがありません。  これにより、開発者は、観測されたトラフィック負荷に基づいて、App Service Environment の追加や削除を迅速かつ簡単に実行できます。

次の概念図は、単一リージョン内の 3 つの App Service Environment で水平方向にスケールアウトされたアプリを示しています。

![Conceptual Architecture][ConceptualArchitecture] 

このトピックの残りの部分では、複数のApp Service Environment を使用してサンプル アプリの分散トポロジを設定するのに必要な手順について説明します。

## <a name="planning-the-topology"></a>トポロジを計画する
分散アプリケーションのフットプリントを構築する前に、いくつかの情報を前もって用意しておくと作業がスムーズになります。

* **アプリのカスタム ドメイン:** 顧客がアプリへのアクセスに使用するカスタム ドメイン名が必要です。  サンプル アプリでは、カスタム ドメイン名は `www.scalableasedemo.com` です。
* **Traffic Manager ドメイン:** ドメイン名は、[Azure Traffic Manager プロファイル][AzureTrafficManagerProfile]の作成時に選択します。  この名前は、Traffic Manager が管理するドメイン エントリを登録する際に、 *trafficmanager.net* サフィックスと組み合わされます。  サンプル アプリでは、選択される名前は *scalable-ase-demo* です。  そのため、Traffic Manager で管理される完全なドメイン名は、 *scalable-ase-demo.trafficmanager.net* になります。
* **アプリ フットプリントのスケーリングに関する戦略:** アプリケーションのフットプリントは単一リージョン内の複数の App Service Environment に分散されるのか、  複数のリージョンなのか、  両方のアプローチの最適な組み合わせなのか。  これは、顧客のトラフィックが発生する場所に加えて、アプリをサポートするバックエンド インフラストラクチャの他の部分がどの程度適切にスケーリングできるかという見込みに基づいて決定する必要があります。  たとえば、完全にステートレスなアプリケーションでは、各 Azure リージョンで多数の App Service Environment を組み合わせ、これに多くの Azure リージョンにデプロイされた App Service Environment を掛け合わせることで、大規模なスケーリングを実現できます。  選択できるグローバルな Azure リージョンは 15 以上あるため、顧客はスケーラビリティのきわめて高いアプリケーション フットプリントを世界規模で構築できます。  この記事に使用されるサンプル アプリでは、単一の Azure リージョン (米国中南部) に 3 つの App Service Environment が作成されています。
* **App Service Environment の名前付け規則:** 各 App Service Environment には一意の名前が必要です。  App Service Environment が 1 つか 2 つ以上になるとき、各 App Service Environment を識別する目的で名前付け規則を用意すると便利です。  サンプル アプリでは、シンプルな名前付け規則が使用されています。  3 つの App Service Environment の名前は *fe1ase*、*fe2ase*、*fe3ase* です。
* **アプリの名前付け規則**:アプリのインスタンスが複数デプロイされるため、デプロイされたアプリの各インスタンスに名前が必要です。  App Service Environment のあまり知られていない便利な機能の 1 つですが、同じアプリ名を複数の App Service Environment で使用できます。 App Service Environment ごとに一意のドメイン サフィックスがあるため、開発者は各環境でまったく同じアプリ名を再利用できます。  たとえば、開発者は、*myapp.foo1.p.azurewebsites.net*、*myapp.foo2.p.azurewebsites.net*、*myapp.foo3.p.azurewebsites.net* のようなアプリ名を設定できます。ただし、サンプル アプリでは、各アプリ インスタンスに一意の名前を設定しています。  使用されているアプリ インスタンス名は *webfrontend1*、*webfrontend2*、*webfrontend3* です。

## <a name="setting-up-the-traffic-manager-profile"></a>Traffic Manager プロファイルを設定する
アプリの複数のインスタンスを複数の App Service Environment にデプロイしたら、個々のアプリ インスタンスを Traffic Manager に登録できます。  サンプル アプリでは、顧客を次のデプロイ済みアプリ インスタンスのいずれかにルーティングするための *scalable-ase-demo.trafficmanager.net* 用 Traffic Manager プロファイルが必要です。

* **webfrontend1.fe1ase.p.azurewebsites.net:** 1 つ目の App Service Environment にデプロイされているサンプル アプリのインスタンス。
* **webfrontend2.fe2ase.p.azurewebsites.net:** 2 つ目の App Service Environment にデプロイされているサンプル アプリのインスタンス。
* **webfrontend3.fe3ase.p.azurewebsites.net:** 3 つ目の App Service Environment にデプロイされているサンプル アプリのインスタンス。

**同じ** Azure リージョンで実行される複数の Azure App Service エンドポイントを登録する最も簡単な方法は、PowerShell で [Azure Resource Manager による Traffic Manager のサポート][ARMTrafficManager]を使用することです。  

最初の手順では、Azure Traffic Manager プロファイルの作成です。  次のコードでは、サンプル アプリ用プロファイルの作成方法を示しています。

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

*RelativeDnsName* パラメーターが *scalable-ase-demo* に設定されていることに注意してください。  このパラメーターによって、ドメイン名 *scalable-ase-demo.trafficmanager.net* が作成され、Traffic Manager プロファイルに関連付けられます。

*TrafficRoutingMethod* パラメーターには負荷分散ポリシーが定義されており、Traffic Manager はこのポリシーを使用して、顧客の負荷を利用可能なすべてのエンドポイントに分散する方法を判断します。  この例では、"*重み付け*" 方式が選択されています。  この選択により、顧客の要求が、各エンドポイントに関連付けられている相対的な重みに基づいて、登録済みのすべてのアプリケーション エンドポイント間で分散されます。 

作成されるプロファイルに対して、各アプリ インスタンスがネイティブ Azure エンドポイントとして追加されます。  次のコードにより、各フロントエンド Web アプリへの参照がフェッチされます。 次に、*TargetResourceId* パラメーターを介して、各アプリが Traffic Manager エンドポイントとして追加されます。

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

アプリ インスタンスごとに *Add-AzureTrafficManagerEndpointConfig* が　1 回ずつ呼び出されている点に注意してください。  各 PowerShell コマンドの *TargetResourceId* パラメーターによって、デプロイされた 3 つのアプリ インスタンスのうちの 1 つが参照されます。  Traffic Manager プロファイルにより、プロファイルに登録されている 3 つのエンドポイントすべての間で負荷が分散されます。

3 つのエンドポイントすべてで *Weight* (重み) パラメーターに同じ値 (10) が使用されています。  この状況では、Traffic Manager によって顧客の要求が 3 つすべてのアプリ インスタンス間で比較的均等に分散されます。 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>アプリのカスタム ドメインが Traffic Manager ドメインをポイントするように設定する
最後の必須手順として、アプリのカスタム ドメインが Traffic Manager ドメインをポイントするように設定します。  サンプル アプリの場合は、`www.scalableasedemo.com` が `scalable-ase-demo.trafficmanager.net` をポイントするようにします。  この手順は、カスタム ドメインを管理するドメイン レジストラーを通じて実行します。  

利用しているレジストラーのドメイン管理ツールを使用して、カスタム ドメインが Traffic Manager ドメインをポイントする CNAME レコードを作成します。  次の図に、この CNAME の構成の例を示します。

![CNAME for Custom Domain][CNAMEforCustomDomain] 

このトピックでは説明しませんが、アプリ インスタンスごとにカスタム ドメインを登録する必要があることに注意してください。  要求がアプリ インスタンスに到達しても、アプリケーションにカスタム ドメインが登録されていないと、要求は失敗します。

この例では、カスタム ドメインは `www.scalableasedemo.com` で、各アプリ インスタンスにカスタム ドメインが関連付けられています。

![カスタム ドメイン][CustomDomain] 

Azure App Service アプリにカスタム ドメインを登録する方法の概要については、[カスタム ドメインの登録][RegisterCustomDomain]に関するページを参照してください。

## <a name="trying-out-the-distributed-topology"></a>分散トポロジを試す
Traffic Manager と DNS を構成すると、最終的に、`www.scalableasedemo.com` への要求は次の順で処理されます。

1. ブラウザーまたはデバイスが `www.scalableasedemo.com` の DNS 参照を行います
2. ドメイン レジストラーの CNAME エントリによって、DNS 参照が Azure Traffic Manager にリダイレクトされます。
3. Azure Traffic Manager の DNS サーバーのいずれかに対して、 *scalable-ase-demo.trafficmanager.net* の DNS 参照が実行されます。
4. 先ほど *TrafficRoutingMethod* パラメーターに指定した負荷分散ポリシーに基づいて、構成済みのエンドポイントのいずれかが Traffic Manager によって選択されます。 次に、そのエンドポイントの FQDN がブラウザーまたはデバイスに返されます。
5. エンドポイントの FQDN は App Service Environment で実行されるアプリ インスタンスの URL であるため、ブラウザーまたはデバイスは FQDN を IP アドレスに解決するよう Microsoft Azure DNS サーバーに要請します。 
6. ブラウザーまたはデバイスはその IP アドレスに HTTP/S 要求を送信します。  
7. 要求は、App Service Environment のいずれかで実行されているアプリ インスタンスのいずれかに到達します。

次のコンソール画像は、サンプル アプリのカスタム ドメインの DNS 参照を示しています。 ここでは、3 つのサンプル App Service Environment のいずれか (この場合は 3 つの App Service Environment の 2 つ目) で実行されるアプリ インスタンスに正しく解決されています。

![DNS Lookup][DNSLookup] 

## <a name="additional-links-and-information"></a>その他のリンクおよび情報
PowerShell を使用した [Azure Resource Manager による Traffic Manager のサポート][ARMTrafficManager]に関するドキュメント。  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
