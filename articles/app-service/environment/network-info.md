---
title: App Service Environment でのネットワークの考慮事項 - Azure
description: ASE ネットワーク トラフィックと、ASE を使用して NSG と UDR を設定する方法について説明します
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
ms.date: 05/31/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3f80f3c6be747cf84aa9d8b2c386c0568a7511ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069389"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>App Service Environment のネットワークの考慮事項 #

## <a name="overview"></a>概要 ##

 Azure [App Service Environment][Intro] は、Azure 仮想ネットワーク (VNet) 内のサブネットへの Azure App Service のデプロイです。 App Service Environment (ASE) のデプロイの種類には、次の 2 つがあります。

- **外部 ASE**:ASE でホストされたアプリをインターネット アクセス可能な IP アドレスで公開します。 詳細については、[外部 ASE の作成][MakeExternalASE]に関するページを参照してください。
- **ILB ASE**:ASE でホストされたアプリを VNet 内部の IP アドレスで公開します。 内部エンドポイントは内部ロード バランサー (ILB) であるため、ILB ASE と呼ばれています。 詳細については、[ILB ASE の作成と使用][MakeILBASE]に関するページを参照してください。

ASE、外部、および ILB のすべてに、受信管理トラフィックに使用され、ASE からインターネットに電話をかけるときの送信元アドレスとして使用されるパブリック VIP があります。 インターネットにアクセスする ASE からの呼び出しは、ASE に割り当てられた VIP を経由して VNet から出ます。 この VIP のパブリック IP は、インターネットにアクセスする ASE からのすべての呼び出しのための送信元 IP になります。 ASE 内のアプリが VNet 内のリソースまたは VPN 全体にわたるリソースへの呼び出しを行う場合、ソース IP は、その ASE によって使用されるサブネット内の IP のいずれかです。 ASE は VNet 内にあるため、追加の構成をしなくても、VNet 内のリソースにもアクセスできます。 VNet がオンプレミスのネットワークに接続されている場合、追加の構成なしで ASE 内のアプリもそこにあるリソースにアクセスできます。

![外部 ASE][1] 

外部 ASE が存在する場合は、パブリック VIP も、ASE アプリが次のために解決されるエンドポイントです。

* HTTP/S 
* FTP/S
* Web デプロイ
* リモート デバッグ

![ILB ASE][2]

ILB ASE が存在する場合、ILB アドレスのアドレスは、HTTP/S、FTP/S、Web デプロイ、およびリモート デバッグのためのエンドポイントです。

## <a name="ase-subnet-size"></a>ASE サブネットのサイズ ##

ASE をデプロイした後は、ASE をホストするために使用されるサブネットのサイズを変更することはできません。  ASE では、インフラストラクチャのロールごと、および独立した App Service プラン インスタンスごとにアドレスが使用されます。  さらに、作成されたすべてのサブネットに対して Azure ネットワークが使用するアドレスが 5 つあります。  App Service プランを持たない ASE では、アプリを作成する前の段階で 12 個のアドレスが使用されます。  ILB ASE の場合は、その ASE にアプリを作成する前の段階で 13 個のアドレスが使用されます。 ご利用の ASE をスケールアウトすると、インフラストラクチャ ロールが App Service プラン インスタンスの 15 および 20 の倍数ごとに追加されます。

   > [!NOTE]
   > サブネット内には ASE の他に何も存在できません。 将来の拡張を考慮に入れたアドレス空間を選択するようにしてください。 この設定を後で変更することはできません。 推奨されるサイズは、256 のアドレスを持つ `/24` です。

スケールアップまたはスケールダウンすると、適切なサイズの新しいロールが追加されてから、ワークロードが現在のサイズからターゲット サイズに移行されます。 元の VM は、ワークロードが移行された後にのみ削除されました。 100 ASP インスタンスの ASE がある場合、VM の数を 2 倍にする必要がある期間があることになります。  この理由から、必要になる可能性がある変更に対応するために、'/24' を使用することをお勧めします。  

## <a name="ase-dependencies"></a>ASE の依存関係 ##

### <a name="ase-inbound-dependencies"></a>ASE の受信依存関係 ###

ASE を動作させるためだけに、ASE では次のポートが開いている必要があります。

| 用途 | ソース | ターゲット |
|-----|------|----|
| 管理 | App Service の管理アドレス | ASE サブネット:454、455 |
|  ASE 内部通信 | ASE サブネット:すべてのポート | ASE サブネット:すべてのポート
|  Azure Load Balancer の着信を許可 | Azure Load Balancer | ASE サブネット:16001

ポート スキャンで、開いていると表示される可能性があるポートが他に 2 つあります。7654 と 1221 です。 それらは、IP アドレスのみで応答します。 必要に応じてそれらをブロックすることもできます。 

受信管理トラフィックでは、システムの監視に加え、ASE のコマンドと制御が提供されます。 このトラフィックのソース アドレスは、[ASE 管理アドレス][ASEManagement]に関するドキュメントにリストされています。 ネットワーク セキュリティ構成では、ポート 454 および 455 上の ASE 管理アドレスからのアクセスを許可する必要があります。 これらのアドレスからのアクセスをブロックすると、ASE が正常でなくなり、中断されます。 ポート 454 と 455 に入ってくる TCP トラフィックは同じ VIP から出ていく必要があります。そうしないと、非対称ルーティングの問題が発生します。 

ASE サブネット内には、内部コンポーネントの通信に使用されるポートが多数あり、それらのポートは変更可能です。 そのためには、ASE サブネットのすべてのポートに、ASE サブネットからアクセスできる必要があります。 

Azure Load Balancer と ASE サブネット間の通信では、最低限開く必要のあるポートは 454、455、16001 です。 ロード バランサーと ASE 間のキープ アライブ トラフィックでは、ポート 16001 が使用されます。 ILB ASE を使用している場合は、ポート 454、455、16001 へのトラフィックのみをロックダウンできます。  外部 ASE を使用している場合は、通常のアプリのアクセス ポートについて考慮する必要があります。  

注意が必要なその他のポートは、アプリケーション ポートです。

| 用途 | Port |
|----------|-------------|
|  HTTP/HTTPS  | 80、443 |
|  FTP/FTPS    | 21、990、10001-10020 |
|  Visual Studio リモート デバッグ  |  4020、4022、4024 |
|  Web 配置サービス | 8172 |

アプリケーション ポートをブロックしても、ASE は引き続き機能できますが、アプリは機能しない可能性があります。  アプリ割り当ての IP アドレスを外部 ASE で使用している場合は、アプリに割り当てられた IP から、[ASE portal] > [IP アドレス] ページに表示される、ポート上の ASE サブネットへのトラフィックを許可する必要があります。

### <a name="ase-outbound-dependencies"></a>ASE の送信依存関係 ###

発信アクセスについては、ASE は複数の外部システムに依存します。 これらのシステム依存関係の多くは DNS 名で定義され、固定された一連の IP アドレスにはマップされません。 そのため、ASE には、ASE サブネットからさまざまなポートにまたがるすべての外部 IP への送信アクセスが必要です。 

ASE は、次のポートでインターネット アクセス可能なアドレスと通信します。

| 用途 | Port |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| 8CRL、Windows 更新プログラム、Linux の依存関係、Azure サービス | 80/443 |
| Azure SQL | 1433 | 
| 監視 | 12000 |

送信依存関係は、[App Service Environment の送信トラフィックのロック](./firewall-integration.md)について説明しているドキュメントに一覧表示されています。 ASE でその依存関係へのアクセスが失われた場合、動作は停止します。 それがある程度長く続くと、ASE は中断されます。 

### <a name="customer-dns"></a>顧客 DNS ###

VNet に顧客が定義した DNS サーバーが構成されている場合、テナント ワークロードはそれを使用します。 ASE は管理目的で Azure DNS を使用します。 顧客が選択した DNS サーバーで VNet が構成される場合は、ASE が含まれるサブネットからその DNS サーバーに到達できる必要があります。

Web アプリから DNS の解決をテストする場合は、コンソール コマンドの *nameresolver* を使用できます。 ご利用のアプリ用の scm サイトにあるデバッグ ウィンドウに移動するか、ポータルのアプリに移動してコンソールを選択します。 シェル プロンプトから、検索する DNS 名と共に *nameresolver* コマンドを実行できます。 返される結果は、同じ検索を行ったときにアプリで得られる内容と同じです。 nslookup を使用する場合は、代わりに Azure DNS を使って検索します。

ご利用の ASE が含まれる VNet の DNS 設定を変更する場合は、その ASE を再起動する必要があります。 ASE を再起動しないようにする場合は、ASE を作成する前に、ご利用の VNet に対して DNS 設定を構成することを強くお勧めします。  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>ポータルの依存関係 ##

ASE の機能的な依存関係に加えて、ポータルのエクスペリエンスに関連したいくつかの追加項目があります。 Azure Portal の一部の機能は、_SCM サイト_ への直接アクセスに依存しています。 Azure App Service 内のどのアプリにも 2 つの URL が存在します。 1 つ目の URL はアプリにアクセスするためのものです。 2 つ目の URL は SCM サイト (_Kudu コンソール_ とも呼ばれます) にアクセスするためのものです。 SCM サイトを使用する機能には、次のものが含まれます。

-   Web ジョブ
-   Functions
-   ログ ストリーミング
-   Kudu
-   Extensions
-   プロセス エクスプローラー
-   コンソール

ILB ASE を使用する場合、SCM サイトは VNet の外部からアクセスできません。 一部の機能は、アプリの SCM サイトへのアクセスが必要なため、アプリ ポータルからは機能しません。 ポータルを使用するのではなく、SCM サイトに直接接続できます。 

ILB ASE がドメイン名 *contoso.appserviceenvironnment.net* であり、アプリ名が *testapp* である場合、そのアプリには *testapp.contoso.appserviceenvironment.net* で到達します。 それで移動する SCM サイトには *testapp.scm.contoso.appserviceenvironment.net* で到達します。

## <a name="ase-ip-addresses"></a>ASE IP アドレス ##

ASE で認識されている必要のある IP アドレスがいくつかあります。 次に例を示します。

- **パブリック受信 IP アドレス**:外部 ASE でのアプリ トラフィック、および外部 ASE と ILB ASE の両方での管理トラフィックに使用されます。
- **送信パブリック IP**:VPN 経由でルーティングされない、VNet を離れる ASE からの送信接続の "発信元" IP として使用されます。
- **ILB IP アドレス**:ILB IP アドレスは ILB ASE にのみ存在します。
- **アプリに割り当てられた IP ベースの SSL アドレス**:外部 ASE でのみ、かつ IP ベースの SSL が構成されている場合にのみ可能です。

これらすべての IP アドレスは、ASE の UI から Azure portal に表示されます。 ILB ASE が存在する場合は、ILB の IP が一覧表示されます。

   > [!NOTE]
   > ASE が稼働している限り、これらの IP アドレスは変更されません。  ASE が中断され、復元されると、ASE で使用されるアドレスが変更されます。 通常は、受信管理アクセスをブロックしたり、ASE 依存関係へのアクセスをブロックした場合に、ASE が中断されます。 

![IP アドレス][3]

### <a name="app-assigned-ip-addresses"></a>アプリに割り当てられた IP アドレス ###

外部 ASE では、個々のアプリに IP アドレスを割り当てることができます。 これは ILB ASE では実行できません。 独自の IP アドレスを持つようにアプリを構成する方法の詳細については、「[既存のカスタム SSL 証明書の Azure App Service へのバインド](../app-service-web-tutorial-custom-ssl.md)」をご覧ください。

アプリに独自の IP ベースの SSL アドレスがある場合、ASE はその IP アドレスにマップするために 2 つのポートを予約します。 1 つのポートは HTTP トラフィック用であり、もう 1 つのポートは HTTPS 用です。 これらのポートは、ASE UI の [IP アドレス] セクションに表示されます。 トラフィックは、VIP からこれらのポートに到達できる必要があります。そうでないと、アプリにアクセスできません。 この要件は、ネットワーク セキュリティ グループ (NSG) を構成するときに思い出すことが重要です。

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ ##

[ネットワーク セキュリティ グループ][NSGs]は、VNet 内のネットワーク アクセスを制御する機能を提供します。 ポータルを使用する場合は、すべてを拒否するための暗黙的な拒否規則が、最も低い優先順位に存在します。 自分で構築したものは許可規則になります。

ASE では、ASE 自体をホストするために使用する VM にアクセスすることはできません。 これらは、Microsoft によって管理されるサブスクリプションに含まれています。 ASE 上のアプリへのアクセスを制限する場合は、ASE サブネット上で NSG を設定します。 そのときは、ASE の依存関係に十分注意してください。 いずれかの依存関係をブロックすると、ASE は動作を停止します。

NSG は、Azure Portal または PowerShell を使用して構成できます。 ここにある情報は、Azure Portal を示しています。 ポータルで NSG を **[ネットワーク]** の下の最上位のリソースとして作成して管理します。

ASE が機能するために NSG に必要なエントリは、トラフィックを許可することです。

**受信**
* ポート 454、455 で IP サービス タグ AppServiceManagement から
* ポート 16001 でロード バランサーから
* すべてのポートで ASE サブネットから ASE サブネットへ

**Outbound**
* ポート 123 ですべての IP へ
* ポート 80、443 ですべての IP へ
* ポート 1433 で IP サービス タグの AzureSQL へ
* ポート 12000 ですべての IP へ
* すべてのポートで ASE サブネットへ

DNS へのトラフィックは NSG 規則の影響を受けないので、DNS ポートを追加する必要はありません。 これらのポートには、アプリを正常に使用するために必要なポートは含まれていません。 通常のアプリのアクセス ポートは次のとおりです。

| 用途 | Port |
|----------|-------------|
|  HTTP/HTTPS  | 80、443 |
|  FTP/FTPS    | 21、990、10001-10020 |
|  Visual Studio リモート デバッグ  |  4020、4022、4024 |
|  Web 配置サービス | 8172 |

受信と送信の要件を考慮すると、NSG は、この例に示されている NSG のようになります。 

![受信セキュリティ規則][4]

既定の規則を使用すると、VNet 内の IP は ASE サブネットにアクセスできます。 別の既定の規則を使用すると、ロード バランサー (パブリック VIP とも呼ばれます) は ASE と通信できます。 既定の規則を確認するには、 **[追加]** アイコンの横にある **[既定の規則]** を選択します。 既定の規則の前に他のすべてを拒否する規則を設定すると、VIP と ASE の間のトラフィックが妨げられます。 VNet の内部からトラフィックが来ないようにするには、受信を許可するための独自の規則を追加します。 **[任意]** の宛先と **\*** のポート範囲が含まれた AzureLoadBalancer に等しいソースを使用します。 NSG 規則は ASE サブネットに適用されるため、宛先を限定する必要はありません。

アプリに IP アドレスを割り当てた場合は、必ずポートを開いたままにしてください。 ポートを確認するには、 **[App Service 環境]**  >  **[IP アドレス]** を選択します。  

次の送信規則に示されている項目は、最後の項目を除き、すべて必要です。 これらは、この記事の前の方で説明した ASE の依存関係へのネットワーク アクセスを有効にします。 それらのいずれかをブロックすると、ASE は動作を停止します。 リストの最後の項目は、ASE が VNet 内の他のリソースと通信できるようにします。

![送信セキュリティ規則][5]

NSG が定義されたら、それを ASE が存在するサブネットに割り当てます。 ASE VNet またはサブネットを覚えていない場合は、ASE ポータル ページから確認できます。 NSG をサブネットに割り当てるには、サブネット UI に移動して、NSG を選択します。

## <a name="routes"></a>Routes ##

VNet でルートを設定し、送信トラフィックがインターネットに直接送信されるのではなく、ExpressRoute ゲートウェイや仮想アプライアンスなどの他の場所に送信されるようにすることを強制トンネリングといいます。  ASE をこのように構成する必要がある場合には、「[強制トンネリングを使用した App Service Environment の構成][forcedtunnel]」のドキュメントを参照してください。  このドキュメントでは、ExpressRoute および強制トンネリングで使用できるオプションについて説明しています。

ポータルで ASE を作成するときに、ASE で作成されるサブネットのルート テーブルのセットも作成します。  これらのルートでは、単純に送信トラフィックを直接インターネットに送信します。  
同じルートを手動で作成するには、次の手順に従います。

1. Azure Portal にアクセスします。 **[ネットワーク]**  >  **[ルート テーブル]** を選択します。

2. VNet と同じリージョン内に新しいルート テーブルを作成します。

3. ルート テーブル UI 内から、 **[ルート]**  >  **[追加]** を選択します。

4. **[次ホップの種類]** を **[インターネット]** に、 **[アドレス プレフィックス]** を **[0.0.0.0/0]** に設定します。 **[保存]** を選択します。

    次のような内容が表示されます。

    ![機能ルート][6]

5. 新しいルート テーブルを作成したら、ASE が含まれるサブネットに移動します。 ポータルで一覧からルート テーブルを選択します。 変更を保存すると、指定した NSG とルートがサブネットと共に表示されます。

    ![NSG とルート][7]

## <a name="service-endpoints"></a>サービス エンドポイント ##

サービス エンドポイントを設けることで、マルチテナント サービスへのアクセスを、一連の Azure 仮想ネットワークとサブネットに制限することができます。 サービス エンドポイントについて詳しくは、「[仮想ネットワーク サービス エンドポイント][serviceendpoints]」のドキュメントをご覧ください。 

リソースに対するサービス エンドポイントを有効にすると、他のどのルートよりも高い優先度でルートが作成されます。 ASE を強制的にトンネリングして、Azure サービスに対してサービス エンドポイントを使用する場合、それらのサービスへのトラフィックは強制的にトンネリングされません。 

サブネットに対し、Azure SQL インスタンスとのサービス エンドポイントを有効にすると、そのサブネットから接続されるすべての Azure SQL インスタンスについてサービス エンドポイントが有効になります。 同じサブネットから複数の Azure SQL インスタンスにアクセスする場合に、サービス エンドポイントの有効と無効を Azure SQL インスタンスごとに分けることはできません。 他の Azure サービスは、サービス エンドポイントに関して Azure SQL のように動作しません。 Azure Storage とのサービス エンドポイントを有効にした場合、そのリソースには、自分のサブネットからしかアクセスできないようロックされますが、他の Azure Storage アカウントには、サービス エンドポイントが有効になっていなくても引き続きアクセスすることができます。  

![サービス エンドポイント][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
