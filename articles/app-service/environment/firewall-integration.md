---
title: App Service Environment の送信トラフィックのロックダウン - Azure
description: Azure Firewall と統合して送信トラフィックをセキュリティで保護する方法について説明します
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: b57ac43b02e8630528e7ed3f77f51befa52ed45f
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498467"
---
# <a name="locking-down-an-app-service-environment"></a>App Service 環境をロックする

App Service Environment (ASE) が適切に動作するには、アクセスする必要がある外部の依存関係が複数あります。 ASE は、お客様の Azure Virtual Network (VNet) 内にあります。 お客様は、ASE の依存トラフィックを許可する必要があります。これは、自社の VNet からのすべての送信をロックしたいお客様にとって問題です。

ASE が持っている受信依存関係は複数あります。 受信管理トラフィックはファイアウォール デバイスを介して送信できません。 このトラフィックのソース アドレスは既知であり、「[App Service Environment の管理アドレス](https://docs.microsoft.com/azure/app-service/environment/management-addresses)」ドキュメントで公開されています。 その情報を使用してネットワーク セキュリティ グループ ルールを作成し、受信トラフィックをセキュリティで保護することができます。

ASE の送信依存関係は、ほぼすべて、背後に静的アドレスがない FQDN を使用して定義されています。 静的アドレスがないということは、ネットワーク セキュリティ グループ (NSG) を使用して ASE からの送信トラフィックをロックできないことを意味します。 アドレスは頻繁に変わるので、現在の解決策に基づいてルールを設定し、それを使用して NSG を作成することができません。 

送信アドレスをセキュリティで保護する解決策は、ドメイン名に基づいて送信トラフィックを制御できるファイアウォール デバイスの使用方法にあります。 Azure Firewall では、宛先の FQDN に基づいて送信 HTTP および HTTPS トラフィックを制限できます。  

## <a name="system-architecture"></a>システム アーキテクチャ

アウトバウンドのトラフィックがファイアウォール デバイスを経由するようにして ASE をデプロイするには、ASE サブネットのルートを変更する必要があります。 ルートは IP レベルで動作します。 ルートを慎重に定義しないと、別のアドレスからの TCP 応答トラフィックの発信が強制される可能性があります。 これは、非対称ルーティングと呼ばれ、TCP が破られます。

ASE へのインバウンドのトラフィックが、そのトラフィックが入ってきたのと同じ方法で応答できるようにルートが定義されている必要があります。 これは、インバウンドの管理要求にも、インバウンドのアプリケーション要求にも当てはまります

ASE との間のトラフィックは、次の規則に従っている必要があります

* Azure SQL、Storage、および Event Hub へのトラフィックは、ファイアウォール デバイスを使用する場合、サポートされません。 このトラフィックは、それらのサービスに直接送信される必要があります。 それを実行する方法は、それらの 3 つのサービスのサービス エンドポイントを構成することです。 
* インバウンドの管理トラフィックをその送信元から送り返すルート テーブル規則が定義されている必要があります。
* インバウンドのアプリケーション トラフィックをその送信元から送り返すルート テーブル規則が定義されている必要があります。 
* ASE を離れる他のすべてのトラフィックは、ルート テーブル規則を使用してファイアウォール デバイスに送信することができます。

![ASE と Azure Firewall の接続フロー][5]

## <a name="configuring-azure-firewall-with-your-ase"></a>ASE に合わせて Azure Firewall を構成する 

Azure Firewall を使用して既存の ASE からのエグレスをロックダウンする手順は次のとおりです。

1. ASE サブネット上の SQL、Storage、Event Hub へのサービス エンドポイントを有効にします。 これを行うには、ネットワーク ポータル、[サブネット] の順に移動し、[サービス エンドポイント] ドロップダウンから [Microsoft.EventHub]、[Microsoft.SQL]、[Microsoft.Storage] を選択します。 Azure SQL に対するサービス エンドポイントを有効にした場合、お使いのアプリの Azure SQL の依存関係もすべて、サービス エンドポイントで構成する必要があります。 

   ![サービス エンドポイントの選択][2]
  
1. ASE が存在する VNet に AzureFirewallSubnet という名前のサブネットを作成します。 「[Azure Firewall のドキュメント](https://docs.microsoft.com/azure/firewall/)」の指示に従って Azure Firewall を作成します。
1. Azure Firewall UI の [ルール]、[アプリケーション ルール コレクション] から、[アプリケーション ルール コレクションの追加] を選択します。 名前、優先度を指定し、[許可] を設定します。 [FQDN タグ] セクションで、名前を指定し、[ソース アドレス] を「*」に設定して、FQDN タグとして App Service Environment と Windows Update を選択します。 
   
   ![アプリケーション ルールの追加][1]
   
1. Azure Firewall UI の [ルール]、[ネットワーク ルール コレクション] から、[ネットワーク ルール コレクションの追加] を選択します。 名前、優先度を指定し、[許可] を設定します。 [ルール] セクションで、名前を指定し、 **[任意]** を選択して、[ソース アドレス] と [宛先アドレス] に「*」を設定し、ポートを「123」を設定します。 このルールにより、システムでは NTP を使用してクロック同期を実行できます。 システムのあらゆる問題をトリアージできるように、ポート 12000 に対しても同様に別のルールを作成します。

   ![NTP ネットワーク ルールの追加][3]

1. インターネットの次ホップがある [App Service Environment 管理アドレス]( https://docs.microsoft.com/azure/app-service/environment/management-addresses)から管理アドレスを使用してルート テーブルを作成します。 ルート テーブル エントリは、非対称ルーティングの問題を回避するために必要です。 インターネットの次ホップがある IP アドレスの依存関係に、以下に示す IP アドレスの依存関係のルートを追加します。 0\.0.0.0/0 のルート テーブルに、次ホップが Azure Firewall プライベート IP アドレスである仮想アプライアンス ルートを追加します。 

   ![ルート テーブルの作成][4]
   
1. 作成したルート テーブルを ASE サブネットに割り当てます。

#### <a name="deploying-your-ase-behind-a-firewall"></a>ファイアウォールの内側に ASE をデプロイする

ファイアウォールの内側に ASE をデプロイする手順は、Azure Firewall を使用して既存の ASE を構成する手順と同じです。ただし、例外として、ASE サブネットを作成してから前の手順に従う必要があります。 既存のサブネットに ASE を作成するには、[Resource Manager テンプレートを使用した ASE の作成](https://docs.microsoft.com/azure/app-service/environment/create-from-template)に関するドキュメントで説明されているように、Resource Manager テンプレートを使用する必要があります。

## <a name="application-traffic"></a>アプリケーション トラフィック 

上記の手順で、ASE が問題なく動作するようになります。 ただし、実際のアプリケーションのニーズに合わせて構成する必要があります。 Azure Firewall を使用して構成された ASE 内のアプリケーションには 2 つの問題があります。  

- アプリケーションの依存関係を Azure Firewall またはルート テーブルに追加する必要がある。 
- 非対称ルーティングの問題を回避するために、アプリケーションのトラフィック用にルートを作成する必要がある。

アプリケーションに依存関係がある場合は、それらを Azure Firewall に追加する必要があります。 その他すべてに関する HTTP/HTTPS トラフィックとネットワークのルールを許可するようにアプリケーション ルールを作成します。 

アプリケーション要求トラフィックの送信元であるアドレス範囲がわかっている場合は、そのアドレス範囲を ASE サブネットに割り当てられているルート テーブルに追加することができます。 アドレス範囲が大きい場合や指定されていない場合は、Application Gateway などのネットワーク アプライアンスを使用して、ルート テーブルに追加するアドレスを 1 つ指定することができます。 ILB ASE に合わせて Application Gateway を構成する方法の詳細については、[ILB ASE を Application Gateway と統合する方法](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)に関するページを参照してください。

この Application Gateway の使用は、システムの構成方法の一例にすぎません。 このパスに従わなかった場合は、ASE サブネット ルート テーブルへのルートの追加が必要になります。その結果、Application Gateway に送信された応答トラフィックはそこに直接送られます。 

## <a name="logging"></a>ログの記録 

Azure Firewall は、Azure Storage、Event Hub、または Azure Monitor ログにログを送信できます。 お使いのアプリをサポート対象の送信先と統合するには、Azure Firewall ポータルの [診断ログ] に移動し、目的の送信先のログを有効にします。 Azure Monitor ログと統合すると、Azure Firewall に送信されたすべてのトラフィックのログを確認できます。 拒否されているトラフィックを表示するには、Log Analytics ワークスペース ポータルの [ログ] を開き、次のようなクエリを入力します 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Azure Firewall を Azure Monitor ログと統合すると、アプリケーションのすべての依存関係がわからないときに初めてアプリケーションを動作させる場合に非常に役立ちます。 Azure Monitor ログについて詳しくは、「[Azure Monitor でログ データを分析する](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)」をご覧ください
 
## <a name="dependencies"></a>依存関係

次の情報が必要なのは、Azure Firewall 以外のファイアウォール アプライアンスを構成する場合のみです。 

- サービス エンドポイント対応のサービスは、サービス エンドポイントを使用して構成する必要があります。
- IP アドレスの依存関係が HTTP/S 以外のトラフィック (TCP トラフィックと UDP トラフィックの両方) に対応しています
- FQDN HTTP/HTTPS エンドポイントは、ファイアウォール デバイスに配置することができます。
- ワイルドカード HTTP/HTTPS エンドポイントは、いくつかの修飾子に基づき、ASE によって異なる依存関係になります。 
- Linux の依存関係は、ASE に Linux アプリをデプロイする場合にのみ考慮する必要があります。 Linux アプリを ASE に展開していない場合は、これらのアドレスをファイアウォールに追加する必要はありません。 

#### <a name="service-endpoint-capable-dependencies"></a>サービス エンドポイント対応の依存関係 

| エンドポイント |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP アドレスの依存関係

| エンドポイント | 詳細 |
|----------| ----- |
| \*:123 | NTP クロック チェック。 トラフィックは、ポート 123 上の複数のエンドポイントでチェックされます |
| \*:12000 | このポートは、一部のシステム監視に使用されています。 ブロックされている場合、一部の問題はトリアージが難しくなりますが、ASE は引き続き動作します |
| 40.77.24.27:80 | ASE の問題の監視とアラート通知に必要 |
| 40.77.24.27:443 | ASE の問題の監視とアラート通知に必要 |
| 13.90.249.229:80 | ASE の問題の監視とアラート通知に必要 |
| 13.90.249.229:443 | ASE の問題の監視とアラート通知に必要 |
| 104.45.230.69:80 | ASE の問題の監視とアラート通知に必要 |
| 104.45.230.69:443 | ASE の問題の監視とアラート通知に必要 |
| 13.82.184.151:80 | ASE の問題の監視とアラート通知に必要 |
| 13.82.184.151:443 | ASE の問題の監視とアラート通知に必要 |

Azure Firewall を使用すると、FQDN タグで構成された以下のものすべてが自動的に取得されます。 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS の依存関係 

| エンドポイント |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Wildcard HTTP/HTTPS dependencies 

| エンドポイント |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |

#### <a name="linux-dependencies"></a>Linux の依存関係 

| エンドポイント |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
