---
title: App Service 環境を使用した geo 分散スケール - Azure
description: Traffic Manager および App Service 環境による geo 分散を使用してアプリを水平方向にスケールする方法について説明します。
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: eaefebc569f5bf5461ff7c4407fa77a0c62d4fe8
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070215"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>App Service 環境を使用した geo 分散スケール
## <a name="overview"></a>概要

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

きわめて高いスケールを必要とするアプリケーション シナリオでは、単一のアプリ デプロイメントで使用できるコンピューティング リソース容量では足りないことがあります。  投票アプリケーション、スポーツ イベント、テレビ放送される娯楽イベントは、いずれも非常に高いスケールを必要とするシナリオの例です。 高スケール要件を満たすには、極端に負荷の大きい要件に対応できるように、単一のリージョン内、または複数のリージョンにわたって、複数のアプリ デプロイメントを使用してアプリを水平方向に拡張する必要があります。

App Service 環境は、水平方向のスケールアウトに最適なプラットフォームです。既知の要求レートをサポートできる App Service 環境構成を選択していれば、開発者は追加の App Service 環境を "ひな型" 方式でデプロイして、必要なピーク時負荷容量を確保できます。

たとえば、App Service 環境構成で実行されるアプリが 20K RPS (1 秒あたりの要求数) を処理することがテストでわかっているとします。  必要なピーク時負荷容量が 100K RPS である場合、5 つの App Service 環境を作成して、予想される最大負荷をアプリケーションが処理できるように構成できます。

顧客は通常、カスタム ドメイン (またはバニティ ドメイン) を使用してアクセスするため、開発者は App Service 環境のすべてのインスタンスにアプリの要求を分散する方法が必要になります。  これを実現する優れた方法が、[Azure Traffic Manager プロファイル][AzureTrafficManagerProfile]を使用したカスタム ドメインの解決です。  Traffic Manager プロファイルは、個々の App Service 環境をすべてポイントするように構成できます。  すべての App Service 環境を対象とした顧客の分散は、Traffic Manager が Traffic Manager プロファイルの負荷分散設定に基づいて自動的に処理します。  このアプローチは、すべての App Service 環境が単一の Azure リージョンにデプロイされている場合はもちろん、世界の複数の Azure リージョンに展開されている場合でも、問題なく機能します。

さらに、顧客はバニティ ドメインを使用してアプリにアクセスするため、アプリを実行する App Service 環境の数を意識することがありません。  これにより、開発者は、観測されたトラフィック負荷に基づいて、App Service 環境の追加や削除を迅速かつ簡単に実行できます。

次の概念図は、単一リージョン内の 3 つの App Service 環境で水平方向にスケールアウトされたアプリを示しています。

![Conceptual Architecture][ConceptualArchitecture] 

このトピックの残りの部分では、複数の App Service 環境を使用してサンプル アプリの分散トポロジを設定するのに必要な手順について説明します。

## <a name="planning-the-topology"></a>トポロジを計画する
分散アプリケーションのフットプリントを構築する前に、いくつかの情報を前もって用意しておくと作業がスムーズになります。

* **アプリのカスタム ドメイン:** 顧客がアプリへのアクセスに使用するカスタム ドメイン名が必要です。  サンプル アプリでは、カスタム ドメイン名は `www.scalableasedemo.com` です。
* **Traffic Manager ドメイン:** [Azure Traffic Manager プロファイル][AzureTrafficManagerProfile]の作成時に、ドメイン名を選択する必要があります。  この名前は、Traffic Manager が管理するドメイン エントリを登録する際に、 *trafficmanager.net* サフィックスと組み合わされます。  サンプル アプリでは、選択される名前は *scalable-ase-demo*です。  そのため、Traffic Manager で管理される完全なドメイン名は、 *scalable-ase-demo.trafficmanager.net*になります。
* **アプリ フットプリントのスケーリングに関する戦略:** アプリケーションのフットプリントは単一リージョン内の複数の App Service Environment に分散されるのか、  複数のリージョンなのか、  両方のアプローチの最適な組み合わせなのか。  この決定は、顧客のトラフィックが発生する場所に加えて、アプリをサポートするバックエンド インフラストラクチャの他の要素のスケーラビリティに関する期待事項に基づく必要があります。  たとえば、完全にステートレスなアプリケーションでは、各 Azure リージョンで複数の App Service Environment を組み合わせ、さらに複数の Azure リージョンにデプロイされた App Service Environment を掛け合わせることで、大規模なスケーリングを実施できます。  選択できるパブリック Azure リージョンは 15 以上あるため、顧客はスケーラビリティのきわめて高いアプリケーション フットプリントを世界規模で構築できます。  この記事のサンプル アプリでは、単一の Azure リージョン (米国中南部) に 3 つの App Service 環境が作成されています。
* **App Service Environment の名前付け規則:** 各 App Service Environment には一意の名前が必要です。  1 つや 2 つではなく数の多い App Service 環境では、各 App Service 環境を識別しやすい命名規則があると便利です。  サンプル アプリでは、シンプルな命名規則が使用されています。  3 つの App Service Environment の名前は *fe1ase*、*fe2ase*、*fe3ase* です。
* **アプリの名前付け規則**:アプリのインスタンスが複数デプロイされるため、デプロイされたアプリの各インスタンスに名前が必要です。  App Service 環境のあまり知られていない便利な特長の 1 つですが、同じアプリ名を複数の App Service 環境で使用できます。  App Service 環境ごとに一意のドメイン サフィックスがあるため、開発者は各環境でまったく同じアプリ名を再利用できます。  たとえば、開発者は、*myapp.foo1.p.azurewebsites.net*、*myapp.foo2.p.azurewebsites.net*、*myapp.foo3.p.azurewebsites.net* のようなアプリ名を設定できます。ただし、サンプル アプリでは、各アプリ インスタンスにも一意の名前を設定しています。  使用されているアプリ インスタンス名は *webfrontend1*、*webfrontend2*、*webfrontend3* です。

## <a name="setting-up-the-traffic-manager-profile"></a>Traffic Manager プロファイルを設定する
アプリの複数のインスタンスを複数の App Service Environment にデプロイしたら、個々のアプリ インスタンスを Traffic Manager に登録できます。  サンプル アプリでは、顧客を次のデプロイ済みアプリ インスタンスのいずれかにルーティングするための *scalable-ase-demo.trafficmanager.net* 用 Traffic Manager プロファイルが必要です。

* **webfrontend1.fe1ase.p.azurewebsites.net:** 1 つ目の App Service Environment にデプロイされているサンプル アプリのインスタンス。
* **webfrontend2.fe2ase.p.azurewebsites.net:** 2 つ目の App Service Environment にデプロイされているサンプル アプリのインスタンス。
* **webfrontend3.fe3ase.p.azurewebsites.net:** 3 つ目の App Service Environment にデプロイされているサンプル アプリのインスタンス。

**同じ** Azure リージョンで実行される複数の Azure App Service エンドポイントを登録するには、PowerShell を使用した [Azure Resource Manager による Traffic Manager のサポート][ARMTrafficManager]を使用すれば最も簡単に実現できます。  

最初の手順では、Azure Traffic Manager プロファイルの作成です。  次のコードでは、サンプル アプリ用プロファイルの作成方法を示しています。

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

*RelativeDnsName* パラメーターが *scalable-ase-demo* に設定されていることに注意してください。  ここでは、ドメイン名として *scalable-ase-demo.trafficmanager.net* が作成され、Traffic Manager プロファイルに関連付けられています。

*TrafficRoutingMethod* パラメーターは負荷分散ポリシーを定義しており、Traffic Manager はこのポリシーを使用して、顧客の負荷を利用可能なすべてのエンドポイントに分散する方法を判断します。  この例では、 *重み付け* 方式が選択されています。  この方式では、顧客の要求が、各エンドポイントに関連付けられている相対的な重みに基づいて、登録済みのすべてのアプリケーション エンドポイント間で分散されます。 

作成されるプロファイルに対して、各アプリ インスタンスがネイティブ Azure エンドポイントとして追加されます。  次のコードは、各フロント エンド Web アプリへの参照をフェッチし、 *TargetResourceId* パラメーターを使用して各アプリを Traffic Manager エンドポイントとして追加します。

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

アプリ インスタンスごとに *Add-AzureTrafficManagerEndpointConfig* が　1 回ずつ呼び出されている点に注意してください。  各 PowerShell コマンドの *TargetResourceId* パラメーターは、デプロイされたアプリの 3 つのインスタンスのうちの 1 つを参照します。  Traffic Manager プロファイルにより、プロファイルに登録されている 3 つのエンドポイントすべての間で負荷が分散されます。

3 つのエンドポイントすべてで *Weight* (重み) パラメーターに同じ値 (10) が使用されています。  これにより、顧客の要求が Traffic Manager によって 3 つのアプリケーションのすべてのインスタンス間で比較的均等に分散されます。 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>アプリのカスタム ドメインが Traffic Manager ドメインをポイントするように設定する
最後の必須手順として、アプリのカスタム ドメインが Traffic Manager ドメインをポイントするように設定します。  サンプル アプリでは、`www.scalableasedemo.com` が `scalable-ase-demo.trafficmanager.net` をポイントするように設定します。  この手順は、カスタム ドメインを管理するドメイン レジストラーを通じて実行する必要があります。  

利用しているレジストラーのドメイン管理ツールを使用して、カスタム ドメインが Traffic Manager ドメインをポイントする CNAME レコードを作成します。  次の図に、この CNAME の構成の例を示します。

![CNAME for Custom Domain][CNAMEforCustomDomain] 

このトピックでは説明しませんが、アプリ インスタンスごとにカスタム ドメインを登録する必要があることに注意してください。  これを忘れた場合、要求がアプリ インスタンスに到達したときに、アプリケーションにカスタム ドメインが登録されていないと、要求は失敗します。  

この例では、カスタム ドメインは `www.scalableasedemo.com` で、各アプリケーション インスタンスにカスタム ドメインが関連付けられています。

![カスタム ドメイン][CustomDomain] 

Azure App Service のアプリにカスタム ドメインを登録する方法については、[カスタム ドメインの登録][RegisterCustomDomain]に関するページを参照してください。

## <a name="trying-out-the-distributed-topology"></a>分散トポロジを試す
Traffic Manager と DNS を構成すると、最終的に、`www.scalableasedemo.com` への要求は次の順で処理されます。

1. ブラウザーまたはデバイスが `www.scalableasedemo.com` の DNS 参照を行います
2. ドメイン レジストラーの CNAME エントリによって、DNS 参照が Azure Traffic Manager にリダイレクトされます。
3. Azure Traffic Manager の DNS サーバーのいずれかに対して、 *scalable-ase-demo.trafficmanager.net* の DNS 参照が実行されます。
4. Traffic Manager が、負荷分散ポリシー (前半で Traffic Manager プロファイルを作成するときに使用した *TrafficRoutingMethod* パラメーター) に基づいて、構成済みのエンドポイントのいずれかを選択し、ブラウザーまたはデバイスにそのエンドポイントの FQDN を返します。
5. エンドポイントの FQDN は App Service 環境で実行されているアプリ インスタンスの URL であるため、ブラウザーまたはデバイスは FQDN を IP アドレスに解決するよう Microsoft Azure の DNS サーバーに要請します。 
6. ブラウザーまたはデバイスはその IP アドレスに HTTP/S 要求を送信します。  
7. 要求は、App Service 環境のいずれかで実行されているアプリ インスタンスのいずれかに到達します。

次のコンソールのスクリーンショットは、サンプル アプリのカスタム ドメインの DNS 参照が、3 つあるサンプル App Service 環境の 1 つ (この場合は、3 つの App Service 環境の 2 つ目) で実行されているアプリ インスタンスに正しく解決されたところを示しています。

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
