---
title: App Service Environment | Microsoft Docs
description: "Azure App Service Environment とは App Service Environment の概要"
keywords: "azure app service environment, 仮想ネットワーク, ネットワークの保護"
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: 1db5c057-3c56-4537-b580-cdd21fe3f3a7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: stefsch
translationtype: Human Translation
ms.sourcegitcommit: df2fa1d0790f8942a0350f9734232cc4b25cadc1
ms.openlocfilehash: a54f559801e1a4f4b752bc2268ea8d6cb20b1267
ms.lasthandoff: 02/16/2017


---
# <a name="app-service-environment-documentation"></a>App Service 環境のドキュメント
App Service 環境は、Azure App Service アプリを高スケールで安全に実行するために完全に分離された専用の環境を提供する、Azure App Service の [Premium][PremiumTier] サービス プラン オプションです。App Service 環境には、[Web Apps][WebApps]、[Mobile Apps][MobileApps]、[API Apps][APIApps] が含まれています。  

App Service 環境は、以下を必要とするアプリケーション ワークロードに最適です。

* 高スケール
* 分離およびセキュリティで保護されたネットワーク アクセス

顧客は、複数の App Service 環境を 1 つの Azure リージョン内に作成することも、複数の Azure リージョンにわたって作成することもできます。  そのため、App Service 環境は、高 RPS のワークロードをサポートするステートレス アプリケーション層の水平方向のスケーリングに最適です。

App Service 環境は、単一の顧客のアプリケーションだけを実行するために分離され、常に仮想ネットワークにデプロイされます。  顧客は、[ネットワーク セキュリティ グループ][NetworkSecurityGroups]を使用することで、受信と送信の両方のアプリケーション ネットワーク トラフィックをきめ細かく制御することができます。  また、アプリケーションは仮想ネットワークを介した、オンプレミスの企業リソースへのセキュリティで保護された高速な接続を確立することができます。

アプリは、内部データベースや Web サービスなどの企業リソースに頻繁にアクセスする必要があります。  App Service Environment で動作するアプリは、[サイト間][SiteToSite] VPN 接続や [Azure ExpressRoute][ExpressRoute] 接続を介してリソースにアクセスすることができます。

* [App Service 環境とは](../app-service-web/app-service-app-service-environment-intro.md)
* [App Service 環境の作成](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [App Service 環境でのアプリの作成](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [App Service Environment での内部ロード バランサーの作成と使用](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [App Service 環境の構成](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [App Service 環境でのアプリのスケーリング](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [ネットワークのセキュリティとアーキテクチャ](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>方法
[!INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]

## <a name="videos"></a>ビデオ
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]



<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

