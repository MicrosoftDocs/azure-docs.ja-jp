---
title: "Azure App Service、Virtual Machines、Service Fabric、Cloud Services の比較 | Microsoft Docs"
description: "Web アプリケーションをホストするにあたり、使用するサービス (Azure App Service、Virtual Machines、Service Fabric、Cloud Services) を選択する方法について説明します。"
services: app-service\web, virtual-machines, cloud-services
documentationcenter: 
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: 7d346a23-532a-42a9-98a8-23b7286d32a8
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2016
ms.author: tdykstra
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 2f5e6d100effee99b1a1576c0e21f1a311f8131b
ms.lasthandoff: 12/08/2016


---
# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Azure App Service、Virtual Machines、Service Fabric、Cloud Services の比較
## <a name="overview"></a>概要
Azure には、Web サイトをホストするための方法がいくつかあります ([Azure App Service][Azure App Service]、[Virtual Machines][Virtual Machines]、[Service Fabric][Service Fabric]、[Cloud Services][Cloud Services])。 それぞれの選択肢についてこの記事で理解を深め、実際の Web アプリケーションに適した方法をお選びください。

Azure App Service は、ほとんどの Web アプリに適しています。 デプロイと管理機能がそのプラットフォームに統合され、トラフィックの負荷に応じてサイトのスケールを機敏に調整できるほか、組み込みの負荷分散機能と Traffic Manager によって高い可用性が得られます。 既にあるサイトは、[オンライン移行ツール](https://www.migratetoazure.net/)を使用して簡単に Azure App Service に移行することができます。Web アプリケーション ギャラリーからオープン ソースのアプリケーションを使用したり、好きなフレームワークとツールを使用して新しいサイトを作成したりできます。 [Web ジョブ][WebJobs]機能を使用すると、バックグラウンド ジョブの処理を簡単に、App Service Web アプリに追加できます。

Service Fabric は、新しいアプリを作成する場合やマイクロサービス アーキテクチャを使用するように既存のアプリを書き換える場合にお勧めします。 共有プールのマシン上で動作するアプリは、小規模から開始し、必要に応じて数百または数千ものコンピューターを含む非常に大きなスケールまで拡張することができます。 ステートフル サービスによってアプリの状態を一貫して確実に格納することが容易になり、Service Fabric はサービスのパーティション分割、スケーリング、可用性を自動的に管理します。  また、Service Fabric は、Open Web Interface for .NET (OWIN) と ASP.NET Core を使用した Web API もサポートします。  App Service に比べて、Service Fabric は基になるインフラストラクチャへのより細かい制御 (直接アクセス) も提供します。 サーバーにリモート接続したり、サーバーのスタートアップ タスクを構成したりできます。 Cloud Services は、制御の程度と使いやすさにおいて Service Fabric と似ていますが、現在ではレガシ サービスになりつつあり、新しい開発には Service Fabric をお勧めします。

既存のアプリケーションを App Service や Service Fabric で実行するためにはかなりの修正が必要という場合、Virtual Machines を選ぶことで、クラウドへの移行を単純化できる場合があります。 ただし、VM の構成、セキュリティ保護、メンテナンスを正しく行うためには、Azure App Service や Service Fabric と比べて、はるかに時間がかかり、IT に対する豊富な知識と経験が要求されます。 Azure Virtual Machines を選択する場合は、VM 環境に対する修正プログラムの適用、更新、管理に伴って日々発生するメンテナンスの労力を考慮してください。 Azure Virtual Machines は Infrastructure-as-a-Service (IaaS) で、App Service と Service Fabric は Platform-as-a-Service (Paas) です。 

## <a name="a-namefeaturesafeature-comparison"></a><a name="features"></a>機能の比較
次の表では、最善の選択ができるように、App Service、Cloud Services、Virtual Machines、および Service Fabric の機能を比較しています。 それぞれのホスティング方法に関する最新の SLA 情報については、 [Azure サービス レベル アグリーメント](/support/legal/sla/)を参照してください。

| 機能 | App Service (Web Apps) | Cloud Services (Web ロール) | Virtual Machines | Service Fabric | メモ |
| --- | --- | --- | --- | --- | --- |
| ほぼ即時のデプロイ |○ | | |○ |アプリケーションまたはアプリケーションの更新プログラムを Cloud Services にデプロイしたり、VM を作成したりするには、最低でも数分かかります。一方、アプリケーションを Web アプリにデプロイする場合の所要時間は数秒です。 |
| 再デプロイなしでの大型マシンへのスケールアップ |○ | | |○ | |
| Web サーバーのインスタンスは、コンテンツと構成を共有します。つまり、スケールする際に、デプロイまたは構成をやり直す必要はありません。 |○ | | |○ | |
| 複数のデプロイ環境 (運用環境とステージング環境) |○ |○ | |○ |Service Fabric を使用すると、アプリ用の複数の環境を所有したり、アプリの複数のバージョンを並行してデプロイしたりできます。 |
| OS の自動更新の管理 |○ |○ | | |OS の自動更新が今後の Service Fabric リリースで予定されています。 |
| シームレスなプラットフォームの切り替え (32 ビットと 64 ビット間で簡単に移動) |○ |○ | | | |
| GIT、FTP によるコードのデプロイ |○ | |○ | | |
| Web デプロイによるコードのデプロイ |○ | |○ | |Cloud Services では、Web 配置を使用して、個々のロール インスタンスに更新プログラムをデプロイできます。 ただし、ロールの初回デプロイに Web 配置を使用することはできません。また、更新プログラムに Web 配置を使用する場合は、各ロールのインスタンスに対して個別にデプロイする必要があります。 運用環境で Cloud Services の SLA を満たすには、複数のインスタンスが必要です。 |
| WebMatrix サポート |○ | |○ | | |
| Service Bus、Storage、SQL Database のようなサービスへのアクセス |○ |○ |○ |○ | |
| 多層アーキテクチャの Web 層または Web サービス層のホスト |○ |○ |○ |○ | |
| 多層アーキテクチャの中間層のホスト |○ |○ |○ |○ |REST API 中間層は、App Service Web Apps で簡単にホストできます。バックグラウンド処理ジョブは、[Web ジョブ](http://go.microsoft.com/fwlink/?linkid=390226)機能でホストできます。 Web ジョブを専用 Web サイトで実行することにより、その階層のスケーラビリティを個別に確保できます。 プレビュー [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) 機能には、REST サービスをホストする機能が多数搭載されています。 |
| 統合されたサービスとしての MySQL のサポート |○ |○ |○ | |Cloud Services は、ClearDB のサービスを介してサービスとしての MySQL を統合できますが、Azure ポータル ワークフローの一部として統合することはできません。 |
| ASP.NET、クラシック ASP、Node.js、PHP、Python のサポート |○ |○ |○ |○ |Service Fabric では、[ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) を使用した Web フロントエンドの作成がサポートされています。または、あらゆる種類のアプリケーション (Node.js、Java など) を[ゲスト実行可能ファイル](../service-fabric/service-fabric-deploy-existing-app.md)としてデプロイできます。 |
| 再デプロイなしでの複数インスタンスへのスケールアウト |○ |○ |○ |○ |Virtual Machines は複数のインスタンスにスケールアウトできますが、そこで実行されるサービスが、このようなスケールアウトに対応できるように記述されていなければなりません。 要求を複数のコンピューターにルーティングするためのロード バランサーを構成すると共に、アフィニティ グループを作成して、メンテナンスやハードウェアの障害で全インスタンスが同時に再起動するのを防ぐ必要があります。 |
| SSL のサポート |○ |○ |○ |○ |App Service Web Apps の場合、カスタム ドメイン名の SSL は Basic モードと Standard モードでのみサポートされます。 Web Apps での SSL の使い方については、[Azure Web サイトの SSL 証明書の構成](web-sites-configure-ssl-certificate.md)に関するページを参照してください。 |
| Visual Studio 統合 |○ |○ |○ |○ | |
| リモート デバッグ |○ |○ |○ | | |
| TFS によるコードのデプロイ |○ |○ |○ |○ | |
| [Azure Virtual Network](/services/virtual-network/) |○ |○ |○ |○ |「 [Azure Websites Virtual Network Integration (Azure Websites Virtual Network の統合)](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) |
| [Azure Traffic Manager](/services/traffic-manager/) |○ |○ |○ |○ | |
| 統合エンドポイント監視 |○ |○ |○ | | |
| サーバーへのリモート デスクトップ アクセス | |○ |○ |○ | |
| カスタム MSI のインストール | |○ |○ |○ |Service Fabric を使用すると、任意の実行可能ファイルを [ゲスト実行可能ファイル](../service-fabric/service-fabric-deploy-existing-app.md) としてホストしたり、任意のアプリを VM にインストールしたりできます。 |
| スタートアップ タスクの定義と実行 | |○ |○ |○ | |
| ETW イベントのリッスン | |○ |○ |○ | |

## <a name="a-namescenariosascenarios-and-recommendations"></a><a name="scenarios"></a>シナリオと推奨事項
以降、一般的なアプリケーションのシナリオをいくつか取り上げると共に、それぞれのシナリオで最適と思われる Azure Web ホスティングの選択肢を紹介します。

* [Web フロント エンドとバックエンド (バックグラウンド処理とデータベース) を組み合わせて、ビジネス アプリケーションをオンプレミスの資産と連係させたいのですが。](#onprem)
* [拡張性が高く、世界展開も視野に入れた信頼性の高い方法で会社の Web サイトをホスティングする必要があります。](#corp)
* [Windows Server 2003 上で動作する IIS6 アプリケーションがあるのですが。](#iis6)
* [スモール ビジネスのオーナーです。サイトをホストするための安価な方法が必要ですが、将来の成長も考慮する必要があります。](#smallbusiness)
* [Web デザイナーまたはグラフィック デザイナーです。顧客の Web サイトをデザインしたり構築したりする必要があります。](#designer)
* [Web フロント エンドを含む多層アプリケーションをクラウドに移行しています。](#multitier)
* [使用しているアプリケーションが、高度にカスタマイズされた Windows または Linux 環境に依存しています。そのアプリケーションをクラウドに移行したいのですが。](#custom)
* [自分のサイトではオープン ソース ソフトウェアを使用しており、そのソフトウェアを Azure でホストする必要があります。](#oss)
* [社内ネットワークに接続する必要がある基幹業務アプリケーションがあります。](#lob)
* [モバイル クライアント向けの REST API や Web サービスをホストします。](#mobile)

### <a name="a-idonprema-i-need-a-web-front-end-with-background-processing-and-database-backend-to-run-business-applications-integrated-with-on-premise-assets"></a><a id="onprem"></a> Web フロント エンドとバックエンド (バックグラウンド処理とデータベース) を組み合わせて、ビジネス アプリケーションをオンプレミスの資産と連係させたいのですが。
そのような複合的なビジネス アプリケーションには、Azure App Service が最適です。 Azure Websites 向けにアプリケーションを開発することで、負荷分散プラットフォーム上でスケールを自動的に調整し、Active Directory でセキュリティを確保しながら、オンプレミスのリソースに接続することが可能です。 それらのアプリケーションは、ワールドクラスのポータルと API によって簡単に管理できます。さらに、そのアプリケーションが顧客によってどのように利用されているかを、アプリケーション洞察ツールで深く見通すことができます。 [Web ジョブ][Webjobs]機能を使用すると、バックグラウンドのプロセスとタスクを Web 階層の中で実行すると共に、ハイブリッド接続と VNET 機能を使ってオンプレミスのリソースに対して簡単に接続できます。 Azure App Service は Web アプリの 99.95% の稼働率が SLA で保証されています。Azure App Service でできることを次に示します。

* 自己復旧機能と自動修正機能を備えたクラウド プラットフォーム上でアプリケーションを実行し、高い信頼性を確保する。
* グローバルなデータセンターのネットワークを活かしてスケールを自動的に調整する。
* バックアップと復元によって障害復旧に対応する。
* ISO、SOC2、PCI に準拠する。
* Active Directory と連係する。

### <a name="a-idcorpa-i-need-a-reliable-way-to-host-my-corporate-website-that-scales-well-and-offers-global-reach"></a><a id="corp"></a> 拡張性が高く、世界展開も視野に入れた信頼性の高い方法で会社の Web サイトをホスティングする必要があります。
Azure App Service は、企業 Web サイトをホスティングするためのソリューションとしてきわめて優れた特長を持っています。 グローバルなデータセンターのネットワークを活かして Web アプリのスケールを短期間で拡張し、すぐに需要に応えることができます。 また、ローカル リーチ、フォールト トレランス、インテリジェントなトラフィック管理が実現されています。 そのすべてが、ワールドクラスの管理ツールを備えたプラットフォームに集約され、サイトの稼働状態とトラフィックの状況を速やかにかつ簡単に把握できます。 Azure App Service は Web アプリの 99.9% の稼働率が SLA で保証されています。Azure App Service でできることを次に示します。

* 自己復旧機能と自動修正機能を備えたクラウド プラットフォーム上で Web サイトを実行し、高い信頼性を確保する。
* グローバルなデータセンターのネットワークを活かしてスケールを自動的に調整する。
* バックアップと復元によって障害復旧に対応する。
* 統合されたツールを使用してログとトラフィックを管理する。
* ISO、SOC2、PCI に準拠する。
* Active Directory と連係する。

### <a name="a-idiis6a-i-have-an-iis6-application-running-on-windows-server-2003"></a><a id="iis6"></a> Windows Server 2003 上で動作する IIS6 アプリケーションがあるのですが。
Azure App Service は、過去の IIS6 アプリケーションの移行に伴うインフラストラクチャ コストを効果的に抑制します。 互換性をチェックして必要な変更点を洗い出すことのできる [使いやすい移行ツールと詳細な移行ガイド](https://www.movemetowebsites.net/) がマイクロソフトから提供されています。 Visual Studio や TFS、さらに、広く普及している CMS ツールとの高い親和性により、IIS6 アプリケーションを手間なく直接クラウドにデプロイすることができます。 デプロイ後は、Azure Portal の堅牢な管理ツールを使ってスケールを調整 (スケールダウンまたはスケールアップ) することにより、必要に応じてコストを抑制したり需要の増大に対応したりすることができます。 移行ツールを使用すると、次のことができます。

* 既存の Windows Server 2003 Web アプリケーションをすばやく簡単にクラウドへと移行する。
* 必要であれば、接続先の SQL Database をオンプレミスに維持し、ハイブリッド型のアプリケーションを作成する。
* SQL Database をレガシ アプリケーションと共に自動的に移動する。

### <a name="a-idsmallbusinessaim-a-small-business-owner-and-i-need-an-inexpensive-way-to-host-my-site-but-with-future-growth-in-mind"></a><a id="smallbusiness"></a>スモール ビジネスのオーナーです。サイトをホストするための安価な方法が必要ですが、将来の成長も考慮する必要があります。
このシナリオでは、無料で使用を開始して必要なときにさらに機能を追加できる、Azure App Service が最適なソリューションです。 無料の Web アプリにはいずれも、Azure によって提供されるドメインが付属しています (<*会社名*>.azurewebsites.net)。また、統合デプロイ/管理ツールや、導入作業を支援するアプリケーション ギャラリーも、このプラットフォームに含まれています。 他にも、ユーザーの要求が増えるにつれて拡大できる多くのサービスや拡張オプションがあります。 Azure App Service を使用すると、次のことができます。

* Free レベルから開始し、必要に応じてスケールアップできます。
* アプリケーション ギャラリーを使用して、WordPress などの一般的な Web アプリケーションをすばやくセットアップできます。
* 必要に応じて、Azure のその他のサービスや機能をアプリケーションに追加できます。
* Web アプリのセキュリティを HTTPS で保護することができます。

### <a name="a-iddesignera-im-a-web-or-graphic-designer-and-i-want-to-design-and-build-websites-for-my-customers"></a><a id="designer"></a> Web デザイナーまたはグラフィック デザイナーです。顧客の Web サイトをデザインおよび構築する必要があります。
Web 開発者とデザイナーのために、Azure App Service は、Git や FTP でのデプロイに対応するなど、さまざまなフレームワークおよびツールと容易に連係できるようになっているほか、Visual Studio や SQL Database などのツールやサービスと緊密に統合されています。 App Service を使用すると、次のことができます。

* [タスクの自動化][scripting]のためのコマンド ライン ツールを使用できます。
* [.Net][dotnet]、[PHP][PHP]、[Node.js][nodejs]、[Python][Python] など、一般的な言語で作業できます。
* 非常に高い容量に拡張するための 3 つの異なる拡張レベルを選択できます。
* [SQL Database][sqldatabase]、[Service Bus][servicebus]、[Storage][Storage] などの他の Azure サービスや、MySQL、MongoDB など、[Azure Store][azurestore] からのパートナーの製品と統合できます。
* Visual Studio、Git、WebMatrix、WebDeploy、TFS、FTP などのツールと統合できます。

### <a name="a-idmultitieraim-migrating-my-multi-tier-application-with-a-web-front-end-to-the-cloud"></a><a id="multitier"></a>Web フロント エンドを含む多層アプリケーションをクラウドに移行しています。
データベースに接続する Web サーバーなど多層アプリケーションを実行する用途において、Azure SQL Database と緊密に連係する Azure App Service は良い選択といえます。 Web ジョブ機能を使用してバックエンド プロセスを実行することもできます。

サーバーへのリモート アクセス、サーバーのスタートアップ タスクの構成など、さらに細かくサーバー環境を制御する必要がある場合は、1 つ以上の階層で Service Fabric を使用します。

独自のマシン イメージを使用する場合や、Service Fabric 上で構成できないサーバー ソフトウェアやサーバー サービスを実行する場合は、1 つ以上の階層で Virtual Machines を使用します。

### <a name="a-idcustomamy-application-depends-on-highly-customized-windows-or-linux-environments-and-i-want-to-move-it-to-the-cloud"></a><a id="custom"></a>使用しているアプリケーションが、高度にカスタマイズされた Windows または Linux 環境に依存しています。そのアプリケーションをクラウドに移行したいのですが。
アプリケーションを使用するために、ソフトウェアおよびオペレーティング システムの複雑なインストールまたは構成が必要な場合、Virtual Machines が最良のソリューションであると思われます。 Virtual Machines を使用すると、次のことができます。

* 仮想マシン ギャラリーを使用して、Windows や Linux などのオペレーティング システムから始め、後でアプリケーションの要件に合わせてカスタマイズできます。
* 既存のオンプレミス サーバーのカスタム イメージを作成してアップロードすることで、Azure の仮想マシンで実行できます。

### <a name="a-idossamy-site-uses-open-source-software-and-i-want-to-host-it-in-azure"></a><a id="oss"></a>自分のサイトではオープン ソース ソフトウェアを使用しており、そのソフトウェアを Azure でホストする必要があります。
そのオープン ソース フレームワークが App Service でサポートされている場合、ご利用のアプリケーションに必要な言語とフレームワークが自動的に構成されます。 App Service を使用すると、次のことができます。

* [.NET][dotnet]、[PHP][PHP]、[Node.js][nodejs]、[Python][Python] など、多くの一般的なオープン ソース言語を使用できます。
* WordPress、Drupal、Umbraco、DNN、および多くのサード パーティ製 Web アプリケーションをセットアップできます。
* 既存のアプリケーションを移行することも、アプリケーション ギャラリーから新規アプリケーションを作成することもできます。

ご利用のオープン ソース フレームワークが App Service でサポートされていない場合は、他の Azure Web ホスティング方法のいずれかで実行できます。 Virtual Machines を使用する場合、Windows または Linux ベースのマシン イメージにソフトウェアをインストールして構成します。

### <a name="a-idlobai-have-a-line-of-business-application-that-needs-to-connect-to-the-corporate-network"></a><a id="lob"></a>社内ネットワークに接続する必要がある基幹業務アプリケーションがあります。
基幹業務アプリケーションを作成する場合は、Web サイトから社内ネットワーク上のサービスやデータに直接アクセスすることが必要な場合があります。 これは、App Service、Service Fabric、Virtual Machines で、 [Azure Virtual Network サービス](/services/virtual-network/)を使用して実現できます。 App Service 上では、 [VNET 統合機能](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)を使用して、見かけ上、社内ネットワークで動作しているかのように Azure アプリケーションを実行することができます。

### <a name="a-idmobileai-want-to-host-a-rest-api-or-web-service-for-mobile-clients"></a><a id="mobile"></a>モバイル クライアント向けの REST API や Web サービスをホストします。
HTTP ベースの Web サービスを使用すると、モバイル クライアントを含めて広範囲のクライアントをサポートすることができます。 ASP.NET Web API のようなフレームワークは、REST サービスを作成および使用しやすくするために Visual Studio と統合されています。  これらのサービスは Web エンドポイントから公開されるため、Azure での Web ホスト手法を使用してこのシナリオをサポートすることができます。 ただし、REST API をホストするためには App Service が適切な選択です。 App Service を使用すると、次のことができます。

* [モバイル アプリ](../app-service-mobile/app-service-mobile-value-prop.md)や [API アプリ](../app-service-api/app-service-api-apps-why-best-platform.md)を迅速に作成して、グローバルに分散した Azure のデータセンターの 1 つで HTTP Web サービスをホストします。
* 既にあるサービスを移行したり、新しいサービスを作成したりすることができます。
* 1 つのインスタンスで可用性の SLA を実現するか、または複数の専用コンピューターにスケールアウトします。
* 発行済みのサイトを使用して、モバイル クライアントを含む HTTP クライアントに REST API を提供します。

> [!NOTE]
> アカウントにサインアップする前に Azure App Service を実際に使ってみるには、<a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a> にアクセスしてください。Azure App Service で、有効期限付きのスターター アプリを無償ですぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="a-idnextstepsa-next-steps"></a><a id="nextsteps"></a> 次のステップ
3 つの Web ホスティング方法の詳細については、「 [Microsoft Azure の概要](../fundamentals-introduction-to-azure.md)」を参照してください。

アプリケーションに使用するホスティング方法が決まったら、次のリソースで基本事項を確認してください。

* [Azure App Service](/documentation/services/app-service/)
* [Azure Cloud Services](/documentation/services/cloud-services/)
* [Azure Virtual Machines](/documentation/services/virtual-machines/)
* [Service Fabric](/documentation/services/service-fabric)

<!-- URL List -->

[Azure App Service]: /services/app-service/
[Cloud Services]: http://go.microsoft.com/fwlink/?LinkId=306052
[Virtual Machines]: http://go.microsoft.com/fwlink/?LinkID=306053
[Service Fabric]: /services/service-fabric
[ClearDB]: http://www.cleardb.com/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
[azurestore]: http://www.windowsazure.com/gallery/store/
[scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
[dotnet]: http://www.windowsazure.com/develop/net/
[nodejs]: http://www.windowsazure.com/develop/nodejs/
[PHP]: http://www.windowsazure.com/develop/php/
[Python]: http://www.windowsazure.com/develop/python/
[servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
[sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
[Storage]: http://www.windowsazure.com/documentation/services/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png

