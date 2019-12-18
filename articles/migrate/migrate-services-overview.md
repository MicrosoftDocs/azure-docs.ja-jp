---
title: Azure Migrate について
description: Azure Migrate サービスを使用したサーバーの評価と移行について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 54aea9fac946713ee6c1709e952e3962bb7288e9
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951770"
---
# <a name="about-azure-migrate"></a>Azure Migrate について

この記事では、Azure Migrate の概要を簡単に説明します。

Azure Migrate は、オンプレミスから Azure へのエンタープライズの移行に役立ちます。 Azure Migrate には、オンプレミス インフラストラクチャ、アプリケーション、およびデータの検出、評価、および Azure への移行を追跡するための一元的なハブが用意されています。  Azure Migrate では次のものが提供されます。

- **統合された移行プラットフォーム**:Azure への移行の過程を開始、実行、追跡するための単一のポータル。
- **ツールの範囲**:このハブには、評価および移行のための Azure Migrate ツールが用意されています。また、他の Azure サービスや、他のツールおよび独立系ソフトウェア ベンダー (ISV) のオファリングと統合されています。
- **ワークロード**:Azure Migrate では、以下のものについての評価と移行を提供します。
    - **サーバー**:サーバーの評価と Azure VM への移行に、Azure Migrate Server Assessment、Azure Migrate Server Migration、およびその他のツールを使用します。
    - **データベース**: オンプレミスのデータベースを評価して Azure SQL DB または Azure SQL Managed Instance に移行するには、Microsoft および ISV のツールを利用します。
    - **Web アプリケーション**: オンプレミスの Web アプリケーションを評価して Azure App Service に移行するには、Azure App Service Assistant を使用します。
    - **仮想デスクトップ**: オンプレミスの仮想デスクトップ インフラストラクチャ (VDI) を評価して Azure の Windows Virtual Desktop に移行するには、ISV のツールを使用します。
    - **データ**:大量のデータを迅速かつコスト効果よく Azure に移行するには、Azure Data Box 製品ファミリを使用します。

## <a name="azure-migrate-versions"></a>Azure Migrate のバージョン

現在、Azure Migrate サービスには次の 2 つのバージョンがあります。

- **現在のバージョン**: Azure Migrate プロジェクトの作成、オンプレミス マシンの検出、評価と移行の調整を行う場合はこちらのバージョンを使用します。 このバージョンの新機能については[こちらを参照してください](whats-new.md)。
- **以前のバージョン**: 旧バージョンの Azure Migrate (オンプレミスの VMware VM の評価のみサポート) を使用していた場合、今後は最新バージョンを使用してください。 前のバージョンを使用して Azure Migrate プロジェクトを作成することはできなくなりました。新しい検出を実行しないことをお勧めします。 既存のプロジェクトにアクセスするには、Azure portal で、 **[Azure Migrate]** を検索して選択します。 **Azure Migrate** ダッシュボードに、通知と、過去の Azure Migrate プロジェクトにアクセスするためのリンクが表示されます。



## <a name="isv-integration"></a>ISV 統合

Azure ネイティブ ツールに加え、Azure Migrate は多数の ISV オファリングと統合されています。 

**ISV** | **機能**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | サーバーの移行
[Cloudamize](https://www.cloudamize.com/platform) | サーバーの評価
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | サーバーの評価と移行
[Device 42](https://docs.device42.com/) | サーバーの評価
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI の評価
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | サーバーの移行
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | サーバーの評価
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | サーバーとデータベースの評価

## <a name="azure-tool-integration"></a>Azure ツールの統合

この表は、Azure Migrate に統合されているその他のツールをまとめたものです。

**ツール** | **詳細**
--- | ---
Azure Migrate: Server Assessment | サーバーの評価
Azure Migrate: Server Migration | サーバーの移行
Database Migration Assistant (DMA) | データベースの評価
Database Migration Service (DMS) | データベースを移行する
Movere | サーバーの評価
Web App Migration Assistant | Web アプリの評価と移行



### <a name="selecting-a-tool"></a>ツールの選択

必要なツールを特定し、それを Azure Migrate プロジェクトに追加します。

- ISV ツールまたは Movere を追加している場合:
    - ツールのポリシーに従って、ライセンスを取得するか、無料試用版にサインアップすることによって、使用を開始します。 ツールのライセンスは、ISV またはツールのライセンス モデルに準拠します。
    - 各ツールには、Azure Migrate に接続するためのオプションがあります。 ツールの指示とドキュメントに従って、ツールを Azure Migrate に接続します。
- Azure とその他のツール全体にわたって、Azure Migrate プロジェクト内から移行の過程を一元的に追跡します。



## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate Server Assessment ツール

Azure Migrate: Server Assessment ツールを使うと、オンプレミスの VMware VM、Hyper-V VM、物理サーバー を検出し、Azure への移行について評価することができます。 次を特定するために役立ちます。

- **Azure の準備状況:** オンプレミスのマシンが Azure に移行する準備ができているかどうかを評価します。
- **Azure のサイズ変更:** 移行後の Azure VM の推定サイズ。
- **Azure コストの見積もり:** Azure でオンプレミス サーバーを実行する場合の推定コスト。
- **依存関係の視覚化:** サーバー間の依存関係 (依存関係の視覚化が有効になっている場合)、および依存サーバーを Azure に移動するための最適な方法。

Server Assessment では、オンプレミスで展開する軽量のアプライアンスを使用し、Server Assessment に登録します。

- アプライアンスにより、オンプレミスのマシンが検出されます。
- Server Assessment に接続することで、マシンのメタデータとパフォーマンスのデータが、Azure Migrate に継続的に送信されます。
- アプライアンスによる検出はエージェントレスです。 検出されたマシンには何もインストールする必要はありません。
- 検出後は、検出されたマシンをグループにまとめます。 通常は、一緒に移行するマシンをまとめます。
- グループの評価を作成します。 その後、評価を分析して、移行戦略を明らかにします。

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate Server Migration ツール

Azure Migrate: Server Migration ツールを使うと、オンプレミスの VMware VM、Hyper-V VM、物理サーバー、その他の仮想マシン、パブリック クラウド VM を Azure に移行できます。 マシンは、評価後でも、評価なしでも移行することができます。


## <a name="database-migration-assistant"></a>Database Migration Assistant

Azure Migrate は、Azure SQL DB、Azure SQL Managed Instance、または SQL Server を実行する Azure VM に移行する対象のオンプレミスの SQL Server データベースを評価するために、Microsoft Data Migration Assistant (DMA) と統合されます。 DMA からは、移行に関する潜在的な障害となっている問題に関する情報が提供されます。 サポートされていない機能だけでなく、移行後に利用できる新機能が特定されます。また、データベース移行の適切なパスを特定するために役立ちます。 [詳細情報](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)。

## <a name="database-migration-service"></a>Database Migration Service

Azure Migrate は、オンプレミスのデータベースを Azure に移行するために Azure Database Migration Service (DMS) と統合されています。 DMS を使用して、SQL、Azure SQL DB、Azure SQL マネージド インスタンスを実行している Azure VM にオンプレミスのデータベースを移行します。 [詳細情報](https://docs.microsoft.com/azure/dms/dms-overview)。

## <a name="movere"></a>Movere

 
Movere は、1 日以内に IT 環境全体を正確に表現することによってビジネス インテリジェンスを向上させる SaaS プラットフォームです。 組織の成長、変化、およびデジタル最適化に対応して、このソリューションは、プラットフォーム、アプリケーション、または地理的な場所に関係なく、企業が環境の把握と制御について自信が持てるようにします。 Movere は、Microsoft によって[買収](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)され、スタンドアロンのオファーとしては販売されなくなりました。  Movere は、Microsoft Solution Assessment および Cloud Economics Programs を通じて入手できます。 Movere の[詳細を参照](https://www.movere.io)してください。 ご質問がある場合は、movereq@microsoft.com に送信するか、Microsoft の担当者にお問い合わせください。

また、組み込みの移行サービスである Azure Migrate も参照することをお勧めします。 Azure Migrate は、クラウドへの移行を簡略化する中央ハブを提供します。 このハブは、物理サーバー、仮想サーバー、データベース、アプリケーションなど、さまざまなワークロードに対する包括的なサポートを備えています。 エンド ツー エンドの可視性により、検出、評価、および移行全体の進行状況を簡単に追跡できます。 また、Azure Migrate には Azure とパートナー ISV の両方のツールが組み込まれており、仮想サーバーおよび物理サーバーの検出、パフォーマンスベースの適切なサイズ設定、コスト計画、インポートベースの評価、エージェントレスなアプリケーション依存関係分析など、広範な機能があります。 使用の開始にあたって専門家の支援が必要な場合は、Microsoft の熟練した [Azure Expert マネージド サービス プロバイダー](https://azure.microsoft.com/partners)が工程全体にわたってお手伝いします。 [Azure Migrate Web サイト](https://azure.microsoft.com/services/azure-migrate/)を確認してください。 
 

## <a name="web-app-migration-assistant"></a>Web App Migration Assistant

Azure Migrate は Azure App Service Migration Assistant と統合されます。 次のように、Azure Migrate ハブから、Assistant を使用して、オンプレミスの Web アプリを評価し、Azure に移行することができます。

- **オンラインで Web アプリを評価する**:Azure App Service に移行するためにオンプレミスの Web サイトを評価するには、Azure App Service Migration Assistant を使用します。
- **Web アプリを移行する**:Azure App Service Migration Assistant を使用して、.NET および PHP Web アプリを Azure に移行します。

Assistant について[詳しくは、こちらをご覧ください](https://appmigration.microsoft.com/)。



## <a name="offline-data-migration"></a>オフライン データの移行

Azure Data Box 製品を使うと、大量のデータを Azure にオフラインで移動することができます。 [詳細情報](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>次の手順

- [VMware VM](tutorial-assess-vmware.md) と [Hyper-V VM](tutorial-assess-hyper-v.md) を評価するチュートリアルをお試しください。
- Azure Migrate の価格について、[詳しくはこちら](https://azure.microsoft.com/pricing/details/azure-migrate/)を参照してください。
- Azure Migrate について[よく寄せられる質問を確認](resources-faq.md)します。
