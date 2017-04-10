---
title: "App Service 環境の Web アプリケーション ファイアウォール (WAF) を構成する"
description: "App Service 環境の前に Web アプリケーション ファイアウォールを構成する方法について説明します。"
services: app-service\web
documentationcenter: 
author: naziml
manager: erikre
editor: jimbe
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 36f38409366d2a0b52387091c5e93296756a0178
ms.lasthandoff: 03/01/2017


---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>App Service 環境の Web アプリケーション ファイアウォール (WAF) を構成する
## <a name="overview"></a>Overview
[Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) で入手できる [Barracuda WAF for Azure](https://www.barracuda.com/programs/azure) などの Web アプリケーション ファイアウォールは、受信 Web トラフィックを検査して、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、アプリケーション DDoS などの攻撃をブロックすることによって、Web アプリケーションを保護します。 さらに、データ損失防止 (DLP) のためにバックエンド Web サーバーからの応答を検査します。 App Service 環境が提供する分離と追加スケーリングと組み合わせることで、悪意のある要求と大量のトラフィックに対処する必要がある、ビジネスに不可欠な Web アプリケーションをホストする理想的な環境が用意されます。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>セットアップ
このドキュメントでは、複数の負荷分散されたBarracuda WAF インスタンスの背後に App Service 環境を構成して、WAF からのトラフィックのみが App Service 環境に到着でき、DMZ からアクセスできないようにします。 さらに、Azure Traffic Manager を Barracuda WAF インスタンスの前に配置して、Azure のデータセンターとリージョン全体で負荷が分散されるようにします。 このセットアップの高レベルの図は次のようになります。

![アーキテクチャ][Architecture] 

> 注: [App Service 環境での ILB のサポート](app-service-environment-with-internal-load-balancer.md)の導入により、ASE を DMZ からアクセスできないように設定し、プライベート ネットワークでのみ使用できるように構成することができます。 
> 
> 

## <a name="configuring-your-app-service-environment"></a>App Service 環境の構成
App Service 環境の構成については、このテーマに関する [ドキュメント](app-service-web-how-to-create-an-app-service-environment.md) を参照してください。 App Service Environment を作成した後、その環境の中に [Web アプリ](app-service-web-overview.md)、[API アプリ](../app-service-api/app-service-api-apps-why-best-platform.md)、[Mobile アプリ](../app-service-mobile/app-service-mobile-value-prop.md) を作成できます。これらはすべて、次のセクションで構成する WAF の背後で保護されます。

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Barracuda WAF クラウド サービスを構成する
Barracuda には、その WAF を Azure の仮想マシンにデプロイすることに関する [詳細な記事](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) があります。 ただし、これらの手順に従うときは、冗長性を持たせ、単一障害点の発生を防ぐために、少なくとも 2 つの WAF インスタンスの VM を同じクラウド サービスにデプロイします。

### <a name="adding-endpoints-to-cloud-service"></a>クラウド サービスへのエンドポイントの追加
2 つ以上の WAF VM インスタンスをクラウド サービスに用意した後、 [Azure ポータル](https://portal.azure.com/) を使用して、次の図に示すように、アプリケーションで使用される HTTP エンドポイントと HTTPS エンドポイントを追加できます。

![エンドポイントを構成する][ConfigureEndpoint]

アプリケーションがその他のエンドポイントを使用する場合は、それらもこの一覧に追加してください。 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>管理ポータルを使用した Barracuda WAF の構成
Barracuda WAF は、管理ポータルによる構成で TCP ポート 8000 を使用します。 WAF VM の複数のインスタンスがあるため、ここに示す手順を 各 VM インスタンスに対して繰り返す必要があります。 

> 注: WAF 構成が完了したら、WAF を安全に保つために、すべての WAF VM から TCP/8000 エンドポイントを削除してください。
> 
> 

次の図に示すように、管理エンドポイントを追加して、Barracuda WAF を構成します。

![管理エンドポイントを追加する][AddManagementEndpoint]

ブラウザーを使用して、クラウド サービスの管理エンドポイントを参照します。 クラウド サービスの名前が test.cloudapp.net の場合、http://test.cloudapp.net:8000 を参照して、そのエンドポイントにアクセスします。 次のようなログイン ページが表示されます。WAF VM のセットアップ フェーズで指定した資格情報を使用してログインできます。

![管理ログイン ページ][ManagementLoginPage]

ログインすると、次の図のような、WAF 保護に関する基本的な統計情報を示すダッシュボードが表示されます。

![管理ダッシュボード][ManagementDashboard]

[サービス] タブをクリックして、保護対象のサービスの WAF を構成できます。 Barracuda WAF の構成の詳細については、 [このドキュメント](https://techlib.barracuda.com/waf/getstarted1)を参照してください。 次の例では、HTTP と HTTPS でトラフィックを提供する Azure Web アプリが構成されています。

![管理ダッシュボードでサービスを追加する][ManagementAddServices]

> 注: App Service 環境内でアプリケーションがどのように構成され、どのような機能が使用されているかに応じて、トラフィックを 80 と 443 以外の TCP ポートに転送する必要があります (例: Web アプリの IP SSL を設定している場合)。 App Service 環境で使用されるネットワーク ポートの一覧については、 [着信トラフィックの制御に関するドキュメント](app-service-app-service-environment-control-inbound-traffic.md) のネットワーク ポートのセクションを参照してください。
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Microsoft Azure Traffic Manager の構成 (省略可能)
アプリケーションが複数のリージョンで使用できる場合は、 [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)の背後でそれらの負荷を分散できます。 これを行うには、次の図に示すように、 [Azure クラシック ポータル](https://manage.azure.com) で、WAF のクラウド サービス名を使用して、Traffic Manager プロファイルにエンドポイントを追加します。 

![Traffic Manager のエンドポイント][TrafficManagerEndpoint]

アプリケーションが認証を必要とする場合は、Traffic Manager がアプリケーションの使用可能性をチェックする ping を実行するための、認証を必要としないリソースがあることを確認します。 次に示すように、 [Azure クラシック ポータル](https://manage.azure.com) の [構成] セクションで URL を構成できます。

![Traffic Manager を構成する][ConfigureTrafficManager]

Traffic Manager の ping を WAF からアプリケーションに転送するには、次の例に示すように、Barracuda WAF に Web サイト変換を設定して、トラフィックをアプリケーションに転送します。

![Web サイトの変換][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>App Service 環境へのトラフィックをネットワーク セキュリティ グループ (NSG) を使用して保護する
App Service 環境へのトラフィックを、クラウド サービスの VIP アドレスを使用して WAF からのトラフィックだけに制限する方法の詳細については、 [着信トラフィックの制御に関するドキュメント](app-service-app-service-environment-control-inbound-traffic.md) を参照してください。 このタスクを TCP ポート 80 に対して実行するサンプル Powershell コマンドを次に示します。

    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

SourceAddressPrefix を、WAF のクラウド サービスの仮想 IP アドレス (VIP) に置き換えます。

> 注: クラウド サービスの VIP は、クラウド サービスを削除して再作成すると変更されます。 これを行った場合は、ネットワーク リソース グループ内の IP アドレスを必ず更新してください。 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png

