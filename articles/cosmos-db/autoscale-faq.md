---
title: Azure Cosmos DB のプロビジョニング スループットの自動スケーリングについてよく寄せられる質問
description: Azure Cosmos DB データベースおよびコンテナーの自動スケーリングでプロビジョニングされたスループットについてよく寄せられる質問
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: 31b96f03a8519b068eaa816443be0a0f374a4a8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98247031"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Azure Cosmos DB の自動スケーリングでプロビジョニングされたスループットについてよく寄せられる質問
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB で自動スケーリング プロビジョニング スループットを使用すると、使用状況に応じてお使いのデータベースまたはコンテナーの RU/秒が自動的に管理およびスケーリングされます。 この記事では、自動スケーリング モードについてよく寄せられる質問に回答します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Azure Cosmos DB での "オートパイロット" と "自動スケーリング" の違いは何ですか?
"自動スケーリング" または "自動スケーリング プロビジョニング スループット" は、以前は "オートパイロット" と呼ばれていた機能の更新された名前です。 自動スケーリングの現在のリリースでは、カスタムの最大 RU/秒を設定する機能やプログラムによるサポートなどの新機能が追加されています。 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>以前のオートパイロット階層モデルで作成されたデータベースまたはコンテナーはどうなりますか?
以前の階層モデルで作成されたリソースは、自動的に新しい自動スケーリングのカスタム最大 RU/秒モデルでサポートされます。 階層の上限は新しい最大 RU/秒になり、結果として同じスケール範囲になります。 

たとえば、以前に 400 から 4,000 RU/秒の間でスケーリングされる階層を選択していた場合、データベースまたはコンテナーの最大 RU/秒は 4,000 RU/秒 (400 から 4,000 RU/秒の間でのスケーリング) になります。 これに基づいて、最大 RU/秒をワークロードに合わせてカスタム値に変更できます。 

### <a name="how-quickly-will-autoscale-scale-up-based-on-spikes-in-traffic"></a>自動スケーリングでは、トラフィックの急増に応じてスケールアップがどれほど迅速に行われますか?
自動スケーリングでは、スループット (RU/秒) `T` は、受信トラフィックに基づいて、`0.1 * Tmax` と `Tmax` の範囲内でスケールアップおよびスケールダウンされます。 スケーリングは自動的かつ瞬時に行われるため、どの時点でも、プロビジョニングされた `Tmax` まで遅延なく使用できます。 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>システムが現在どのくらいの RU/秒までスケールアップされているかを判別するにはどうすればよいですか?
[Azure Monitor メトリック](how-to-choose-offer.md#measure-and-monitor-your-usage)を使用して、プロビジョニングされた自動スケーリングの最大 RU/秒と、システムがスケーリングされている現在のスループット (RU/秒) の両方を監視できます。 

### <a name="what-is-the-pricing-for-autoscale"></a>自動スケーリングの価格はいくらですか?
課金は、1 時間ごとに、その 1 時間内にシステムがスケーリングされた最大スループット `T` に対して行われます。 1 時間の間にリソースに対する要求がなかった場合、または `0.1 * Tmax` を超えてスケーリングされなかった場合は、最小の `0.1 * Tmax` に対して課金されます。 詳細については、Azure Cosmos DB の[価格に関するページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>自動スケーリングは請求書にどのように表示されますか?
単一書き込みリージョン アカウントの場合、100 RU/秒あたりの自動スケーリングのレートは、Standard (手動) プロビジョニング スループットのレートの 1.5 倍です。 請求書には、既存の Standard プロビジョニング スループット測定が表示されます。 この測定の数量に 1.5 が掛けられます。 たとえば、システムがスケーリングされ、1 時間以内の最大 RU/秒が 6,000 RU/秒であった場合、その 1 時間の測定の 60 * 1.5 = 90 単位が請求されます。

複数書き込みリージョンのアカウントの場合、100 RU/秒あたりの自動スケーリングのレートは、Standard (手動) プロビジョニングの複数書き込みリージョンのスループットのレートと同じです。 請求書には、既存の複数書き込みリージョンの測定が表示されます。 レートは同じであるため、自動スケーリングを使用した場合は、Standard スループットと同じ数量が示されます。

### <a name="does-autoscale-work-with-reserved-capacity"></a>自動スケーリングは予約容量で機能しますか?
はい。 単一書き込みリージョンのアカウントに対して予約容量を購入すると、自動スケーリング リソースの予約割引が測定の使用量に適用されます。比率は、1.5 * [特定リージョンの比率](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region)になります。 

複数書き込みリージョンの予約容量は、自動スケーリングと Standard (手動) プロビジョニング スループットに対して同様に機能します。 [Azure Cosmos DB の予約容量](cosmos-db-reserved-capacity.md)に関するページを参照してください

### <a name="does-autoscale-work-with-free-tier"></a>自動スケーリングは Free レベルで機能しますか?
はい。 Free レベルでは、コンテナーで自動スケーリング スループットを利用できます。 カスタム最大 RU/秒を使用した自動スケーリング Shared スループット データベースのサポートはまだ利用できません。 [Free レベルでの自動スケーリングの請求のしくみ](understand-your-bill.md#azure-free-tier)に関するページを参照してください。

### <a name="is-autoscale-supported-for-all-apis"></a>すべての API で自動スケーリングがサポートされていますか?
はい。自動スケーリングは、すべての API でサポートされています: Core (SQL)、Gremlin、Table、Cassandra、および MongoDB 用 API。

### <a name="is-autoscale-supported-for-multi-region-write-accounts"></a>自動スケーリングは複数リージョン書き込みアカウントに対してサポートされていますか?
はい。 最大 RU/秒は、Azure Cosmos DB アカウントに追加された各リージョンで利用できます。 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>新しいデータベースまたはコンテナーに対して自動スケーリングを有効にするにはどうすればよいですか?
[自動スケーリングを有効にする方法](how-to-provision-autoscale-throughput.md)に関する記事を参照してください。

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>既存のデータベースまたはコンテナーに対して自動スケーリングを有効にすることはできますか?
はい。 必要に応じて自動スケーリングと Standard (手動) プロビジョニング スループットを切り替えることもできます。 現在、[Azure portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) を使用した場合にのみ、すべての API に対してこれらの操作を実行できます。

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>自動スケーリングと Standard (手動) プロビジョニング スループットの間の移行はどのように行われますか?
概念的には、スループットの種類の変更は 2 段階のプロセスです。 最初に、自動スケーリングまたは手動プロビジョニング スループットを使用するようにスループット設定を変更する要求を送信します。 どちらの場合も、現在のスループット設定とストレージに基づいて、初期 RU/秒値がシステムによって自動的に決定、設定されます。 この手順では、ユーザーが指定した RU/秒値は受け入れられません。 その後、更新が完了したら、自分のワークロードに合わせて [RU/秒を変更する](#can-i-change-the-max-rus-on-the-database-or-container)ことができます。 

**Standard (手動) プロビジョニング スループットから自動スケーリングへの移行**

コンテナーの場合は、式 ``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)`` (値は最も近い 1,000 RU/秒に丸められます) を使用して、初期自動スケーリングの最大 RU/秒を見積もってください。 実際の初期自動スケーリングの最大 RU/秒は、アカウントの構成によって異なる場合があります。

例 1:10,000 RU/秒の手動プロビジョニング スループットと 25 GB のストレージを備えたコンテナーがあるとします。 自動スケーリングを有効にすると、初期自動スケーリングの最大 RU/秒は次のようになります: 10,000 RU/秒 (1,000 から 10,000 RU/秒の間でのスケーリング)。 

例 2:50,000 RU/秒の手動プロビジョニング スループットと 2,500 GB のストレージを備えたコンテナーがあるとします。 自動スケーリングを有効にすると、初期自動スケーリングの最大 RU/秒は次のようになります: 250,000 RU/秒 (25,000 から 250,000 RU/秒の間でのスケーリング)。 

**自動スケーリングから Standard (手動) プロビジョニング スループットへの移行**

初期手動プロビジョニング スループットは、現在の自動スケーリングの最大 RU/秒と同じになります。 

例:20,000 RU/秒の最大 RU/秒 (2,000 から 20,000 RU/秒の間でのスケーリング) を備えた自動スケーリング データベースまたはコンテナーがあるとします。 手動プロビジョニング スループットを使用するように更新すると、初期スループットは 20,000 RU/秒になります。 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>自動スケーリングを備えたデータベースまたはコンテナーを管理するための Azure CLI または PowerShell のサポートはありますか?
現時点では、Azure portal、.NET V3 SDK、Java V4 SDK、および Azure Resource Manager からのみ、自動スケーリングを備えたリソースを作成および管理できます。 Azure CLI、PowerShell、その他の SDK でのサポートはまだ利用できません。

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>自動スケーリングは共有スループット データベースでサポートされていますか?
はい。自動スケーリングは共有スループット データベースでサポートされています。 この機能を有効にするには、データベースを作成するときに、自動スケーリングと **[スループットのプロビジョニング]** オプションを選択します。 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>自動スケーリングが有効になっているとき、Shared スループット データベースごとに許可されるコンテナーの数はどのくらいですか?
Azure Cosmos DB では、Shared スループット データベースあたり 25 個のコンテナーという上限が適用され、これが自動スケーリングまたは Standard (手動) スループットのデータベースに適用されます。 

### <a name="what-is-the-impact-of-autoscale-on-database-consistency-level"></a>データベースの整合性レベルでの自動スケーリングの影響はどのようなものですか?
データベースの整合性レベルでの自動スケーリングの影響はありません。
使用可能な整合性レベルの詳細については、[整合性レベル](consistency-levels.md)に関する記事を参照してください。

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>各最大 RU/秒オプションに関連付けられたストレージの上限はどれだけですか?  
最大 RU/秒ごとのストレージの上限 (GB) は次のとおりです: データベースまたはコンテナーの最大 RU/秒 / 100。 たとえば、最大 RU/秒が 20,000 RU/秒の場合、リソースは 200 GB のストレージをサポートできます。 使用可能な最大 RU/秒およびストレージ オプションについては、[自動スケーリングの制限](provision-throughput-autoscale.md#autoscale-limits)に関する記事を参照してください。 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>最大スループットに関連付けられているストレージの上限を超えるとどうなりますか?
データベースまたはコンテナーの最大スループットに関連付けられているストレージの上限を超えた場合、Azure Cosmos DB によって、そのストレージのレベルをサポートできる次に高い RU/秒に自動的に最大スループットが引き上げられます。

たとえば、50,000 RU/秒 (5,000 から 50,000 RU/秒の間でのスケーリング) の最大 RU/秒から始めた場合、最大で 500 GB のデータを格納できます。 500 GB を超えると (現在、ストレージが 600 GB になっているなど)、新しい最大 RU/秒は 60,000 RU/秒 (6,000 から 60,000 RU/秒の間でのスケーリング) になります。

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>データベースまたはコンテナーの最大 RU/秒を変更することはできますか? 
はい。 最大 RU/秒を変更する方法については、こちらの[記事](how-to-provision-autoscale-throughput.md)を参照してください。 最大 RU/秒を変更する場合、要求された値によっては、非同期操作となり、完了するまでに時間がかかる場合があります (選択した RU/秒によっては、最大 4 から 6 時間かかる場合があります)

#### <a name="increasing-the-max-rus"></a>最大 RU/秒を引き上げる
最大 RU/秒 `Tmax` を引き上げる要求を送信すると、選択した最大 RU/秒に応じて、より高い最大 RU/秒をサポートするためにより多くのリソースがサービスによってプロビジョニングされます。 この操作が行われている間、既存のワークロードと操作は影響を受けません。 システムでは、`0.1*Tmax_new` から `Tmax_new` の新しいスケール範囲が準備できるまで、前の `0.1*Tmax` から `Tmax` の間でデータベースまたはコンテナーのスケーリングが引き続き実行されます。

#### <a name="lowering-the-max-rus"></a>最大 RU/秒を引き下げる
最大 RU/秒を引き下げる場合に設定できる最小値は `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` です (値は最も近い 1,000 RU/秒に丸められます)。 

例 1:20,000 RU/秒 (2,000 から 20,000 RU/秒の間でのスケーリング) の最大 RU/秒と 50 GB のストレージを備えた自動スケーリング コンテナーがあるとします。 設定できる最大 RU/秒の最小値は次のとおりです: MAX(4,000, 20,000 / 10, **50 * 100**) = 5,000 RU/秒 (500 から 5,000 RU/秒の間でのスケーリング)。

例 2:100,000 RU/秒の最大 RU/秒と 100 GB のストレージを備えた自動スケーリング コンテナーがあるとします。 ここで、最大 RU/秒を最大 150,000 RU/秒 (15,000 から 150,000 RU/秒の間でのスケーリング) までスケーリングするとします。 ここで設定できる最大 RU/秒の最小値は次のとおりです: MAX(4,000, **150,000 / 10**, 100 * 100) = 15,000 RU/秒 (1,500 から 15,000 RU/秒の間でのスケーリング)。 

Shared スループット データベースの場合、最大 RU/秒を引き下げる場合に設定できる最小値は `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))` です (値は最も近い 1,000 RU/秒に丸められます)。  

上記の式と例は、自動スケーリングの最大 RU/秒に設定できる最小値に関連したものであり、システムが自動的にスケーリングされる `0.1 * Tmax` から `Tmax` の範囲とは異なります。 最大 RU/秒に関係なく、システムは常に `0.1 * Tmax` から `Tmax` の間でスケーリングされます。 

### <a name="how-does-ttl-work-with-autoscale"></a>TTL は自動スケーリングでどのように機能しますか?
自動スケーリングでは、TTL 操作は RU/秒のスケーリングには影響しません。 TTL が原因で消費された RU は、自動スケーリング コンテナーの課金対象の RU/秒には含まれません。 

たとえば、400 から 4,000 RU/秒の自動スケーリング コンテナーがあるとします。
- 時間 1: T=0: コンテナーは使用されていません (TTL またはワークロードの要求はありません)。 課金対象の RU/秒は 400 RU/秒です。
- 時間 1: T=1: TTL は有効です。
- 時間 1: T=2: コンテナーは、1 秒で 1,000 RU を消費する要求の取得を開始します。 また、発生が不可欠な 200 RU 相当の TTL もあります。 課金対象の RU/秒は、引き続き 1,000 RU/秒です。 TTL がいつ発生しても、自動スケーリングのスケーリング ロジックには影響しません。

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>最大 RU/秒と物理パーティション間のマッピングとはどのようなものですか?
最初に最大 RU/秒を選択すると、Azure Cosmos DB によって次のようにプロビジョニングされます: 最大 RU/秒 / 10,000 RU/秒 = 物理パーティションの数。 各[物理パーティション](partitioning-overview.md#physical-partitions)は、最大 10,000 RU/秒と 50 GB のストレージをサポートできます。 ストレージのサイズが大きくなると、Azure Cosmos DB によってパーティションが自動的に分割され、ストレージの増加に対応するように物理パーティションが追加されます。または、ストレージが[関連付けられた上限を超えている](#what-is-the-storage-limit-associated-with-each-max-rus-option)場合は、最大 RU/秒が引き上げられます。 

データベースまたはコンテナーの最大 RU/秒は、すべての物理パーティション全体で均等に分割されます。 そのため、1 つの物理パーティションでスケーリングできる合計スループットは次のようになります: データベースまたはコンテナーの最大 RU/秒 / 物理パーティションの数。 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>受信要求がデータベースまたはコンテナーの最大 RU/秒を超えるとどうなりますか?
消費された RU/秒の全体がデータベースまたはコンテナーの最大 RU/秒を超えた場合、最大 RU/秒を超えた要求はスロットルされ、429 状態コードが返されます。 100% を超える正規化された使用率が発生する要求もスロットルされます。 正規化された使用率は、すべての物理パーティションにおける RU/秒の使用率の最大値として定義されます。 たとえば、最大スループットが 20,000 RU/秒で、P_1 および P_2 という 2 つの物理パーティションがあり、それぞれが 10,000 RU/秒にスケーリングできるとします。 ある 1 秒間で、P_1 が 6000 RU を使用し、P_2 が 8000 RU を使用した場合、正規化された使用率は MAX(6000 RU / 10,000 RU, 8000 RU / 10,000 RU) = 0.8 になります。

> [!NOTE]
> Azure Cosmos DB クライアント SDK とデータ インポート ツール (Azure Data Factory、一括実行プログラム ライブラリ) は、429 で自動的に再試行をするため、不定期に 429 が発生しても問題はありません。 429 の数が多いままの場合は、最大 RU/秒を増やす必要があるか、または[ホット パーティション](#autoscale-rate-limiting)のパーティション分割方法を確認する必要があることを示しています。

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> 自動スケーリングが有効になっていても、429 (スロットリング/レート制限) が引き続き表示される可能性はありますか? 
はい。 2 つのシナリオで、429 が表示される可能性があります。 最初に、消費された RU/秒の全体がデータベースまたはコンテナーの最大 RU/秒を超えると、サービスはそれに応じて要求をスロットルします。 

2 番目に、ホット パーティションがある場合、つまり、他のパーティション キー値と比較して要求が過剰に増加する論理パーティション キー値がある場合、基になる物理パーティションが RU/秒の予算を超える可能性があります。 ベスト プラクティスとして、ホット パーティションを回避するには、ストレージとスループットの両方が均等に分散される[適切なパーティション キーを選択](partitioning-overview.md#choose-partitionkey)します。 

たとえば、20,000 RU/秒の最大スループットのオプションを選択し、4 つの物理パーティションを含む 200 GB のストレージを使用している場合、各物理パーティションは 5000 RU/秒に自動スケールアップできます。 特定の論理パーティション キーにホット パーティションがある場合、それが存在している基になる物理パーティションが 5000 RU/秒を超えると (つまり、100% の正規化された使用率を超えると)、429 が表示されます。


## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB データベースまたはコンテナーで自動スケーリングを有効にする方法](how-to-provision-autoscale-throughput.md)について学習します。
* [自動スケーリングを使用したプロビジョニング スループットの利点](provision-throughput-autoscale.md#benefits-of-autoscale)について学習します。
* [論理および物理パーティション](partitioning-overview.md)の詳細について学習します。
                        
