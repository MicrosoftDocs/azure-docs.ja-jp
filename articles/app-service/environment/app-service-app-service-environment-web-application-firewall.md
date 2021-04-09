---
title: WAF を構成する
description: Azure Application Gateway またはサードパーティの Web アプリケーション ファイアウォール (WAF) を使用して、App Service Environment の前に WAF を構成する方法について説明します。
author: ccompy
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.topic: tutorial
ms.date: 03/03/2018
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 354568fa3ab3816b643a8f08305ab55868a9b0b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90973713"
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>App Service Environment の Web アプリケーション ファイアウォール (WAF) を構成する
## <a name="overview"></a>概要

Web アプリケーション ファイアウォール (WAF) は、着信する Web トラフィックを検査して、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、アプリケーション DDoS、およびその他の攻撃をブロックすることにより、Web アプリケーションのセキュリティを確保するのに役立ちます。 さらに、データ損失防止 (DLP) のためにバックエンド Web サーバーからの応答を検査することもできます。 App Service Environment が提供する分離と追加スケーリングと組み合わせることで、悪意のある要求と大量のトラフィックに対処する必要がある、ビジネスに不可欠な Web アプリケーションをホストする理想的な環境が用意されます。 Azure では、[Application Gateway](../../application-gateway/overview.md) で WAF 機能が提供されます。  App Service Environment と Application Gateway を統合する方法については、[ILB ASE と Application Gateway の統合](./integrate-with-application-gateway.md)に関するドキュメントを参照してください。

Azure Application Gateway に加えて、[Azure 用 Barracuda WAF](https://www.barracuda.com/programs/azure) のように [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf?tab=PlansAndPrice) で入手できるマーケットプレース オプションがいくつかあります。 このドキュメントの残りの部分では、App Service Environment と Barracuda WAF デバイスを統合する方法について説明します。

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>セットアップ
このドキュメントでは、複数の負荷分散されたBarracuda WAF インスタンスの背後に App Service Environment を構成して、WAF からのトラフィックのみが App Service Environment に到着でき、DMZ からアクセスできないようにします。 さらに、Azure Traffic Manager を Barracuda WAF インスタンスの前に配置して、Azure のデータセンターとリージョン全体で負荷が分散されるようにします。 設定の概要図は次のようになります。

![この図は、Web Application Firewall のインスタンスに接続するオプションの Azure Traffic Manager を示しています。その接続先のネットワーク ACL では、2 つのリージョンの Web、API、Mobile Apps を含んだ App Service Environment 内のファイアウォールからのトラフィックのみが許可されます。][Architecture] 

> [!NOTE]
> [App Service Environment での ILB のサポート](app-service-environment-with-internal-load-balancer.md)の導入により、ASE を DMZ からアクセスできないように設定し、プライベート ネットワークでのみ使用できるように構成することができます。 
> 
> 

## <a name="configuring-your-app-service-environment"></aApp Service Environment の構成
App Service Environment の構成については、このテーマに関する[ドキュメント](app-service-web-how-to-create-an-app-service-environment.md)を参照してください。 App Service Environment を作成した後、その環境の中に Web Apps、API Apps、[Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) を作成できます。これらはすべて、次のセクションで構成する WAF の背後で保護されます。

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Barracuda WAF クラウド サービスを構成する
Barracuda には、その WAF を Azure の仮想マシンにデプロイすることに関する [詳細な記事](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) があります。 ただし、これらの手順に従うときは、冗長性を持たせ、単一障害点の発生を防ぐために、少なくとも 2 つの WAF インスタンスの VM を同じクラウド サービスにデプロイします。

### <a name="adding-endpoints-to-cloud-service"></a>クラウド サービスへのエンドポイントの追加
2 つ以上の WAF VM インスタンスをクラウド サービスに用意した後、 [Azure Portal](https://portal.azure.com/) を使用して、次の図に示すように、アプリケーションで使用される HTTP エンドポイントと HTTPS エンドポイントを追加できます。

![エンドポイントを構成する][ConfigureEndpoint]

アプリケーションがその他のエンドポイントを使用する場合は、それも、この一覧に追加してください。 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>管理ポータルを使用した Barracuda WAF の構成
Barracuda WAF は、管理ポータルによる構成で TCP ポート 8000 を使用します。 WAF VM のインスタンスが複数ある場合は、ここに示す手順を VM インスタンスごとに繰り返す必要があります。 

> [!NOTE]
> WAF 構成が完了したら、WAF を安全に保つために、すべての WAF VM から TCP/8000 エンドポイントを削除してください。
> 
> 

次の図に示すように、管理エンドポイントを追加して、Barracuda WAF を構成します。

![管理エンドポイントを追加する][AddManagementEndpoint]

ブラウザーを使用して、クラウド サービスの管理エンドポイントを参照します。 クラウド サービスの名前が test.cloudapp.net の場合、`http://test.cloudapp.net:8000` を参照して、そのエンドポイントにアクセスします。 次の図のようなログイン ページが表示されます。WAF VM の設定フェーズで指定した資格情報を使用してログインできます。

![管理ログイン ページ][ManagementLoginPage]

ログインすると、次の図のような、WAF 保護に関する基本的な統計情報を示すダッシュボードが表示されます。

![管理ダッシュボード][ManagementDashboard]

**[サービス]** タブをクリックして、保護対象のサービスの WAF を構成できます。 Barracuda WAF の構成の詳細については、[そのドキュメント](https://techlib.barracuda.com/waf/getstarted1)を参照してください。 次の例では、HTTP と HTTPS でトラフィックを提供する App Service アプリが構成されています。

![管理ダッシュボードでサービスを追加する][ManagementAddServices]

> [!NOTE]
> お客様の App Service Environment 内でアプリケーションがどのように構成され、どのような機能が使用されているかに応じて、80 と 443 以外の TCP ポートに対するトラフィックを転送する必要があります (例: App Service アプリの IP TLS を設定している場合)。App Service Environment で使用されるネットワーク ポートの一覧については、[着信トラフィックの制御に関するドキュメント](app-service-app-service-environment-control-inbound-traffic.md)のネットワーク ポートのセクションを参照してください。
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Microsoft Azure Traffic Manager の構成 (省略可能)
アプリケーションが複数のリージョンで使用できる場合は、 [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)の背後でそれらの負荷を分散できます。 これを行うには、次の図に示すように、[Azure Portal](https://portal.azure.com) で、WAF のクラウド サービス名を使用して、Traffic Manager プロファイルにエンドポイントを追加します。 

![Traffic Manager のエンドポイント][TrafficManagerEndpoint]

アプリケーションが認証を必要とする場合は、Traffic Manager がアプリケーションの使用可能性をチェックする ping を実行するための、認証を必要としないリソースがあることを確認します。 次の図に示すように、[Azure Portal](https://portal.azure.com) の **[構成]** ページで URL を構成できます。

![Traffic Manager を構成する][ConfigureTrafficManager]

Traffic Manager の ping を WAF からアプリケーションに転送するには、次の例に示すように、Barracuda WAF に Web サイト変換を設定して、トラフィックをアプリケーションに転送します。

![Web サイトの変換][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>App Service Environment へのトラフィックをネットワーク セキュリティ グループ (NSG) を使用して保護する
App Service Environment へのトラフィックを、クラウド サービスの VIP アドレスを使用して WAF からのトラフィックだけに制限する方法の詳細については、 [着信トラフィックの制御に関するドキュメント](app-service-app-service-environment-control-inbound-traffic.md) を参照してください。 このタスクを TCP ポート 80 に対して実行するサンプル PowerShell コマンドを次に示します。

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
```

SourceAddressPrefix を、WAF のクラウド サービスの仮想 IP アドレス (VIP) に置き換えます。

> [!NOTE]
> クラウド サービスの VIP は、クラウド サービスを削除して再作成すると変更されます。 これを行った場合は、ネットワーク リソース グループ内の IP アドレスを必ず更新してください。 
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
