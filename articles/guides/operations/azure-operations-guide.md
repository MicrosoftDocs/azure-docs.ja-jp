---
title: Azure IT オペレーター向けファースト ステップ ガイド | Microsoft Docs
description: Azure IT オペレーター向けファースト ステップ ガイド
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 1eb0bf2c0e214c3e076895903176d15ee792c70c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093049"
---
# <a name="get-started-for-azure-it-operators"></a>Azure IT オペレーター向けのファースト ステップ

このガイドでは、Microsoft Azure インフラストラクチャのデプロイと管理に関する、中核となる概念をご紹介します。 クラウド コンピューティング、あるいは Azure 自体を使用するのが初めての場合は、このガイドを利用すれば、概念、デプロイ、管理詳細を短時間で学習できます。 このガイドのさまざまなセクションで、仮想マシンのデプロイなどの操作について説明し、さらに詳細な技術情報へのリンクを掲載しています。

## <a name="cloud-computing-overview"></a>クラウド コンピューティングの概要

クラウド コンピューティングは、従来のオンプレミス データセンターの代替となる最新技術です。 パブリック クラウド ベンダーがあらゆるコンピューティング インフラストラクチャと基礎となる管理ソフトウェアを提供します。 ベンダーは幅広いクラウド サービスを提供しています。 この場合のクラウド サービスには、仮想マシン、Web サーバー、クラウドホステッド データベース エンジンがあります。 クラウド プロバイダーの利用者は、必要に応じてクラウド サービスをリースします。 この方法で、ハードウェアの保守管理にかかる資本的支出を運用費に変換します。 クラウド サービスのその他の長所:

- 大規模コンピューティング環境の迅速なデプロイ

- 不要になったシステムの迅速な割り当て解除

- ロード バランサーなど、従来の複雑なシステムの簡単なデプロイ

- 計算処理能力を柔軟に提供し、必要に応じて拡大縮小する機能

- 対費用効果に優れたコンピューティング環境

- Web ベースのポータルやプログラミングによる自動化により、どこからでもアクセスが可能

- コンピューティングとアプリケーションに関するほとんどのニーズに対応するクラウドベースのサービス

オンプレミス インフラストラクチャでは、デプロイされているハードウェアやソフトウェアを完全に制御できます。 そのため、拡張を念頭にハードウェアの調達を決定する必要があります。 たとえば、ピーク時のパフォーマンス ニーズに合わせ、コア数の多いサーバーを購入します。 残念ながら、このインフラストラクチャは、必要とされない部分があれば十分に活用されません。 Azure の場合、必要なインフラストラクチャのみをデプロイし、いつでも増やしたり、減らしたりできます。 そのため、追加のコンピューティング ノードをデプロイすることでパフォーマンス ニーズを満たす、という作業が拡張の中心となります。 クラウド サービスの追加による拡張は、高価なハードウェアの購入による拡張より対費用効果に優れています。

Microsoft はたくさんの Azure データセンターを世界中にデプロイしており、今後もさらに追加する予定です。 また、Microsoft は、中国やドイツなどの地域で、独立したクラウドを増やしています。 このようにデータセンターをデプロイできるのはグローバルな大企業だけです。Azure を利用することで、あらゆる規模の企業が自社のサービスを顧客の近くに簡単にデプロイできます。

小規模な事業では Azure を低コストのエントリ ポイントで使用できます。さらに多くの計算処理能力が必要になったら、すぐに拡張できます。 インフラストラクチャに大規模な先行投資をする必要がなくなります。必要に応じてシステムを柔軟に設計したり、設計をやり直したりできます。 クラウド コンピューティングは、ベンチャー企業の成長モデルである「Scale-Fast Fail-Fast (早く展開し、フェールファストを積み重ねて成長する)」に最適です。

Azure リージョンに関する詳細については、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。

### <a name="cloud-computing-model"></a>クラウド コンピューティング モデル

Azure では、顧客に提供されるサービスのカテゴリに基づいたクラウド コンピューティング モデルを使用します。 サービス の 3 つのカテゴリとして、サービスとしてのインフラストラクチャ (IaaS)、サービスとしてのプラットフォーム (PaaS)、およびサービスとしてのソフトウェア (SaaS) があります。 ベンダーは、これらの各カテゴリのコンピューティング スタック内にあるコンポーネントに対して、一部またはすべての責任を共有します。 クラウド コンピューティングの各カテゴリを確認しましょう。
![クラウド コンピューティング スタックの比較](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS:サービスとしてのインフラストラクチャ

IaaS クラウド ベンダーがあらゆる物理的コンピューティング リソースとコンピューターの仮想化に必要なソフトウェアを実行し、管理します。 このサービスの利用者は、ホステッド データセンターに仮想マシンをデプロイします。 仮想マシンは離れた場所のデータセンターに置かれますが、IaaS コンシューマーは、基になるインフラストラクチャをクラウド ベンダーに配置したままのオペレーティング システムを自由に構成し、管理できます。

Azure に含まれる IaaS ソリューションには、仮想マシン、仮想マシン スケール セット、関連ネットワーキング インフラストラクチャがあります。 "リフトとシフト" 移行モデルが可能なため、Azure に初めてサービスを移行するときに仮想マシンは人気の選択肢となっています。 データセンターでサービスを現在実行しているインフラストラクチャに似せた VM を構成し、ソフトウェアを新しい VM に移行できます。 場合によっては、URL を他のサービスやストレージに変更するなど、構成を更新する必要がありますが、たくさんのアプリケーションをこの方法で移行できます。

仮想マシン スケール セットは Azure Virtual Machines 上に構築されます。同一の VM のクラスターを簡単にデプロイできます。 仮想マシン スケール セットは自動拡張にも対応しています。必要になったとき、新しい VM を自動的にデプロイできます。 Azure Service Fabric や Azure Container Service など、高いレベルのマイクロサービス コンピューティング クラスターをホストするプラットフォームとして仮想マシン スケール セットは理想的です。

#### <a name="paas-platform-as-a-service"></a>PaaS:サービスとしてのプラットフォーム

PaaS を利用する場合、クラウド サービス ベンダーが提供する環境にアプリケーションをデプロイします。 ベンダーがインフラストラクチャ管理のすべてを行うので、利用者はアプリケーション開発とデータ管理に集中できます。

Azure で提供される PaaS コンピューティング サービスには、Azure App Service と Azure Cloud Services の Web Apps 機能があります (web ロールと worker ロール)。 いずれの場合も、開発者はサポート技術の基礎について何も知らなくても自分のアプリケーションを複数の方法でデプロイできます。 開発者は仮想マシン (VM) を作成したり、リモート デスクトップ プロトコル (RDP) を利用したりしなくてもいずれにもサインインできるし、アプリケーションをインストールできます。 ボタンを押すだけで (あるいは、同様な簡単な操作で)、Microsoft 提供のツールが VM をプロビジョニングし、それにアプリケーションをデプロイし、インストールします。

#### <a name="saas-software-as-a-service"></a>SaaS:サービスとしてのソフトウェア

SaaS は、1 か所でまとめてホストし、管理するソフトウェアです。 通常、マルチテナント アーキテクチャを基盤とします。すべての顧客に対して 1 つのバージョンのアプリケーションが利用されます。 複数のインスタンスに拡張することで、すべての場所で最良のパフォーマンスが得られます。 SaaS ソフトウェアは通常、月間または年間サブスクリプションでライセンス供与されます。 SaaS ソフトウェア ベンダーは、ソフトウェア スタックのすべてのコンポーネントに対して責任を担うため、利用者が管理するのは提供されているサービスだけです。

SaaS サービスの最もわかりやすい例が Microsoft 365 です。 サブスクライバーは月間または年間サブスクリプションの利用料を支払い、Microsoft Exchange、Microsoft OneDrive、Microsoft Office スイートの残りのアプリケーションをサービスとして利用します。 サブスクライバーには常に最新版が与えられます。Exchange サーバーは自分で管理する必要がありません。 Office を毎年インストールしたり、アップグレードしたりする場合と比較し、安価で簡単です。

## <a name="azure-services"></a>Azure サービス

Azure は、そのクラウド コンピューティング プラットフォームでさまざまなサービスを提供しています。 たとえば、次のサービスがあります。

### <a name="compute-services"></a>Compute Services

アプリケーション ワークロードをホストし、実行するためのサービス:

- Azure Virtual Machines - Linux と Windows の両方

- App Services (Web Apps、Mobile Apps、Logic Apps、API Apps、Function Apps)

- Azure Batch (大規模な並列/バッチ コンピューティング ジョブ用)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>データ サービス

データを保存し、管理するためのサービス:

- Azure Storage (Azure BLOB、Queue、Table、File サービスから構成)

- Azure SQL データベース

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache for Redis

### <a name="application-services"></a>アプリケーション サービス

アプリケーションを構築し、運用するためのサービス:

- Azure Active Directory (Azure AD)

- 分散システムを接続するための Azure Service Bus

- ビッグ データを処理するための Azure HDInsight

- ワークフローの統合とオーケストレーションのための Azure Logic Apps

- Azure Media Services

### <a name="network-services"></a>ネットワーク サービス

Azure 内でネットワークを構築するためのサービスと Azure とオンプレミス データセンターの間でネットワークを構築するためのサービス:

- Azure Virtual Network

- Azure ExpressRoute

- Azure 提供の DNS

- Azure の Traffic Manager

- Azure Content Delivery Network

Azure サービスの詳細については、「[Azure サービスのドキュメント](/azure)」を参照してください。

## <a name="azure-key-concepts"></a>Azure の主要な概念

### <a name="datacenters-and-regions"></a>データセンターとリージョン

Azure は、世界中のさまざまな地域で一般的に利用できるグローバル クラウド プラットフォームです。 Azure でサービス、アプリケーション、VM をプロビジョニングするとき、リージョン (地域) を選択するように求められます。 選択したリージョンは、アプリケーションが実行される特定のデータセンターに相当します。 詳細については、「[Azure のリージョン](https://azure.microsoft.com/regions/)」をご覧ください。

Azure を使用する利点の 1 つは、世界中のさまざまなデータセンターにアプリケーションをデプロイできるということです。 選択したリージョンによっては、アプリケーションのパフォーマンスが変わることがあります。 大部分の顧客に近いリージョンを選択すると、ネットワーク要求における待ち時間が少なくなります。 特定の国/地域でアプリを配信するための法的要件を満たせるリージョンを選択することもあります。

### <a name="azure-portal"></a>Azure portal

Azure Portal は Web ベースのアプリケーションであり、Azure のリソースやサービスの作成、管理、削除に利用できます。 Azure ポータルは、[portal.azure.com](https://portal.azure.com) にあります。 Azure リソースを管理するためのダッシュボードとツールがあり、カスタマイズも可能です。 課金とサブスクリプションに関する情報も確認できます。 詳細は、「[Microsoft Azure Portal の概要](https://azure.microsoft.com/documentation/articles/azure-portal-overview/)」と「[ポータルを使用した Azure リソースの管理](../../azure-resource-manager/management/manage-resources-portal.md)」を参照してください。

### <a name="resources"></a>リソース

Azure リソースは、Azure サブスクリプションにデプロイされる個々のコンピューティング サービス、ネットワーキング サービス、データ サービス、アプリ ホスティング サービスです。 共通リソースには、仮想マシン、ストレージ アカウント、SQL データベースがあります。 Azure サービスは多くの場合、いくつかの関連する Azure リソースから構成されています。 たとえば、Azure 仮想マシンには、VM、ストレージ アカウント、ネットワーク アダプター、パブリック IP アドレスが含まれることがあります。 リソースは個別に、あるいはグループとして作成、管理、削除できます。 Azure リソースについては、このガイドの後半で説明します。

### <a name="resource-groups"></a>リソース グループ

Azure リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 リソース グループには、ソリューションのすべてのリソースか、グループとして管理するリソースのみを含めることができます。 Azure リソース グループについては、このガイドの後半で説明します。

### <a name="resource-manager-templates"></a>Resource Manager テンプレート

Azure Resource Manager テンプレートは、リソース グループへのデプロイ対象となるリソースを定義する JavaScript Object Notation (JSON) ファイルです。 デプロイ対象リソース間の依存関係も、このテンプレートによって定義されます。 Resource Manager テンプレートについては、このガイドの後半で説明します。

### <a name="automation"></a>オートメーション

Azure Portal を利用したリソースの作成、管理、削除に加え、PowerShell または Azure コマンド ライン インターフェイス (CLI) を利用し、リソースの作成、管理、削除を自動化することもできます。

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell は、Azure を管理するためのコマンドレットを提供するモジュール セットです。 コマンドレットを使用し、Azure サービスを作成、管理、削除できます。 コマンドレットを利用すれば、自動的かつ一貫性のある方法で繰り返しデプロイできます。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-Az-ps)」を参照してください。

#### <a name="azure-command-line-interface"></a>Azure コマンド ライン インターフェイス

Azure コマンド ライン インターフェイスは、コマンド ラインから Azure リソースを作成、管理、削除するためのツールです。 Azure CLI は Linux、Mac OS X、Windows で使用できます。 技術的な詳細などについては、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

#### <a name="rest-apis"></a>REST API

Azure は、Azure Portal UI をサポートする REST API のセットに基づいて構築されています。 そのような REST API の多くもサポートされており、あらゆるインターネット対応デバイスから Azure リソースとアプリをプログラミングでプロビジョニングしたり、管理したりできます。 詳細については、「[Azure REST SDK リファレンス](/rest/api/index)」を参照してください。

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

管理者は、Azure Cloud Shell というブラウザー アクセス可能なエクスペリエンス経由で、Azure PowerShell と Azure CLI にアクセスできます。 この対話型インターフェイスは、Linux および Windows 管理者がBash または PowerShell のうち選択したどちらかのコマンド ライン インターフェイスを使用するための、柔軟なツールを提供します。 Azure Cloud Shell は、[shell.azure.com](https://shell.azure.com) のスタンドアロン Web インターフェイスとして、または多数の他のアクセス ポイントから、ポータル経由でアクセスできます。 詳細については、[Azure Cloud Shell の概要](../../cloud-shell/overview.md)に関するページを参照してください。

## <a name="azure-subscriptions"></a>Azure サブスクリプション

サブスクリプションは、Azure アカウントにリンクされている Azure サービスを論理的にグループ化したものです。 1 つの Azure アカウントに複数のサブスクリプションを含めることができます。 Azure サービスの課金は、サブスクリプションごとに行われます。 Azure サブスクリプションにはアカウント管理者とサービス管理者が割り当てられます。アカウント管理者にはサブスクリプションを制御する権限が与えられ、サービス管理者にはサブスクリプションに含まれるすべてのサービスを制御する権限が与えられます。 従来のサブスクリプション管理者の詳細については、「[Azure サブスクリプション管理者を追加または変更する](../../cost-management-billing/manage/add-change-subscription-administrator.md)」を参照してください。 管理者に加え、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を使用して Azure リソースを細かく管理する権限が個々のアカウントに与えられます。

### <a name="select-and-enable-an-azure-subscription"></a>Azure サブスクリプションを選択し、有効にする

Azure サービスを利用するには、サブスクリプションが必要です。 いくつかの種類のサブスクリプションを利用できます。

**無料アカウント**: [Azure Web サイト](https://azure.microsoft.com/)に、無料アカウントの新規登録リンクがあります。 無料アカウントでは、30 日間のコースでクレジットが与えられます。Azure のリソースをあらゆる組み合わせでお試しいただけます。 クレジット額を超えると、アカウントが一時停止されます。 試用期間が終わると、サービスが撤去され、使用不可になります。 従量課金制サブスクリプションにいつでもアップグレードできます。

**MSDN サブスクリプション**: MSDN サブスクリプションを契約すると、毎月、Azure クレジットに一定額が与えられます。 たとえば、 MSDN サブスクリプション付きの Microsoft Visual Studio Enterprise を契約すると、Azure クレジットに毎月 \$150 与えられます。

このクレジット額を超えると、翌月が始まるまでサービスは無効になります。 使用制限をオフにしてクレジット カードを追加し、追加料金分を支払うことができます。 MSDN アカウントの場合、一部の料金が割り引きされます。 たとえば、Windows Server を実行している VM に Linux 価格を支払っている場合、Microsoft SQL Server など、Microsoft のサーバーには追加料金が発生しません。 そのため、MSDN アカウントは開発シナリオやテスト シナリオに最適です。

**BizSpark アカウント**: Microsoft BizSpark プログラムでは、ベンチャー企業向けにさまざまな特典が用意されています。 その 1 つに、開発環境とテスト環境を対象に、最大 5 つの MSDN アカウントですべての Microsoft ソフトウェアにアクセスできるというものがあります。 5 つの MSDN アカウントのそれぞれに、$150 の Azure クレジットが与えられます。仮想マシンなど、一部の Azure サービスに関して割り引きが受けられます。

**従量課金制**: このサブスクリプションでは、クレジット カードまたはデビット カードをアカウントに登録することで、使用した分だけ支払います。 契約者が組織の場合、請求承認を受けることもできます。

**エンタープライズ契約**: エンタープライズ契約では、向こう 1 年間、Azure の一定数のサービスを使用する契約を結び、料金を前払いします。 契約分はその年を通して使用します。 契約額を超えた場合、超過分は後払いできます。 契約額によっては、Azure のサービスで割り引きが受けられます。

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Azure サブスクリプションに管理アクセスを与える

Azure RBAC にはいくつかの組み込みロールがあり、それらを使用して、アクセス許可を割り当てることができます。 ユーザーを Azure サブスクリプションの管理者にするには、サブスクリプションのスコープで、そのユーザーに[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールを割り当てます。 これにより、他のユーザーへアクセス権を委任する権限を含め、サブスクリプションにあるすべてのリソースへのフル アクセスがユーザーに付与されます。

詳細については、[Azure portal を使用して Azure ロールを割り当てる方法](../../role-based-access-control/role-assignments-portal.md)に関するページを参照してください。

### <a name="view-billing-information-in-the-azure-portal"></a>Azure Portal で請求情報を表示する

Azure の使用で重要になることの 1 つが請求情報を表示する機能です。 Azure Portal では、Azure の課金情報の詳細を確認できます。

詳細については、「[Azure の請求書と毎日の使用状況データをダウンロードする方法](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)」を参照してください。

### <a name="get-billing-information-from-billing-apis"></a>課金 API から課金情報を取得する

課金情報はポータルで表示する以外に、Azure Billing REST API でスクリプトまたはプログラムを利用し、アクセスするという方法があります。

- Azure Usage API を利用し、使用データを取得できます。 関連 Azure リソースにタグを付けることで、課金使用情報を微調整できます。 たとえば、リソース グループの各リソースに部門名やプロジェクト名のタグを付け、そのタグを対象にコストを追跡記録できます。

- 「[Azure Consumption API の概要](../../cost-management-billing/manage/consumption-api-overview.md)」を使用すると、利用可能なすべてのリソースを、そのメタデータと共に一覧表示できます。 価格の詳細については、「[Azure 小売価格の概要](/rest/api/cost-management/retail-prices/azure-retail-prices)」を参照してください。

### <a name="forecast-cost-with-the-pricing-calculator"></a>料金計算ツールでコストを予測する

Azure では、サービスごとに価格が異なります。 多くの Azure サービスでは、Basic、Standard、Premium という価格層を提供しています。 通常、階層ごとにいくつかの価格レベルとパフォーマンス レベルが用意されています。 [オンライン料金計算ツール](https://azure.microsoft.com/pricing/calculator)を利用すれば、価格を見積もることができます。 このツールでは、1 つのリソースまたはリソース グループのコストを柔軟に見積もることができます。

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager は、Azure リソースをデプロイ、管理、整理するためのメカニズムを提供します。 Resource Manager を利用すれば、個々のさまざまなリソースをリソース グループにまとめることができます。

Resource Manager はデプロイ機能も備えています。デプロイをカスタマイズしたり、関連リソースを構成したりできます。 たとえば、Resource Manager を使用し、複数の仮想マシン、ロード バランサー、Azure SQL Database のデータベースで構成されるアプリケーションを 1 つのユニットとしてデプロイできます。 デプロイは Resource Manager テンプレートで開発します。

リソース マネージャーには、いくつかの利点があります。

- ソリューションのリソースを個別に処理するのではなく、すべてのリソースをグループとしてデプロイ、管理、監視できます。

- ソリューションを開発のライフサイクル全体で繰り返しデプロイできます。また、常にリソースが一貫した状態でデプロイされます。

- スクリプトではなく宣言型のテンプレートを使用してインフラストラクチャを管理できます。

- 正しい順序でデプロイされるようにリソース間の依存性を定義できます。

- Azure RBAC が管理プラットフォームにネイティブ統合されるため、リソース グループのすべてのサービスにアクセス制御を適用できます。

- タグをリソースに適用し、サブスクリプションのすべてのリソースを論理的に整理できます。

- 同じタグを共有するリソース グループのコストを表示することで、組織の課金をわかりやすくすることができます。

### <a name="tips-for-creating-resource-groups"></a>リソース グループ作成のヒント

リソース グループ関連で決定するときは、以下の助言について検討してください。

- リソース グループ内のすべてのリソースには、同じライフサイクルが設定されている必要があります。

- リソースは一度に 1 つのグループだけに割り当てることができます。

- リソースはいつでもリソース グループに追加したり、グループから削除したりできます。 いずれのリソースもリソース グループに属している必要があります。 そのため、リソースをあるグループから削除したら、別のグループに追加する必要があります。

- ほとんどの種類のリソースは、いつでも別のリソース グループに移動できます。

- あるリソース グループのリソースは、複数のリージョンにまたがることができます。

- リソース グループを利用し、その中に属するリソースのアクセスを制御できます。

### <a name="building-resource-manager-templates"></a>Resource Manager テンプレートを作成する

Resource Manager テンプレートにより、1 つのリソース グループにデプロイされるリソースとリソース構成が宣言されます。 Resource Manager テンプレートを利用すれば、複雑なデプロイを調整できます。たくさんのスクリプトを作成したり、手動で構成したりする必要がありません。 テンプレートを作成したら、それを何回でもデプロイできます。結果は常に同じになります。

Resource Manager テンプレートを構成する 4 つのセクション:

- **パラメーター**: デプロイに入力される値です。 パラメーター値は手動または自動化プロセスで入力できます。 たとえば、Windows VM の管理者ユーザー名やパスワードがパラメーターとして入力されます。 パラメーター値が指定されると、デプロイ全体で使用されます。

- **変数**: デプロイ全体で使用する値を保持するために使用されます。 パラメーターとは異なり、変数値はデプロイ時に提供されません。 ハードコードされているか、動的に生成されます。

- **リソース**: テンプレートのこのセクションにより、仮想マシン、ストレージ アカウント、仮想ネットワークなど、デプロイするリソースが定義されます。

- **出力**:デプロイが完了すると、Resource Manager から、動的に生成された接続文字列などのデータを取得できます。

デプロイ自動化では、次のメカニズムを利用できます。

- **関数**: Resource Manager テンプレートでは、いくつかの関数を利用できます。 たとえば、文字列を小文字に変換する、定義されたリソースのインスタンスを複数デプロイする、ターゲット リソース グループを動的に返すなどの操作があります。 Resource Manager 関数は、動的デプロイの構築に役立ちます。

- **リソース依存関係**: 複数のリソースをデプロイするとき、リソースによっては他のリソースに依存するものがあります。 デプロイを簡単にするために、依存関係宣言を利用できます。従属リソースが他のリソースの前にデプロイされます。

- **テンプレートのリンク作成**: 1 つの Resource Manager テンプレートから別のテンプレートへのリンクを作成できます。 目的に基づいてターゲットが設定された一連のテンプレートにデプロイを分解できます。

Resource Manager テンプレートはあらゆるテキスト エディターで作成できます。 Azure SDK for Visual Studio に便利なツールが付属しているので推奨しています。 Visual Studio を利用すれば、ウィザードでリソースをテンプレートに追加し、Visual Studio 内から直接、テンプレートをデプロイし、デバッグできます。 詳細については、「[Azure Resource Manager のテンプレートの作成](../../azure-resource-manager/templates/template-syntax.md)」をご覧ください。

最後になりますが、Azure Portal で、既存のリソース グループを再利用可能なテンプレートに変換できます。 既存のリソース グループのデプロイ可能テンプレートを作成したり、基礎 JSON を調べたりする場合に便利です。 リソース グループをエクスポートするには、リソース グループの設定にある **[Automation スクリプト]** ボタンを選択します。

## <a name="security-of-azure-resources-azure-rbac"></a>Azure リソースのセキュリティ (Azure RBAC)

指定したスコープ (サブスクリプション、リソース グループ、個々のリソース) でユーザー アカウントに作業のためのアクセス権を付与できます。 つまり、仮想マシンや関連するすべてのリソースなど、リソース グループにリソース セットをデプロイし、特定のユーザーまたはグループにアクセス許可を付与できます。 この手法では、対象のリソース グループに属するリソースにのみアクセスを制限できます。 仮想マシンや仮想ネットワークなど、1 つのリソースにアクセス権を付与することもできます。

アクセス権を付与するには、ロールをユーザーまたはユーザー グループに割り当てます。 さまざまなロールが事前定義されています。 独自のロールを定義することもできます。

[Azure に組み込まれているロール](../../role-based-access-control/built-in-roles.md)の例:

- **所有者**:このロールが与えられたユーザーは、アクセス権を含め、すべてを管理できます。

- **閲覧者**:このロールが与えられたユーザーは (シークレット以外の) あらゆる種類のリソースを閲覧できますが、変更はできません。

- **仮想マシンの共同作業者**: このロールが与えられたユーザーは仮想マシンを管理できますが、仮想マシンが接続されている仮想ネットワークや VHD ファイルが置かれているストレージ アカウントを管理することはできません。

- **SQL DB 共同作業者**: このロールが与えられたユーザーは SQL データベースを管理できますが、セキュリティ関連ポリシーは管理できません。

- **SQL セキュリティ管理者**: このロールが与えられたユーザーは、SQL のサーバーとデータベースのセキュリティ関連ポリシーを管理できます。

- **ストレージ アカウント共同作業者**: このロールが与えられたユーザーはストレージ アカウントを管理できますが、ストレージ アカウントへのアクセスを管理することはできません。

詳細については、[Azure portal を使用して Azure ロールを割り当てる方法](../../role-based-access-control/role-assignments-portal.md)に関するページを参照してください。

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure Virtual Machines は Azure の中心的 IaaS サービスの 1 つです。 Azure Virtual Machines では、Windows または Linux の仮想マシンを Microsoft Azure データセンターにデプロイできます。 Azure Virtual Machines を利用すれば、VM 構成を完全制御できます。また、すべてのソフトウェアをインストール、構成、保守管理できます。

Azure VM をデプロイするとき、Azure Marketplace から画像を選択できます。あるいは、自分で一般化したイメージを指定できます。 このイメージを使用し、オペレーティング システムと初期構成を適用します。 デプロイ中、Resource Manager により、コンピューター名、管理資格情報、ネットワーク構成の割り当てなど、一部の構成設定が処理されます。 Azure の仮想マシンの拡張を使用すると、ソフトウェア インストール、ウイルス対策、監視ソリューションなど、他の構成も自動化できます。

仮想マシンはさまざまなサイズで作成できます。 仮想マシンのサイズは、処理、メモリ、ストレージ容量など、リソースの割り当てを決定します。 場合によっては、RDMA 対応のネットワーク アダプターや SSD ディスクなどの機能は特定のサイズの VM のみで利用できます。 VM のサイズと機能の完全な一覧については、"Azure での仮想マシンのサイズ" の [Windows](../../virtual-machines/sizes.md) と [Linux](../../virtual-machines/sizes.md) の項目をご覧ください。

### <a name="use-cases"></a>ユース ケース

Azure Virtual Machines では構成を完全に制御できるため、1 つの PaaS モデルに収まらない幅広いサーバー ワークロードに最適です。 データベース サーバー (SQL Server、Oracle、MongoDB)、Windows Server Active Directory、Microsoft SharePoint などのサーバー ワークロードが Microsoft Azure プラットフォームで実行できるようになりました。 必要であれば、このようなワークロードをオンプレミス データセンターから 1 つまたは複数の Azure リージョンに移動できます。大量の再構成は不要です。

### <a name="deployment-of-virtual-machines"></a>仮想マシンのデプロイ

Azure Virtual Machines のデプロイには、Azure Portal、Azure PowerShell モジュールによる自動化、クロスプラットフォームの CLI による自動化を利用できます。

#### <a name="portal"></a>ポータル

Azure Portal で仮想マシンをデプロイする場合、有効な Azure サブスクリプションと Web ブラウザーへのアクセスが必要になります。 構成が異なる、さまざまなオペレーティング システム イメージを選択できます。 ストレージ要件とネットワーク要件はすべて、デプロイ中に構成されます。 詳細については、Azure portal で仮想マシンを作成する方法に関するページを参照してください。[Windows](../../virtual-machines/windows/quick-create-portal.md) 向けのページと [Linux](../../virtual-machines/linux/quick-create-portal.md) 向けのページとがあります。

Azure Portal から仮想マシンをデプロイするだけでなく、ポータルから Azure Resource Manager テンプレートをデプロイできます。 テンプレートに定義されているすべてのリソースがデプロイされ、構成されます。 詳細については、「[Deploy resources with Resource Manager templates and Azure portal](../../azure-resource-manager/templates/deploy-portal.md)」 (Resource Manager テンプレートと Azure Portal を使用したリソースのデプロイ) を参照してください。

#### <a name="powershell"></a>PowerShell

PowerShell を利用した Azure 仮想マシンのデプロイでは、ストレージやネットワークなど、関連するあらゆる仮想マシン リソースのデプロイを完全に自動化できます。 詳細については、「[Resource Manager と PowerShell を使用して Windows VM を作成する](../../virtual-machines/windows/quick-create-powershell.md)」を参照してください。

Azure のコンピューティング リソースを個別にデプロイする以外に、Azure PowerShell モジュールを利用し、Azure Resource Manager テンプレートをデプロイできます。 詳細については、「[Deploy resources with Resource Manager templates and Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)」 (Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ) を参照してください。

#### <a name="command-line-interface-cli"></a>コマンド ライン インターフェイス (CLI)

PowerShell モジュールの場合と同様に、Azure コマンド ライン インターフェイスでデプロイを自動化できます。Windows、OS X、Linux システムで利用できます。 Azure CLI **vm quick-create** コマンドを使用すると、関連するすべての仮想マシン リソース (ストレージとネットワークを含む) と仮想マシン自体がデプロイされます。 詳細については、「[CLI を使用した Azure での Linux VM の作成](../../virtual-machines/linux/quick-create-cli.md)」をご覧ください。

同様に、Azure CLI を利用し、Azure Resource Manager テンプレートをデプロイできます。 詳細については、「[Deploy resources with Resource Manager templates and Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)」 (Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ) を参照してください。

### <a name="access-and-security-for-virtual-machines"></a>仮想マシンのアクセスとセキュリティ

インターネットから仮想マシンにアクセスするには、関連するネットワーク インターフェイスまたは (該当する場合) ロード バランサーをパブリック IP アドレスで構成する必要があります。 パブリック IP アドレスには、仮想マシンまたはロード バランサーに解決される DNS 名が含まれています。 詳細については、「[Azure 内の IP アドレス](../../virtual-network/public-ip-addresses.md)」を参照してください。

ネットワーク セキュリティ グループ (NSG) リソースを利用し、パブリック IP アドレスによる仮想マシンへのアクセスを管理します。 NSG はファイアウォールのように機能し、定義された一連のポートで、ネットワーク インターフェイスまたはサブネットを通過するトラフィックを許可または拒否します。 たとえば、Azure VM でリモート デスクトップ セッションを作成するには、ポート 3389 で入ってくるトラフィックを許可するように NSG を構成する必要があります。 詳細については、「[Azure Portal を使用した Azure の VM へのポートの開放](../../virtual-machines/windows/nsg-quickstart-portal.md)」を参照してください。

最後になりますが、あらゆるコンピューター システムの管理と同様に、セキュリティ資格情報とソフトウェア ファイアウォールを利用し、オペレーティング システムで Azure 仮想マシンを守る必要があります。

## <a name="azure-storage"></a>Azure Storage

Azure Storage は耐久性がありスケーラブルな冗長ストレージを提供するサービスであり、Microsoft が管理しています。 リソース デプロイ方法を利用し、リソース グループにリソースとして Azure ストレージ アカウントを追加できます。 Azure には、4 種類のストレージがあります: Blob Storage、File Storage、Table Storage、Queue Storage。 ストレージ アカウントをデプロイするとき、2 種類のアカウントを利用できます。汎用ストレージと BLOB ストレージです。 汎用ストレージ アカウントの場合、4 種類すべてのストレージにアクセスできます。 BLOB ストレージ アカウントは汎用アカウントと似ていますが、ホット アクセス層とコールド アクセス層を含む、特別な BLOB が含まれています。 BLOB ストレージの詳細については、「[Azure Blob Storage](../../storage/blobs/storage-blob-storage-tiers.md)」を参照してください。

Azure ストレージ アカウントはさまざまな冗長レベルで構成できます。

- **ローカル冗長ストレージ** は可用性が高く、すべてのデータのコピーが同期的に 3 つ作成されてから書き込みが成功したと見なされます。 コピーは、1 つのリージョン内の 1 つの施設に保存されます。 レプリカが別個の障害ドメインとアップグレード ドメインに置かれます。 つまり、データが収納されているストレージ ノードが壊れたり、更新のためにオフラインになったりしてもデータを利用できます。

- **geo 冗長ストレージ** の場合、高可用性のために、プライマリ リージョンにデータのコピーが 3 つ同期的に作成されます。その後、ディザスター リカバリーのために、ペアになっているリージョンで 3 つのレプリカが非同期的に作成されます。

- **読み取りアクセス geo 冗長ストレージ** の場合、geo 冗長ストレージの機能に加え、セカンダリ リージョンのデータを読み込めます。 部分的なディザスター リカバリーに最適です。 プライマリ リージョンに問題がある場合、アプリケーションを変更し、ペアになっているリージョンへの読み取り専用アクセスを与えることができます。

### <a name="use-cases"></a>ユース ケース

各種類のストレージには異なるユース ケースがあります。

#### <a name="blob-storage"></a>BLOB ストレージ

*blob* は *binary large object* (バイナリ ラージ オブジェクト) の略です。 BLOB は、コンピューターに保存するファイルのような、非構造化ファイルです。 Blob Storage は、ドキュメント、メディア ファイル、アプリケーション インストーラーなど、任意の種類のテキスト データやバイナリ データを格納できます。 Blob Storage は、オブジェクト ストレージとも呼ばれます。 Azure Blob ストレージには、Azure Virtual Machines データ ディスクも保存されます。

Azure Storage でサポートされている 3 種類の BLOB:

- **ブロック BLOB** は最大 195 GB サイズの普通のファイルの保存に使用されます (4 MB × 50,000 ブロック)。 ブロック BLOB は主に、始めから終わりまで読み込まれるファイルの保存に使用されます。メディア ファイルや Web サイトの画像ファイルなどです。 64 MB を超えるファイルは小さなブロックとしてアップロードする必要があるため、ブロック BLOB という名前が付いています。 アップロード後、小さなブロックが連結され (あるいはコミットされ)、最終的な BLOB が作られます。

- **ページ BLOB** は最大 1 TB のランダムアクセス ファイルの保存に使用されます。 ページ BLOB は主に、Azure の IaaS コンピューティング サービス、Azure Virtual Machines に耐久性のあるディスクを提供する VHD のバッキング ストレージとして使用されます。 512 バイト ページを読み書きするランダム アクセスを提供するため、ページ BLOB という名前が付いています。

- **追加 BLOB** はブロック BLOB のようなブロックで構成されますが、追加操作のために最適化されています。 多くの場合、1 つまたは複数のソースから同じ BLOB に情報を記録するために使用されます。 たとえば、複数の VM で実行されているアプリケーションに関して、すべてのトレース ログを同じ追加 BLOB に書き込みます。 1 つの追加 BLOB は最大 195 GB になります。

詳細については、「[.NET を使用して Azure Blob Storage を使用する](../../storage/blobs/storage-quickstart-blobs-dotnet.md)」をご覧ください。

#### <a name="file-storage"></a>File Storage

Azure File Storage は、標準のサーバー メッセージ ブロック (SMB) プロトコルを使用してクラウドでファイル共有を提供するサービスです。 このサービスは SMB 2.1 と SMB 3.0 の両方に対応しています。 Azure File Storage を使用すると、コストがかかる書き換えを行わずに、ファイル共有に依存しているアプリケーションをすばやく Azure に移行することができます。 AzureVirtual Machines またはクラウド サービスで実行されているアプリケーション、またはオンプレミスのクライアントから実行されているアプリケーションは、クラウドにファイル共有をマウントできます。 これはデスクトップ アプリケーションが一般的な SMB 共有をマウントするのに似ています。 このため、任意の数のアプリケーション コンポーネントが、File Storage 共有をマウントして同時にアクセスできます。

File Storage 共有は標準の SMB ファイル共有であるため、Azure で実行されているアプリケーションは、ファイル システム I/O API を介して共有内のデータにアクセスできます。 そのため、開発者は、既存のコードとスキルを利用し、既存のアプリケーションを移行することができます。 IT プロフェッショナルは、Azure アプリケーションの管理の一部として、PowerShell コマンドレットを使用して、File Storage 共有を作成、マウント、管理できます。

詳しくは、「[Windows で Azure File Storage を使用する](../../storage/files/storage-how-to-use-files-windows.md)」または「[Linux で Azure File Storage を使用する方法](../../storage/files/storage-how-to-use-files-linux.md)」をご覧ください。

#### <a name="table-storage"></a>テーブル ストレージ

Azure Table Storage は、NoSQL の構造化データをクラウド内に格納するサービスです。 Table Storage は、スキーマなしの設計によるキーまたは属性ストアです。 Table Storage はスキーマがないため、アプリケーションの進化のニーズに合わせてデータを容易に修正できます。 あらゆる種類のデータに、高速かつ経済的にアクセスできます。 Table Storage は、通常、従来の SQL と比較して、同様の容量のデータをはるかに低コストで保存できます。

テーブル ストレージを使用すると、Web アプリケーションのユーザー データ、アドレス帳、デバイス情報、およびサービスに必要なその他の種類のメタデータなど、柔軟なデータセットを保存できます。 任意の数のエンティティをテーブルに保存できます。 ストレージ アカウントには、ストレージ アカウントの容量の上限を超えない限り、任意の数のテーブルを含めることができます。

詳細については、「[Azure Table Storage を使用する](../../cosmos-db/tutorial-develop-table-dotnet.md)」をご覧ください。

#### <a name="queue-storage"></a>ストレージ

Azure Queue Storage は、アプリケーション コンポーネント間のクラウド メッセージングを提供します。 拡張性を重視してアプリケーションを設計する場合、通常、アプリケーション コンポーネントを個別に拡張できるように分離します。 Queue Storage は、アプリケーション コンポーネントがクラウド、デスクトップ、オンプレミスのサーバー、モバイル デバイスのいずれで実行されている場合でも、アプリケーション コンポーネント間の通信に非同期メッセージングを提供します。 Queue Storage ではまた、非同期タスクの管理とプロセス ワークフローの構築もサポートします。

詳細については、「[Azure Queue Storage を使用する](../../storage/queues/storage-dotnet-how-to-use-queues.md)」をご覧ください。

### <a name="deploying-a-storage-account"></a>ストレージ アカウントのデプロイ

ストレージ アカウントのデプロイにはいくつかの選択肢があります。

#### <a name="portal"></a>ポータル

Azure Portal でストレージ アカウントをデプロイする場合、有効な Azure サブスクリプションと Web ブラウザーへのアクセスが必要になります。 新しいストレージ アカウントを新しいリソース グループや既存のリソース グループにデプロイできます。 ストレージ アカウントを作成したら、ポータルを利用し、BLOB コンテナーまたはファイル共有を作成できます。 Table Storage エンティティや Queue Storage エンティティをプログラミングで作成できます。 詳しくは、「[ストレージ アカウントの作成](../../storage/common/storage-account-create.md)」をご覧ください。

Azure Portal からストレージ アカウントをデプロイするだけでなく、ポータルから Azure Resource Manager テンプレートをデプロイできます。 ストレージ アカウントを含め、テンプレートに定義されているすべてのリソースがデプロイされ、構成されます。 詳細については、「[Deploy resources with Resource Manager templates and Azure portal](../../azure-resource-manager/templates/deploy-portal.md)」 (Resource Manager テンプレートと Azure Portal を使用したリソースのデプロイ) を参照してください。

#### <a name="powershell"></a>PowerShell

PowerShell を利用し、Azure ストレージ アカウントをデプロイする方法では、ストレージ アカウントのデプロイを完全に自動化できます。 詳細については、「[Azure Storage で Azure PowerShell を使用する](/powershell/module/az.storage/)」を参照してください。

Azure リソースを個別にデプロイする以外に、Azure PowerShell モジュールを利用し、Azure Resource Manager テンプレートをデプロイできます。 詳細については、「[Deploy resources with Resource Manager templates and Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)」 (Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ) を参照してください。

#### <a name="command-line-interface-cli"></a>コマンド ライン インターフェイス (CLI)

PowerShell モジュールの場合と同様に、Azure コマンド ライン インターフェイスでデプロイを自動化できます。Windows、OS X、Linux システムで利用できます。 Azure CLI **storage account create** コマンドを利用し、ストレージ アカウントを作成できます。 詳細については、「[Azure Storage での Azure CLI の使用](../../storage/blobs/storage-quickstart-blobs-cli.md)」を参照してください。

同様に、Azure CLI を利用し、Azure Resource Manager テンプレートをデプロイできます。 詳細については、「[Deploy resources with Resource Manager templates and Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)」 (Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ) を参照してください。

### <a name="access-and-security-for-azure-storage"></a>Azure Storage のアクセスとセキュリティ

Azure Storage にはさまざまな方法でアクセスできます。たとえば、Azure Portal や Storage クライアント ライブラリからアクセスしたり、VM の作成中や操作中にアクセスしたりできます。

#### <a name="virtual-machine-disks"></a>仮想マシンのディスク

仮想マシンをデプロイするとき、仮想マシンのオペレーティング システム ディスクや追加のデータ ディスクを保存するためのストレージ アカウントを作成する必要もあります。 既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成できます。 BLOB の最大サイズは 1,024 GB であるため、1 つの VM ディスクの最大サイズは 1,023 GB です。 より大きなデータ ディスクを構成するには、複数のデータ ディスクを仮想マシンに提示し、1 つの論理ディスクとしてまとめてプールできます。 詳細については、[Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) および [Linux](../../virtual-machines/linux/tutorial-manage-disks.md) 向けの Azure ディスクの管理に関するページを参照してください。

#### <a name="storage-tools"></a>Storage ツール

Azure ストレージ アカウントには、Visual Studio Cloud Explorer など、さまざまなストレージ エクスプローラーからアクセスできます。 ツールを利用し、ストレージ アカウントやデータを閲覧できます。 利用できるストレージ エクスプローラーに関する情報と一覧については、「[Azure Storage クライアント ツール](../../storage/common/storage-explorers.md)」をご覧ください。

#### <a name="storage-api"></a>Storage API

Storage のリソースは、HTTP/HTTPS 要求が可能な任意の言語からアクセスできます。 さらに、主要な複数の言語のプログラミング ライブラリも用意されています。 ライブラリにより、同期呼び出しと非同期呼び出し、操作のバッチ処理、例外管理、自動再試行などの詳細が処理され、Azure Storage の使用が簡略化されます。 詳細については、「[Azure Storage サービスの REST API リファレンス](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference)」を参照してください。

#### <a name="storage-access-keys"></a>ストレージ アクセス キー

各ストレージ アカウントに 2 つの認証キーが与えられます。プライマリとセカンダリです。 いずれもストレージ アクセス操作に利用できます。 ストレージ キーを利用してストレージ アカウントを守ります。また、データにプログラミングでアクセスする際に必要になります。 セキュリティの強化目的で時折、キーをロールオーバーするとき、2 つのキーが利用されます。 キーの安全を維持することが重要です。アカウント名と共にキーを手に入れると、ストレージ アカウントのあらゆるデータに無制限でアクセスできます。

#### <a name="shared-access-signatures"></a>共有アクセス署名

ストレージ リソースへの制御アクセスをユーザーに与える場合、Shared Access Signature を作成できます。 Shared Access Signature は、URL に追加できるトークンであり、ストレージ リソースへのアクセスの委任を可能にするトークンです。 トークンを持つすべてのユーザーは、トークンが有効な間、トークンに指定されたアクセス許可を使用して、トークンが示すリソースにアクセスできます。 詳細については、「[Shared Access Signatures (SAS) の使用](../../storage/common/storage-sas-overview.md)」を参照してください。

## <a name="azure-virtual-network"></a>Azure Virtual Network

仮想ネットワークは、仮想マシン間の通信をサポートするために必要です。 サブネット、カスタム IP アドレス、DNS 設定、セキュリティ フィルター処理、負荷分散を定義できます。 Azure では異なるユース ケースがサポートされています (クラウド専用ネットワークまたはハイブリッド仮想ネットワーク)。

### <a name="cloud-only-virtual-networks"></a>クラウド専用仮想ネットワーク

既定では、Azure 仮想ネットワークは Azure に保存されているリソースにのみアクセスできます。 同じ仮想ネットワークに接続されているリソースは互いに通信できます。 仮想マシン ネットワーク インターフェイスとロード バランサーをパブリック IP アドレスに関連付けることで、仮想マシンにインターネット経由でアクセスできます。 ネットワーク セキュリティ グループを利用し、一般公開されているリソースに安全にアクセスできます。

![2 層の Web アプリケーション用の Azure Virtual Network](/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>ハイブリッド仮想ネットワーク

ExpressRoute またはサイト間 VPN 接続を使用し、オンプレミス ネットワークを Azure 仮想ネットワークに接続できます。 この構成では、Azure Virtual Network は基本的に、オンプレミス ネットワークのクラウド ベースの拡張機能です。

Azure 仮想ネットワークはオンプレミス ネットワークに接続されるため、クロスプレミス仮想ネットワークは、組織によって使用されるアドレス空間の一意の部分を使用する必要があります。 企業内の異なる場所に特定の IP サブネットが割り当てられるのと同様に、ネットワークを拡張すると、Azure を別の場所として扱うことができます。
仮想ネットワークのデプロイにはいくつかの選択肢があります。

- [ポータル](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [コマンド ライン インターフェイス (CLI)](../../virtual-network/quick-create-cli.md)

- Azure Resource Manager のテンプレート

> **いつ使用するか**: Azure で VM を使用するときはいつでも、仮想ネットワークを使用します。 これにより、オンプレミスのデータ センターのように公開されるサブネットとプライベート サブネットに VM をセグメント化できます。
>
> **作業開始**: Azure 仮想ネットワークで仮想マシンをデプロイする場合、有効な Azure サブスクリプションと Web ブラウザーへのアクセスが必要になります。 新しい仮想ネットワークを新しいリソース グループや既存のリソース グループにデプロイできます。 ポータルから新しい仮想マシンを作成するとき、既存の仮想ネットワークを選択するか、新しい仮想ネットワークを作成できます。 概要および「[Azure portal を使用した仮想ネットワークの作成](../../virtual-network/quick-create-portal.md)」。

### <a name="access-and-security-for-virtual-networks"></a>仮想ネットワークのアクセスとセキュリティ

ネットワーク セキュリティ グループを利用し、Azure 仮想ネットワークを保護できます。 NSG には、Virtual Network の VM インスタンスに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) 規則が含まれています。 NSG は、サブネットまたはそのサブネット内の個々の VM インスタンスと関連付けることができます。 NSG がサブネットに関連付けられている場合、ACL 規則はそのサブネット内のすべての VM インスタンスに適用されます。 また、NSG を直接 VM に関連付けることにより、その個々の VM に対するトラフィックをさらに制限できます。 詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](../../virtual-network/network-security-groups-overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Windows VM の作成](../../virtual-machines/windows/quick-create-portal.md)
- [Linux VM の作成](../../virtual-machines/linux/quick-create-portal.md)