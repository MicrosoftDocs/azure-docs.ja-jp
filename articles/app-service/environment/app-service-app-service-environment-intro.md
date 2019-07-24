---
title: App Service Environment v1 の概要 - Azure
description: すべてのアプリを実行するためのセキュリティで保護され、VNet に参加している専用のスケール ユニットを提供する、App Service Environment v1 の機能について説明します。
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 2bb1a9c3922f435b6be78614aacff6e85bf475ff
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56454244"
---
# <a name="introduction-to-app-service-environment-v1"></a>App Service Environment v1 の概要

> [!NOTE]
> この記事は、App Service Environment v1 に関するものです。  より強力なインフラストラクチャ上で実行できる、使いやすい新しいバージョンの App Service Environment があります。 新しいバージョンの詳細については、「[App Service Environment の概要](intro.md)」を参照してください。

## <a name="overview"></a>概要

App Service 環境は、Azure App Service アプリを高スケールで安全に実行するために完全に分離された専用の環境を提供する、[Azure App Service](../overview.md) の [Premium][PremiumTier] サービス プラン オプションです。App Service 環境には、Web Apps、Mobile Apps、API Apps が含まれています。  

App Service 環境は、以下を必要とするアプリケーション ワークロードに最適です。

* 高スケール
* 分離およびセキュリティで保護されたネットワーク アクセス

顧客は、複数の App Service 環境を 1 つの Azure リージョン内に作成することも、複数の Azure リージョンにわたって作成することもできます。  そのため、App Service 環境は、高 RPS のワークロードをサポートするステートレス アプリケーション層の水平方向のスケーリングに最適です。

App Service 環境は、単一の顧客のアプリケーションだけを実行するために分離され、常に仮想ネットワークにデプロイされます。  顧客は、受信および送信アプリケーション ネットワーク トラフィックをきめ細かく制御できます。また、アプリケーションは、オンプレミスの企業リソースへの仮想ネットワーク経由のセキュリティで保護された高速接続を確立できます。

App Service 環境で高スケールおよびセキュリティで保護されたネットワーク アクセスを有効にする方法の概要については、App Service 環境の [AzureCon の詳細][AzureConDeepDive]に関する記述を参照してください。

複数の App Service 環境を使用する水平方向のスケーリングの詳細については、[地理的に分散されたアプリのフットプリント][GeodistributedAppFootprint]のセットアップ方法に関する記事を参照してください。

AzureCon Deep Dive に示されたセキュリティ アーキテクチャがどのように構成されたかを確認するには、App Service 環境での [レイヤード セキュリティ アーキテクチャ](app-service-app-service-environment-layered-security.md) のインプリメントに関する記事を参照してください。

App Service 環境で実行されるアプリへのアクセスは、Web アプリケーション ファイアウォール (WAF) などのアップ ストリーム デバイスによって制限できます。  このシナリオについては、記事「 [App Service 環境の Web アプリケーション ファイアウォール (WAF) を構成する](app-service-app-service-environment-web-application-firewall.md) 」で説明します。

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>専用のコンピューティング リソース

App Service 環境内のすべてのコンピューティング リソースは、1 つのサブスクリプション専用です。App Service 環境は、1 つのアプリケーションだけが使用する最大 50 個のコンピューティング リソースで構成できます。

App Service 環境は、1 つのフロントエンド コンピューティング リソース プールと、1 ～ 3 個の worker コンピューティング リソース プールで構成されます。

フロントエンド プールには、SSL ターミネーションと、App Service 環境内でのアプリ要求の自動負荷分散を担当するコンピューティング リソースが含まれます。

各ワーカー プールには、[App Service プラン][AppServicePlan]に割り当てられたコンピューティング リソースが含まれ、コンピューティング リソースには 1 つ以上の Azure App Service アプリが含まれます。  App Service 環境には、異なるワーカー プールを 3 つまで配置できるため、ワーカー プールごとに異なるコンピューティング リソースを柔軟に選択できます。  

たとえば、これにより、開発アプリまたはテスト アプリ向けの App Service プランでは、性能の低いコンピューティング リソースを含むワーカー プールを 1 つ作成できます。  2 番目 (または 3 番目) のワーカー プールでは、実稼働アプリを実行する App Service プラン向けにより性能の高いコンピューティング リソースを使用することができます。

フロントエンドとワーカー プールで使用可能なコンピューティング リソースの量の詳細については、[App Service Environment の構成方法][HowToConfigureanAppServiceEnvironment]に関するページを参照してください。  

App Service Environment でサポートされる使用可能なコンピューティング リソースのサイズの詳細については、[App Service の料金][AppServicePricing]のページで、Premium 価格レベルの App Service Environment で使用できるオプションを確認してください。

## <a name="virtual-network-support"></a>Virtual Network のサポート

App Service Environment は、Azure Resource Manager の仮想ネットワーク、**または**クラシック デプロイ モデルの仮想ネットワークの**どちらにでも**作成できます ([仮想ネットワークについて詳しくはこちら][MoreInfoOnVirtualNetworks])。  App Service 環境は常に仮想ネットワーク (もっと正確に言えば、仮想ネットワークのサブネット内) に存在するため、仮想ネットワークのセキュリティ機能を使用して、受信と送信の両方のネットワーク通信を制御できます。  

App Service Environment は、パブリック IP アドレスでインターネットに接続することも、Azure 内部ロード バランサー (ILB) アドレスだけで内部接続することもできます。

[ネットワーク セキュリティ グループ][NetworkSecurityGroups]を使用して、受信ネットワーク通信を、App Service Environment が存在するサブネットに制限できます。  これによって、Web アプリケーション ファイアウォールやネットワーク SaaS プロバイダーなど、アップストリーム デバイスおよびサービスの背後でアプリを実行できます。

また、アプリは、内部データベースや Web サービスなどの企業リソースに頻繁にアクセスする必要があります。  一般的な方法では、これらのエンドポイントを、Azure 仮想ネットワーク内を通過する内部ネットワーク トラフィックのみが使用できるようにします。  App Service Environment が内部サービスと同じ仮想ネットワークに参加すると、その環境内で実行されているアプリは、[サイト間][SiteToSite]接続や [Azure ExpressRoute][ExpressRoute] 接続で到達可能なエンドポイントなど、内部サービスにアクセスできます。

仮想ネットワークとオンプレミス ネットワークでの App Service 環境の機能の詳細については、「[App Service 環境のネットワーク アーキテクチャの概要][NetworkArchitectureOverview]」、「[App Service 環境への受信トラフィックを制御する方法][ControllingInboundTraffic]」、および「[App Service 環境からバックエンド リソースへの安全な接続][SecurelyConnectingToBackends]」をご覧ください。 

## <a name="getting-started"></a>使用の開始

App Service 環境の使用を開始するには、[App Service Environment の作成方法][HowToCreateAnAppServiceEnvironment]に関するページを参照してください。

App Service Environment のネットワーク アーキテクチャの概要については、「[App Service 環境のネットワーク アーキテクチャの概要][NetworkArchitectureOverview]」をご覧ください。

ExpressRoute での App Service Environment の使用方法の詳細については、「[ExpressRoute を使用した App Service 環境のネットワーク構成の詳細][NetworkConfigDetailsForExpressRoute]」をご覧ください。

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: https://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->