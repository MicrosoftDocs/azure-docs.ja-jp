---
title: Azure 開発者向けファースト ステップ ガイド | Microsoft Docs
description: この記事では、開発のニーズに対応するために Microsoft Azure プラットフォームの使用を検討している開発者に必要不可欠な情報を提供します。
author: ggailey777
ms.service: azure
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: 12b90bf709cb20bed844940570663a76052a540d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738941"
---
# <a name="get-started-guide-for-azure-developers"></a>Azure 開発者向けファースト ステップ ガイド

## <a name="what-is-azure"></a>Azure とは

Azure は、既存のアプリケーションをホストし、新しいアプリケーションの開発を簡易化することができる機能が一式そろったクラウド プラットフォームです。 Azure では、オンプレミスのアプリケーションを拡張することもできます。 Azure はアプリケーションの開発、テスト、デプロイ、管理に必要なクラウド サービスを統合しているだけでなく、クラウド コンピューティングの効率性も利用しています。

Azure でアプリケーションをホストすると、アプリケーションのデプロイを小規模から始めて、顧客の需要増大に合わせて規模を拡大することができます。 また、Azure では、異なるリージョン間のフェールオーバーも含めて、高可用性アプリケーションに必要な信頼性が提供されます。 [Azure Portal ](https://portal.azure.com)を使用すると、すべての Azure サービスを簡単に管理できます。 また、サービス固有の API とテンプレートを使用して、プログラムでサービスを管理することもできます。

このガイドは、Azure プラットフォームのアプリケーション開発者向け入門ガイドです。 初めて Azure で新しいアプリケーションを構築する場合、または既存のアプリケーションを Azure に移行する場合のガイダンスと手順を説明しています。

## <a name="where-do-i-start"></a>どこから始めるか

Azure が提供しているどのサービスでも、ソリューション アーキテクチャのサポートに必要なサービスをなかなか見つけ出せないことがあります。 ここでは、開発者が一般的に使用する Azure サービスを取り上げます。 すべての Azure サービスの一覧については、[Azure のドキュメント](../../index.yml)を参照してください。

まず、Azure でアプリケーションをホストする方法を決める必要があります。 インフラストラクチャ全体を仮想マシン (VM) として管理する必要はありますか? Azure が提供するプラットフォーム管理施設を使用できますか。 それとも、コードの実行をホストする用途でのみサーバーレス フレームワークが必要でしょうか。

アプリケーションにクラウド記憶域が必要なら、Azure には複数のオプションがあります。 Azure のエンタープライズ認証を利用することができます。 また、クラウドベースの開発および監視用のツールもあり、ほとんどのホスティング サービスでは DevOps 統合が提供されています。

それでは、アプリケーションに合わせて調査をお勧めするサービスをいくつか見てみましょう。

### <a name="application-hosting"></a>アプリケーションのホスティング

Azure は、アプリケーションの実行に利用できるクラウドベースのコンピューティング サービスをいくつか提供しています。そのため、インフラストラクチャの詳細について心配する必要はありません。 アプリケーションの使用量の増大に合わせてリソースのスケール アップまたはスケール アウトを簡単に実行できます。

Azure は、アプリケーション開発とホスティングのニーズをサポートするサービスを提供しています。 また、アプリケーション ホスティングを詳細に制御できるサービスとしてのインフラストラクチャ (IaaS) も提供しています。 Azure のサービスとしてのプラットフォーム (PaaS) オファリングは、アプリの強化に必要なフル マネージドのサービスを提供しています。 Azure には、本当の意味でのサーバーレス ホスティングもあります。このサービスを利用する場合、ユーザーに必要な作業はコードの作成のみです。

![Azure アプリケーション ホスティングのオプション](./media/azure-developer-guide/azure-developer-hosting-options.png)

#### <a name="azure-app-service"></a>Azure App Service

Web ベースのプロジェクトを最も短時間で公開できる方法が必要な場合は、Azure App Service をお勧めします。 App Service では、モバイル クライアントをサポートする Web アプリを拡張し、使用されている REST API を公開する作業も簡単です。 このプラットフォームには、ソーシャル プロバイダーを使用した認証、トラフィックベースの自動スケーリング、実稼働環境でのテスト、継続的かつコンテナーベースのデプロイの機能があります。

Web アプリ、モバイル アプリ バックエンド、および API アプリを作成できます。

上に示した 3 つのアプリケーションの種類は、いずれも App Service ランタイムを共有しているため、1 つのプロジェクトやソリューションから、Web サイトのホスト、モバイル クライアントのサポート、自作 API を Azure で公開することのすべてを行うことができます。 App Service の詳細については、「[Web Apps の概要](../../app-service/overview.md)」を参照してください。

App Service は DevOps を念頭に置いて設計されています。 発行と継続的インテグレーションのデプロイのためのさまざまなツールがサポートされています。 これらのツールには、GitHub Webhook、Jenkins、Azure DevOps、TeamCity などが含まれます。

[オンライン移行ツール](https://appmigration.microsoft.com/)を使用して、既存のアプリケーションを App Service に移行することができます。

> **いつ使用するか**: App Service を使用するのは、既存の Web アプリケーションを Azure に移行する場合、および Web アプリ用にフル マネージドのホスティング プラットフォームが必要な場合です。 また、モバイル クライアントをサポートする必要がある場合、またはアプリと共に REST API を公開する必要がある場合にも App Service を使用できます。
>
> **作業開始**: App Service を使用すると、初めての [Web アプリ](../../app-service/quickstart-dotnetcore.md)、[モバイル アプリ](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started)、[API アプリ](../../app-service/app-service-web-tutorial-rest-api.md)でも簡単に作成、デプロイできます。
>
> **今すぐ試す**:App Service なら、Azure アカウントを新規登録することなく、一時的なアプリをプロビジョニングしてプラットフォームを試すことができます。 プラットフォームと [Azure App Service アプリの作成](https://tryappservice.azure.com/)を試してみましょう。

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

サービスとしてのインフラストラクチャ (IaaS) プロバイダーの場合、Azure を使用すると、自社のアプリケーションを Windows または Linux VM にデプロイまたは移行することができます。 Azure Virtual Machines は、Azure Virtual Network と組み合わせると、Windows または Linux VM を Azure にデプロイすることもできます。 VM では、マシンの構成全体を制御できます。 VM を使用しているときは、すべてのサーバー ソフトウェアのインストール、構成、メンテナンス、オペレーティング システムの修正プログラムについては、お客様の責任となります。

VM の場合は細かいレベルで制御できるため、PaaS モデルに合わない幅広いサーバー ワークロードを Azure 上で実行できます。 たとえば、データベース サーバー、Windows Server Active Directory、Microsoft SharePoint などのワークロードが含まれます。 詳細については、[Linux](../../virtual-machines/index.yml) または [Windows](../../virtual-machines/index.yml) の Virtual Machines のドキュメントを参照してください。

> **いつ使用するか**: アプリケーション インフラストラクチャを完全に制御したい場合、またはオンプレミス アプリケーション ワークロードを変更せずに Azure に移行したい場合は、Virtual Machines を使用します。
>
> **作業開始**: [Linux VM](../../virtual-machines/linux/quick-create-portal.md) または [Windows VM](../../virtual-machines/windows/quick-create-portal.md) を Azure portal から作成します。

#### <a name="azure-functions-serverless"></a>Azure Functions (サーバーレス)

コードを実行するためにアプリケーション全体やインフラストラクチャを構築して管理する必要がなく、コードを記述するだけで、それをイベントやスケジュールに応じて実行できたとしたら、どうでしょうか。  [Azure Functions](../../azure-functions/functions-overview.md) は、必要なコードを作成するだけで済む "サーバーレス" スタイルのサービスです。 Functions を使用すると、コードの実行を HTTP 要求、Webhook、クラウド サービス イベント、またはスケジュールに応じてトリガーすることができます。 C\#、F\#、Node.js、Python、PHP など、好きな言語でコードを開発することもできます。 使用量ベースの課金の場合、コードの実行時にのみ課金されます。また、必要に応じて Azure は拡大縮小されます。

> **いつ使用するか**: 他の Azure サービス、Web ベースのイベント、またはスケジュールに応じてトリガーされるコードがある場合に、Azure Functions を使用します。 また、完全なホスト型プロジェクトのオーバーヘッドが必要ない場合、またはコードの実行時にのみ料金を支払いたい場合にも Functions を使用できます。 詳細については、「[Azure Functions の作業開始](../../azure-functions/functions-overview.md)」を参照してください。
>
> **作業開始**: ポータルから [初めて関数を作成する](../../azure-functions/functions-get-started.md)には、Functions のクイックスタート チュートリアルに従います。
>
> **今すぐ試す**:Azure Functions を使用すると、Azure アカウントを新規登録することなくコードを実行できます。 今すぐ試して[初めての Azure 関数を作成](https://tryappservice.azure.com/)しましょう。

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric は、分散システム プラットフォームです。 このプラットフォームにより、スケーラブルで信頼性に優れたマイクロサービスの構築、パッケージ化、デプロイ、管理が簡単になります。 また、次のような包括的なアプリケーション管理機能も提供されます。

* プロビジョニング
* デプロイ中
* 監視
* アップグレードおよび修正プログラム
* 削除中

共有プールのマシン上で動作するアプリは、小規模から開始し、必要に応じて数百または数千ものコンピューターの規模まで拡張することができます。

Service Fabric は、Open Web Interface for .NET (OWIN) と ASP.NET Core を使用した Web API をサポートします。 また、.NET Core と Java の両方で Linux 上のサービスを構築するための SDK を提供しています。 Service Fabric の詳細については、「[Service Fabric のドキュメント](../../service-fabric/index.yml)」を参照してください。

> **使用する場合:** マイクロサービス アーキテクチャを使用するアプリケーションを作成する場合、または既存のアプリケーションを書き換える場合は、Service Fabric がお勧めです。 基になるインフラストラクチャをより細かく制御する場合、または直接アクセスする場合は、Service Fabric を使用します。
>
> **概要:** [最初の Azure Service Fabric アプリケーションを作成します](../../service-fabric/service-fabric-tutorial-create-dotnet-app.md)。

#### <a name="azure-spring-cloud"></a>Azure Spring Cloud

Azure Spring Cloud は、クラウドでのアプリケーションのビルド、デプロイ、スケーリング、監視を可能にする、サーバーレスのマイクロサービス プラットフォームです。 Spring Cloud を使用して、最新のマイクロサービス パターンを Spring Boot アプリに適用し、スケルトン コードを排除して、堅牢な Java アプリを迅速に作成します。

* 管理されたバージョンの Spring Cloud Service Discovery および Config Server を活用しつつ、重要なコンポーネントが最適な条件下で実行されるようにします。
* お客様はビジネス ロジックの構築に専念して、Microsoft はセキュリティ パッチ、コンプライアンス標準、高可用性によりサービス ランタイムを管理します。
* アプリケーションのライフサイクル (デプロイ、起動、停止、スケーリングなど) を、Azure Kubernetes Service により管理します。
* アプリと、Azure Database for MySQL や Azure Cache for Redis などの Azure サービスとの間の接続を簡単にバインドします。
* アプリケーション依存関係と運用のテレメトリに関する詳細な分析情報を提供するエンタープライズ レベルの統合監視ツールを使用して、マイクロサービスとアプリケーションの監視およびトラブルシューティングを行います。

> **使用する場合:** Azure で Spring Boot または Spring Cloud ベースのマイクロサービスを実行する運用コストを最小限に抑える場合は、フル マネージドサービスとしての Azure Spring Cloud がお勧めです。 
>
> **概要:** [初めての Azure Spring Cloud アプリケーションをデプロイする](../../spring-cloud/spring-cloud-quickstart.md)


### <a name="enhance-your-applications-with-azure-services"></a>Azure サービスでアプリケーションを強化する

Azure には、アプリケーションのホスティングと共に、機能を拡張できるサービス オファリングが用意されています。 Azure では、クラウドとオンプレミスの両方において、アプリケーションの開発と保守を向上させることもできます。

#### <a name="hosted-storage-and-data-access"></a>ホストされる記憶域とデータ アクセス

ほとんどのアプリケーションはデータを格納する必要があるため、Azure でアプリケーションをホストする方法の選択にかかわらず、次のストレージとデータ サービスの 1 つまたは複数を検討してください。

* **Azure Cosmos DB**:グローバル分散型のマルチモデル データベース サービス。 このデータベースでは、包括的な SLA により、任意の数の地理的リージョン間でスループットとストレージを弾力的にスケーリングできるようになります。

  > **使用する場合:** アプリケーションに、明確に定義された複数の整合性モデルのドキュメント、テーブル、またはグラフ データベース (MongoDB データベースを含む) が必要な場合。
  >
  > **作業開始**: [Azure Cosmos DB Web アプリをビルドします](../../cosmos-db/create-sql-api-dotnet.md)。 MongoDB 開発者の場合は、[Azure Cosmos DB を使用した MongoDB Web アプリの構築](../../cosmos-db/create-mongodb-dotnet.md)に関する記事をご覧ください。

* **Azure Storage**:BLOB、クエリ、ファイルなどの非リレーショナル データに対して、耐久性があり、高可用な記憶域を提供します。 Storage は、VM 向けに記憶域の基盤を提供します。

  > **いつ使用するか**: キーと値のペア (テーブル)、BLOB、ファイル共有、メッセージ (キュー) など、非リレーショナル データを格納するアプリケーションの場合。
  >
  > **作業開始**: [BLOB](../../storage/blobs/storage-quickstart-blobs-dotnet.md)、[テーブル](../../cosmos-db/tutorial-develop-table-dotnet.md)、[クエリ](../../storage/queues/storage-dotnet-how-to-use-queues.md)、または [ファイル](../../storage/files/storage-dotnet-how-to-use-files.md)のいずれかのストレージの種類から選択します。

* **Azure SQL Database**:クラウドにリレーショナル表形式データを格納する Azure ベース バージョンの Microsoft SQL Server エンジンです。 SQL Database は、予測可能なパフォーマンス、ダウンタイムなしのスケーラビリティ、ビジネス継続性、データ保護を提供しています。

  > **いつ使用するか**: 参照整合性、トランザクションのサポート、および TSQL クエリのサポートがあるデータ記憶域が必要なアプリケーションの場合。
  >
  > **作業開始**: [Azure portal を使用して、数分で Azure SQL Database でデータベースを作成します](../../azure-sql/database/single-database-create-quickstart.md)。


[Azure Data Factory](../../data-factory/introduction.md) を使用して既存のオンプレミス データを Azure に移行することができます。 データをクラウドに移動する準備ができていない場合は、Azure App Service の[ハイブリッド接続](../../app-service/app-service-hybrid-connections.md)を使用して、App Service でホストされているアプリをオンプレミス リソースに接続できます。 また、オンプレミス アプリケーションから Azure データと記憶域サービスに接続することもできます。

#### <a name="docker-support"></a>Docker サポート

OS 仮想化の形式の 1 つである Docker コンテナーを使用すると、より効率的で予測可能な方法でアプリケーションをデプロイできます。 コンテナー化されたアプリケーションは、開発およびテスト システム上と同様に実稼働環境でも動作します。 コンテナーは、標準の Docker ツールを使用して管理できます。 コンテナーベースのアプリケーションは、既存のスキルと人気のあるオープンソース ツールを使用して Azure でデプロイおよび管理できます。

Azure には、アプリケーションでコンテナーを使用する方法がいくつか用意されています。


* **Azure Kubernetes Service**:コンテナー化されたアプリケーションを実行するように事前構成されている仮想マシンのクラスターを簡単に作成、構成および管理できます。 Azure Kubernetes Service の詳細については、[Azure Kubernetes Service の概要](../../aks/intro-kubernetes.md)に関する記事を参照してください。

  > **いつ使用するか**: 追加のスケジュール設定および管理ツールが提供される、運用に対応したスケーラブルな環境を作成する必要がある場合、または Docker Swarm クラスターをデプロイする場合。
  >
  > **作業開始**: [Kubernetes Service クラスター](../../aks/tutorial-kubernetes-deploy-cluster.md)をデプロイします。

* **Docker Machine**:docker-machine コマンドを使用して、仮想ホスト上で Docker Engine をインストールおよび管理できます。

  >**いつ使用するか**: 1 つの Docker ホストを作成して、アプリのプロトタイプを短時間で作成する必要がある場合。

* **App Service 用のカスタム Docker イメージ**:Linux 上に Web アプリをデプロイするときに、コンテナー レジストリまたは顧客のコンテナーから Docker コンテナーを使用できます。

  > **いつ使用するか**: Linux 上の Web アプリを Docker イメージにデプロイする場合。
  >
  > **作業開始**: [Linux で App Service 用のカスタム Docker イメージを使用します](../../app-service/quickstart-custom-container.md?pivots=platform-linux%253fpivots%253dplatform-linux)。

### <a name="authentication"></a>認証

アプリケーションの使用者を把握することだけでなく、リソースへの不正アクセスを防止することも重要です。 Azure には、アプリ クライアントを認証する方法がいくつか用意されています。

* **Azure Active Directory (Azure AD)** :Microsoft のマルチテナントでクラウドベースの ID およびアクセス管理サービスです。 Azure AD と統合することで、シングル サインオン (SSO) 機能をアプリケーションに追加できます。 ディレクトリのプロパティには、Azure AD Graph API から直接、または Microsoft Graph API からアクセスできます。 OAuth 2.0 認証フレームワークと Open ID Connect の場合、ネイティブ HTTP/REST エンドポイントとマルチプラットフォーム Azure AD Authentication ライブラリを使用して、Azure AD のサポートと統合できます。

  > **いつ使用するか**: SSO エクスペリエンスを提供する場合、Graph ベースのデータを使用する場合、またはドメインベースのユーザーを認証する場合。
  >
  > **作業開始**: 詳細については、「[開発者のための Azure Active Directory](../../active-directory/develop/v2-overview.md)」を参照してください。

* **App Service 認証**:App Service を選択してアプリをホストする場合、ソーシャル ID プロバイダー (Facebook、Google、Microsoft、Twitter など) と共に、Azure AD の組み込み認証サポートも利用できます。

  > **いつ使用するか**: Azure AD、ソーシャル ID プロバイダー、またはその両方を使用して App Service アプリで認証を有効にする場合。
  >
  > **作業開始**: App Service の認証の詳細については、「[Azure App Service での認証および承認](../../app-service/overview-authentication-authorization.md)」を参照してください。

Azure でのセキュリティのベスト プラクティスについては、「[Azure セキュリティのベスト プラクティスとパターン](../../security/fundamentals/best-practices-and-patterns.md)」を参照してください。

### <a name="monitoring"></a>監視

Azure でアプリケーションを起動し、実行する場合、パフォーマンスを監視し、イシューを観察し、ユーザーがアプリを使用する方法に目を配る必要があります。 Azure には、いくつかの監視オプションがあります。

* **Application Insights**:Visual Studio と統合してライブ Web アプリケーションを監視する、Azure でホストされる拡張可能な分析サービスです。 アプリのパフォーマンスと使いやすさを継続的に改善するために必要なデータを入手できます。 この改善は、Azure でアプリケーションをホストするかどうかにかかわらず行われます。

  > **作業開始**: [Application Insights](../../azure-monitor/app/app-insights-overview.md) のチュートリアルに従ってください。

* **Azure Monitor**:Azure インフラストラクチャとリソースから生成されるメトリックとログに対して、視覚化、クエリ、ルート、アーカイブ、および処理を実行できるサービスです。 Monitor は、Azure リソースを監視できる単一のソースで、Azure portal に表示されるデータ ビューを提供します。

  > **作業開始**: 「[Azure Monitor の概要](../../azure-monitor/overview.md)」をご覧ください。

### <a name="devops-integration"></a>DevOps 統合

VM をプロビジョニングするか、継続的インテグレーションによって Web アプリを発行するかにかかわらず、Azure は人気のある DevOps ツールの多くと統合できます。 次のような既に所有しているツールを利用し、経験を最大限に活用することができます。

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **作業開始**: App Service アプリの DevOps オプションの詳細については、「[Azure App Service への継続的なデプロイ](../../app-service/deploy-continuous-deployment.md)」を参照してください。
>
> **今すぐ試す:** [DevOps 統合のいくつかを試しましょう](https://azure.microsoft.com/try/devops/)。

## <a name="azure-regions"></a>Azure Azure リージョン

Azure は、世界中のさまざまな地域で一般的に利用できるグローバル クラウド プラットフォームです。 Azure でサービス、アプリケーション、VM をプロビジョニングするとき、リージョンを選択するように求められます。 このリージョンは、アプリケーションが実行される、またはデータが格納される特定のデータセンターに相当します。 これらのリージョンは特定の場所に対応しており、「[Azure リージョン](https://azure.microsoft.com/regions/)」ページに公開されています。

### <a name="choose-the-best-region-for-your-application-and-data"></a>アプリケーションとデータに最適なリージョンを選択する

Azure を使用する利点の 1 つは、世界中のさまざまなデータセンターにアプリケーションをデプロイできるということです。 選択したリージョンによっては、アプリケーションのパフォーマンスが変わることがあります。 たとえば、大部分の顧客に近いリージョンを選択すると、ネットワーク要求における待機時間が少なくなります。 特定の国や地域でアプリを配信するための法的要件を満たせるリージョンを選択することもあります。 アプリケーションをホストしているデータセンターと同じデータセンター、またはできるだけ近いデータセンターにアプリケーションのデータを格納することが、常にベスト プラクティスです。

### <a name="multi-region-apps"></a>マルチリージョン アプリ

可能性は低いものの、自然災害やインターネット障害などの事象によってデータ センター全体がオフラインになる場合があります。 最大限の可用性を提供するため、ベスト プラクティスとして、重要なビジネス アプリケーションは複数のデータ センターでホストすることをお勧めします。 複数のリージョンを使用することで、ローカル ユーザーの待機時間が短縮され、アプリケーションの更新時に柔軟性が向上する可能性もあります。

Virtual Machine や App Service など、一部のサービスでは、[Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) を使用してリージョン間のフェールオーバーによるマルチリージョン サポートを可能にして、可用性の高いエンタープライズ アプリケーションをサポートできます。 例については、「[Azure 参照アーキテクチャ:高可用性を得るために複数の Azure リージョンで Web アプリケーションを実行する](/azure/architecture/reference-architectures/app-service-web-app/multi-region)」を参照してください。

>**いつ使用するか**: フェールオーバーとレプリケーションを利用するエンタープライズおよび高可用性アプリケーションがある場合。

## <a name="how-do-i-manage-my-applications-and-projects"></a>アプリケーションとプロジェクトを管理する方法

Azure には、プログラムと [Azure Portal ](https://portal.azure.com/)の両方で Azure リソース、アプリケーション、プロジェクトを作成および管理できるさまざまな機能があります。

### <a name="command-line-interfaces-and-powershell"></a>コマンド ライン インターフェイスと PowerShell

Azure には、コマンドラインからアプリケーションとサービスを管理する 2 つの方法が用意されています。 Bash、ターミナル、コマンド プロンプト、または選択したコマンドライン ツールなどのツールを使用できます。 通常、コマンド ラインからは、Azure portal と同じタスクを実行できます。たとえば、仮想マシン、仮想ネットワーク、Web アプリ、他のサービスの作成や構成などを実行できます。

* [Azure コマンド ライン インターフェイス (CLI)](/cli/azure/install-azure-cli):コマンド ラインから Azure サブスクリプションに接続し、Azure リソースに対して多様なタスクをプログラミングできます。

* [Azure PowerShell](/powershell/azure/):Windows PowerShell を使用して Azure リソースを管理できるモジュールとコマンドレットのセットを提供します。

### <a name="azure-portal"></a>Azure portal

[Azure portal](https://portal.azure.com) は、Web ベースのアプリケーションです。 Azure portal を使用して Azure のリソースやサービスの作成、管理、削除ができます。 次の情報が含まれます。

* 構成可能なダッシュボード
* Azure リソース管理ツール
* サブスクリプション設定と課金情報へのアクセス。 詳細については、「[Microsoft Azure Portal の概要](https://azure.microsoft.com/features/azure-portal/)」を参照してください。

### <a name="rest-apis"></a>REST API

Azure は、Azure Portal UI をサポートする REST API のセットに基づいて構築されています。 これら REST API のほとんどは、任意のインターネット対応デバイスから Azure リソースとアプリケーションをプログラムでプロビジョニングおよび管理する場合にもサポートされます。 REST API ドキュメントの詳細については、「[Azure REST SDK Reference](/rest/api/)」(Azure REST SDK リファレンス) を参照してください。

### <a name="apis"></a>API

REST API だけでなく、多くの Azure サービスでも、次の開発プラットフォーム用 SDK を含め、プラットフォーム固有の Azure SDK を使用して、アプリケーションのリソースをプログラムで管理できます。

* [.NET](/dotnet/api/)
* [Node.js](/azure/developer/javascript/)
* [Java](/java/azure)
* [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
* [Python](/azure/python/)
* [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
* [Go](/azure/go)

[Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) や [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) などのサービスには、Web およびモバイル クライアント アプリからサービスにアクセスできるクライアント側 SDK があります。

### <a name="azure-resource-manager"></a>Azure Resource Manager

Azure でのアプリの実行には、複数の Azure サービスの使用を伴う可能性があります。 これらのサービスは同じライフサイクルに従うため、論理ユニットと考えることができます。 たとえば、Web アプリでは、Web Apps、SQL Database、Storage、Azure Cache for Redis、Azure Content Delivery Network サービスを使用する可能性があります。 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) を使用すると、アプリケーション内の複数リソースを 1 つのグループとして操作できます。 これらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。

Azure Resource Manager では、関連するリソースを論理的にグループ化して管理できるだけでなく、関連するリソースのデプロイと構成をカスタマイズできるデプロイ機能があります。 たとえば、Resource Manager を使用してアプリケーションをデプロイし、構成することができます。 このアプリケーションでは、複数の仮想マシン、ロードバランサー、Azure SQL Database のデータベースを、1 つのユニットとして構成できます。

このようなデプロイは、JSON 形式のドキュメントである Azure Resource Manager テンプレートを使用して開発できます。 テンプレートでは、スクリプトではなく宣言型テンプレートを使用してデプロイを定義し、アプリケーションを管理できます。 テンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。 たとえば、テンプレートを使用して、1 回のクリックでリポジトリのコードを Azure サービスのセットにデプロイするボタンを GitHub リポジトリに追加できます。

> **いつ使用するか**: REST API、Azure CLI、Azure PowerShell を使用してプログラムで管理できるテンプレートベースのデプロイがアプリに必要な場合は、Resource Manager テンプレートを使用します。
>
> **作業開始**: テンプレートを初めて使用する場合は、「[Azure Resource Manager テンプレートの構造と構文の詳細](../../azure-resource-manager/templates/template-syntax.md)」を参照してください。

## <a name="understanding-accounts-subscriptions-and-billing"></a>アカウント、サブスクリプション、課金の概要

開発者として、コードについて詳しく説明し、できるだけ早くアプリケーションを実行してみましょう。 できるだけ簡単に Azure の作業を始められることが重要です。 そのために、Azure には[無料試用版](https://azure.microsoft.com/free/)が用意されています。 [Azure App Service](https://tryappservice.azure.com/) など、一部のサービスには "無料で試すことができる" 機能があり、アカウントを作成する必要すらありません。 アプリケーションのコーディングと Azure へのデプロイはおもしろい作業ですが、Azure のしくみを理解するために時間をかけることも重要です。 特に、ユーザー アカウント、サブスクリプション、課金の観点からしくみを理解する必要があります。

### <a name="what-is-an-azure-account"></a>Azure アカウントとは

Azure サブスクリプションの作成または使用には、Azure アカウントを持っている必要があります。 Azure アカウントは、Azure AD、または Azure AD から信頼されているディレクトリ (職場、学校組織など) の単なる ID です。 このような組織に属していない場合でも、Azure AD から信頼されている Microsoft アカウントを使用して、常にサブスクリプションを作成できます。 オンプレミス Windows Server Active Directory を Azure AD と統合する方法については、「[オンプレミスのディレクトリと Azure Active Directory の統合](../../active-directory/hybrid/whatis-hybrid-identity.md)」を参照してください。

すべての Azure サブスクリプションには、Azure AD インスタンスとの間に信頼関係があります。 つまり、ディレクトリを信頼してユーザー、サービス、デバイスを認証します。 複数のサブスクリプションが同じディレクトリを信頼できますが、1 つのサブスクリプションは 1 つのディレクトリだけを信頼します。 詳細については、「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

Azure AD では、個々の Azure アカウント ID (*ユーザー* とも呼ばれます) を定義できるだけでなく、*グループ* も定義できます。 ロールベースのアクセス制御 (RBAC) を使用して、サブスクリプション内のリソースへのアクセスを管理するには、ユーザー グループを作成することをお勧めします。 グループの作成の詳細については、「[Azure Active Directory でグループを作成し、メンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。 [PowerShell を使用](../../active-directory/enterprise-users/groups-settings-v2-cmdlets.md)してグループの作成と管理を行うこともできます。

### <a name="manage-your-subscriptions"></a>サブスクリプションを管理する

サブスクリプションは、Azure アカウントにリンクされている Azure サービスを論理的にグループ化したものです。 1 つの Azure アカウントに複数のサブスクリプションを含めることができます。 Azure サービスの課金は、サブスクリプションごとに行われます。 使用できるサブスクリプション オファーの種類別一覧については、「[Microsoft Azure オファーの詳細](https://azure.microsoft.com/support/legal/offer-details/)」を参照してください。 Azure サブスクリプションには、サブスクリプションを完全に制御できるアカウント管理者がいます。 また、サブスクリプション内のすべてのサービスを制御できるサービス管理者もいます。 従来のサブスクリプション管理者の詳細については、「[Azure サブスクリプション管理者を追加または変更する](../../cost-management-billing/manage/add-change-subscription-administrator.md)」を参照してください。 個々のアカウントに、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を使用して Azure リソースを詳細に制御する権限を与えることができます。

#### <a name="resource-groups"></a>リソース グループ

新しい Azure サービスをプロビジョニングする場合は、特定のサブスクリプションで実行します。 個々の Azure サービス (リソースとも呼ばれます) は、リソース グループのコンテキストで作成されます。 リソース グループを使用すると、アプリケーションのリソースのデプロイと管理が簡単になります。 リソース グループには、ユニットとして使用するアプリケーションのすべてのリソースを含める必要があります。 リソース グループ間や、異なるサブスクリプションとの間でも、リソースを移動できます。 リソースの移動の詳細については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。

Azure Resource Explorer は、サブスクリプションで作成済みのリソースを視覚化するために適したツールです。 詳細については、「[Resource Manager REST API](/rest/api/)」を参照してください。

#### <a name="grant-access-to-resources"></a>リソースへのアクセス権を付与する

Azure リソースへのアクセスを許可する場合は、ベスト プラクティスとして、常に、特定のタスクの実行に必要な最小限の特権をユーザーに付与することをお勧めします。

* **Azure ロールベースのアクセス制御 (Azure RBAC)** : Azure では、指定したスコープ (サブスクリプション、リソース グループ、または個々のリソース) でユーザー アカウント (プリンシパル) にアクセス権を付与することができます。 Azure RBAC を使用すると、リソースをリソース グループにデプロイし、特定のユーザーまたはグループにアクセス許可を付与できます。 また、対象のリソース グループに属するリソースにのみアクセスを制限することもできます。 仮想マシンや仮想ネットワークなど、1 つのリソースにアクセス権を付与することもできます。 アクセス権を付与するには、ロールをユーザー、グループ、またはサービス プリンシパルに割り当てます。 定義済みのロールが多数ありますが、独自のカスタム ロールを定義することもできます。 詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。

  > **いつ使用するか**: ユーザーやグループに対する詳細なアクセス管理が必要な場合、またはユーザーをサブスクリプションの所有者にする必要がある場合に使用します。
  >
  > **作業開始**: 詳細については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

- **サービス プリンシパル オブジェクト**:ユーザー プリンシパルとグループへのアクセス権を付与するだけでなく、サービス プリンシパルに同じアクセス権を付与することができます。

  > **いつ使用するか**: Azure リソースの管理やアプリケーションのアクセス権付与をプログラムで行う場合。 詳細については、「[リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](../../active-directory/develop/howto-create-service-principal-portal.md)」を参照してください。

#### <a name="tags"></a>Tags

Azure Resource Manager を使用すると、カスタム タグを個々のリソースに割り当てることができます。 キーと値のペアであるタグは、課金または監視のためにリソースを整理する必要がある場合に便利です。 タグには、複数のリソース グループ全体のリソースを追跡する機能があります。 タグは、次の方法で割り当てることができます。

* ポータルで
* Azure Resource Manager のテンプレートで
* REST API の使用
* Azure CLI の使用
* PowerShell の使用

各リソースには複数のタグを割り当てることができます。 詳細については、「[タグを使用した Azure リソースの整理](../../azure-resource-manager/management/tag-resources.md)」を参照してください。

### <a name="billing"></a>課金

オンプレミス コンピューティングからクラウドホスト型サービスに移行する場合、サービスの使用状況とそれに関連するコストの管理と見積もりは重要な懸案事項です。 新しいリソースを実行するコストを月単位で見積もることが重要です。 また、現在の支出に基づいて、特定の月の課金額を予測することもできます。

#### <a name="get-resource-usage-data"></a>リソースの使用状況データを取得する

Azure には、Azure サブスクリプションのリソース使用状況とメタデータ情報にアクセスできる Billing REST API のセットが用意されています。 これらの Billing API を使用すると、Azure コストの予測と管理を適切に実行できるようになります。 支出を 1 時間単位で追跡して分析し、支出アラートを作成できます。 現在の使用傾向に基づいて将来の課金額を予測することもできます。

>**作業開始**: Billing API の使用については、「[Azure Consumption API の概要](../../cost-management-billing/manage/consumption-api-overview.md)」を参照してください。

#### <a name="predict-future-costs"></a>今後のコストを予測する

事前にコストを見積もることは困難ですが、Azure には便利なツールが用意されています。 デプロイされたリソースのコストを見積もるための、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)が用意されています。 ポータルと Billing REST API で課金リソースを使用し、現在の使用状況に基づいて、今後のコストを見積もることもできます。

>**作業開始**: 「[Azure Consumption API の概要](../../cost-management-billing/manage/consumption-api-overview.md)」を参照してください。