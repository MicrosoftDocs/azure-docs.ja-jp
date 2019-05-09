---
title: Azure 開発者向けファースト ステップ ガイド | Microsoft Docs
description: ここでは、開発のニーズに対応するために Microsoft Azure プラットフォームの使用を検討している開発者に必要不可欠な情報を説明します。
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: caddb542e806457e085db096813e504ddc278ac0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108326"
---
# <a name="get-started-guide-for-azure-developers"></a>Azure 開発者向けファースト ステップ ガイド

## <a name="what-is-azure"></a>Azure とは

Azure は、既存のアプリケーションをホストし、新しいアプリケーションの開発を簡易化し、さらにオンプレミス アプリケーションも強化することができる機能が一式そろったクラウド プラットフォームです。 Azure はアプリケーションの開発、テスト、デプロイ、管理に必要なクラウド サービスを統合しているだけでなく、クラウド コンピューターの効率性も利用しています。

Azure でアプリケーションをホストすると、アプリケーションのデプロイを小規模から始めて、顧客の需要増大に合わせて規模を拡大することができます。 また、高可用性アプリケーションに必要な信頼性があり、リージョン間のフェールオーバー機能もあります。 [Azure Portal ](https://portal.azure.com)を使用すると、すべての Azure サービスを簡単に管理できます。 また、サービス固有の API とテンプレートを使用して、プログラムでサービスを管理することもできます。

**対象読者**:このガイドは、Azure プラットフォームのアプリケーション開発者向け入門ガイドです。 初めて Azure で新しいアプリケーションを構築する場合、または既存のアプリケーションを Azure に移行する場合のガイダンスと手順を説明しています。

## <a name="where-do-i-start"></a>どこから始めるか

Azure が提供しているどのサービスでも、ソリューション アーキテクチャのサポートに必要なサービスをなかなか見つけ出せないことがあります。 ここでは、開発者が一般的に使用する Azure サービスを取り上げます。 すべての Azure サービスの一覧については、[Azure のドキュメント](../../index.md)を参照してください。

まず、Azure でアプリケーションをホストする方法を決める必要があります。 インフラストラクチャ全体を仮想マシン (VM) として管理する必要はありますか。 Azure が提供するプラットフォーム管理施設を使用できますか。 それとも、コードの実行をホストする用途でのみサーバーレス フレームワークが必要でしょうか。

アプリケーションにクラウド記憶域が必要なら、Azure には複数のオプションがあります。 Azure のエンタープライズ認証を利用することができます。 また、クラウドベースの開発および監視用のツールもあり、ほとんどのホスティング サービスでは DevOps 統合が提供されています。

それでは、アプリケーションに合わせて調査をお勧めするサービスをいくつか見てみましょう。

### <a name="application-hosting"></a>アプリケーションのホスティング

Azure は、アプリケーションの実行に利用できるクラウドベースのコンピューティング サービスをいくつか提供しています。そのため、インフラストラクチャの詳細について心配する必要はありません。 アプリケーションの使用量の増大に合わせてリソースのスケール アップまたはスケール アウトを簡単に実行できます。

Azure は、アプリケーション開発とホスティングのニーズをサポートするサービスを提供しています。 また、アプリケーション ホスティングを詳細に制御できるサービスとしてのインフラストラクチャ (IaaS) も提供しています。 Azure のサービスとしてのプラットフォーム (PaaS) サービスは、アプリの強化に必要なフル マネージドのサービスを提供しています。 Azure には、本当の意味でのサーバーレス ホスティングもあります。このサービスを利用する場合、ユーザーに必要な作業はコードの作成のみです。

![Azure アプリケーション ホスティングのオプション](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

Web ベースのプロジェクトを最も短時間で公開できる方法が必要な場合は、Azure App Service をお勧めします。 App Service では、モバイル クライアントをサポートする Web アプリを拡張し、使用されている REST API を公開する作業も簡単です。 このプラットフォームには、ソーシャル プロバイダーを使用した認証、トラフィックベースの自動スケーリング、実稼働環境でのテスト、継続的かつコンテナーベースのデプロイの機能があります。

Web アプリ、モバイル アプリ バックエンド、および API アプリを作成できます。

上に示した 3 つのアプリケーションの種類は、いずれも App Service ランタイムを共有しているため、1 つのプロジェクトやソリューションから、Web サイトのホスト、モバイル クライアントのサポート、自作 API を Azure で公開することのすべてを行うことができます。 App Service の詳細については、「[Web Apps の概要](../../app-service/overview.md)」を参照してください。

App Service は DevOps を念頭に置いて設計されています。 GitHub webhooks、Jenkins、Azure DevOps、TeamCity など、公開と継続的インテグレーション デプロイに対応する多様なツールをサポートしています。

[オンライン移行ツール](https://www.migratetoazure.net/)を使用して、既存のアプリケーションを App Service に移行することができます。

> **使用する場合**:App Service を使用するのは、既存の Web アプリケーションを Azure に移行する場合、および Web アプリ用にフル マネージドのホスティング プラットフォームが必要な場合です。 また、モバイル クライアントをサポートする必要がある場合、またはアプリと共に REST API を公開する必要がある場合にも App Service を使用できます。
> 
> **作業開始**:App Service を使用すると、初めての [Web アプリ](../../app-service/app-service-web-get-started-dotnet.md)、[モバイル アプリ](../../app-service-mobile/app-service-mobile-ios-get-started.md)、[API アプリ](../../app-service/app-service-web-tutorial-rest-api.md)でも簡単に作成、デプロイできます。
> 
> **今すぐ試す**:App Service なら、Azure アカウントを新規登録することなく、一時的なアプリをプロビジョニングしてプラットフォームを試すことができます。 プラットフォームと [Azure App Service アプリの作成](https://tryappservice.azure.com/)を試してみましょう。

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

サービスとしてのインフラストラクチャ (IaaS) プロバイダーの場合、Azure を使用すると、自社のアプリケーションを Windows または Linux VM にデプロイまたは移行することができます。 Azure Virtual Machines は、Azure Virtual Network と組み合わせると、Windows または Linux VM を Azure にデプロイすることもできます。 VM では、マシンの構成全体を制御できます。 VM を使用する場合、すべてのサーバー ソフトウェアのインストール、構成、メンテナンス、オペレーティング システムの修正プログラムを担当することになります。

VM の場合は細かいレベルで制御できるため、Azure 上で PaaS モデルに合わない幅広いサーバー ワークロードを実行できます。 たとえば、データベース サーバー、Windows Server Active Directory、Microsoft SharePoint などのワークロードが含まれます。 詳細については、[Linux](/azure/virtual-machines/linux/) または [Windows](/azure/virtual-machines/windows/) の Virtual Machines のドキュメントを参照してください。

> **使用する場合**:アプリケーション インフラストラクチャを完全に制御したい場合、またはオンプレミス アプリケーション ワークロードを変更せずに Azure に移行したい場合は、Virtual Machines を使用します。
> 
> **作業開始**:[Linux VM](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) または [Windows VM](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) を Azure portal から作成します。

#### <a name="azure-functions-serverless"></a>Azure Functions (サーバーレス)

アプリケーションまたはコードを実行するインフラストラクチャ全体を構築し、管理する心配は不要です。 コードを作成し、イベントやスケジュールに応答して実行させるだけでよいとしたらどうしますか。  [Azure Functions](../../azure-functions/functions-overview.md) は、必要なコードを作成するだけで済む "サーバーレス" スタイルのサービスです。 Functions を使用すると、コードの実行は HTTP 要求、webhook、クラウド サービス イベント、またはスケジュールに応じてトリガーされます。 C\#、F\#、Node.js、Python、PHP など、好きな言語でコードを開発することもできます。 使用量ベースの課金の場合、コードの実行時にのみ課金されます。また、必要に応じて Azure は拡大縮小されます。

> **使用する場合**:他の Azure サービス、Web ベースのイベント、またはスケジュールに応じてトリガーされるコードがある場合に、Azure Functions を使用します。 また、完全なホスト型プロジェクトのオーバーヘッドが必要ない場合、またはコードの実行時にのみ料金を支払いたい場合にも Functions を使用できます。 詳細については、「[Azure Functions の作業開始](../../azure-functions/functions-overview.md)」を参照してください。
> 
> **作業開始**:ポータルから[初めて関数を作成する](../../azure-functions/functions-create-first-azure-function.md)には、Functions のクイックスタート チュートリアルに従います。
> 
> **今すぐ試す**:Azure Functions を使用すると、Azure アカウントを新規登録することなくコードを実行できます。 今すぐ試して[初めての Azure 関数を作成](https://tryappservice.azure.com/)しましょう。

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric は、スケーラブルで信頼性に優れたマイクロサービスの構築、パッケージ化、デプロイ、管理を簡単に行うことができる分散システム プラットフォームです。 また、デプロイ済みのアプリケーションをプロビジョニング、デプロイ、監視、アップグレード/修正、削除するための包括的なアプリケーション管理機能も提供します。 共有プールのマシン上で動作するアプリは、小規模から開始し、必要に応じて数百または数千ものコンピューターの規模まで拡張することができます。

Service Fabric は、Open Web Interface for .NET (OWIN) と ASP.NET Core を使用した Web API をサポートします。 また、.NET Core と Java の両方で Linux 上のサービスを構築するための SDK を提供しています。 Service Fabric の詳細については、「[Service Fabric のドキュメント](https://docs.microsoft.com/azure/service-fabric/)」を参照してください。

> **使用する場合:** マイクロサービス アーキテクチャを使用するアプリケーションを作成する場合、または既存のアプリケーションを書き換える場合は、Service Fabric がお勧めです。 基になるインフラストラクチャをより細かく制御する場合、または直接アクセスする場合は、Service Fabric を使用します。
> 
> **概要:**[最初の Azure Service Fabric アプリケーションを作成します](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md)。

### <a name="enhance-your-applications-with-azure-services"></a>Azure サービスでアプリケーションを強化する

Azure では、アプリケーション ホスティングに加え、クラウドとオンプレミス両方のアプリケーションの機能、開発、およびメンテナンスを強化できるサービス プランを提供しています。

#### <a name="hosted-storage-and-data-access"></a>ホストされる記憶域とデータ アクセス

ほとんどのアプリケーションはデータを格納する必要があるため、Azure でアプリケーションをホストする方法の選択にかかわらず、次の機器とデータ サービスの 1 つまたは複数を検討してください。

- **Azure Cosmos DB**:包括的な SLA により、任意の数の地理的リージョンでスループットとストレージを柔軟にスケーリングできるグローバル分散マルチモデル データベースです。 
  > **使用する場合:** アプリケーションに、明確に定義された複数の整合性モデルのドキュメント、テーブル、またはグラフ データベース (MongoDB データベースを含む) が必要な場合。 
  > 
  > **作業開始**:[Azure Cosmos DB Web アプリをビルドします](../../cosmos-db/create-sql-api-dotnet.md)。 MongoDB 開発者でない場合は、[Azure Cosmos DB を使用した MongoDB Web アプリのビルド](../../cosmos-db/create-mongodb-dotnet.md)に関する記事をご覧ください。

- **Azure Storage**:BLOB、クエリ、ファイルなどの非リレーショナル データに対して、耐久性があり、高可用な記憶域を提供します。 Storage は、VM 向けに記憶域の基盤を提供します。

  > **使用する場合**:キーと値のペア (テーブル)、BLOB、ファイル共有、メッセージ (キュー) など、非リレーショナル データを格納するアプリケーションの場合。
  > 
  > **作業開始**:[BLOB](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)、[テーブル](../../cosmos-db/table-storage-how-to-use-dotnet.md)、[クエリ](../../storage/queues/storage-dotnet-how-to-use-queues.md)、または[ファイル](../../storage/files/storage-dotnet-how-to-use-files.md)のいずれかの記憶域から選択します。

- **Azure SQL Database**:クラウドにリレーショナル表形式データを格納する Azure ベース バージョンの Microsoft SQL Server エンジンです。 SQL Database は、予測可能なパフォーマンス、ダウンタイムなしのスケーラビリティ、ビジネス継続性、データ保護を提供しています。

  > **使用する場合**:参照整合性、トランザクションのサポート、および TSQL クエリのサポートがあるデータ記憶域が必要なアプリケーションの場合。
  > 
  > **作業開始**:[Azure portal を使用して数分で SQL データベースを作成できます](../../sql-database/sql-database-get-started.md)。


[Azure Data Factory](../../data-factory/introduction.md) を使用して既存のオンプレミス データを Azure に移行することができます。 データをクラウドに移行する準備ができていない場合は、BizTalk Services の[ハイブリッド接続](../../biztalk-services/integration-hybrid-connection-overview.md)を使用して、App Service でホストされるアプリをオンプレミス リソースに接続できます。 また、オンプレミス アプリケーションから Azure データと記憶域サービスに接続することもできます。

#### <a name="docker-support"></a>Docker のサポート

OS 仮想化の形式の 1 つである Docker コンテナーを使用すると、より効率的で予測可能な方法でアプリケーションをデプロイできます。 コンテナー化されたアプリケーションは、開発およびテスト システム上と同様に実稼働環境でも動作します。 コンテナーは、標準の Docker ツールを使用して管理できます。 コンテナーベースのアプリケーションは、既存のスキルと人気のあるオープンソース ツールを使用して Azure でデプロイおよび管理できます。

Azure には、アプリケーションでコンテナーを使用する方法がいくつか用意されています。

- **Azure Docker VM 拡張機能**:Docker ツールを使用して、Docker ホストとして動作するように VM を構成できます。

  > **使用する場合**:VM 上のアプリケーション用に一貫性のあるコンテナー デプロイを生成する場合、または [Docker Compose](https://docs.docker.com/compose/overview/) を使用する場合。
  > 
  > **作業開始**:[Docker VM 拡張機能を使用して Azure に Docker 環境を作成します](../../virtual-machines/virtual-machines-linux-dockerextension.md)。

- **Azure Container Service**:コンテナー化されたアプリケーションを実行するように事前構成されている仮想マシンのクラスターを簡単に作成、構成および管理できます。 Container Service の詳細については、「[Azure Container Service を使用した Docker コンテナー ホスティング ソリューションの概要](../../container-service/container-service-intro.md)」を参照してください。

  > **使用する場合**:追加のスケジュール設定および管理ツールを提供する実稼働レベルのスケーラブルな環境を作成する場合、または Docker Swarm クラスターをデプロイする場合。
  > 
  > **作業開始**:[Container Service クラスターをデプロイします](../../container-service/dcos-swarm/container-service-deployment.md)。

- **Docker Machine**:docker-machine コマンドを使用して、仮想ホスト上で Docker Engine をインストールおよび管理できます。

  >**使用する場合**:1 つの Docker ホストを作成して、アプリのプロトタイプを短時間で作成する必要がある場合。

- **App Service 用のカスタム Docker イメージ**:Linux 上に Web アプリをデプロイするときに、コンテナー レジストリまたは顧客のコンテナーから Docker コンテナーを使用できます。

  > **使用する場合**:Linux 上の Web アプリを Docker イメージにデプロイする場合。
  > 
  > **作業開始**:[Linux で App Service 用のカスタム Docker イメージを使用します](../../app-service/containers/quickstart-docker-go.md)。

### <a name="authentication"></a>Authentication

アプリケーションの使用者を把握することだけでなく、リソースへの不正アクセスを防止することも重要です。 Azure には、アプリ クライアントを認証する方法がいくつか用意されています。

- **Azure Active Directory (Azure AD)**:Microsoft のマルチテナントでクラウドベースの ID およびアクセス管理サービスです。 Azure AD と統合することで、シングル サインオン (SSO) 機能をアプリケーションに追加できます。 ディレクトリのプロパティには、Azure AD Graph API から直接、または Microsoft Graph API からアクセスできます。 OAuth 2.0 認証フレームワークと Open ID Connect の場合、ネイティブ HTTP/REST エンドポイントとマルチプラットフォーム Azure AD Authentication ライブラリを使用して、Azure AD のサポートと統合できます。

  > **使用する場合**:SSO エクスペリエンスを提供する場合、Graph ベースのデータを使用する場合、またはドメインベースのユーザーを認証する場合。
  > 
  > **作業開始**:詳細については、「[開発者のための Azure Active Directory](../../active-directory/develop/v1-overview.md)」を参照してください。

- **App Service 認証**:App Service を選択してアプリをホストする場合、Azure AD の組み込みの認証サポートと共に、ソーシャル ID プロバイダー (Facebook、Google、Microsoft、Twitter など) も利用できます。

  > **使用する場合**:Azure AD、ソーシャル ID プロバイダー、またはその両方を使用して App Service アプリで認証を有効にする場合。
  > 
  > **作業開始**:App Service の認証の詳細については、「[Azure App Service での認証および承認](../../app-service/overview-authentication-authorization.md)」を参照してください。

Azure でのセキュリティのベスト プラクティスについては、「[Azure セキュリティのベスト プラクティスとパターン](../../security/security-best-practices-and-patterns.md)」を参照してください。

### <a name="monitoring"></a>監視

Azure でアプリケーションを起動し、実行する場合、パフォーマンス、問題、ユーザーがアプリを使用する方法を監視する機能が必要です。 Azure には、いくつかの監視オプションがあります。

-   **Visual Studio Application Insights**:Visual Studio と統合してライブ Web アプリケーションを監視する、Azure でホストされる拡張可能な分析サービスです。 Azure 上でホストするかどうかにかかわらず、アプリのパフォーマンスと使いやすさを継続的に改善するために必要なデータを入手できます。

    >**作業開始**:[Application Insights](../../azure-monitor/app/app-insights-overview.md) のチュートリアルに従ってください。

-   **Azure Monitor**:Azure インフラストラクチャとリソースから生成される指標とログに対して、視覚化、クエリ、ルート、アーカイブ、および処理を実行できるサービスです。 Monitor には、Azure Portal に表示されるデータ ビューが用意されています。また、Azure リソースを監視できる 1 つのソースです。
 
    >**作業開始**:「[Azure Monitor の概要](../../monitoring-and-diagnostics/monitoring-get-started.md)」をご覧ください。

### <a name="devops-integration"></a>DevOps 統合

VM をプロビジョニングするか、継続的インテグレーションによって Web アプリを発行するかにかかわらず、Azure は人気のある DevOps ツールの多くと統合できます。 Jenkins、GitHub、Puppet、Chef、TeamCity、Ansible、Azure DevOps などのツールがサポートされているため、既に所有しているツールを利用し、経験を最大限に活用することができます。

> **今すぐ試す:**[DevOps 統合のいくつかを試しましょう](https://azure.microsoft.com/try/devops/)。
> 
> **作業開始**:App Service アプリの DevOps オプションの詳細については、「[Azure App Service への継続的なデプロイ](../../app-service/deploy-continuous-deployment.md)」を参照してください。


## <a name="azure-regions"></a>Azure Azure リージョン

Azure は、世界中のさまざまな地域で一般的に利用できるグローバル クラウド プラットフォームです。 Azure にサービス、アプリケーション、または VM をプロビジョニングする場合、リージョンを選択するように求められます。リージョンは、アプリケーションが実行される、またはデータが格納される特定のデータセンターを示しています。 これらのリージョンは特定の場所に対応しており、「[Azure リージョン](https://azure.microsoft.com/regions/)」ページに公開されています。

### <a name="choose-the-best-region-for-your-application-and-data"></a>アプリケーションとデータに最適なリージョンを選択する

Azure を使用する利点の 1 つは、世界中のさまざまなデータセンターにアプリケーションをデプロイできるということです。 選択したリージョンによっては、アプリケーションのパフォーマンスが変わることがあります。 たとえば、大部分の顧客に近いリージョンを選択すると、ネットワーク要求における待機時間が少なくなります。 特定の国でアプリを配信するための法的要件を満たせるリージョンを選択することもあります。 アプリケーションをホストしているデータセンターと同じデータセンター、またはできるだけ近いデータセンターにアプリケーションのデータを格納することが、常にベスト プラクティスです。

### <a name="multi-region-apps"></a>マルチリージョン アプリ

可能性は低いのですが、自然災害やインターネット障害などの事象により、データ センター全体がオフラインになる可能性があります。 最大限の可用性を提供するため、重要なビジネス アプリケーションは複数のデータ センターでホストすることがベスト プラクティスです。 複数のリージョンを使用することで、ローカル ユーザーの待機時間が短縮され、アプリケーションの更新時に柔軟性が向上する可能性もあります。

Virtual Machine や App Service など、一部のサービスでは、[Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) を使用してリージョン間のフェールオーバーによるマルチリージョン サポートを可能にして、可用性の高いエンタープライズ アプリケーションをサポートできます。 例については、「[Azure 参照アーキテクチャ:高可用性を得るために複数の Azure リージョンで Web アプリケーションを実行する](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)」を参照してください。

>**使用する場合**:フェールオーバーとレプリケーションを利用するエンタープライズおよび高可用性アプリケーションがある場合。

## <a name="how-do-i-manage-my-applications-and-projects"></a>アプリケーションとプロジェクトを管理する方法

Azure には、プログラムと [Azure Portal ](https://portal.azure.com/)の両方で Azure リソース、アプリケーション、プロジェクトを作成および管理できるさまざまな機能があります。

### <a name="command-line-interfaces-and-powershell"></a>コマンド ライン インターフェイスと PowerShell

Azure には、Bash、ターミナル、コマンド プロンプト、またはコマンドライン ツールのいずれかを使用して、コマンド ラインからアプリケーションとサービスを管理する方法が 2 つあります。 通常、コマンド ラインからは、Azure Portal と同じタスクを実行できます。たとえば、仮想マシン、仮想ネットワーク、Web アプリ、他のサービスの作成や構成などを実行できます。

-   [Azure コマンド ライン インターフェイス (CLI)](../../xplat-cli-install.md):コマンド ラインから Azure サブスクリプションに接続し、Azure リソースに対して多様なタスクをプログラミングできます。

-   [Azure PowerShell](../../powershell-install-configure.md):Windows PowerShell を使用して Azure リソースを管理できるモジュールとコマンドレットのセットを提供します。

### <a name="azure-portal"></a>Azure ポータル

Azure Portal は Web ベースのアプリケーションであり、Azure のリソースやサービスの作成、管理、削除に利用できます。 Azure Portal は <https://portal.azure.com> にあります。 カスタマイズ可能なダッシュボード、Azure リソースを管理するツール、サブスクリプション設定と課金情報へのアクセス機能が含まれています。 詳細については、「[Microsoft Azure Portal の概要](../../azure-portal-overview.md)」を参照してください。

### <a name="rest-apis"></a>REST API

Azure は、Azure Portal UI をサポートする REST API のセットに基づいて構築されています。 これら REST API のほとんどは、任意のインターネット対応デバイスから Azure リソースとアプリケーションをプログラムでプロビジョニングおよび管理する場合にもサポートされます。 REST API ドキュメントの詳細については、「[Azure REST SDK Reference](https://docs.microsoft.com/rest/api/)」(Azure REST SDK リファレンス) を参照してください。

### <a name="apis"></a>API

REST API だけでなく、多くの Azure サービスでは、次の開発プラットフォーム用 SDK を含め、プラットフォーム固有の Azure SDK を使用して、アプリケーションのリソースをプログラムで管理できます。

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.JS](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](https://docs.microsoft.com/python/azure)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/go/azure)

[Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) や [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) などのサービスには、Web およびモバイル クライアント アプリからサービスにアクセスできるクライアント側 SDK があります。

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Azure 上でアプリを実行すると、多くの場合は複数の Azure サービスを利用します。これらのサービスはいずれも同じライフ サイクルに従い、1 つの論理ユニットとして考えることができます。 たとえば、Web アプリでは、Web Apps、SQL Database、Storage、Azure Cache for Redis、Azure Content Delivery Network サービスを使用する可能性があります。 [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) を使用すると、アプリケーション内の複数リソースを 1 つのグループとして操作できます。 これらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。

Azure Resource Manager では、関連するリソースを論理的にグループ化して管理できるだけでなく、関連するリソースのデプロイと構成をカスタマイズできるデプロイ機能があります。 たとえば、Resource Manager を使用すると、複数の仮想マシン、ロード バランサー、Azure SQL Database で構成されるアプリケーションを 1 つのユニットとしてデプロイおよび構成することができます。

このようなデプロイは、JSON 形式のドキュメントである Azure Resource Manager テンプレートを使用して開発できます。 テンプレートでは、スクリプトではなく宣言型テンプレートを使用してデプロイを定義し、アプリケーションを管理できます。 テンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。 たとえば、テンプレートを使用して、1 回のクリックでリポジトリのコードを Azure サービスのセットにデプロイするボタンを GitHub リポジトリに追加できます。

> **使用する場合**:REST API、Azure CLI、Azure PowerShell を使用してプログラムで管理できるテンプレートベースのデプロイがアプリに必要な場合は、Resource Manager テンプレートを使用します。
> 
> **作業開始**:テンプレートを初めて使用する場合は、「[Azure Resource Manager テンプレートの構造と構文の詳細](../../resource-group-authoring-templates.md)」を参照してください。

## <a name="understanding-accounts-subscriptions-and-billing"></a>アカウント、サブスクリプション、課金の概要

開発者として、コードについて詳しく説明し、できるだけ早くアプリケーションを実行してみましょう。 できるだけ簡単に Azure の作業を始められることが重要です。 そのために、Azure には[無料試用版](https://azure.microsoft.com/free/)が用意されています。 [Azure App Service](https://tryappservice.azure.com/) など、一部のサービスには "無料で試すことができる" 機能があり、アカウントを作成する必要すらありません。 アプリケーションのコーディングと Azure へのデプロイはおもしろい作業ですが、ユーザー アカウント、サブスクリプション、課金の観点から Azure のしくみを理解するために時間をかけることも重要です。

### <a name="what-is-an-azure-account"></a>Azure アカウントとは

Azure サブスクリプションの作成または使用には、Azure アカウントを持っている必要があります。 Azure アカウントは、Azure AD、または Azure AD から信頼されているディレクトリ (職場、学校組織など) の単なる ID です。 このような組織に属していない場合でも、Azure AD から信頼されている Microsoft アカウントを使用して、常にサブスクリプションを作成できます。 オンプレミス Windows Server Active Directory を Azure AD と統合する方法については、「[オンプレミスのディレクトリと Azure Active Directory の統合](../../active-directory/hybrid/whatis-hybrid-identity.md)」を参照してください。

すべての Azure サブスクリプションには、Azure AD インスタンスとの間に信頼関係があります。 つまり、ディレクトリを信頼してユーザー、サービス、デバイスを認証します。 複数のサブスクリプションが同じディレクトリを信頼できますが、1 つのサブスクリプションは 1 つのディレクトリだけを信頼します。 詳細については、「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

Azure AD では、個々の Azure アカウント ID (*ユーザー*とも呼ばれます) を定義できるだけでなく、*グループ*も定義できます。 ロールベースのアクセス制御 (RBAC) を使用して、サブスクリプション内のリソースへのアクセスを管理するには、ユーザー グループを作成することをお勧めします。 グループの作成の詳細については、「[Azure Active Directory でグループを作成し、メンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。 [PowerShell を使用](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md)してグループの作成と管理を行うこともできます。

### <a name="manage-your-subscriptions"></a>サブスクリプションを管理する

サブスクリプションは、Azure アカウントにリンクされている Azure サービスを論理的にグループ化したものです。 1 つの Azure アカウントに複数のサブスクリプションを含めることができます。 Azure サービスの課金は、サブスクリプションごとに行われます。 使用できるサブスクリプション オファーの種類別一覧については、「[Microsoft Azure オファーの詳細](https://azure.microsoft.com/support/legal/offer-details/)」を参照してください。 Azure サブスクリプションにはアカウント管理者とサービス管理者が割り当てられます。アカウント管理者にはサブスクリプションを制御する権限が与えられ、サービス管理者にはサブスクリプションに含まれるすべてのサービスを制御する権限が与えられます。 従来のサブスクリプション管理者の詳細については、「[Azure サブスクリプション管理者を追加または変更する](../../billing/billing-add-change-azure-subscription-administrator.md)」を参照してください。 管理者に加え、[ロールベースのアクセス制御 (RBAC)](../../role-based-access-control/overview.md) を使用して Azure リソースを細かく管理する権限が個々のアカウントに与えられます。

#### <a name="resource-groups"></a>リソース グループ

新しい Azure サービスをプロビジョニングする場合は、特定のサブスクリプションで実行します。 個々の Azure サービス (リソースとも呼ばれます) は、リソース グループのコンテキストで作成されます。 リソース グループを使用すると、アプリケーションのリソースのデプロイと管理が簡単になります。 リソース グループには、ユニットとして使用するアプリケーションのすべてのリソースを含める必要があります。 リソース グループ間や、異なるサブスクリプションとの間でも、リソースを移動できます。 リソースの移動の詳細については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../resource-group-move-resources.md)」を参照してください。

Azure Resource Explorer は、サブスクリプションで作成済みのリソースを視覚化するために適したツールです。 詳細については、「[Resource Manager REST API](../../resource-manager-resource-explorer.md)」を参照してください。

#### <a name="grant-access-to-resources"></a>リソースへのアクセス権を付与する

Azure リソースへのアクセス権を許可する場合、そのタスクの実行に必要最小限の特権をユーザーに付与することが常にベスト プラクティスです。

- **ロールベースのアクセス制御 (RBAC)**:Azure では、指定したスコープ (サブスクリプション、リソース グループ、または個々のリソース) でユーザー アカウント (プリンシパル) にアクセス権を付与することができます。 RBAC を使用すると、リソースのセットをリソース グループにデプロイし、特定のユーザーまたはグループにアクセス許可を付与できます。 また、対象のリソース グループに属するリソースにのみアクセスを制限することもできます。 仮想マシンや仮想ネットワークなど、1 つのリソースにアクセス権を付与することもできます。 アクセス権を付与するには、ロールをユーザー、グループ、またはサービス プリンシパルに割り当てます。 定義済みのロールが多数ありますが、独自のカスタム ロールを定義することもできます。 詳細については、「[ロールベースのアクセス制御 (RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。

  > **使用する場合**:ユーザーやグループに対する詳細なアクセス管理が必要な場合、またはユーザーをサブスクリプションの所有者にする必要がある場合に使用します。
  > 
  > **作業開始**:詳細については、「[RBAC と Azure portal を使用してアクセスを管理する](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

- **サービス プリンシパル オブジェクト**:ユーザー プリンシパルとグループへのアクセス権を付与するだけでなく、サービス プリンシパルに同じアクセス権を付与することができます。

  > **使用する場合**:Azure リソースの管理やアプリケーションのアクセス権付与をプログラムで行う場合。 詳細については、「[リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](../../active-directory/develop/howto-create-service-principal-portal.md)」を参照してください。

#### <a name="tags"></a>Tags

Azure Resource Manager を使用すると、カスタム タグを個々のリソースに割り当てることができます。 キーと値のペアであるタグは、課金または監視のためにリソースを整理する必要がある場合に便利です。 タグには、複数のリソース グループ全体のリソースを追跡する機能があります。 タグの割り当ては、ポータル、Azure Resource Manager テンプレート、またはプログラムで行うことができます。また、REST API、Azure CLI、または PowerShell を使用できます。 各リソースには複数のタグを割り当てることができます。 詳細については、「[タグを使用した Azure リソースの整理](../../resource-group-using-tags.md)」を参照してください。

### <a name="billing"></a>課金

オンプレミス コンピューティングからクラウドホスト型サービスに移行する場合、サービスの使用状況とそれに関連するコストの管理と見積もりは重要な懸案事項です。 毎月かかる新しいリソース コストを見積もることができる点が重要です。 また、現在の費用に基づいて、特定の月の課金額を予測できることも必要です。

#### <a name="get-resource-usage-data"></a>リソースの使用状況データを取得する

Azure には、Azure サブスクリプションのリソース使用状況とメタデータ情報にアクセスできる Billing REST API のセットが用意されています。 これらの Billing API を使用すると、Azure コストの予測と管理を適切に実行できるようになります。 費用の時間単位の増加を追跡して分析し、費用のアラートを作成し、現在の使用状況の傾向に基づいて今後の課金を予測することができます。

>**作業開始**:Billing API の使用の詳細については、「[Azure Billing API を使用して、Azure の使用状況に関する詳細なデータをプログラムによって取得する](../../billing-usage-rate-card-overview.md)」を参照してください。

#### <a name="predict-future-costs"></a>今後のコストを予測する

コストを事前に見積もることは困難ですが、Azure には、デプロイされているリソースのコストを見積もるときに使用できる[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)があります。 ポータルと Billing REST API で [課金] ブレードを使用し、現在の使用状況に基づいて、今後のコストを見積もることもできます。

>**作業開始**:「[Azure Billing API を使用して、Azure の使用状況に関する詳細なデータをプログラムによって取得する](../../billing-usage-rate-card-overview.md)」を参照してください。
