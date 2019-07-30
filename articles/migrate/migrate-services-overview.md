---
title: Azure Migrate について | Microsoft Docs
description: Azure Migrate サービスの概要を示します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5409ed799454a6bb64077ee884065fc518556142
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227805"
---
# <a name="about-azure-migrate"></a>Azure Migrate について

この記事では、Azure Migrate の概要を簡単に説明します。

Azure Migrate は、Azure への移行に役立ちます。 Azure Migrate には、オンプレミス インフラストラクチャ、アプリケーション、およびデータの検出、評価、および Azure への移行を追跡するための一元的なハブが用意されています。 このハブには、評価および移行のための Azure ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。 共有サービスには次のものが含まれています。

- **統合された移行プラットフォーム**:1 つのポータルを使用して、Azure への移行の過程を開始、実行、および追跡します。
- **ツールの範囲**:Azure Migrate にはネイティブ ツールが用意されています。また、他の Azure サービスや ISV ツールと統合されています。 組織の要件に基づいて、適切な評価と移行ツールを選択します。 
- **Azure Migrate Server Assessment**:Server Assessment ツールを使用して、Azure への移行について、オンプレミスの VMware VM と Hyper-V VM を評価します。
- **Azure Migrate Server Migration**:Server Migration ツールを使用して、オンプレミスの VMware VM、Hyper-V VM、クラウド VM、および物理サーバーを Azure に移行します。
- **Azure Migrate Database Assessment**:Azure への移行の対象となるオンプレミスのデータベースを評価します。
- **Azure Migrate Database Migration**:オンプレミスのデータベースを Azure に移行します。


## <a name="azure-migrate-versions"></a>Azure Migrate のバージョン

Azure Migrate サービスには次の 2 つのバージョンがあります。

- **最新バージョン**:Azure Migrate プロジェクトの作成、オンプレミス マシンの検出、評価と移行の調整を行う場合はこちらのバージョンを使用します。 このバージョンの新機能については[こちらを参照してください](whats-new.md)。
- **前のバージョン**:前バージョンの Azure Migrate (評価のサポート対象がオンプレミスの VMware VM のみ) を使用している場合、今後は最新バージョンを使用してください。 前のバージョンを使用して Azure Migrate プロジェクトを作成することはできなくなりました。新しい検出を実行しないことをお勧めします。 既存のプロジェクトにアクセスするには、Azure portal > **[すべてのサービス]** で **Azure Migrate** を検索します。 Azure Migrate ダッシュボードに、通知と、過去の Azure Migrate プロジェクトにアクセスするためのリンクが表示されます。

## <a name="isv-integration"></a>ISV 統合

Azure ネイティブ ツールに加え、Azure Migrate は多数の ISV オファリングと統合されています。 必要なツールを特定し、それを Azure Migrate プロジェクトに追加します。 Azure と ISV のツール全体にわたって、Azure Migrate プロジェクト内から移行の過程を一元的に追跡できます。

**ISV** | **機能**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | 評価
[Device 42](https://docs.device42.com/) | 評価
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | 評価
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | 評価
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | 評価と移行
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | 移行

### <a name="selecting-an-isv-tool"></a>ISV ツールの選択

Azure Migrate プロジェクトに ISV ツールを追加した後は、ISV ポリシーに従って、ライセンスを取得するか、無料試用版にサインアップすることでツールの使用を開始します。 各ツールには、Azure Migrate に接続するためのオプションがあります。 ツールの指示とドキュメントに従って、ツールを Azure Migrate に接続します。

## <a name="azure-migrate-server-assessment"></a>Azure Migrate Server Assessment

Azure Migrate Server Assessment では、Azure への移行について、オンプレミスの VMware VM と Hyper-V VM を検出して評価します。 次を特定するために役立ちます。

- **Azure の準備状況:** オンプレミスのマシンが Azure に移行する準備ができているかどうかを評価します。
- **Azure のサイズ変更:** 移行後の Azure VM のサイズを見積もります。
- **Azure コストの見積もり:** Azure でオンプレミス サーバーを実行する場合のコストを見積もります。
- **依存関係の視覚化:** サーバー間の依存関係を特定し、依存サーバーを Azure に移行するための最適な方法を示します。 

Server Assessment では、オンプレミスで展開する軽量のアプライアンスを使用し、Server Assessment に登録します。

- アプライアンスによって、オンプレミスのマシンが検出され、Server Assessment に接続され、メタデータとパフォーマンスに関連するデータが継続的に Azure Migrate に送信されます。
- 検出はエージェントレスです。 検出された VM には何もインストールする必要はありません。
- マシンが検出されたら、移行する VM で構成されるグループにそれらをまとめます。
- グループの評価を作成します。 次に、評価を分析して、移行戦略を解明できます。

## <a name="azure-migrate-server-migration"></a>Azure Migrate Server Migration

Azure Migrate Server Migration により、オンプレミスの VMware VM、Hyper-V VM、物理サーバー、その他の仮想マシン、パブリック クラウド VM を Azure に移行できます。 マシンは、評価後でも、評価なしでも移行することができます。 

## <a name="azure-migrate-database-assessment"></a>Azure Migrate Database Assessment

Azure Migrate は、Azure SQL DB、Azure SQL Managed Instance、または SQL Server を実行する Azure VM に移行する対象のオンプレミスの SQL Server データベースを評価するために、Data Migration Assistant (DMA) と統合されます。 DMA からは、移行に関する潜在的な障害となっている問題に関する情報が提供されます。 サポートされていない機能だけでなく、移行後に利用できる新機能が特定されます。また、データベース移行の適切なパスを特定するために役立ちます。 [詳細情報](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)。


## <a name="azure-migrate-database-migration"></a>Azure Migrate Database Migration

Azure Migrate は、オンプレミスのデータベースを Azure に移行するために Azure Database Migration Service (DMS) と統合されています。 DMS を使用して、SQL、Azure SQL DB、Azure SQL マネージド インスタンスを実行している Azure VM にオンプレミスのデータベースを移行します。 [詳細情報](https://docs.microsoft.com/azure/dms/dms-overview)。

## <a name="web-app-assessment-and-migration"></a>Web アプリの評価と移行

Azure Migrate ハブから、オンプレミスの Web アプリを評価して Azure に移行することができます。

- **オンラインで Web アプリを評価する**:Azure App Service に移行するためにオンプレミスの Web サイトを評価するには、Azure App Service Migration Assistant を使用します。
- **Web アプリを移行する**:Azure App Service Migration Assistant を使用して、.NET および PHP Web アプリを Azure に移行します。

[詳細情報。](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>オフライン データの移行

Data Box のオフラインの製品ファミリを使用して、大量のデータを Azure に移動することができます。 [詳細情報](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>次の手順

- [VMware VM](tutorial-assess-vmware.md) と [Hyper-V VM](tutorial-assess-hyper-v.md) を評価するチュートリアルをお試しください。
- Azure Migrate の価格について、[詳しくはこちら](https://azure.microsoft.com/pricing/details/azure-migrate/)を参照してください。
- Azure Migrate について[よく寄せられる質問を確認](resources-faq.md)します。
