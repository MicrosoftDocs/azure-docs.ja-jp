---
title: Azure Migrate について
description: Azure Migrate サービスについて説明します。
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 72f2dfb3e5e05466892d838af4e1c84c855b94a9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122083"
---
# <a name="about-azure-migrate"></a>Azure Migrate について

この記事では、Azure Migrate サービスの概要を簡単に説明します。

Azure Migrate では、オンプレミスのサーバー、インフラストラクチャ、アプリケーション、データを評価し、Azure への移行を行うための一元化されたハブが提供されます。 以下が提供されます。

- **統合された移行プラットフォーム**:Azure への移行を開始、実行、追跡するための単一のポータル。
- **ツールの範囲**:評価と移行のためのさまざまなツール。 Azure Migrate ツールには、Server Assessment と Azure Migrate が含まれています。Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。 また、Azure Migrate は、他の Azure サービスやツール、さらに独立系ソフトウェア ベンダー (ISV) オファリングと統合されています。
- **評価と移行**: Azure Migrate ハブでは、以下の評価と移行を行うことができます。
    - **サーバー**:オンプレミスのサーバーを評価し、Azure の仮想マシンまたは Azure VMware Solution (AVS) (プレビュー) に移行します。
    - **データベース**: オンプレミスのデータベースを評価し、Azure SQL Database または SQL Managed Instance に移行します。
    - **Web アプリケーション**: Azure App Service Migration Assistant を使用して、オンプレミスの Web アプリケーションを評価し、Azure App Service に移行します。
    - **仮想デスクトップ**: オンプレミスの仮想デスクトップ インフラストラクチャ (VDI) を評価し、Azure の Windows Virtual Desktop に移行します。
    - **Data**:Azure Data Box 製品を使用して、大量のデータを迅速かつコスト効果よく Azure に移行します。

## <a name="integrated-tools"></a>統合されているツール

Azure Migrate ハブには、次のツールが含まれています。

**ツール** | **評価と移行** | **詳細**
--- | --- | ---
**Azure Migrate: Server Assessment** | サーバーを評価します。 | Azure への移行の準備として、オンプレミスの VMware VM、Hyper-V VM、および物理サーバーを検出して評価します。
**Azure Migrate: Server Migration** | サーバーを移行します。 | VMware VM、Hyper-V VM、物理サーバー、その他の仮想マシン、およびパブリック クラウド VM を Azure に移行します。
**Data Migration Assistant** | Azure SQL Database、Azure SQL Managed Instance、または SQL Server を実行する Azure VM に移行する SQL Server データベースを評価します。 | Data Migration Assistant は、移行を妨げる可能性のある問題の特定に役立ちます。 サポートされていない機能、移行後に役に立つ新機能、データベースの移行の適切なパスが明らかになります。 [詳細については、こちらを参照してください](/sql/dma/dma-overview?view=sql-server-2017)。
**Azure Database Migration Service** | オンプレミスのデータベースを、SQL Server を実行している Azure VM、Azure SQL Database、または SQL Managed Instance に移行します。 | Database Migration Service の[詳細を参照](../dms/dms-overview.md)してください。
**Movere** | サーバーを評価します。 | Movere の[詳細を参照](#movere)してください。
**Web App Migration Assistant** | オンプレミスの Web アプリを評価し、Azure に移行します。 |  Azure App Service に移行するためにオンプレミスの Web サイトを評価するには、Azure App Service Migration Assistant を使用します。<br/><br/> .NET と PHP の Web アプリを Azure に移行するには、Migration Assistant を使用します。 Azure App Service Migration Assistant の[詳細を参照](https://appmigration.microsoft.com/)してください。
**Azure Data Box** | オフライン データを移行します。 | Azure Data Box 製品を使用して、大量のオフライン データを Azure に移動します。 [詳細については、こちらを参照してください](../databox/index.yml)。

> [!NOTE]
> Azure Government をご利用の場合、外部の統合ツールや ISV オファリングから Azure Migrate プロジェクトにデータを送信することができません。 別途ツールをご利用ください。

## <a name="isv-integration"></a>ISV 統合

Azure Migrate は、いくつかの ISV オファリングと統合されています。 

**ISV**    | **機能**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | サーバーを移行します。
[Cloudamize](https://www.cloudamize.com/platform) | サーバーを評価します。
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | サーバーを評価して移行します。
[Device42](https://docs.device42.com/) | サーバーを評価します。
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI を評価します。
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | サーバーを移行します。
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | サーバーを評価します。
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | サーバーとデータベースを評価します。

## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: Server Assessment ツール

Azure Migrate: Server Assessment ツールを使うと、オンプレミスの VMware VM、Hyper-V VM、物理サーバーを検出し、Azure への移行について評価することができます。 

ツールには次のような機能があります。

- **Azure 対応性**: オンプレミスのコンピューターで Azure に移行する準備ができているかどうかを評価します。
- **Azure のサイジング**: 移行後の Azure VM のサイズまたは Azure VMware ノードの数を推定します。
- **Azure コストの見積もり**: Azure でオンプレミス サーバーを実行する場合のコストを見積もります。
- **依存関係の分析**: 相互に依存するサーバーを Azure に移動できるように、サーバー間の依存関係と最適化戦略を特定します。 [依存関係の分析](concepts-dependency-visualization.md)によるサーバー評価の詳細を参照してください。

Server Assessment では、オンプレミスに展開されている軽量の [Azure Migrate アプライアンス](migrate-appliance.md)が使用されます。

- アプライアンスは、VM または物理サーバーで実行されます。 ダウンロードしたテンプレートを使用して、簡単にインストールできます。
- アプライアンスにより、オンプレミスのマシンが検出されます。 また、マシンのメタデータとパフォーマンス データが Azure Migrate に継続的に送信されます。
- アプライアンスによる検出はエージェントレスです。 検出されたマシンには何もインストールされません。
- アプライアンスによる検出後、検出されたマシンをグループ化し、各グループの評価を実行できます。

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Server Migration ツール

Azure Migrate: Server Migration ツールは、以下を Azure に移行する際に役立ちます。

**移行** | **詳細**
--- | ---
オンプレミスの VMware VM | エージェントレスまたはエージェントベースの移行を使用して、VM を Azure に移行します。<br/><br/> エージェントレス移行の場合、Server Migration では、オンプレミスに配置されている Azure Migrate アプライアンスが使用されます。 これは、Server Assessment に使用するものと同じ種類のアプライアンスです。<br/><br/> エージェントベース移行の場合、Server Assessment ではレプリケーション アプライアンスが使用されます。
オンプレミスの Hyper-V VM | VM を Azure に移行します。<br/><br/> Server Assessment では、Hyper-V ホストにインストールされているプロバイダー エージェントが移行に使用されます。
オンプレミスの物理サーバー | 物理マシンを Azure に移行できます。 また、他の仮想化されたマシンや他のパブリック クラウドの VM を移行の目的で仮想マシンとして扱うことで移行することができます。 | Server Assessment では、レプリケーション アプライアンスが移行に使用されます。


## <a name="selecting-assessment-and-migration-tools"></a>評価および移行ツールの選択

Azure Migrate ハブで、評価または移行に使用するツールを選択し、それを Azure Migrate プロジェクトに追加します。 ISV ツールまたは Movere を追加する場合は、次のようにします。

- 使い始めるには、ツールの指示に従って、ライセンスを取得するか、無料試用版にサインアップします。 ISV またはツールごとに、ツールのライセンスが指定されています。
- 各ツールには、Azure Migrate に接続するためのオプションがあります。 ツールの指示に従って接続します。
- Azure Migrate プロジェクト内から、すべてのツールにわたって移行を追跡します。

## <a name="movere"></a>Movere

Movere は、SaaS (サービスとしてのソフトウェア) プラットフォームです。 Movere では、1 日以内に IT 環境全体を正確に表現することによってビジネス インテリジェンスを向上させることができます。 組織や企業は、成長したり、変化したり、デジタル最適化を行ったりします。 そのような際に Movere を使用すると、プラットフォーム、アプリケーション、地理的な場所に関係なく、自信を持って環境の確認や制御を行うことができます。

Movere は、Microsoft によって[買収](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)されたため、スタンドアロンのオファリングとしては販売されなくなりました。 Movere は、Microsoft Solution Assessment および Microsoft Cloud Economics Program を通じて入手できます。 Movere の[詳細を参照](https://www.movere.io)してください。

また、組み込みの移行サービスである Azure Migrate も参照することをお勧めします。 Azure Migrate では、クラウド移行を簡略化する中央ハブが提供されます。 このハブでは、物理サーバー、仮想サーバー、データベース、アプリケーションなどのワークロードが包括的にサポートされます。 エンド ツー エンドの可視性により、検出、評価、および移行全体の進行状況を簡単に追跡できます。

Azure Migrate には Azure のツールとパートナー ISV のツールの両方が組み込まれているため、次のような幅広い機能を利用できます。

- 仮想サーバーと物理サーバーの検出。
- パフォーマンスベースの適切なサイジング。
- コストの計画。
- インポートベースの評価。
- エージェントレス アプリケーションの依存関係分析。

使用の開始にあたって専門家の支援が必要な場合は、Microsoft の熟練した [Azure Expert マネージド サービス プロバイダー](https://azure.microsoft.com/partners)がお手伝いします。 [Azure Migrate Web サイト](https://azure.microsoft.com/services/azure-migrate/)を確認してください。 

## <a name="azure-migrate-versions"></a>Azure Migrate のバージョン

Azure Migrate サービスには、次の 2 つのバージョンがあります。

- **現在のバージョン**: Azure Migrate プロジェクトの作成、オンプレミス マシンの検出、評価と移行の調整を行う場合はこちらのバージョンを使用します。 このバージョンの新機能については[こちらを参照してください](whats-new.md)。
- **以前のバージョン**: 以前のバージョンの Azure Migrate では、オンプレミスの VMware VM の評価のみがサポートされています。 以前のバージョンを使用していた場合は、最新バージョンを使用する必要があります。 以前のバージョンを使用して Azure Migrate プロジェクトを作成することはできなくなりました。 また、以前のバージョンを使用して、新たに検出を行わないでください。

    Azure portal で既存のプロジェクトにアクセスするには、「**Azure Migrate**」を検索して選択します。 **Azure Migrate** ダッシュボードに、通知と、過去の Azure Migrate プロジェクトにアクセスするためのリンクが表示されます。

## <a name="next-steps"></a>次のステップ

- チュートリアルを実行し、[VMware VM](tutorial-prepare-vmware.md)、[Hyper-V VM](tutorial-prepare-hyper-v.md)、または[物理サーバー](tutorial-prepare-physical.md)を評価します。
- Azure Migrate について[よく寄せられる質問を確認](resources-faq.md)します。
