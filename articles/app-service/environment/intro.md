---
title: はじめに
description: Azure App Service Environments を利用して、完全に分離された専用の環境でアプリをスケーリング、セキュリティ保護、および最適化する方法について学習します。
author: ccompy
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.topic: overview
ms.date: 04/19/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 6fff19498e9ca70991d3190165df70a48136f502
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92631140"
---
# <a name="introduction-to-the-app-service-environments"></a>App Service Environment の概要 #
## <a name="overview"></a>概要 ##

Azure App Service Environment は、App Service アプリを大規模かつ安全に実行するために完全に分離された専用の環境を提供する、Azure App Service の機能です。 この機能は、以下をホストできます。

* Windows Web アプリ
* Linux Web アプリ 
* Docker コンテナー
* モバイル アプリ
* 関数

App Service Environment (ASE) は、以下を必要とするアプリケーション ワークロードに最適です。

* 高スケール。
* 分離およびセキュリティで保護されたネットワーク アクセス。
* 高いメモリ使用率。

顧客は、複数の ASE を 1 つの Azure リージョン内に作成することも、複数の Azure リージョンにわたって作成することもできます。 この柔軟性により、ASE は、高 RPS (1 秒あたりの要求数) のワークロードをサポートするステートレス アプリケーション層の水平方向のスケーリングに最適です。

ASE では、単一の顧客のアプリケーションは VNet の 1 つでホストされます。 顧客は、受信と送信の両方のアプリケーション ネットワーク トラフィックをきめ細かく制御することができます。 アプリケーションは VPN を介した、オンプレミスの企業リソースへのセキュリティで保護された高速な接続を確立することができます。

* ASE には専用の価格レベルがあります。[独立したオファー](https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment)が高い拡張性とセキュリティを促進するのにどのように役立つかを学習してください。
* [App Service Environments v2](https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment) は、ネットワークのサブネット内のアプリを保護するための環境と、Azure App Service の独自のプライベートな展開を提供します。
* 複数の ASE を使って水平方向にスケーリングすることができます。 詳細については、[地理的に分散したアプリ フットプリントの設定](app-service-app-service-environment-geo-distributed-scale.md)に関するページを参照してください。
* AzureCon Deep Dive に示されているようなセキュリティ アーキテクチャは、ASE を使用して構成できます。 AzureCon Deep Dive に示されたセキュリティ アーキテクチャがどのように構成されたかを確認するには、App Service Environment での[レイヤード セキュリティ アーキテクチャのインプリメント方法](app-service-app-service-environment-layered-security.md)に関する記事を参照してください。
* ASE で実行されるアプリへのアクセスは、Web アプリケーション ファイアウォール (WAF) などのアップストリーム デバイスによって制限できます。 詳細については、[Web アプリケーション ファイアウォール (WAF)][AppGW] に関するページを参照してください。
* Availability Zones (AZ) には、ゾーンの固定を使用して App Service Environment をデプロイできます。  詳細については、「[App Service Environment の Availability Zones サポート][ASEAZ]」を参照してください。

## <a name="dedicated-environment"></a>専用の環境 ##

ASE は、1 つのサブスクリプション専用であり、100 の App Service プラン インスタンスをホストできます。 1 つの App Service プラン内の 100 のインスタンスから、100 の単一インスタンス App Service プラン、およびその間のすべてのプランがあります。

ASE は、フロントエンドとワーカーで構成されます。 フロントエンドは、HTTP/HTTPS ターミネーションと、ASE 内でのアプリ要求の自動負荷分散を担当します。 ASE で App Service プランをスケールアウトすると、フロントエンドが自動的に追加されます。

ワーカーは、お客様のアプリをホストする役割です。 ワーカーは、3 つの固定サイズで使用できます。

* 1 vCPU/3.5 GB RAM
* 2 vCPU/7 GB RAM
* 4 vCPU/14 GB RAM

フロントエンドとワーカーをお客様が管理する必要はありません。 すべてのインフラストラクチャは、お客様が App Service プランをスケール アウトしたときに自動的に追加されます。 App Service プランが ASE で作成、スケーリングされるときに、必要なインフラストラクチャが適宜追加または削除されます。

インフラストラクチャの支払いを行うための ASE の一定の月額料金があり、ASE のサイズが変化しても料金は変わりません。 それに加えて、App Service プランの vCPU あたりのコストがあります。 ASE でホストされているすべてのアプリは、分離された価格 SKU に含まれます。 ASE の価格については、「[App Service の価格][Pricing]」で、ASE に関して提供されている選択肢を参照してください。

## <a name="virtual-network-support"></a>Virtual Network のサポート ##

ASE 機能は、お客様の Azure Resource Manager 仮想ネットワークに Azure App Service を直接デプロイしたものです。 Azure の仮想ネットワークの詳細については、「[Azure 仮想ネットワークについてよく寄せられる質問 (FAQ)](../../virtual-network/virtual-networks-faq.md)」を参照してください。 ASE は常に仮想ネットワーク内 (正確には仮想ネットワークのサブネット内) に存在します。 仮想ネットワークのセキュリティ機能を使って、アプリの送受信方向のネットワーク通信を制御することができます。

ASE は、パブリック IP アドレスでインターネットに接続することも、Azure 内部ロード バランサー (ILB) アドレスだけで内部接続することもできます。

[ネットワーク セキュリティ グループ][NSGs]を使用して、受信ネットワーク通信を、ASE が存在するサブネットに制限できます。 NSG を使用することで、WAF やネットワーク SaaS プロバイダーなど、アップストリーム デバイスおよびサービスの背後でアプリを実行できます。

また、アプリは、内部データベースや Web サービスなどの企業リソースに頻繁にアクセスする必要があります。 オンプレミス ネットワークへの VPN 接続が備わった仮想ネットワークに ASE をデプロイした場合、その ASE 内のアプリはオンプレミス リソースにアクセスすることができます。 この機能は、VPN が[サイト対サイト](../../vpn-gateway/vpn-gateway-multi-site.md)か [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) VPN かに関係なく当てはまります。

ASE と仮想ネットワークおよびオンプレミス ネットワークとの連携方法の詳細については、[App Service Environment のネットワークの考慮事項][ASENetwork]に関するページを参照してください。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##

App Service Environment には、ASEv1 と ASEv2 です。 前述の情報は ASEv2 に基づいていました。 このセクションでは、ASEv1 と ASEv2 の違いについて説明します。 

ASEv1 では、すべてのリソースを手動で管理する必要があります。 これには、フロントエンド、ワーカー、IP ベースの SSL に使用する IP アドレスが含まれます。 App Service プランをスケールアウトするには、そのプランをホストするワーカー プールを先にスケールアウトしておく必要があります。

ASEv1 では、ASEv2 とは異なる価格モデルを使用します。 ASEv1 では、割り当てられた vCPU ごとに料金を支払います。 これには、フロントエンドまたはどのワークロードもホストしていないワーカーに使用される vCPU が含まれます。 ASEv1 では、ASE の既定の最大スケール サイズは合計で 55 ホストです。 これにはワーカーとフロントエンドが含まれます。 ASEv1 の利点の 1 つは、従来の仮想ネットワークと Resource Manager 仮想ネットワークにデプロイできることです。 ASEv1 について詳しくは、[App Service Environment v1 の概要][ASEv1Intro]に関するページを参照してください。

<!--Links-->
[App Service Environments v2]: https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment
[Isolated offering]: https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEAZ]: https://azure.github.io/AppService/2019/12/12/App-Service-Environment-Support-for-Availability-Zones.html
