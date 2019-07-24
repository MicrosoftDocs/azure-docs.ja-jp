---
title: App Service 環境のネットワーク アーキテクチャの概要 - Azure
description: App Service 環境のネットワーク トポロジのアーキテクチャの概要
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 0d7d4af46e54ad89e0d084cb15af13e56115e996
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274172"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>App Service 環境のネットワーク アーキテクチャの概要
## <a name="introduction"></a>はじめに
App Service 環境は、常に[仮想ネットワーク][virtualnetwork]のサブネット内に作成され、App Service Environment で実行されるアプリは、同じ仮想ネットワーク トポロジ内に配置されたプライベート エンドポイントと通信できます。  顧客が仮想ネットワーク インフラストラクチャの一部をロックダウンする場合があるため、App Service 環境で発生するネットワーク通信フローの種類を理解しておくことが重要です。

## <a name="general-network-flow"></a>一般的なネットワーク フロー
App Service 環境 (ASE) でパブリック仮想 IP アドレス (VIP) をアプリに使用している場合、着信トラフィックはすべてそのパブリック VIP に到着します。  これには、アプリの HTTP/HTTPS トラフィックだけでなく、FTP のその他のトラフィック、リモート デバッグ機能、および Azure の管理操作も含まれます。  パブリック VIP で使用できるポート (必須およびオプションの両方) の完全な一覧については、[着信トラフィックの制御][controllinginboundtraffic]に関する記事の App Service Environment を参照してください。 

App Service 環境では、仮想ネットワークの内部アドレスだけにバインドされている実行中のアプリもサポートされます。この内部アドレスは ILB (内部ロード バランサー) アドレスとも呼ばれます。  ILB が有効になっている ASE では、アプリの HTTP/HTTPS トラフィックとリモート デバッグの呼び出しは ILB アドレスに到着します。  最も一般的な ILB ASE 構成の場合、FTP/FTPS トラフィックも ILB アドレスに到着します。  ただし、Azure の管理操作は、ILB が有効になっている ASE でも引き続きパブリック VIP のポート 454/455 に流れます。

次の図は、アプリがパブリック仮想 IP アドレスにバインドされている App Service 環境におけるさまざまな着信および発信ネットワーク フローの概要を示しています。

![一般的なネットワーク フロー][GeneralNetworkFlows]

App Service 環境は、さまざまな顧客のプライベート エンドポイントと通信できます。  たとえば、App Service 環境で実行されるアプリは、同じ仮想ネットワーク トポロジ内の IaaS 仮想マシンで実行されているデータベース サーバーに接続できます。

> [!IMPORTANT]
> ネットワーク図を見ると、"その他のコンピューティング リソース" が App Service 環境とは別のサブネットにデプロイされています。 ASE と同じサブネットにリソースをデプロイすると、(特定の ASE 内のルーティングを除き) ASE からそれらのリソースへの接続がブロックされます。 代わりに (同じ VNET 内の) 別のサブネットにデプロイします。 そうすると、App Service 環境から接続できるようになります。 追加の構成は必要ありません。
> 
> 

App Service 環境は、App Service 環境の管理と運用を行うために必要な SQL DB と Azure Storage リソースとも通信します。  Azure Storage 環境が通信する SQL と Storage リソースの一部は、App Service 環境と同じリージョン内に配置されますが、それ以外のリソースは、リモート Azure リージョンに配置されます。  その結果、App Service 環境が正常に機能するためには、インターネットへの発信接続が常に必要です。 

App Service 環境はサブネットにデプロイされるため、ネットワーク セキュリティ グループを使用してサブネットへの着信トラフィックを制御できます。  App Service Environment への着信トラフィックを制御する方法の詳細については、次の[記事][controllinginboundtraffic]を参照してください。

App Service Environment からの発信インターネット接続を許可する方法の詳細については、[Express Route][ExpressRoute] の操作に関する記事を参照してください。  記事で説明されている方法は、サイト間接続を操作する場合と強制トンネリングを使用する場合にも適用されます。

## <a name="outbound-network-addresses"></a>発信ネットワーク アドレス
App Service 環境で発信呼び出しを行うと、IP アドレスが常に発信呼び出しに関連付けられます。  使用される IP アドレスは、呼び出し先のエンドポイントが仮想ネットワーク トポロジの内部にあるか外部にあるかによって異なります。

呼び出し先のエンドポイントが仮想ネットワーク トポロジの **外部** にある場合、使用される発信アドレス (発信 NAT アドレス) は、App Service 環境のパブリック VIP になります。  このアドレスは、[プロパティ] ブレードの App Service 環境用のポータル ユーザー インターフェイスで確認できます。

![発信 IP アドレス][OutboundIPAddress]

このアドレスは、パブリック VIP だけを持つ ASE の場合、App Service 環境でアプリを作成した後、アプリのアドレスに対して *nslookup* を実行することでも判別できます。 結果の IP アドレスは、パブリック VIP であり、App Service 環境の発信 NAT アドレスでもあります。

呼び出し先のエンドポイントが仮想ネットワーク トポロジの **内部** にある場合、呼び出し元のアプリの発信アドレスは、アプリを実行している個々のコンピューティング リソースの内部 IP アドレスになります。  ただし、仮想ネットワークの内部 IP アドレスとアプリのマッピングは固定されていません。  アプリは複数のコンピューティング リソースの間を移動でき、App Service 環境内で使用できるコンピューティング リソースのプールは、スケーリング操作によって変更される可能性があります。

ただし、App Service 環境は常にサブネット内にあるため、アプリを実行するコンピューティング リソースの内部 IP アドレスは、常にサブネットの CIDR 内であることが保証されます。  その結果、きめ細かく調整された ACL またはネットワーク セキュリティ グループを使用して、仮想ネットワーク内の他のエンドポイントへのアクセスを保護する場合は、App Service 環境が含まれているサブネット範囲へのアクセスが許可される必要があります。

次の図に、これらの概念の詳細を示します。

![発信ネットワーク アドレス][OutboundNetworkAddresses]

上の図の説明です。

* App Service 環境のパブリック VIP は 192.23.1.2 であるため、それは "インターネット" エンドポイントを呼び出す際に使用される発信 IP アドレスです。
* App Service 環境が含まれるサブネットの CIDR 範囲は 10.0.1.0/26 です。  同じ仮想ネットワーク インフラストラクチャ内の他のエンドポイントは、アプリからの呼び出しが、このアドレスの範囲内のどこかから発信されているとみなします。

## <a name="calls-between-app-service-environments"></a>App Service 環境間の呼び出し
同じバーチャル ネットワーク内で、複数の App Service 環境をデプロイし、App Service 環境同士で発信呼び出しを行う場合、より複雑なシナリオとなるでしょう。  このような交差したタイプの App Service 環境間呼び出しは、”インターネット” 呼び出しとしても扱われます。

次の図に階層アーキテクチャの例を示します。1 つ目の App Service 環境のアプリ (たとえば、"玄関口" である Web アプリ) は 2 つ目の App Service 環境のアプリ (たとえば、インターネットからアクセスできないようにする内部のバックエンド API アプリ) を呼び出します。 

![App Service 環境間の呼び出し][CallsBetweenAppServiceEnvironments] 

上記の例では、App Service 環境 "ASE One" は、192.23.1.2 という発信 IP アドレスを使用します。  この App Service 環境で実行されているアプリが、同じ仮想ネットワーク内にある 2 つ目の App Service 環境 ("ASE Two") で実行されているアプリに発信呼び出しを行う場合、発信呼び出しは "インターネット" 呼び出しとして扱われます。  その結果、2 つ目の App Service 環境に到着するネットワーク トラフィックは、192.23.1.2 (つまり、1 つ目の App Service 環境のサブネット アドレス範囲ではない) から送信されているように表示されます。

異なる App Service 環境間での呼び出しは "インターネット" 呼び出しとして扱われるものの、両方の App Service 環境が同じ Azure リージョンに位置している場合は、ネットワーク トラフィックは同じリージョンの Azure ネットワークにとどまり、物理的にパブリック インターネット上に流出することはありません。  その結果、2 つ目の App Service 環境のサブネット上でネットワーク セキュリティ グループを使用して、1 つ目の App Service 環境 (発信 IP アドレスが 192.23.1.2) からの受信呼び出しのみを許可することができるため、App Service 環境間での安全な通信が確保されます。

## <a name="additional-links-and-information"></a>その他のリンクおよび情報
App Service 環境で使用される着信ポートと、ネットワーク セキュリティ グループを使用した着信トラフィック制御の詳細については、[ここ][controllinginboundtraffic]を参照してください。

App Service 環境への発信インターネット アクセスを許可するためにユーザーが定義したルートの使用の詳細については、この[記事][ExpressRoute]を参照してください。 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

