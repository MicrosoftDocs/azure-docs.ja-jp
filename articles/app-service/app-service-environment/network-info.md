---
title: "Azure App Service Environment でのネットワークの考慮事項"
description: "ASE ネットワーク トラフィックと、ASE を使用して NSG と UDR を設定する方法について説明します"
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
ms.date: 05/08/2017
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 3be0d7a202ff53f5532fd7169a50a04cfaf88832
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="networking-considerations-for-an-app-service-environment"></a>App Service Environment のためのネットワークの考慮事項 #

## <a name="overview"></a>概要 ##

 Azure [App Service Environment][Intro] は、Azure 仮想ネットワーク (VNet) 内のサブネットへの Azure App Service のデプロイです。 App Service Environment (ASE) のデプロイの種類には、次の 2 つがあります。

- **外部 ASE**: ASE でホストされたアプリをインターネット アクセス可能な IP アドレスで公開します。 詳細については、[外部 ASE の作成][MakeExternalASE]に関するページを参照してください。
- **ILB ASE**: ASE でホストされたアプリを VNet 内部の IP アドレスで公開します。 内部エンドポイントは内部ロード バランサー (ILB) であるため、ILB ASE と呼ばれています。 詳細については、[ILB ASE の作成と使用][MakeILBASE]に関するページを参照してください。

現在、App Service Environment には ASEv1 と ASEv2 の 2 つのバージョンがあります。 ASEv1 については、「[App Service Environment v1 の概要][ASEv1Intro]」をご覧ください。 ASEv1 は、クラシックまたは Resource Manager VNet にデプロイできます。 ASEv2 は、Resource Manager VNet にのみデプロイできます。

インターネットにアクセスする ASE からのすべての呼び出しは、ASE に割り当てられた VIP を経由して VNet から出ます。 この VIP のパブリック IP は、インターネットにアクセスする ASE からのすべての呼び出しの送信元 IP になります。 ASE 内のアプリが VNet 内のリソースまたは VPN 全体にわたるリソースへの呼び出しを行う場合、ソース IP は、その ASE によって使用されるサブネット内の IP のいずれかです。 ASE は VNet 内にあるため、追加の構成をしなくても、VNet 内のリソースにもアクセスできます。 VNet がオンプレミスのネットワークに接続されている場合、ASE 内のアプリもそこにあるリソースにアクセスできます。 ASE またはアプリをそれ以上構成する必要はありません。

![外部 ASE][1] 

外部 ASE が存在する場合は、パブリック VIP も、ASE アプリが次のために解決されるエンドポイントです。

* HTTP/S 
* FTP/S 
* Web デプロイ。
* リモート デバッグ

![ILB ASE][2]

ILB ASE が存在する場合、ILB の IP アドレスは、HTTP/S、FTP/S、Web デプロイ、およびリモート デバッグのためのエンドポイントです。

通常のアプリのアクセス ポートは次のとおりです。

| 用途 | ソース | ターゲット |
|----------|---------|-------------|
|  HTTP/HTTPS  | ユーザーが構成可能 |  80、443 |
|  FTP/FTPS    | ユーザーが構成可能 |  21、990、10001-10020 |
|  Visual Studio リモート デバッグ  |  ユーザーが構成可能 |  4016、4018、4020、4022 |

これは、ユーザーが外部 ASE または ILB ASE にいる場合に当てはまります。 外部 ASE にいる場合は、パブリック VIP でこれらのポートにヒットします。 ILB ASE にいる場合は、ILB でこれらのポートにヒットします。 ポート 443 をロックダウンすると、ポータルで公開されている一部の機能に影響が出る場合があります。 詳細については、「[ポータルの依存関係](#portaldep)」を参照してください。

## <a name="ase-dependencies"></a>ASE の依存関係 ##

ASE の着信アクセスの依存関係は次のとおりです。

| 用途 | ソース | ターゲット |
|-----|------|----|
| 管理 | App Service の管理アドレス | ASE サブネット: 454、455 |
|  ASE 内部通信 | ASE サブネット: すべてのポート | ASE サブネット: すべてのポート
|  Azure Load Balancer の着信を許可 | Azure Load Balancer | ASE サブネット: すべてのポート
|  アプリに割り当てられた IP アドレス | アプリに割り当てられたアドレス | ASE サブネット: すべてのポート

着信トラフィックは、システムの監視に加え、ASE のコマンドと制御を提供します。 このトラフィックのソース IP は、[ASE 管理アドレス][ ASEManagement]に関するドキュメントで一覧を確認できます。 ネットワーク セキュリティ構成では、ポート 454 および 455 上のすべての IP からのアクセスを許可する必要があります。

ASE サブネット内には、内部コンポーネントの通信に使用されるポートが多数あり、それらのポートは変更可能です。  そのためには、ASE サブネットのすべてのポートに、ASE サブネットからアクセスできる必要があります。 

Azure Load Balancer と ASE サブネット間の通信では、最低限開く必要のあるポートは 454、455、16001 です。 ロード バランサーと ASE 間のキープ アライブ トラフィックでは、ポート 16001 が使用されます。 ILB ASE を使用している場合は、ポート 454、455、16001 へのトラフィックのみをロックダウンできます。  外部 ASE を使用している場合は、通常のアプリのアクセス ポートについて考慮する必要があります。  アプリに割り当てられたアドレスを使用している場合は、それをすべてのポートに開く必要があります。  アドレスが特定のアプリに割り当てられている場合は、HTTP および HTTPS トラフィックを ASE に送信する場合にロード バランサーが使用するポートは事前にわかりません。

アプリに割り当てられた IP アドレスを使用している場合は、ご使用のアプリに割り当てられている IP から ASE サブネットへのトラフィックを許可する必要があります。

発信アクセスについては、ASE は複数の外部システムに依存します。 これらのシステム依存関係は DNS 名で定義され、固定された一連の IP アドレスにはマップされません。 そのため、ASE には、ASE サブネットからさまざまなポートにまたがるすべての外部 IP への送信アクセスが必要です。 ASE には、次の発信の依存関係があります。

| 用途 | ソース | ターゲット |
|-----|------|----|
| Azure Storage (Azure Storage) | ASE サブネット | table.core.windows.net、blob.core.windows.net、queue.core.windows.net、file.core.windows.net: 80、443、445 (445 は ASEv1 にのみ必要です。) |
| Azure SQL Database | ASE サブネット | database.windows.net: 1433、11000-11999、14000-14999 (詳細については、[SQL Database V12 ポートの使用](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)に関するページを参照。)|
| Azure の管理 | ASE サブネット | management.core.windows.net、management.azure.com: 443 
| SSL 証明書の検証 |  ASE サブネット            |  ocsp.msocsp.com、mscrl.microsoft.com、crl.microsoft.com: 443
| Azure Active Directory        | ASE サブネット            |  インターネット: 443
| App Service の管理        | ASE サブネット            |  インターネット: 443
| Azure DNS                     | ASE サブネット            |  インターネット: 53
| ASE 内部通信    | ASE サブネット: すべてのポート |  ASE サブネット: すべてのポート

ASE がこれらの依存関係へのアクセスを失うと、ASE は停止します。 それがある程度長く続くと、ASE は中断されます。

### <a name="customer-dns"></a>顧客 DNS ###

VNet に顧客が定義した DNS サーバーが構成されている場合、テナント ワークロードはそれを使用します。 ASE は、管理目的で Azure DNS と引き続き通信する必要があります。 

VNet で VPN のもう一方の側に顧客 DNS が構成されている場合は、ASE が含まれるサブネットからその DNS サーバーに到達できる必要があります。

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>ポータルの依存関係 ##

ASE の機能的な依存関係に加えて、ポータルのエクスペリエンスに関連したいくつかの追加項目があります。 Azure Portal の一部の機能は、_SCM サイト_への直接アクセスに依存しています。 Azure App Service 内のどのアプリにも 2 つの URL が存在します。 1 つ目の URL はアプリにアクセスするためのものです。 2 つ目の URL は SCM サイト (_Kudu コンソール_とも呼ばれます) にアクセスするためのものです。 SCM サイトを使用する機能には、次のものが含まれます。

-   Web ジョブ
-   関数
-   ログ ストリーミング
-   Kudu
-   拡張機能
-   プロセス エクスプローラー
-   コンソール

ILB ASE を使用する場合、SCM サイトは VNet の外部からインターネット アクセスできません。 アプリが ILB ASE でホストされている場合、一部の機能はポータルで動作しません。  

SCM サイトに依存するこれらの機能の多くは、Kudu コンソールでも直接使用できます。 ポータルを使用するのではなく、直接そこに接続できます。 アプリが ILB ASE でホストされている場合は、自分の公開資格情報を使用してサインインします。 ILB ASE でホストされているアプリの SCM サイトにアクセスする URL には、次の形式があります。 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

ILB ASE がドメイン名 *contoso.net* であり、アプリ名が *testapp* である場合、そのアプリには *testapp.contoso.net* で到達します。 それで移動する SCM サイトには *testapp.scm.contoso.net* で到達します。

## <a name="functions-and-web-jobs"></a>関数と Web ジョブ ##

関数と Web ジョブはどちらも SCM サイトによって異なりますが、アプリが ILB ASE 内にある場合でも、ブラウザーで SCM サイトに到達できる限り、ポータルでの使用がサポートされます。  ILB ASE で自己署名証明書を使用している場合は、ブラウザーがその証明書を信頼するようにできる必要があります。  IE と Edge の場合には、証明書がコンピューターのトラスト ストア内に存在している必要があることになります。  Chrome を使用している場合は、ブラウザーで事前に証明書を受け入れたことになります。SCM サイトに直接到達することによるものと考えられます。  最も良い方法として、ブラウザーの信頼チェーンにある商用証明書を使用することをお勧めします。  

## <a name="ase-ip-addresses"></a>ASE IP アドレス ##

ASE で認識されている必要のある IP アドレスがいくつかあります。 次に例を示します。

- **パブリック受信 IP アドレス**: 外部 ASE でのアプリ トラフィック、および外部 ASE と ILB ASE の両方での管理トラフィックに使用されます。
- **送信パブリック IP**: VPN 経由でルーティングされない、VNet を離れる ASE からの送信接続の "発信元" IP として使用されます。
- **ILB IP アドレス**: ILB ASE を使用する場合。
- **アプリに割り当てられた IP ベースの SSL アドレス**: 外部 ASE でのみ、かつ IP ベースの SSL が構成されている場合にのみ可能です。

これらすべての IP アドレスは、ASE UI から Azure Portal の ASEv2 で簡単に確認できます。 ILB ASE が存在する場合は、ILB の IP が一覧表示されます。

![IP アドレス][3]

### <a name="app-assigned-ip-addresses"></a>アプリに割り当てられた IP アドレス ###

外部 ASE では、個々のアプリに IP アドレスを割り当てることができます。 これは ILB ASE では実行できません。 独自の IP アドレスを持つようにアプリを構成する方法の詳細については、「[既存のカスタム SSL 証明書の Azure Web Apps へのバインド](../../app-service-web/app-service-web-tutorial-custom-ssl.md)」をご覧ください。

アプリに独自の IP ベースの SSL アドレスがある場合、ASE はその IP アドレスにマップするために 2 つのポートを予約します。 1 つのポートは HTTP トラフィック用であり、もう 1 つのポートは HTTPS 用です。 これらのポートは、ASE UI の [IP アドレス] セクションに表示されます。 トラフィックは、VIP からこれらのポートに到達できる必要があります。そうでないと、アプリにアクセスできません。 この要件は、ネットワーク セキュリティ グループ (NSG) を構成するときに思い出すことが重要です。

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ ##

[ネットワーク セキュリティ グループ][NSGs]は、VNet 内のネットワーク アクセスを制御する機能を提供します。 ポータルを使用する場合は、すべてを拒否するための暗黙的な拒否規則が、最も低い優先順位に存在します。 自分で構築したものは許可規則になります。

ASE では、ASE 自体をホストするために使用する VM にアクセスすることはできません。 これらは、Microsoft によって管理されるサブスクリプションに含まれています。 ASE 上のアプリへのアクセスを制限する場合は、ASE サブネット上で NSG を設定します。 そのときは、ASE の依存関係に十分注意してください。 いずれかの依存関係をブロックすると、ASE は動作を停止します。

NSG は、Azure Portal または PowerShell を使用して構成できます。 ここにある情報は、Azure Portal を示しています。 ポータルで NSG を **[ネットワーク]** の下の最上位のリソースとして作成して管理します。

受信と送信の要件を考慮すると、NSG は、この例に示されている NSG のようになります。 VNet アドレスの範囲は _192.168.250.0/16_ であり、ASE が存在するサブネットは _192.168.251.128/25_ です。

この例の一覧の先頭には、ASE が機能するための最初の 2 つの受信要件が示されています。 これらは ASE 管理を有効にし、ASE に自身との通信を許可します。 その他のエントリはすべてテナント構成可能であり、ASE でホストされたアプリケーションへのネットワーク アクセスを制御できます。 

![受信セキュリティ規則][4]

既定の規則を使用すると、VNet 内の IP は ASE サブネットにアクセスできます。 別の既定の規則を使用すると、ロード バランサー (パブリック VIP とも呼ばれます) は ASE と通信できます。 既定の規則を確認するには、**[追加]** アイコンの横にある **[既定の規則]** を選択します。 NSG 規則が表示された後に他のすべてを拒否する規則を設定すると、VIP と ASE の間のトラフィックが妨げられます。 VNet の内部からトラフィックが来ないようにするには、受信を許可するための独自の規則を追加します。 **[任意]** の宛先と **\*** のポート範囲が含まれた AzureLoadBalancer に等しいソースを使用します。 NSG 規則は ASE サブネットに適用されるため、宛先を限定する必要はありません。

アプリに IP アドレスを割り当てた場合は、必ずポートを開いたままにしてください。 ポートを確認するには、**[App Service 環境]** > **[IP アドレス]** を選択します。  

次の送信規則に示されている項目は、最後の項目を除き、すべて必要です。 これらは、この記事の前の方で説明した ASE の依存関係へのネットワーク アクセスを有効にします。 それらのいずれかをブロックすると、ASE は動作を停止します。 リストの最後の項目は、ASE が VNet 内の他のリソースと通信できるようにします。

![送信セキュリティ規則][5]

NSG が定義されたら、それを ASE が存在するサブネットに割り当てます。 ASE VNet またはサブネットを覚えていない場合は、ASE 管理ポータルから確認できます。 NSG をサブネットに割り当てるには、サブネット UI に移動して、NSG を選択します。

## <a name="routes"></a>ルート ##

ルートで問題が発生するのは通常、Azure ExpressRoute で VNet を構成する場合です。 VNet 内のルートの種類には、次の 3 つがあります。

-   システム ルート
-   BGP のルート
-   ユーザー定義ルート (UDR)

BGP ルートはシステム ルートを上書きします。 UDR は、BGP ルートをオーバーライドします。 Azure 仮想ネットワーク内のルートの詳細については、[ユーザー定義ルートの概要][UDRs]に関するページをご覧ください。

ASE がシステムを管理するために使用する Azure SQL データベースには、ファイアウォールがあります。 これには、ASE パブリック VIP から発信するための通信が必要です。 ASE から SQL データベースへの接続は、ExpressRoute 接続経由で別の IP アドレスに送信される場合は拒否されます。

受信管理要求への応答が ExpressRoute 経由で送信される場合、その応答アドレスは元の宛先とは異なります。 この不一致により、TCP 通信が停止します。

VNet が ExpressRoute で構成されている間も ASE を機能させるために最も簡単な方法は次のとおりです。

-   _0.0.0.0/0_ をアドバタイズするように ExpressRoute を構成します。 既定では、すべての送信トラフィックをオンプレミスに強制的にトンネリングします。
-   UDR を作成します。 それを ASE が含まれるサブネットに _[0.0.0.0/0]_ のアドレス プレフィックスと _[インターネット]_ の次ホップの種類を指定して適用します。

これらの 2 つの変更を行った場合、ASE サブネットから発信されたインターネット宛てのトラフィックは ExpressRoute を強制的に経由せず、ASE は機能します。 

> [!IMPORTANT]
> UDR に定義されているルートは、ExpressRoute 構成でアドバタイズされたどのルートよりも優先されるように、詳細にする必要があります。 前の例では、0.0.0.0/0 という広いアドレス範囲を使用しています。 これは、より具体的なアドレス範囲を使用するルート アドバタイズによって誤って上書きされる可能性があります。
>
> ASE は、ルートをパブリックピアリング パスからプライベートピアリング パスにクロスアドバタイズする ExpressRoute 構成ではサポートされません。 パブリック ピアリングが構成された ExpressRoute 構成は、Microsoft からルート アドバタイズを受信します。 これらのアドバタイズには、Microsoft Azure の一連の広い IP アドレス範囲が含まれています。 これらのアドレス範囲がプライベートピアリング パスでクロスアドバタイズされた場合、ASE のサブネットからの送信ネットワーク パケットはすべて、顧客のオンプレミスのネットワーク インフラストラクチャに強制的にトンネリングされます。 現在、このネットワーク フローは ASE でサポートされていません。 この問題の 1 つの解決策として、パブリックピアリング パスからプライベートピアリング パスへのクロスアドバタイズ ルートの停止があります。

UDR を作成するには、次の手順を実行します

1. Azure Portal にアクセスします。 **[ネットワーク]** > **[ルート テーブル]** を選択します。

2. VNet と同じリージョン内に新しいルート テーブルを作成します。

3. ルート テーブル UI 内から、**[ルート]** > **[追加]** を選択します。

4. **[次ホップの種類]** を **[インターネット]** に、**[アドレス プレフィックス]** を **[0.0.0.0/0]** に設定します。 [ **保存**] を選択します。

    次のような内容が表示されます。

    ![機能ルート][6]

5. 新しいルート テーブルを作成したら、ASE が含まれるサブネットに移動します。 ポータルで一覧からルート テーブルを選択します。 変更を保存すると、指定した NSG とルートがサブネットと共に表示されます。

    ![NSG とルート][7]

### <a name="deploy-into-existing-azure-virtual-networks-that-are-integrated-with-expressroute"></a>ExpressRoute と統合された既存の Azure 仮想ネットワークにデプロイする ###

ASE を ExpressRoute と統合された VNet にデプロイするには、ASE をデプロイするサブネットを事前に構成します。 次に、Resource Manager テンプレートを使用してデプロイします。 VNet で ExpressRoute が既に構成されている ASE を作成するには、次の手順を実行します。

- ASE をホストするサブネットを作成します。

    > [!NOTE]
    > サブネット内には ASE の他に何も存在できません。 将来の拡張を考慮に入れたアドレス空間を選択するようにしてください。 この設定を後で変更することはできません。 推奨されるサイズは、128 のアドレスを持つ `/25` です。

- 先に説明したように UDR (ルート テーブルなど) を作成し、それをサブネット上に設定します。
- [Resource Manager テンプレートを使用した ASE の作成][MakeASEfromTemplate]に関するページに従って、Resource Manager テンプレートを使用して ASE を作成します。

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png

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
[ASEManagement]: ./management-addresses.md

