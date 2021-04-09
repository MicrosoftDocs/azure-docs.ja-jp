---
title: Azure Advisor を使用してサービス コストを削減する
description: Azure Advisor を使用して、Azure のデプロイにかかるコストを最適化します。
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 346b790c6970abc8670661e1ec180662957af47d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503376"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>Azure Advisor を使用してサービス コストを削減する

Azure Advisor は、アイドル状態にあるリソースや活用されていないリソースを識別することによって Azure を最適化し、総合的な Azure の支出を削減します。  コストに関する推奨事項は、Advisor ダッシュボードの **[コスト]** タブで取得できます。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>使用率が低いインスタンスをサイズ変更またはシャットダウンして仮想マシンの支出を最適化する 

特定のアプリケーション シナリオでは、設計によっては使用率が低くなる場合がありますが、多くの場合、仮想マシンのサイズと数を管理することによってコストを削減できます。 

推奨されるアクションは、評価対象のリソースに基づき、シャットダウンかサイズ変更になります。

次のすべてのステートメントが当てはまる場合、Advisor の高度な評価モデルでは仮想マシンのシャットダウンが検討されます。 
- CPU 使用率の最大値の P95th が 3% 未満である。 
- 7 日間にわたるネットワーク使用率が 2% 未満である。
- メモリ不足がしきい値より低い

Advisor は、現在の負荷を (同じ SKU ファミリ内の) より小さな SKU またはより少数インスタンスに次のように合わせることができる場合、仮想マシンのサイズ変更を検討します。
- 現在の負荷が、ユーザー向けでないワークロードの 80% の使用率を超えない。 
- 負荷が、ユーザー向けワークロードの 40% を超えない。 

ここで、Advisor により、ワークロードの CPU 使用率の特性を分析することによってワークロードの種類が決定されます。

Advisor からは、いずれかの推奨アクション (シャットダウンまたはサイズ変更) によるコスト削減の見積もりが提示されます。 サイズ変更の場合、Advisor により現在とターゲットの SKU 情報が提供されます。

使用率が低い仮想マシンをより積極的に特定する場合は、サブスクリプションごとに CPU 使用率ルールを調整できます。

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>適切なサイズ変更により MariaDB、MySQL、PostgreSQL サーバーのコストを最適化する 
Advisor は使用状況を分析し、MariaDB、MySQL、または PostgreSQL データベース サーバーのリソースが過去 7 日間にわたって長期に過少使用されていないかどうかを評価します。 リソース使用率が低いと、不要な支出が発生しますが、これはパフォーマンスに大きな影響を与えずに修正できます。 コストを削減し、リソースを効率的に管理するには、コンピューティング サイズ (仮想コア) を半分に減らすことをお勧めします。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>プロビジョニングが解除された ExpressRoute 回線を排除してコストを削減する

Advisor により、Azure ExpressRoute 回線が 1 か月を超えてプロバイダー内で **未プロビジョニング** 状態であることが検出されました。 接続プロバイダーで回線をプロビジョニングする予定がない場合は、回線を削除することが推奨されます。

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>アイドル状態の仮想ネットワーク ゲートウェイを削除または再構成してコストを削減する

Advisor は、90 日を超えてアイドル状態にある仮想ネットワークのゲートウェイを特定します。 これらのゲートウェイは 1 時間単位で課金されるため、今後使用する予定がない場合は、再構成または削除を検討する必要がありあす。 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>従量課金のコストより費用を節約するために、予約された仮想マシン インスタンスを購入する

Advisor は、過去 30 日間の仮想マシンの使用率を確認し、Azure 予約を購入することでコストを節約できるかどうかを判断します。 Advisor には、節約の可能性が最も高いリージョンとサイズ、および予約の購入によって予想される節約額が表示されます。 Azure Reservation では、お使いの仮想マシンの基本コストを事前購入できます。 割引は、予約と同じサイズとリージョンの新しいまたは既存の VM に自動適用されます。 [Azure Reserved VM Instances の詳細を参照してください。](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor では、今後 30 日以内に有効期限が切れる予約インスタンスについても通知されます。 従量課金制を避けるために、新しい予約インスタンスを購入することが推奨されます。

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>複数のリソースの種類の予約インスタンスを購入して従量課金制よりもコストを節約する

Advisor は、次のリソースについて過去 30 日間の使用パターンを分析し、コストを最適化するために予約容量の購入を推奨します。

### <a name="azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB の予約容量
Advisor は過去 30 日間の Azure Cosmos DB の使用パターンを分析し、コストを最適化するために予約容量の購入を推奨します。 予約容量を使用することにより、Azure Cosmos DB の時間単位の使用量を事前に購入して、従量課金制よりもコストを削減できます。 予約容量は課金特典であり、新規および既存のデプロイに対して自動的に適用されます。 Advisor では、個々のサブスクリプションに対する削減の見積もりを計算するために、3 年分の予約価格が使用され、また過去 30 日間に観察された使用パターンから推定されます。 予約容量の購入では、共有スコープの推奨事項を利用できるため、さらにコストを削減することができます。

### <a name="sql-database-and-sql-managed-instance-reserved-capacity"></a>SQL Database と SQL Managed Instance の予約容量
Advisor では、過去 30 日間における SQL データベースと SQL Managed Instance の使用パターンが分析されます。 その後、コストを最適化するための予約容量の購入が推奨されます。 予約容量を使用すると、SQL DB の時間単位の使用量を事前に購入することで、SQL のコンピューティング コストを削減できます。 SQL ライセンスは別途課金され、予約によって割引されることはありません。 予約容量は課金特典であり、新規および既存のデプロイに対して自動的に適用されます。 Advisor では、個々のサブスクリプションに対する削減の見積もりを計算するために、3 年分の予約価格が使用され、また過去 30 日間に観察された使用パターンから推定されます。 予約容量の購入では、共有スコープの推奨事項を利用できるため、さらにコストを削減することができます。 詳細については、「[SQL Database と SQL Managed Instance の予約容量](../azure-sql/database/reserved-capacity-overview.md)」を参照してください。

### <a name="app-service-stamp-fee-reserved-capacity"></a>App Service 登録料の予約容量
Advisor は、過去 30 日間の Azure App Service 分離環境の登録料使用パターンを分析し、コストを最適化するための予約容量の購入を推奨します。 予約容量を使用すると、分離環境の登録料について時間単位の使用量を事前に購入することで、従量課金制よりもコストを削減できます。 予約容量は、App Service インスタンスではなく、登録料にのみ適用されることに注意してください。 予約容量は課金特典であり、新規および既存のデプロイに対して自動的に適用されます。 Advisor では、個々のサブスクリプションに対するコスト削減の見積もりを計算するために、過去 30 日間の使用パターンに基づく 3 年分の予約価格が使用されます。

### <a name="blob-storage-reserved-capacity"></a>BLOB ストレージの予約容量
Advisor は、過去30日間の Azure Blob Storage と Azure Data Lake ストレージの使用量を分析します。 その後、コストを最適化するための予約容量の購入が計算されます。 予約容量では、時間単位の使用量を事前に購入することで、現在のオンデマンドのコストを削減できます。 BLOB ストレージの予約容量は、Azure BLOB 汎用 v2 および Azure Data Lake Storage Gen2 アカウントの格納データにのみ適用されます。 予約容量は課金特典であり、新規および既存のデプロイに対して自動的に適用されます。 Advisor では、個々のサブスクリプションに対する削減の見積もりを計算するために、3 年分の予約価格と過去 30 日間に観察された使用パターンが使用されます。 予約容量の購入では、共有スコープの推奨事項を利用できるため、さらにコストを削減することができます。

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>MariaDB、MySQL、および PostgreSQL の予約容量
Advisor は、過去 30 日間の Azure Database for MariaDB、Azure Database for MySQL、Azure Database for PostgreSQL の使用パターンを分析します。 その後、コストを最適化するための予約容量の購入が推奨されます。 予約容量を使用すると、MariaDB、MySQL、および PostgreSQL の時間単位の使用量を事前に購入することで、現在のコストを削減できます。 予約容量は課金特典であり、新規および既存のデプロイに対して自動的に適用されます。 Advisor では、個々のサブスクリプションに対する削減の見積もりを計算するために、3 年分の予約価格と過去 30 日間に観察された使用パターンが使用されます。 予約容量の購入では、共有スコープの推奨事項を利用できるため、さらにコストを削減することができます。

### <a name="azure-synapse-analytics-reserved-capacity"></a>Azure Synapse Analytics の予約容量
Advisor は過去 30 日間の Azure Synapse Analytics の使用パターンを分析し、コストを最適化する予約容量の購入を推奨します。 予約容量を使用すると、Synapse Analytics の時間単位の使用量を事前に購入することで、オンデマンドのコストを削減できます。 予約容量は課金特典であり、新規および既存のデプロイに対して自動的に適用されます。 Advisor では、個々のサブスクリプションに対する削減の見積もりを計算するために、3 年分の予約価格と過去 30 日間に観察された使用パターンが使用されます。 予約容量の購入では、共有スコープの推奨事項を利用できるため、さらにコストを削減することができます。

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>関連付けられていないパブリック IP アドレスのコスト節約のための削除

Advisor は、ロード バランサーや VM などの Azure リソースに関連付けられていないパブリック IP アドレスを識別します。 これらのパブリック IP アドレスには、わずかな料金がかかります。 使用する予定がない場合は、削除することでコストを削減できます。

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>障害が発生している Azure Data Factory パイプラインを削除する

Advisor では、繰り返し失敗する Azure Data Factory パイプラインが検出されます。 問題を解決するか、必要でない場合はパイプラインを削除することが推奨されます。 障害発生中にサービスが提供されていない場合でも、これらのパイプラインは課金されています。

## <a name="use-standard-snapshots-for-managed-disks"></a>マネージド ディスクに Standard スナップショットを使用する
コストの 60% を節約するために、親ディスクのストレージの種類には関係なく、スナップショットを Standard ストレージに格納することをお勧めします。 このオプションは、マネージド ディスクのスナップショットの既定オプションです。 Advisor は、Premium ストレージに格納されているスナップショットを識別し、それらを Premium から Standard Storage に移行することを推奨します。 [マネージド ディスクの価格](https://aka.ms/aa_manageddisksnapshot_learnmore)の詳細を確認してください。

## <a name="use-lifecycle-management"></a>ライフサイクル管理を使用する
Advisor では、Azure Blob Storage のオブジェクト数、合計サイズ、およびトランザクションに関するインテリジェンスを利用して、1 つ以上のストレージ アカウントの階層データに対してライフサイクル管理を有効にすべきかどうかを検出します。 アプリケーションの互換性のために Azure Blob Storage にご自身のデータを保持しながらストレージ コストを最適化するために、ライフサイクル管理ルールを作成してデータをクールまたはアーカイブ ストレージに自動的に階層化するように求めるメッセージが表示されます。

## <a name="create-an-ephemeral-os-disk-recommendation"></a>エフェメラル OS ディスクの作成に関する推奨事項
[エフェメラル OS ディスク](../virtual-machines/ephemeral-os-disks.md)では次のことができます。 
- OS ディスクのストレージ コストを節約する。 
- OS ディスクへの読み取り/書き込み待機時間を短縮する。 
- OS (および一時ディスク) を元の状態にリセットすることで、VM の再イメージ化操作を高速化する。

短期の IaaS VM やワークロードがステートレスである VM にはエフェメラル OS ディスクを使用することが推奨されます。 Advisor は、エフェメラル OS ディスクのメリットを得られるリソースに関する推奨事項を提供します。

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>クラスターのコストを最適化するため、Azure Data Explorer のテーブルのキャッシュ期間 (ポリシー) を短くする (プレビュー)
Advisor で、テーブル キャッシュ ポリシーを短くすることにより、CPU 使用率とメモリが低くキャッシュ サイズの構成が高い Azure Data Explorer クラスター ノードで解放できるリソースがあることが識別されました。

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Azure Advisor のコストに関する推奨事項にアクセスする方法

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 任意のページから [ **[Advisor]**](https://aka.ms/azureadvisordashboard) を検索して選択します。

1. **[Advisor]** ダッシュボードで、 **[コスト]** タブをクリックします。

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項の詳細については、以下を参照してください。
* [Advisor 入門](advisor-overview.md)
* [Advisor スコア](azure-advisor-score.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
* [Advisor のオペレーショナル エクセレンスに関する推奨事項](advisor-operational-excellence-recommendations.md)
