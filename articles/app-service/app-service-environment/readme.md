---
title: "Azure App Service Environment の readme"
description: "Azure App Service Environment を説明するドキュメントの一覧を示します"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 77452413-5193-4762-8b3d-5fa8e4edf1ca
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 5b1362854dbc3b0098718bd2ea3cffb06366000c
ms.contentlocale: ja-jp
ms.lasthandoff: 08/03/2017

---

# <a name="app-service-environment-documentation"></a>App Service 環境のドキュメント
 Azure App Service Environment は、App Service アプリを大規模かつ安全に実行するために完全に分離された専用の環境を提供する、Azure App Service の機能です。 この機能は、[Web アプリ][webapps]、[モバイル アプリ][mobileapps]、[API アプリ][APIApps]、および[関数][Functions]をホストすることができます。

App Service Environment (ASE) は、以下を必要とするアプリケーション ワークロードに最適です。

* 高スケール。
* 分離およびセキュリティで保護されたネットワーク アクセス。

顧客は、複数の ASE を 1 つの Azure リージョン内に作成することも、複数の Azure リージョンにわたって作成することもできます。 この柔軟性により、ASE は、高 RPS のワークロードをサポートするステートレス アプリケーション層の水平方向のスケーリングに最適です。

ASE は、単一の顧客のアプリケーションだけを実行するために分離され、常に Azure 仮想ネットワークにデプロイされます。 顧客は、[ネットワーク セキュリティ グループ][NSGs]を使用することで、受信と送信の両方のアプリケーション ネットワーク トラフィックをきめ細かく制御することができます。 また、アプリケーションは仮想ネットワークを介した、オンプレミスの企業リソースへのセキュリティで保護された高速な接続を確立することができます。

アプリは、内部データベースや Web サービスなどの企業リソースに頻繁にアクセスする必要があります。 ASE で動作するアプリは、[サイト間][SiteToSite] VPN 接続や [Azure ExpressRoute][ExpressRoute] 接続を介してリソースにアクセスすることができます。

* [App Service Environment の概要][Intro]
* [App Service Environment を作成する][MakeExternalASE]
* [App Service Environment で内部ロード バランサーを作成する][MakeILBASE]
* [App Service Environment の使用][UsingASE]
* [App Service Environment のネットワークの考慮事項][ASENetwork]
* [テンプレートからの App Service Environment の作成][MakeASEfromTemplate]


## <a name="videos"></a>ビデオ
Azure App Service での企業向けの最新の PaaS をマスターする
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

拡張性が高く安全なアプリのデプロイ
>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

Azure App Service でのエンタープライズ Web アプリとモバイル アプリの実行
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##
App Service Environment には、ASEv1 と ASEv2 の 2 つのバージョンがあります。 ASEv1 について詳しくは、「[App Service 環境のドキュメント][ASEv1README]」をご覧ください。


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[ASEv1README]: ../app-service-app-service-environments-readme.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-site-to-site-create.md
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

