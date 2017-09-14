---
title: "Azure Cosmos DB を使用したデータのグローバル分散 | Microsoft Docs"
description: "グローバル分散型のマルチモデル データベース サービスである Azure Cosmos DB のグローバル データベースを使用した、地球規模の geo レプリケーション、フェールオーバー、データ復旧について説明します。"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: da2cb358d196e41656bd7f6a06ff77e77c7315c1
ms.contentlocale: ja-jp
ms.lasthandoff: 07/22/2017

---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Azure Cosmos DB を使用してデータをグローバルに分散させる方法
Azure はユビキタスです。30 か所以上の地理的リージョンでグローバル展開されており、継続的に拡大しています。 その世界的なプレゼンスにより、Azure が開発者に提供する差別化機能の 1 つは、グローバル分散アプリケーションを簡単に構築し、デプロイ、管理できることです。 

[Azure Cosmos DB](../cosmos-db/introduction.md) は、ミッション クリティカルなアプリケーション向けの、Microsoft のグローバル分散型マルチモデル データベース サービスです。 Azure Cosmos DB は、ターン キー グローバル分散、[スループットとストレージの世界規模でのエラスティック スケーリング](../cosmos-db/partition-data.md)、99 パーセンタイルの 1 桁ミリ秒の待機時間、[明確に定義された 5 種類の整合性レベル](consistency-levels.md)を提供し、高可用性を保証します。これらはすべて[業界最高レベルの SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) によってサポートされています。 Azure Cosmos DB は、[データのインデックスを自動的に作成](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)します。スキーマとインデックスの管理に対処する必要はありません。 Azure Cosmos DB はマルチモデルであり、ドキュメント、キーと値、グラフ、列指向の各データ モデルをサポートします。 Azure Cosmos DB は、クラウド生まれのサービスとして、最初からマルチテナントとグローバル分散を念頭に置いて入念に置いて設計されています。

**1 つの Azure Cosmos DB コレクションをパーティション分割し、複数の Azure リージョンに分散**

![パーティション分割され、3 つのリージョンに分散された Azure Cosmos DB コレクション](./media/distribute-data-globally/global-apps.png)

Azure Cosmos DB の構築中に学んだことですが、グローバル分散を後からの思い付きで追加することはできません。"単一サイト" データベース システムの上に "ボルト締結" することはできないのです。 グローバル分散データベースが提供する機能は、"単一サイト" データベースが提供する従来の地理的な障害復旧 (Geo-DR) の機能の枠を越えて多岐にわたります。 Geo-DR 機能を提供する単一サイト データベースは、グローバル分散データベースの厳密なサブセットです。 

Azure Cosmos DB のターンキー グローバル分散により、開発者はデータベース ログ上でラムダ パターン ([AWS DynamoDB レプリケーション](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)など) を使用するか、複数のリージョン間で "二重書き込み" を実行することによって、レプリケーションの独自のスキャフォールディングを構築する必要はありません。 このような方法の正確さを確保し、厳格な SLA を提供することは不可能であるため、これらの方法はお勧めしません。 

この記事では、Azure Cosmos DB のグローバル分散機能の概要を説明します。 また、Azure Cosmos DB の包括的な SLA を提供する独自のアプローチについても説明します。 

## <a id="EnableGlobalDistribution"></a>ターンキー グローバル配布の有効化
Azure Cosmos DB には、地球規模のアプリケーションを簡単に作成できるようにするための以下の機能が用意されています。 これらの機能は、Azure Cosmos DB のリソース プロバイダー ベースの [REST API](https://docs.microsoft.com/rest/api/documentdbresourceprovider/) と Azure Portal で使用できます。

### <a id="RegionalPresence"></a>ユビキタスなリージョン プレゼンス 
Azure では、[新しいリージョン](https://azure.microsoft.com/regions/)をオンラインにすることで、地理的プレゼンスを常に高めています。 Azure Cosmos DB は、すべての新しい Azure リージョンで既定で利用できます。 これにより、Azure がビジネス向けの新しいリージョンを開設するとすぐに、地理的リージョンを Azure Cosmos DB データベース アカウントに関連付けることができます。

**すべての Azure リージョンで既定で利用できる Azure Cosmos DB**

![すべての Azure リージョンで利用できる Azure Cosmos DB](./media/distribute-data-globally/azure-regions.png)

### <a id="UnlimitedRegionsPerAccount"></a>Azure Cosmos DB データベース アカウントに無制限の数のリージョンを関連付ける
Azure Cosmos DB では、Azure Cosmos DB データベース アカウントに Azure リージョンをいくつでも関連付けることができます。 ジオフェンスの制限 (中国、ドイツなど) を除き、Azure Cosmos DB データベース アカウントに関連付けることができるリージョンの数に制限はありません。 次の図は、25 か所の Azure リージョンにまたがるように構成されたデータベース アカウントを示しています。  

**25 か所の Azure リージョンにまたがるテナントの Azure Cosmos DB データベース アカウント**

![25 か所の Azure リージョンにまたがる Azure Cosmos DB データベース アカウント](./media/distribute-data-globally/spanning-regions.png)

### <a id="PolicyBasedGeoFencing"></a>ポリシー ベースのジオフェンス
Azure Cosmos DB は、ポリシー ベースのジオフェンス機能を備えるように設計されています。 ジオフェンスは、データ ガバナンスとコンプライアンスの制限を保証し、アカウントへの特定のリージョンの関連付けを防ぐことができる重要なコンポーネントです。 ジオフェンスの例として、グローバル配布の範囲を主権のあるクラウド内 (中国、ドイツなど) または政府の課税境界内 (オーストラリアなど) のリージョンに設定することが挙げられます。 ポリシーは、Azure サブスクリプションのメタデータを使用して制御されます。

### <a id="DynamicallyAddRegions"></a>リージョンを動的に追加おおよび削除する
Azure Cosmos DB では、データベース アカウントへのリージョンの追加 (関連付け) とリージョンの削除 (関連付け解除) をいつでも行うことができます ([前の図を参照](#UnlimitedRegionsPerAccount))。 Azure Cosmos DB は、パーティション間でのデータの並列レプリケーションにより、新しいリージョンがオンラインになったときに、最大 100 TB で世界中のどこでも 30 分以内に利用できます。 

### <a id="FailoverPriorities"></a>フェールオーバーの優先順位
複数のリージョンで障害が発生したときにリージョン内フェールオーバーの正確なシーケンスを制御するために、Azure Cosmos DB ではデータベース アカウントに関連付けられたさまざまなリージョンに優先順位を関連付けることができます (次の図を参照)。 Azure Cosmos DB では、指定された優先順位で自動フェールオーバー シーケンスが発生します。 リージョン内フェールオーバーの詳細については、「[Automatic regional failovers for business continuity in Azure Cosmos DB (Azure Cosmos DB でのビジネス継続性のためのリージョン内自動フェールオーバー)](regional-failover.md)」をご覧ください。

**Azure Cosmos DB のテナントは、データベース アカウントに関連付けられているリージョンのフェールオーバーの優先順位を構成することが可能 (右側のウィンドウ)**

![Azure Cosmos DB でのフェールオーバーの優先順位の構成](./media/distribute-data-globally/failover-priorities.png)

### <a id="OfflineRegions"></a>リージョンを動的に "オフライン" にする
Azure Cosmos DB では、特定のリージョンでデータベース アカウントをオフラインにし、後でオンラインに戻すことができます。 オフラインとしてマークされたリージョンは、レプリケーションにアクティブに関与せず、フェールオーバー シーケンスには含まれません。 これにより、リスクを伴う可能性のあるアップグレードをアプリケーションに展開する前に、読み取りリージョンのいずれかで最後に確認された良好なデータベース イメージを凍結することができます。

### <a id="ConsistencyLevels"></a>グローバルにレプリケートされたデータベースの明確に定義された複数の整合性モデル
Azure Cosmos DB では、SLA に支えられた、[明確に定義された複数の整合性レベル](consistency-levels.md)を公開しています。 ワークロードやシナリオに応じて、(使用可能なオプションの一覧から) 特定の整合性モデルを選択できます。 

### <a id="TunableConsistency"></a>グローバルにレプリケートされたデータベースの調整可能な整合性
Azure Cosmos DB では、実行時に、要求ごとに既定の整合性レベルをプログラムを使用して上書きし、緩和することができます。 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>動的に構成可能な読み取りリージョンと書き込みリージョン
Azure Cosmos DB では、"読み取り" リージョン、"書き込み" リージョン、"読み取り/書き込み" リージョンとして (データベースに関連付けられた) リージョンを構成できます。 

### <a id="ElasticallyScaleThroughput"></a>Azure リージョン間でスループットを弾力的にスケーリングする
プログラムを使用してスループットをプロビジョニングすることで、Azure Cosmos DB コレクションを弾力的にスケーリングできます。 スループットは、コレクションが配布されているすべてのリージョンに適用されます。

### <a id="GeoLocalReadsAndWrites"></a>地理的にローカルの読み取りと書き込み
グローバル分散データベースの主な利点は、世界中のどこでもデータへの待機時間の短いアクセスを実現できることです。 Azure Cosmos DB では、各種データベース操作に対して P99 での低待機時間が保証されます。 すべての読み取りは、最寄りのローカル読み取りリージョンにルーティングされます。 読み取り要求を処理するために、読み取りが発行されたリージョンに対してローカルのクォーラムが使用されます。書き込みの場合も同様です。 レプリカの大半が書き込みをローカルで永続的にコミットした後にのみ、書き込みの確認応答が返されます。ただし、書き込みの確認応答を行う際にリモート レプリカでゲート制御は行われません。 言い換えると、Azure Cosmos DB のレプリケーション プロトコルは、読み取りクォーラムと書き込みクォーラムが、それぞれ要求が発行された読み取りリージョンおよび書き込みリージョンに対して常にローカルであるという前提の下で動作します。

### <a id="ManualFailover"></a>リージョン内フェールオーバーを手動で開始する
Azure Cosmos DB では、(データベースの範囲を超えて) アプリケーション全体の*エンド ツー エンド*の可用性プロパティを検証するために、データベース アカウントのフェールオーバーをトリガーできます。 障害検出とリーダー選定の安全性プロパティと存続性プロパティの両方が保証されるため、Azure Cosmos DB ではテナントによる手動フェールオーバー操作で*データ損失なし*が保証されます。

### <a id="AutomaticFailover"></a>自動フェールオーバー
Azure Cosmos DB では、1 つ以上のリージョンで障害が発生した場合に備えて、自動フェールオーバーをサポートしています。 リージョン内フェールオーバー時には、読み取り待機時間、アップタイムの可用性、整合性、スループットの各 SLA が維持されます。 Azure Cosmos DB では、自動フェールオーバー操作が完了するまでの時間に上限が設けられています。 これは、リージョンでの障害発生時にデータが失われる可能性がある期間です。

### <a id="GranularFailover"></a>フェールオーバーのさまざまな粒度に対応する設計
現在、自動および手動フェールオーバー機能はデータベース アカウントの粒度で公開されています。 内部的には、Azure Cosmos DB は、データベース、コレクション、または (さまざまなキーを所有するコレクションの) パーティションというさらに細かい粒度で*自動*フェールオーバーを提供するように設計されています。 

### <a id="MultiHomingAPIs"></a>Azure Cosmos DB のマルチホーム API
Azure Cosmos DB では、論理 (リージョンに依存しない) エンドポイントまたは物理 (リージョン固有の) エンドポイントを使用してデータベースを操作できます。 論理エンドポイントを使用すると、フェールオーバーが発生した場合にアプリケーションを透過的にマルチホームにすることができます。 後者の物理エンドポイントは、読み取りと書き込みを特定のリージョンにリダイレクトするために、アプリケーションにきめ細かい制御を提供します。

[DocumentDB API](../cosmos-db/tutorial-global-distribution-documentdb.md)、[Graph API](../cosmos-db/tutorial-global-distribution-graph.md)、[Table API](../cosmos-db/tutorial-global-distribution-table.md)、[MongoDB API](../cosmos-db/tutorial-global-distribution-mongodb.md) の各 API の読み取り設定を構成する方法については、それぞれのリンク先の記事をご覧ください。

### <a id="TransparentSchemaMigration"></a>データベース スキーマとインデックスの透過的で一貫性のある移行 
Azure Cosmos DB は完全に[スキーマ非依存](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)です。 データベース エンジンの独自の設計により、スキーマやセカンダリ インデックスを必要とせずに、取り込んだすべてのデータのインデックスを自動的かつ同期的に作成できます。 これにより、データベース スキーマとインデックスの移行を気にしたり、スキーマの変更の複数フェーズのアプリケーション ロールアウトを調整したりしなくても、グローバル分散アプリケーションを迅速に反復処理できます。 Azure Cosmos DB では、ユーザーによるインデックス作成ポリシーの明示的な変更によって、パフォーマンスや可用性が低下しないことが保証されます。  

### <a id="ComprehensiveSLAs"></a>(可用性の枠を越えた) 包括的な SLA
グローバル分散型データベース サービスである Azure Cosmos DB は、データベースに関連付けられたリージョンの数に関係なく、**データ損失**、**可用性**、**P99 での待機時間**、**スループット**、**データベース全体の整合性**の明確に定義された SLA を提供します。  

## <a id="LatencyGuarantees"></a>待機時間の保証
Azure Cosmos DB のようなグローバル分散型データベース サービスの主な利点は、世界中のどこでもデータへの待機時間の短いアクセスを実現できることです。 Azure Cosmos DB では、各種データベース操作に対して P99 での低待機時間が保証されます。 Azure Cosmos DB が使用するレプリケーション プロトコルにより、データベース操作 (読み取りと書き込みの両方が理想的) は、クライアントのリージョンに対してローカルのリージョンで常に実行されます。 Azure Cosmos DB の待機時間の SLA には、読み取り、(同期的に) インデックスが付けられた書き込み、さまざまな要求/応答サイズのクエリの P99 が含まれます。 書き込みの待機時間の保証には、ローカル データセンター内でのマジョリティ クォーラムの永続的なコミットが含まれます。

### <a id="LatencyAndConsistency"></a>待機時間と整合性の関係 
グローバル分散サービスがグローバルに分散したセットアップで厳密な整合性を提供するには、書き込みを同期的にレプリケートするか、リージョンをまたがる読み取りを同期実行する必要があります。超高速であることとワイド エリア ネットワークの信頼性が、厳密な整合性によって生じるデータベース操作の長い待機時間と低い可用性に影響します。 そのため、P99 および 99.99 の可用性での低待機時間を保証するために、サービスは非同期レプリケーションを使用する必要があります。 この場合、サービスは[明確に定義された緩やかな整合性の選択肢](consistency-levels.md)も提供する必要があります。厳密な整合性よりも緩やかであり (低待機時間と可用性を保証するため)、なおかつ "最終的な" 整合性よりも厳密である (直感的なプログラミング モデルを提供するため) のが理想的です。

Azure Cosmos DB では、特定の整合性レベルの保証を実現するために、読み取り操作で複数のリージョンのレプリカに接続する必要はありません。 同様に、すべてのリージョンでデータがレプリケートされている間、書き込み操作がブロックされることもありません (つまり、書き込みはリージョン間で非同期にレプリケートされます)。 マルチリージョンのデータベース アカウントでは、複数の緩やかな整合性レベルを利用できます。 

### <a id="LatencyAndAvailability"></a>待機時間と可用性の関係 
待機時間と可用性は表裏一体です。 安定状態での操作の待機時間と障害発生時の可用性について説明します。 アプリケーションの観点から言えば、低速で実行されているデータベース操作は使用不可の状態のデータベースと区別できません。 

長い待機時間と使用不可の状態を区別するために、Azure Cosmos DB では各種データベース操作の待機時間に絶対上限を設けています。 データベース操作に時間がかかり、待機時間の上限を超えた場合、Azure Cosmos DB はタイムアウト エラーを返します。 Azure Cosmos DB の可用性の SLA により、タイムアウトは SLA と照らし合わせてカウントされます。 

### <a id="LatencyAndThroughput"></a>待機時間とスループットの関係
Azure Cosmos DB では、待機時間とスループットのどちらかを選択する必要はありません。 P99 での待機時間とプロビジョニングされたスループットの提供の両方の SLA が考慮されます。 

## <a id="ConsistencyGuarantees"></a>整合性の保証
[厳密な整合性モデル](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)はプログラミングのゴールド スタンダードですが、(安定状態での) 待機時間が長く、(障害発生時の) 可用性が低いという代償を伴います。 

Azure Cosmos DB は、レプリケートされたデータの整合性を推論するために、明確に定義されたプログラミング モデルを提供します。 マルチホームのアプリケーションの構築を可能にするために、Azure Cosmos DB で公開される整合性モデルはリージョンに依存しないように設計されており、読み取りと書き込みが処理される場所のリージョンに依存しません。 

Azure Cosmos DB の整合性の SLA では、すべての読み取り要求が、要求されている整合性レベル (データベース アカウントの既定の整合性レベルまたは要求で上書きされた値) の整合性の保証を満たすことを保証します。 整合性レベルに関連付けられている整合性の保証がすべて満たされていれば、読み取り要求は整合性の SLA を満たしていると見なされます。 次の表は、Azure Cosmos DB で提供される特定の整合性レベルに対応する整合性の保証を示しています。

**Azure Cosmos DB の特定の整合性レベルに関連付けられた整合性の保証**

<table>
    <tr>
        <td><strong>整合性レベル</strong></td>
        <td><strong>整合性の特性</strong></td>
        <td><strong>SLA</strong></td>
    </tr>
    <tr>
        <td rowspan="3">Session</td>
        <td>独自の書き込みの読み取り</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>モノトニックな読み取り</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>一貫性のあるプレフィックス</td>
        <td>100%</td>
    </tr>
    <tr>
        <td rowspan="3">Bounded staleness</td>
        <td>モノトニックな読み取り (リージョン内)</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>一貫性のあるプレフィックス</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>有界整合性制約 &lt; K、T</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>一貫性のあるプレフィックス</td>
        <td>一貫性のあるプレフィックス</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Strong</td>
        <td>線形化可能</td>
        <td>100%</td>
    </tr>
</table>

### <a id="ConsistencyAndAvailability"></a>整合性と可用性の関係
[CAP 定理](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)の[不可能性の結果](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)は、障害発生時にシステムが可用性を維持し、線形化可能な整合性を提供することが実際に不可能であることを証明しています。 データベース サービスは、CP または AP のいずれかであることを選択する必要があります。CP システムは、線形化可能な整合性を優先して可用性を犠牲にし、AP システムは可用性を優先して[線形化可能な整合性](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)を犠牲にします。 Azure Cosmos DB は要求された整合性レベルに違反することはないため、正式に CP システムとなります。 ただし、実際には整合性は全か無かの命題ではありません。線形化可能な整合性と最終的な整合性の間の整合性範囲に沿って、明確に定義された複数の整合性モデルがあります。 Azure Cosmos DB において、Microsoft は実際の適用性と直感的なプログラミング モデルでいくつかの緩やかな整合性モデルを特定することを試みました。 Azure Cosmos DB では、[緩やかでありながら明確に定義された複数の整合性レベル](consistency-levels.md)と共に、99.99 の可用性の SLA を提供することによって、整合性と可用性のトレードオフを調整します。 

### <a id="ConsistencyAndAvailability"></a>整合性と待機時間の関係
CAP のより包括的なバリエーションが Daniel Abadi 教授によって提案されました。これは [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf) と呼ばれ、安定状態での待機時間と整合性のトレードオフも考慮されます。 PACELC は、安定状態では、データベース システムは整合性と待機時間のいずれかを選択しなければならないとしています。 Azure Cosmos DB では、(非同期レプリケーションとローカルの読み取り/書き込みクォーラムによって支えられた) 複数の緩やかな整合性モデルにより、すべての読み取りと書き込みが、それぞれ読み取りリージョンと書き込みリージョンに対してローカルであることが保証されます。  これにより、Azure Cosmos DB は整合性レベルに対してリージョン内で低待機時間を保証できます。  

### <a id="ConsistencyAndThroughput"></a>整合性とスループットの関係
特定の整合性モデルの実装は[クォーラムの種類](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)の選択によって異なるため、スループットも整合性の選択によって異なります。 たとえば、Azure Cosmos DB では、厳密な整合性の読み取りでのスループットは、最終的な整合性の読み取りのスループットの約半分です。 
 
**Azure Cosmos DB における特定の整合性レベルの読み取り能力の関係**

![整合性とスループットの関係](./media/distribute-data-globally/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>スループットの保証 
Azure Cosmos DB では、需要に応じて、さまざまなリージョン間でスループット (およびストレージ) を弾力的にスケーリングできます。 

**1 つの Azure Cosmos DB コレクションを (3 つのシャード間で) パーティション分割した後、3 つの Azure リージョンに分散**

![パーティション分割されて分散された Azure Cosmos DB コレクション](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

Azure Cosmos DB コレクションは、2 つのディメンション (リージョン内とリージョン間) を使用して分散されます。 その方法は次のとおりです。 

* 1 つのリージョン内で、Azure Cosmos DB コレクションはリソース パーティションの観点でスケールアウトされます。 各リソース パーティションは一連のキーを管理し、一連のレプリカ間でのステート マシン レプリケーションにより、厳密な整合性と高可用性を実現します。 Azure Cosmos DB は、リソース パーティションが、割り当てられたシステム リソースの予算に合わせてスループットの共有を提供する役割を担う、リソースによって完全に管理されたシステムです。 Azure Cosmos DB コレクションのスケーリングは完全に透過的です。Azure Cosmos DB がリソース パーティションを管理し、必要に応じてパーティションを分割、結合します。 
* 次に、各リソース パーティションが複数のリージョンに配布されます。 さまざまなリージョンでキーの同じセットを所有するリソース パーティションは、パーティション セットを形成します ([前の図](#ThroughputGuarantees)を参照)。  パーティション セット内のリソース パーティションは、複数のリージョン間でステート マシン レプリケーションを使用して調整されます。 構成されている整合性レベルに応じて、さまざまなトポロジ (スター、デイジーチェーン、ツリーなど) を使用してパーティション セット内のリソース パーティションが動的に構成されます。 

Azure Cosmos DB では、応答性の高いパーティション管理、負荷分散、厳密なリソース ガバナンスにより、複数の Azure リージョン間で Azure Cosmos DB コレクションのスループットを弾力的にスケーリングできます。 コレクションのスループットの変更は、Azure Cosmos DB のランタイム操作です。他のデータベース操作と同様に、Azure Cosmos DB ではスループットの変更要求の待機時間の絶対上限が保証されます。 一例として、次の図は需要に基づいてスループット (2 つのリージョンでの 1 秒あたりの要求数が 1 M ～ 10 M) が弾力的にプロビジョニングされた顧客のコレクションを示しています。
 
**スループット (1 秒あたりの要求数が 1 M ～ 10 M) が弾力的にプロビジョニングされた顧客のコレクション**

![スループットが弾力的にプロビジョニングされた Azure Cosmos DB](./media/distribute-data-globally/elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>スループットと整合性の関係 
「[整合性とスループットの関係](#ConsistencyAndThroughput)」と同じです。

### <a id="ThroughputAndAvailability"></a>スループットと可用性の関係
スループットが変更されても、Azure Cosmos DB は引き続き可用性を維持します。 Azure Cosmos DB はパーティションを透過的に管理し (分割操作、結合操作、複製操作など)、操作によってパフォーマンスや可用性が低下しないようにします。アプリケーションはスループットを弾力的に増減します。 

## <a id="AvailabilityGuarantees"></a>可用性の保証
Azure Cosmos DB は、各データ プレーン操作とコントロール プレーン操作について、アップタイム 99.99% の可用性の SLA を提供します。 前述のように、Azure Cosmos DB の可用性の保証には、各データ プレーン操作とコントロール プレーン操作の待機時間の絶対上限が含まれます。 可用性の保証は強固であり、リージョンの数やリージョン間の地理的距離によって変わることはありません。 可用性の保証は、手動フェールオーバーと自動フェールオーバーの両方で適用されます。 Azure Cosmos DB は、アプリケーションが論理エンドポイントに対して動作することができ、フェールオーバーが発生した場合に要求を新しいリージョンに透過的にルーティングできるようにするための透過的なマルチホーム API を提供します。 言い換えると、リージョン内フェールオーバー時にアプリケーションを再デプロイする必要はなく、可用性の SLA が維持されます。

### <a id="AvailabilityAndConsistency"></a>可用性と整合性、待機時間、スループットの関係
可用性と整合性、待機時間、スループットの関係については、「[整合性と可用性の関係](#ConsistencyAndAvailability)」、「[待機時間と可用性の関係](#LatencyAndAvailability)」、「[スループットと可用性の関係](#ThroughputAndAvailability)」をご覧ください。 

## <a id="GuaranteesAgainstDataLoss"></a>"データ損失" に関する保証とシステムの動作
Azure Cosmos DB では、(コレクションの) 各パーティションの高可用性は、少なくとも 10 ～ 20 個の障害ドメインに分散した多数のレプリカによって実現されます。 すべての書き込みは、クライアントに確認応答が返される前に、レプリカのマジョリティ クォーラムによって同期的かつ永続的にコミットされます。 非同期レプリケーションは、複数のリージョンにまたがるパーティション間の調整時に適用されます。 Azure Cosmos DB では、テナントによる手動フェールオーバーでデータ損失が発生しないことを保証しています。 自動フェールオーバー時には、Azure Cosmos DB では SLA の一部としてデータ損失期間の構成済みの有界整合性制約間隔の上限が保証されます。

## <a id="CustomerFacingSLAMetrics"></a>顧客向け SLA メトリック
Azure Cosmos DB では、スループット、待機時間、整合性、可用性のメトリックを透過的に公開しています。 これらのメトリックには、プログラムまたは Azure Portal を使用してアクセスできます (次の図を参照)。 また、Azure Application Insights を使用して、さまざまなしきい値にアラートを設定することもできます。
 
**整合性、待機時間、スループット、可用性のメトリックを各テナントに透過的に提供**

![Azure Cosmos DB の顧客が表示できる SLA メトリック](./media/distribute-data-globally/customer-slas.png)

## <a id="Next Steps"></a>次のステップ
* Azure Portal を使用して Azure Cosmos DB アカウントにグローバル レプリケーションを実装する方法については、[Azure Portal を使用して Azure Cosmos DB グローバル データベース レプリケーションを実行する方法](tutorial-global-distribution-documentdb.md)に関する記事をご覧ください。
* Azure Cosmos DB を使用してマルチマスター アーキテクチャを実装する方法については、[Azure Cosmos DB を使用したマルチマスター データベース アーキテクチャ](multi-region-writers.md)に関する記事をご覧ください。
* Azure Cosmos DB の自動および手動フェールオーバーのしくみの詳細については、[Azure Cosmos DB のリージョン内フェールオーバー](regional-failover.md)に関する記事をご覧ください。

## <a id="References"></a>参考資料
1. Eric Brewer 著:  「[Towards Robust Distributed Systems (堅牢な分散システムに向けて)](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)」
2. Eric Brewer 著:  「[CAP Twelve Years Later: How the "Rules" have changed (CAP の 12 年後: "ルール" がどのように変わったか)](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf)」
3. Gilbert、Lynch 共著: 「 - [Brewer&#39;s Conjecture and the Feasibility of Consistent, Available, Partition-Tolerant Web Services (ブリュワーの推測と整合性および可用性を備えたパーティション トレラントな Web サービスの実現可能性)](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)」
4. Daniel Abadi 著: 「[Consistency Tradeoffs in Modern Distributed Database Systems Design (最新の分散データベース システム設計における整合性のトレードオフ)](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf)」
5. Martin Kleppmann 著:  「[Please stop calling databases CP or AP (データベースを CP または AP と呼ぶのはやめてください)](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)」
6. Peter Bailis その他共著: 「[Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums (現実的なパーシャル クォーラムのための Probabilistic Bounded Staleness (PBS))](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)」
7. Naor、Wool 共著:  「[The Load, Capacity and Availability of Quorum Systems (クォーラム システムの負荷、容量、および可用性)](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf)」
8. Herlihy、Wing 共著:  「[Lineralizability: A Correctness Condition for Concurrent Objects (線形化可能性: 同時実行オブジェクトの正確性の条件)](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)」
9. [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)

