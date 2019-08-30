---
title: Azure Migrate - よく寄せられる質問 (FAQ) | Microsoft Docs
description: Azure Migrate についてよく寄せられる質問に対応します
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: bf591306220b2c8c1e6c8166686836d96432fc7d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856275"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - よく寄せられる質問 (FAQ)

この記事には、Azure Migrate に関してよく寄せられる質問が含まれます。 この記事の内容についてさらに質問がある場合は、[Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)に投稿してください。

## <a name="general"></a>全般

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Azure Migrate でサポートされている Azure の地域を教えてください。

[VMware の場合はこちら](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects)から、また [Hyper-V の場合はこちら](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)からご覧いただけます。

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Azure Migrate と Azure Site Recovery の違いは何ですか。

Azure Migrate は、マシンとワークロードの検出、評価、および Azure への移行を、開始、実行、追跡するための一元的なハブとして機能します。 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) はディザスター リカバリー ソリューションです。 Azure Migrate Server Migration は、Azure Site Recovery をバックエンドで利用して、オンプレミス マシンのリフトアンドシフト方式による移行シナリオを実現します。

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Azure Migrate アプライアンス (VMware/物理サーバー)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Azure Migrate アプライアンスはどのように Azure に接続しますか。

インターネット経由の接続、または ExpressRoute とパブリック/Microsoft ピアリングを使用した接続が可能です。

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate の Server Assessment と Server Migration に必要なネットワーク接続要件は何ですか。

Azure Migrate が Azure と通信するために必要な URL とポートについては、[VMware](migrate-support-matrix-vmware.md) と [Hyper-V](migrate-support-matrix-hyper-v.md) のサポート マトリックスを参照してください。

### <a name="can-i-harden-the-appliance-vm-i-set-up-with-the-template"></a>テンプレートを使用して設定したアプライアンス VM を強化することはできますか。

Azure Migrate アプライアンスに必要な通信およびファイアウォール規則が変わらないかぎり、新たなコンポーネント (ウイルス対策など) をテンプレートに追加できます。   

### <a name="what-data-is-collected-by-azure-migrate-appliance"></a>Azure Migrate アプライアンスによって収集されるデータは何ですか。

Azure Migrate アプライアンスによって収集されるデータの詳細については、[こちら](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware)を参照してください。

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>分析される VMware または Hyper-V 環境のパフォーマンスに影響は出ませんか。

Azure Migrate アプライアンスは、パフォーマンス データの継続的なプロファイリングによって、オンプレミス マシンをプロファイルして VM のパフォーマンス データを測定します。 これは、Hyper-V/ESXi ホストおよび vCenter Server のパフォーマンスにはほとんど影響しません。

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>収集されたデータはどこに、どれくらいの期間保存されますか。

Azure Migrate アプライアンスによって収集されたデータは、移行プロジェクトを作成するときに指定した Azure の場所に格納されます。 データは Microsoft サブスクリプションに安全に格納され、Azure Migrate プロジェクトを削除すると削除されます。

依存関係の視覚化用として VM にエージェントをインストールした場合は、依存関係エージェントによって収集されたデータが、Azure サブスクリプションで作成された米国の Log Analytics ワークスペースに格納されます。 サブスクリプションの Log Analytics ワークスペースを削除すると、このデータは削除されます。 [詳細情報](concepts-dependency-visualization.md)。

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-appliance-during-continuous-profiling"></a>Azure Migrate アプライアンスが継続的なプロファイリングでアップロードするデータの量はどれくらいですか。

Azure Migrate に送信されるデータの量は、いくつかのパラメーターによって異なります。 参考数値として、10 台のマシン (それぞれがディスクと NIC を 1 つずつ搭載) を使用する Azure Migrate プロジェクトは、1 日あたり約 50 MB を送信します。 これは概算値であり、NIC とディスクに対するデータ ポイントの数によって変わります (マシン、NIC、またはディスクの数が増えた場合、送信されるデータ量の変化は非線形です)。

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>データは保存時および転送中に暗号化されますか。

どちらも「はい」です。 メタデータは https を介してインターネット上で安全に Azure Migrate サービスに送信されます。 メタデータは Microsoft サブスクリプション内の [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) と [Azure BLOB ストレージ](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)に格納され、保存データは暗号化されます。

依存関係エージェントによって収集されたデータも転送中に暗号化され (安全な HTTPS)、ユーザー サブスクリプションの Log Analytics ワークスペースに格納されます。 また、保存データも暗号化されます。

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Azure Migrate アプライアンスは vCenter Server や Azure Migrate サービスとどのように通信しますか。

アプライアンスは、アプライアンスの設定時に提供された資格情報を使って vCenter Server (ポート 443) に接続します。 VMware PowerCLI を使用して vCenter Server を照会し、vCenter Server によって管理される VM に関するメタデータを収集します。 VM の構成データ (コア数、メモリ、ディスク数、NIC など) のほか、前月の各 VM のパフォーマンス履歴を収集します。 収集されたメタデータは、Azure Migrate Server Assessment に (HTTPS によってインターネット経由で) 送信されて評価されます。

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>同じアプライアンスを複数の vCenter Server に接続できますか。

いいえ。 アプライアンスと vCenter Server との間には一対一のマッピングが存在します。 複数の vCenter Server 上の VM を検出する必要がある場合は、複数のアプライアンスをデプロイする必要があります。 


### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>マシン サイズを変更しました。 評価を再実行できますか。

Azure Migrate アプライアンスは、オンプレミス環境の情報を継続的に収集します。 ただし、評価はオンプレミス VM のポイントインタイム スナップショットです。 評価する VM の設定を変更する場合は、[再計算] オプションを使って最新の変更で評価を更新してください。

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate-server-assessment"></a>Azure Migrate Server Assessment でマルチテナント環境を検出するにはどうすればよいですか。

VMware の場合、テナント間で環境が共有されているときに、あるテナントの VM が別のテナントのサブスクリプションで検出されないようにするには、検出したい VM だけにアクセスできる vCenter Server 資格情報を作成します。 その後、その資格情報を、Azure Migrate アプライアンスで検出を開始するときに使用します。

Hyper-V の場合、検出で Hyper-V ホストの資格情報が使用されます。VM が同じ Hyper-V ホストを共有しているときは、現時点では検出を分離する方法はありません。  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>1 つの移行アプライアンスでいくつの VM を検出できますか。

1 つの移行アプライアンスで最大 10,000 の VMware VM と最大 5,000 の Hyper-V VM を検出できます。  オンプレミス環境にさらに多くのマシンがある場合は、[Hyper-V](scale-hyper-v-assessment.md) と [VMware](scale-vmware-assessment.md) の評価のスケーリング方法を参照してください。

## <a name="azure-migrate-server-assessment"></a>Azure Migrate:Server Assessment

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate Server Assessment は物理サーバーの評価をサポートしていますか。

いいえ。現時点では Azure Migrate は物理サーバーの評価をサポートしていません。

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate が VMware 環境を検出するために vCenter Server は必要ですか。

はい。Azure Migrate が VMware 環境を検出するためには vCenter Server が必要です。 vCenter Server で管理されていない ESXi ホストの検出はサポートされていません。

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate:Server Assessment と Map Toolkit の違いは何ですか。

Azure Migrate:Server Assessment は、移行準備を支援する移行評価と、Azure への移行のためのワークロードの評価を提供します。 [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) にはそれ以外の機能があります。たとえば、新しいバージョンの Windows クライアントやサーバー オペレーティング システムでの移行計画や、ソフトウェア使用状況の追跡などです。 そのような目的には、引き続き MAP Toolkit を使用します。

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Azure Migrate:Server Assessment と Azure Site Recovery Deployment Planner の違いは何ですか。

Azure Migrate:Server Assessment は移行計画ツールです。 Azure Site Recovery Deployment Planner は、ディザスター リカバリー計画ツールです。

- **VMware/Hyper-V から Azure への移行**:オンプレミス サーバーを Azure に移行する場合は、Azure Migrate:Server Assessment ツールを使用して移行を計画します。 このツールは、オンプレミスのワークロードを評価し、Azure への移行に役立つガイダンス、分析情報、メカニズムを提供します。 移行計画の準備が完了したら、Azure Migrate:Server Migration などのツールを使ってマシンを Azure に移行できます。
- **VMware/Hyper-V から Azure へのディザスター リカバリー**:Site Recovery を使用して Azure へのディザスター リカバリーを行う場合は、Site Recovery Deployment Planner を使用してディザスター リカバリーを計画します。 Site Recovery Deployment Planner では、オンプレミス環境に対して Site Recovery 独自の詳細な評価が行われます。 Site Recovery でディザスター操作 (VM のレプリケーションやフェールオーバーなど) を適切に実行するために必要な推奨事項が示されます。

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Azure Migrate は Enterprise Agreement (EA) に基づくコスト見積もりをサポートしていますか。

Azure Migrate は現在、[Enterprise Agreement プラン](https://azure.microsoft.com/offers/enterprise-agreement-support/)のコスト見積もりをサポートしていません。 その対処策として、プランに従量課金制を指定し、評価プロパティの [割引] フィールドに、(サブスクリプションに適用される) 割引率を手動で指定します。

  ![Discount](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>オンプレミスに合わせたサイズ設定とパフォーマンス ベースのサイズ設定の違いは何ですか。

- オンプレミスに合わせたサイズ設定では、Azure Migrate が VM のパフォーマンス データを考慮しません。 オンプレミスの構成に基づいて VM のサイズが設定されます。 パフォーマンスベースのサイズ設定では、使用率データに基づいてサイズが設定されます。
- たとえば、オンプレミスの VM が 4 個のコアと 8 GB のメモリを使用し、その CPU 使用率とメモリ使用率がともに 50% である場合、オンプレミスに合わせたサイズ設定では、4 個のコアと 8 GB のメモリを備えた Azure VM SKU が推奨されます。 一方、パフォーマンスベースのサイズ設定では、使用率が考慮されるため、コアが 2 個で 4 GB の VM SKU が推奨されます。
- 同様に、ディスクのサイズ設定は、サイズ設定基準とストレージの種類という 2 つの評価プロパティに依存します。
サイズ設定基準がパフォーマンス ベースで、ストレージの種類が自動の場合は、ターゲット ディスクの種類 (Standard または Premium) を特定するときに、ディスクの IOPS とスループットの値が考慮されます。
- サイズ設定基準がパフォーマンス ベースで、ストレージの種類が Premium の場合は、Premium ディスクが推奨されます。 Premium ディスクの SKU は、オンプレミス ディスクのサイズに基づいて選択されます。 サイズ設定基準が、オンプレミスに合わせたサイズ設定で、ストレージの種類が Standard または Premium である場合は、同じロジックでディスクのサイズ設定が行われます。

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>パフォーマンス履歴と百分位数での使用率は、サイズの推奨にどのような影響を与えますか?

これらのプロパティが適用されるのは、パフォーマンス ベースのサイズ設定の場合のみです。

- Azure Migrate では、オンプレミス マシンのパフォーマンス履歴が収集され、それを使用して Azure での VM のサイズとディスクの種類が推奨されます。
- アプライアンスによりオンプレミス環境が継続的にプロファイルされて、20 秒ごとにリアルタイムの使用率データが収集されます このアプライアンスでは、20 秒のサンプルがロールアップされて、15 分ごとに 1 つのデータ ポイントが作成されます。 1 つのデータ ポイントを作成するために、アプライアンスでは 20 秒のすべてのサンプルからピーク値が選択されて、Azure に送信されます。
- パフォーマンスの期間とパフォーマンス履歴の百分位値に基づいて Azure で評価を作成する場合は、Azure Migrate で有効な使用率値が計算され、その値がサイズ設定に使用されます
- たとえば、パフォーマンスの期間を 1 日に設定し、百分位値を 95 に設定すると、Azure Migrate は、過去 1 日間にコレクターから送信された 15 分のサンプル ポイントを昇順で並べ替えて、95 番目の百分位値を有効な使用率として選択します。
- 95 番目の百分位値を使用することにより、99 番目の百分位を使用した場合に発生する可能性がある外れ値がすべて無視されるようになります。 期間中のピーク使用率を選択し、外れ値を見逃さないようにする場合は、99 番目の百分位を選択する必要があります。

### <a name="what-is-dependency-visualization"></a>依存関係の視覚化とは何ですか。

依存関係の視覚化により、信頼性を高めて移行対象の VM のグループを評価できます。 これにより、評価を実行する前に、マシンの依存関係がクロスチェックされます。 依存関係の視覚化により、見落としがなくなり、Azure への移行時に予期しない障害を回避できます。 Azure Migrate では、依存関係の視覚化を可能にするために、Azure Monitor ログで Service Map ソリューションが利用されます。

> [!NOTE]
> 依存関係可視化機能は、Azure Government では使用できません。

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>依存関係の視覚化機能の利用には料金の支払いが発生しますか。

いいえ。 Azure Migrate の価格について、[詳しくはこちら](https://azure.microsoft.com/pricing/details/azure-migrate/)を参照してください。

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>依存関係の視覚化のために何をインストールする必要がありますか。

依存関係の視覚化を使用するには、評価するオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。

- 各マシンに [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) をインストールする必要があります。
- 各マシンに [Dependency Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) をインストールする必要があります。
- また、インターネットに接続されていないマシンの場合、それらに Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。

依存関係の視覚化を使用している場合を除き、これらのエージェントは不要です。

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>依存関係の視覚化に既存のワークスペースを使用できますか。

はい。既存のワークスペースを移行プロジェクトにアタッチして依存関係の視覚化に活用できます。 [詳細情報](concepts-dependency-visualization.md#how-does-it-work)。

### <a name="can-i-export-the-dependency-visualization-report"></a>依存関係の視覚化のレポートはエクスポートできますか。

いいえ。依存関係の視覚化はエクスポートできません。 ただし、Azure Migrate では依存関係の視覚化に Service Map が使用されているため、[Service Map REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) を使用すると、JSON 形式で依存関係を取得できます。

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Microsoft Monitoring Agent (MMA) と依存関係エージェントのインストールを自動化するには、どうすればよいですか。

エージェントのインストールには、[こちらのスクリプトを使用](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)します。 コマンド ラインまたはオートメーションを使用して MMA をインストールするには、[こちらの手順](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)に従ってください。 MMA には[こちらのスクリプト](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)を利用します。

スクリプトのほかにも、System Center Configuration Manager や [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) などのデプロイ ツールを使ってエージェントをデプロイできます。

### <a name="what-operating-systems-are-supported-by-mma"></a>MMA でサポートされるオペレーティング システムはどれですか。

- MMA でサポートされる Windows オペレーティング システムは[こちらで確認](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)してください。
- MMA でサポートされる Linux オペレーティング システムは[こちらで確認](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)してください。

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>依存関係エージェントでサポートされるオペレーティング システムはどれですか。

依存関係エージェントでサポートされる Windows オペレーティング システムは[こちらで確認](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)してください。
依存関係エージェントでサポートされる Linux オペレーティング システムは[こちらで確認](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)してください。

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Azure Migrate で 1 時間より長く依存関係を視覚化することはできますか。
いいえ。依存関係を視覚化できるのは最大で 1 時間です。 履歴の特定の日付に戻ることができるのは最大で 1 か月前までですが、視覚化の期間は最大で 1 時間です。 たとえば、依存関係マップで期間を指定して昨日の依存関係を見ることはできますが、表示できる時間枠は 1 時間だけです。 ただし、Azure Monitor ログを使用すれば、それより長い期間について[依存関係データのクエリを実行](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)できます。

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>依存関係の視覚化では、10 を超える VM を含むグループはサポートされていますか。
最大 10 台の VM を含む[グループの依存関係を視覚化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)できます。 グループ内の VM の数が 10 を超える場合は、グループを小さなグループに分割してから依存関係を視覚化することをお勧めします。

## <a name="azure-migrate-server-migration"></a>Azure Migrate:Server Migration

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Azure Migrate:Server Migration と Azure Site Recovery の違いは何ですか。

Azure Migrate:Server Migration では、Site Recovery のレプリケーション エンジンを利用して、Azure への VMware VM のエージェント ベースの移行、Hyper-V VM の移行、および物理サーバーの移行が行われます。 VMware VM の移行のエージェントを使用しないオプションが Server Migration でネイティブに構築されます。

## <a name="next-steps"></a>次の手順
[Azure Migrate](migrate-services-overview.md) の概要を確認する
