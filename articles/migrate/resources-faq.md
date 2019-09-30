---
title: Azure Migrate - よく寄せられる質問 (FAQ) | Microsoft Docs
description: Azure Migrate についてよく寄せられる質問に対応します
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: ec4cb58692cd98a799f1dc58f60b11a0552829c8
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934920"
---
# <a name="azure-migrate-frequently-asked-questions-faq"></a>Azure Migrate: よく寄せられる質問 (FAQ)

この記事では、Azure Migrate についてよく寄せられる質問に回答します。 この記事の内容についてさらに質問がある場合は、[Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)に投稿してください。

## <a name="general"></a>全般

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Azure Migrate では Azure のどの地域がサポートされていますか?

[VMware の一覧](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects)と [Hyper-V の一覧](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)を参照してください。

### <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure Migrate と Azure Site Recovery の違い

Azure Migrate には、移行の開始、コンピューターとワークロードの検出と評価の実行と追跡、Azure へのマシンとワークロードの移行の実行と追跡を行う一元的なハブが用意されています。 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) はディザスター リカバリー ソリューションです。 Azure Migrate Server Migration は、Azure Site Recovery をバックエンドで利用して、オンプレミス マシンのリフトアンドシフト方式による移行シナリオを実現します。

### <a name="how-do-i-delete-an-azure-migrate-project"></a>Azure Migrate プロジェクトを削除するにはどうすればよいですか

Azure Migrate プロジェクトと、その関連リソース (サイト、Recovery Services コンテナー、移行コンテナー、キー コンテナー、評価プロジェクトなど) を削除するには、Azure portal の [リソース グループ] ページに移動し、移行プロジェクトが作成されたリソース グループを選択し、[非表示の型の表示] を選択します。 次に、移行プロジェクトと、以下の一覧に示されている関連リソースを選択して、削除します。 また、リソース グループが移行プロジェクトとその関連リソースによって排他的に使用されている場合は、リソース グループ全体を削除することもできます。 この一覧は、すべてのシナリオ (検出、評価、および移行) で作成されるすべてのリソースの種類の完全な一覧であることに注意してください。 このリソース グループでは、実際のシナリオ用に作成されたリソースのみが表示されます。

#### <a name="resources-created-for-discovered-assessed-or-migrated-servers-on-vmware-or-physical-servers-resource-type"></a>VMware または物理サーバー上で検出、評価、または移行されたサーバー用に作成されたリソース [リソース (種類)]:

- "Appliancename"kv (キー コンテナー)
- "Appliancename"site (Microsoft.OffAzure/VMwareSites)
- "ProjectName" (Microsoft.Migrate/migrateprojects)
- "ProjectName"project (Microsoft.Migrate/assessmentProjects)
- "ProjectName"rsvault (Recovery Services コンテナー)
- "ProjectName"-MigrateVault-* (Recovery Services コンテナー)
- migrateappligwsa* (ストレージ アカウント)
- migrateapplilsa* (ストレージ アカウント)
- migrateapplicsa* (ストレージ アカウント)
- migrateapplikv* (キー コンテナー)
- migrateapplisbns16041 (Service Bus 名前空間)

注:ストレージ アカウントとキー コンテナーは、それぞれアプリケーション データとセキュリティ キーが含まれている可能性があるため、注意して削除してください。

#### <a name="resources-created-for-discovered-assessed-or-migrated-servers-on-hyper-v-resource-type"></a>Hyper-V 上で検出、評価、または移行されたサーバー用に作成されたリソース [リソース (種類)]:

- "ProjectName" (Microsoft.Migrate/migrateprojects)
- "ProjectName"project (Microsoft.Migrate/assessmentProjects)
- HyperV*kv (キー コンテナー)
- HyperV*site (Microsoft.OffAzure/HyperVSites)
- "ProjectName"-MigrateVault-* (Recovery Services コンテナー) 

注:キー コンテナーは、セキュリティ キーが含まれている可能性があるため、注意して削除してください。


## <a name="azure-migrate-appliance"></a>Azure Migrate アプライアンス

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Azure Migrate アプライアンスはどのように Azure に接続しますか。

インターネット経由の接続、または Azure ExpressRoute とパブリック/Microsoft ピアリングを使用した接続が可能です。

### <a name="what-are-the-network-connectivity-requirements-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate のサーバーの評価と移行に必要なネットワーク接続要件は何ですか。

Azure Migrate が Azure と通信できるようにするために必要な URL とポートについては、[VMware](migrate-support-matrix-vmware.md) と [Hyper-V](migrate-support-matrix-hyper-v.md) のサポート マトリックスを参照してください。

### <a name="can-i-harden-the-appliance-vm-that-i-set-up-with-the-template"></a>テンプレートを使用して設定したアプライアンス VM を強化することはできますか。

Azure Migrate アプライアンスに必要な通信とファイアウォールの規則をそのままにしておく限り、コンポーネント (ウイルス対策など) をテンプレートに追加できます。

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスによって収集されるデータは何ですか。

Azure Migrate アプライアンスによって収集されるデータの詳細については、[Azure Migrate のドキュメント](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware)を参照してください。

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>分析される VMware または Hyper-V 環境のパフォーマンスに影響は出ませんか。

Azure Migrate アプライアンスでは、VM のパフォーマンス データを測定するために、オンプレミスのマシンが継続的にプロファイルされます。 このプロファイルは、Hyper-V/ESXi ホストや vCenter Server のパフォーマンスにほとんど影響しません。

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>格納データはどこに、どれくらいの期間保存されますか。

Azure Migrate アプライアンスによって収集されたデータは、移行プロジェクトを作成するときに選択した Azure の場所に格納されます。 データは Microsoft サブスクリプションに安全に格納され、Azure Migrate プロジェクトを削除すると削除されます。

依存関係の視覚化用として VM にエージェントをインストールした場合は、依存関係エージェントによって収集されたデータが、Azure サブスクリプションで作成された米国の Log Analytics ワークスペースに格納されます。 サブスクリプションの Log Analytics ワークスペースを削除すると、このデータは削除されます。 詳細については、「[依存関係の視覚化](concepts-dependency-visualization.md)」を参照してください。

### <a name="what-volume-of-data-is-uploaded-by-the-azure-migrate-appliance-during-continuous-profiling"></a>継続的プロファイリング時に Azure Migrate アプライアンスによってアップロードされるデータの量はどのくらいですか。

Azure Migrate に送信されるデータの量は、いくつかのパラメーターによって変わります。 量の概要を説明すると、10 台のマシン (それぞれ 1 台のディスクと 1 つの NIC を搭載) の Azure Migrate プロジェクトからは、1 日あたり約 50 MB が送信されます。 この値は近似値です。 NIC とディスクのデータ ポイント数に基づいて変化します (マシン、NIC、またはディスクの数が増える場合、送信されるデータの増加は非線形です)。

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>保存時と転送中のデータは暗号化されますか。

はい、両方です。 メタデータは HTTPS を介してインターネット上で安全に Azure Migrate サービスに送信されます。 メタデータは、Microsoft サブスクリプションの [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) データベースと [Azure Blob ストレージ](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)に格納されます。 メタデータは保存時に暗号化されます。

また、依存関係エージェントによって収集されたデータも転送中に暗号化されます (セキュア HTTPS)。 これは、サブスクリプションの Log Analytics ワークスペースに格納されます。 また、保存時にも暗号化されます。

### <a name="how-does-the-azure-migrate-appliance-communicate-with-vcenter-server-and-the-azure-migrate-service"></a>Azure Migrate アプライアンスは vCenter Server や Azure Migrate サービスとどのように通信しますか。

アプライアンスは、アプライアンスの設定時に提供された資格情報を使って vCenter Server (ポート 443) に接続します。 VMware PowerCLI を使用して vCenter Server に照会され、vCenter Server によって管理される VM に関するメタデータが収集されます。 VM (コア、メモリ、ディスク、NIC など) に関する構成データと、過去 1 か月間の各 VM のパフォーマンス履歴も収集されます。 収集されたメタデータは、Azure Migrate Server Assessment に (HTTPS によってインターネット経由で) 送信されて評価されます。

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>同じアプライアンスを複数の vCenter Server インスタンスに接続できますか。

いいえ。 アプライアンスと vCenter Server との間には一対一のマッピングが存在します。 複数の vCenter Server インスタンス上の VM を検出する必要がある場合は、複数のアプライアンスをデプロイする必要があります。


### <a name="i-changed-my-machine-size-can-i-run-the-assessment-again"></a>マシン サイズを変更しました。 評価を再実行することはできますか。

Azure Migrate アプライアンスは、オンプレミス環境の情報を継続的に収集します。 ただし、評価はオンプレミス VM のポイントインタイム スナップショットです。 評価する VM の設定を変更する場合は、[再計算] オプションを使って最新の変更で評価を更新してください。

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment-in-azure-migrate-server-assessment"></a>Azure Migrate Server Assessment のマルチテナント環境で検出を実行するにはどうすればよいですか。

VMware の場合、テナント間で環境が共有されているときに、あるテナントの VM が別のテナントのサブスクリプションで検出されないようにするには、検出したい VM だけにアクセスできる vCenter Server 資格情報を作成します。 次に、Azure Migrate アプライアンスで検出を開始するときに、これらの資格情報を使用します。

Hyper-V の場合、検出には Hyper-V ホストの資格情報が使用されます。 VM が同じ Hyper-V ホストを共有している場合、現在、検出を分離する方法はありません。  

### <a name="how-many-vms-can-i-discover-with-a-single-migration-appliance"></a>1 つの移行アプライアンスで検出できる VM の数を教えてください。

1 つの移行アプライアンスで最大 10,000 の VMware VM と最大 5,000 の Hyper-V VM を検出できます。 オンプレミス環境にさらに多くのマシンがある場合は、[Hyper-V](scale-hyper-v-assessment.md) と [VMware](scale-vmware-assessment.md) の評価のスケーリング方法を参照してください。

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>Azure Migrate アプライアンスをプロジェクトから削除できますか。
現在、プロジェクトからアプライアンスを削除することはサポートされていません。 アプライアンスを削除する唯一の方法は、アプライアンスに関連付けられている Azure Migrate プロジェクトがあるリソース グループを削除することですが、他の登録アプライアンス、検出されたインベントリ、評価、リソース グループ内のプロジェクトに関連付けられているその他すべての Azure 成果物も削除されます。

## <a name="azure-migrate-server-assessment"></a>Azure Migrate Server Assessment

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate Server Assessment では物理サーバーの評価がサポートされていますか。

いいえ。現時点では Azure Migrate は物理サーバーの評価をサポートしていません。

### <a name="does-azure-migrate-need-vcenter-server-to-perform-discovery-in-a-vmware-environment"></a>VMware 環境で検出を実行するために Azure Migrate に vCenter Server は必要ですか。

はい。VMware 環境で検出を実行するには Azure Migrate に vCenter Server が必要です。 vCenter Server で管理されていない ESXi ホストの検出はサポートされていません。

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate Server Assessment と MAP Toolkit の違いは何ですか。

Azure Migrate Server Assessment には、移行準備を支援する移行評価と、Azure への移行のためのワークロードの評価があります。 [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) は、新しいバージョンの Windows クライアントやサーバー オペレーティング システムでの移行計画や、ソフトウェア使用状況の追跡など、他のタスクに役立ちます。 そのような目的には、引き続き MAP Toolkit を使用します。

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-azure-site-recovery-deployment-planner"></a>Azure Migrate Server Assessment と Azure Site Recovery Deployment Planner の違いは何ですか。

Azure Migrate Server Assessment は、移行計画ツールです。 Azure Site Recovery Deployment Planner は、ディザスター リカバリー計画ツールです。

**VMware/Hyper-V から Azure への移行**:オンプレミスのサーバーを Azure に移行する予定がある場合は、移行計画に Azure Migrate Server Assessment を使用します。 オンプレミスのワークロードが評価され、サーバーを Azure に移行する際に役立つガイダンス、分析情報、ツールが用意されています。 移行計画の準備ができたら、Azure Migrate Server Migration などのツールを使用して、マシンを Azure に移行できます。

**VMware/Hyper-V から Azure へのディザスター リカバリー**:Site Recovery を使用した Azure へのディザスター リカバリーについては、計画のために Site Recovery Deployment Planner を使用します。 Site Recovery Deployment Planner では、オンプレミス環境に対して Site Recovery 独自の詳細な評価が行われます。 Site Recovery でディザスター操作 (VM のレプリケーションやフェールオーバーなど) を適切に実行するために必要な推奨事項が示されます。

### <a name="does-azure-migrate-support-cost-estimation-for-the-enterprise-agreement-ea-program"></a>Azure Migrate は、Enterprise Agreement (EA) プログラムのコスト見積もりをサポートしていますか。

現在、Azure Migrate は [Enterprise Agreement プログラム](https://azure.microsoft.com/offers/enterprise-agreement-support/)のコスト見積もりをサポートしていません。 その対処策として、 **[オファー]** に **[従量課金制]** を指定し、評価プロパティの **[割引]** ボックスに、(サブスクリプションに適用される) 割引率を手動で指定します。

  ![評価のプロパティ](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>オンプレミスに合わせたサイズ設定とパフォーマンス ベースのサイズ設定の違いは何ですか。

オンプレミスに合わせたサイズ設定では、Azure Migrate が VM のパフォーマンス データを考慮しません。 オンプレミスの構成に基づいて VM のサイズが設定されます。 パフォーマンスベースのサイズ設定では、使用率データに基づいてサイズが設定されます。

たとえば、50% の CPU 使用率と 50% のメモリ使用率で 4 コアと 8 GB のメモリを備えたオンプレミスの VM について考えてみましょう。 この VM の場合、オンプレミスのサイズ設定では、4 コアと 8 GB のメモリを備えた Azure VM SKU が推奨されます。 パフォーマンスベースのサイズ設定では、使用率が考慮されるため、2 コアと 4 GB のメモリを備えた VM SKU を使用することをお勧めします。

同様に、ディスクのサイズ設定は、サイズ設定基準とストレージの種類という 2 つの評価プロパティに依存します。 サイズ設定基準がパフォーマンスベースで、ストレージの種類が自動の場合、Azure Migrate では、ターゲット ディスクの種類 (Standard または Premium) を識別するときに、ディスクの IOPS とスループットの値が考慮されます。

サイズ設定基準がパフォーマンスベースでストレージの種類が Premium の場合、Azure Migrate では Premium ディスクが推奨されます。 Premium ディスクの SKU は、オンプレミス ディスクのサイズに基づいて選択されます。 サイズ設定基準が、オンプレミスに合わせたサイズ設定で、ストレージの種類が Standard または Premium である場合は、同じロジックでディスクのサイズ設定が行われます。

### <a name="what-impact-does-performance-history-and-utilization-percentile-have-on-size-recommendations"></a>パフォーマンス履歴と使用率パーセンタイルのサイズに関する推奨事項にはどのような影響がありますか。

これらのプロパティが適用されるのは、パフォーマンス ベースのサイズ設定の場合のみです。

Azure Migrate では、オンプレミス マシンのパフォーマンス履歴が収集され、それを使用して Azure での VM のサイズとディスクの種類が推奨されます。

アプライアンスによりオンプレミス環境が継続的にプロファイルされて、20 秒ごとにリアルタイムの使用率データが収集されます このアプライアンスでは、20 秒のサンプルがロールアップされて、15 分ごとに 1 つのデータ ポイントが作成されます。 データ ポイントを作成するために、アプライアンスではすべての 20 秒のサンプルからピーク値が選択されます。 このデータ ポイントは、アプライアンスから Azure に送信されます。

パフォーマンスの期間とパフォーマンス履歴のパーセンタイル値に基づいて Azure で評価を作成する場合は、Azure Migrate で有効な使用率値が計算され、その値がサイズ設定に使用されます

たとえば、パフォーマンスの期間を 1 日に、パーセンタイル値を 95 パーセンタイルに設定したとします。 Azure Migrate では、コレクターから過去 1 日間に送信された 15 分間のサンプル ポイントが昇順で並べ替えられ、95 パーセンタイル値が有効な使用率として選択されます。

95 パーセンタイル値にすると、外れ値が無視されます。 99 パーセンタイルを使用する場合は、外れ値が含まれる可能性があります。 期間中のピーク使用率を選択し、外れ値を見逃さないようにする場合は、99 パーセンタイルを選択する必要があります。

### <a name="what-is-dependency-visualization"></a>依存関係の視覚化とは何ですか。

依存関係の視覚化により、信頼性を高めて移行対象の VM のグループを評価できます。 これにより、評価を実行する前に、マシンの依存関係がクロスチェックされます。 依存関係の視覚化により、見落としがなくなり、Azure への移行時に予期しない障害を回避できます。 Azure Migrate では、依存関係の視覚化を実現するために Azure Monitor ログの Service Map ソリューションを使用します。

> [!NOTE]
> 依存関係の視覚化は、Azure Government では使用できません。

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>依存関係の視覚化を使用するために料金はかかりますか。

いいえ。 詳細については、「[Azure Migrate の価格](https://azure.microsoft.com/pricing/details/azure-migrate/)」を参照してください。

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>依存関係の視覚化のために何をインストールする必要がありますか。

依存関係の視覚化を使用するには、評価するオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。

次のエージェントを各マシンにインストールする必要があります。
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- [依存関係エージェント](../azure-monitor/platform/agents-overview.md#dependency-agent)。
- インターネットに接続されていないマシンの場合、それらに Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。

依存関係の視覚化を使用している場合を除き、これらのエージェントは不要です。

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>依存関係の視覚化に既存のワークスペースを使用できますか。

はい。既存のワークスペースを移行プロジェクトにアタッチして依存関係の視覚化に活用できます。 詳細については、[依存関係の視覚化](concepts-dependency-visualization.md#how-does-it-work)に関する記事の「しくみ」を参照してください。

### <a name="can-i-export-the-dependency-visualization-report"></a>依存関係の視覚化のレポートはエクスポートできますか。

いいえ。依存関係の視覚化はエクスポートできません。 ただし、Azure Migrate では依存関係の視覚化に Service Map が使用されているため、[Service Map REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) を使用すると、JSON 形式で依存関係を取得できます。

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Microsoft Monitoring Agent (MMA) と依存関係エージェントのインストールを自動化するには、どうすればよいですか。

こちらの[依存関係エージェントをインストールするスクリプト](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)を使用してください。 コマンド ラインまたはオートメーションを使用して [MMA をインストールするには、こちらの手順](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)に従ってください。 MMA の場合は、[このスクリプト](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)を使用します。

スクリプトのほか、デプロイ ツール (System Center Configuration Manager と [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)) を利用して、エージェントをデプロイすることもできます。

### <a name="what-operating-systems-are-supported-by-mma"></a>MMA でサポートされるオペレーティング システムはどれですか。

- [MMA でサポートされる Windows オペレーティング システムの一覧はこちら](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)を参照してください。
- [MMA でサポートされる Linux オペレーティング システムの一覧はこちら](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)を参照してください。

### <a name="what-operating-systems-are-supported-by-the-dependency-agent"></a>依存関係エージェントではどのオペレーティング システムがサポートされていますか。

[Azure Monitor for VMs がサポートしている Windows および Linux オペレーティング システム](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)の一覧を参照してください。

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Azure Migrate で 1 時間より長く依存関係を視覚化することはできますか。
いいえ。 依存関係を視覚化できるのは最大で 1 時間です。 履歴内で最大 1 か月前の特定の日付に戻ることができますが、視覚化の最大期間は 1 時間です。 たとえば、依存関係マップで期間を指定して昨日の依存関係を見ることはできますが、表示できる時間枠は 1 時間だけです。 ただし、Azure Monitor ログを使用すれば、それより長い期間について[依存関係データのクエリを実行](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)できます。

### <a name="can-i-use-dependency-visualization-for-groups-that-contain-more-than-10-vms"></a>VM 数が 10 個を超えるグループに依存関係の視覚化を使用できますか。
最大 10 個の VM を含む[グループの依存関係を視覚化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)できます。 グループ内の VM の数が 10 を超える場合は、グループを小さなグループに分割してから依存関係を視覚化することをお勧めします。

## <a name="azure-migrate-server-migration"></a>Azure Migrate Server Migration

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-azure-site-recovery"></a>Azure Migrate Server Migration と Azure Site Recovery の違いは何ですか。

Azure Migrate Server Migration では、Site Recovery のレプリケーション エンジンを利用して、Azure への VMware VM のエージェント ベースの移行、Hyper-V VM の移行、および物理サーバーの移行が行われます。 VMware VM の移行のエージェントを使用しないオプションが Server Migration でネイティブに構築されます。

## <a name="next-steps"></a>次の手順
[Azure Migrate の概要](migrate-services-overview.md)を確認します。
