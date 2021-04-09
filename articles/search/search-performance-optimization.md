---
title: パフォーマンスのためのスケール
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のパフォーマンスを調整し、最適なスケールを構成するための技術とベスト プラクティスについて学習します。
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.custom: references_regions
ms.openlocfilehash: 60371888dbc4f0cbc33f1ad1b2a685dbb071c01a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670706"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Azure Cognitive Search のパフォーマンスのためのスケーリング

この記事では、スケーラビリティと可用性に対する高度な要件を持つ高度なシナリオのベスト プラクティスについて説明します。

## <a name="start-with-baseline-numbers"></a>ベースラインの数から始める

大規模なデプロイを行う前に、典型的なクエリ負荷について確認しておきます。 次のガイドラインにより、ベースラインのクエリ数に達することができます。

1. 標準的な検索要求が完了するまでに要する対象待機時間 (または最長時間) を選択します。

1. 実際のワークロードを作成し、現実的なデータ セットを使用して Search サービスでワークロードをテストし、待機時間を測定します。

1. 少ない QPS (1 秒あたりのクエリ数) から始め、あらかじめ定めた目標をクエリの待機時間が下回るまで、数を徐々に増やしながらテストを実行します。 これは、アプリケーションの普及に伴ってスケールの計画を立てる際に役立つ重要なベンチマークです。

1. 可能な限り、HTTP 接続を再利用します。 Azure Cognitive Search .NET SDK を使用している場合、これはインスタンスまたは [SearchClient](/dotnet/api/azure.search.documents.searchclient) インスタンスを再利用することを意味します。REST API を使用している場合は、1 つの HttpClient を再利用します。

1. インデックスのさまざまな部分で検索が行われるように、クエリ要求の内容を変更します。 同じ検索要求を継続的に実行すると、データのキャッシュによって、異なるクエリ セットを使用した場合よりもパフォーマンスが高く見えるようになるため、バリエーションを持たせることが重要です。

1. クエリ要求の構造を、さまざまなクエリの種類を取得するように変更します。 すべての検索クエリが同じレベルで実行されるわけではありません。 たとえば、文書検索や検索候補は、多数のファセットとフィルターを含むクエリよりも通常は高速です。 テスト構成には、運用環境で想定される比率とほぼ同じ比率で、さまざまなクエリを含める必要があります。  

これらのテスト ワークロードの作成時に留意すべき Azure Cognitive Search の特性がいくつかあります。

+ 一度にプッシュする検索クエリが多すぎると、サービスに負荷がかかる可能性があります。 この場合、HTTP 503 応答コードが表示されます。 テスト中に 503 が表示されないようにするには、さまざまな検索要求から始めて、検索要求を追加したときの待機時間の割合の差を確認します。

+ Azure Cognitive Search によって、バックグラウンドでインデックス作成タスクが実行されることはありません。 サービスがクエリとインデックス作成のワークロードを同時に処理する場合は、インデックス作成ジョブをクエリ テストに組み込むか、ピーク時を避けてインデックス作成ジョブを実行するオプションを調べることで、この点を考慮してください。

> [!Tip]
> ロード テスト ツールを利用し、現実的なクエリ負荷をシミュレートすることができます。 [Azure DevOps でロード テスト](/azure/devops/test/load-test/get-started-simple-cloud-load-test)を試すか、次のいずれかの[代替案](/azure/devops/test/load-test/overview#alternatives)を利用してください。

## <a name="scale-for-high-query-volume"></a>大量のクエリに対応するスケーリング

サービスに過度の負荷がかかると、クエリに時間がかかりすぎたり、要求に応答できなくなったりします。 その場合、次のいずれかの方法で問題に対処できます。

+ **レプリカを追加する**  

  レプリカはそれぞれ、データの 1 コピーとなります。サービスでは、複数のコピーを背景に要求の負荷を分散できます。  負荷分散とデータのレプリケーションはすべて Azure Cognitive Search によって管理されます。サービスに割り当てられたレプリカの数はいつでも変更できます。 Standard Search サービスでは最大 12 個、Basic Search サービスでは最大 3 個のレプリカを割り当てることができます。 レプリカは [Azure Portal](search-create-service-portal.md) または [PowerShell](search-manage-powershell.md) から調整できます。

+ **上のレベルで新しいサービスを作成する**  

  Azure Cognitive Search には[複数のレベル](https://azure.microsoft.com/pricing/details/search/)が用意されており、レベルごとにパフォーマンス レベルが異なります。 クエリの数が多すぎて、レプリカを最大数まで増やしたとしても、現在のレベルでは待機時間が適度な長さにならないことがあります。その場合、Standard S3 レベルなど、大量のドキュメントを扱い、クエリのワークロードが極めて高くなるシナリオ向けに設計された上のレベルに移行することを検討してください。

## <a name="scale-for-slow-individual-queries"></a>個々のクエリが遅い場合のスケーリング

待機時間の割合が高くなるもう 1 つの理由は、1 つのクエリが完了するまでに時間がかかりすぎることです。 この場合、レプリカを追加しても役に立ちません。 役に立つ可能性のある 2 つのオプションは、次のとおりです。

+ **パーティションを増やす**

  パーティションによって、追加のコンピューティング リソース間でデータが分割されます。 たとえば、パーティションが 2 つの場合はデータが半分に分割され、パーティションが 3 つの場合は 3 分の 1 に分割されます。 有益な副作用の 1 つは、並列コンピューティングのために、場合によっては低速クエリが高速で実行されることです。 多数のドキュメントと一致するクエリや、多数のドキュメントの数をカウントするファセットなど、選択度の低いクエリの並列処理に注目しました。 ドキュメントの関連性を評価したり、ドキュメントの数をカウントしたりするために、膨大な量の計算が必要となるため、パーティションを追加することでクエリをより速く完了できます。  
   
  Standard Search サービスでは最大 12 個のパーティションを、Basic Search サービスでは 1 つのパーティションを使用できます。 パーティションは [Azure Portal](search-create-service-portal.md) または [PowerShell](search-manage-powershell.md) から調整できます。

+ **高カーディナリティ フィールドを制限する**

  高カーディナリティ フィールドは、多数の一意の値を含むファセット可能フィールドまたはフィルター可能フィールドで構成されます。そのため、結果を計算するときに膨大な量のリソースを消費します。 たとえば、製品 ID フィールドや説明フィールドをファセット可能/フィルター可能として設定すると、ドキュメント間でほとんどの値が一意であるため、高カーディナリティとしてカウントされます。 可能な限り、高カーディナリティ フィールドの数を制限します。

+ **Search のレベルを上げる**  

  低速クエリのパフォーマンスを向上させるもう 1 つの方法として、Azure Cognitive Search のレベルを上げます。 レベルを上げると、CPU が高速化され、メモリ容量が増えるので、その両方によりクエリのパフォーマンスが向上します。

## <a name="scale-for-availability"></a>可用性のためのスケーリング

レプリカを利用すると、クエリの待機時間が短くなるだけでなく、可用性が高くなります。 レプリカが 1 つの場合、ソフトウェアの更新後や発生する他のメンテナンス イベントのためのサーバーの再起動により、定期的なダウンタイムが発生することを想定しておく必要があります。 そのため、アプリケーションで検索 (クエリ) と書き込み (インデックス作成イベント) の高可用性が必要かどうかを検討することが重要です。 Azure Cognitive Search では、次の属性を持つすべての有料検索サービスで SLA オプションを提供しています。

+ 読み取り専用ワークロード (クエリ) の高可用性を実現するには 2 つのレプリカ

+ 読み取り/書き込みワークロード (クエリとインデックス作成) の高可用性を実現するには 3 つ以上のレプリカ

この詳細については、[Azure Cognitive Search のサービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/search/v1_0/)に関するページをご覧ください。

レプリカはデータのコピーであるため、Azure Cognitive Search で複数のレプリカを用意すれば、あるレプリカでコンピューターの再起動やメンテナンスを行い、同時に他のレプリカでクエリの実行を続けることが可能になります。 逆に、レプリカを取り除くと、それらのレプリカは使用率が低いリソースだったと仮定して、クエリのパフォーマンスが低下します。

<a name="availability-zones"></a>

### <a name="availability-zones"></a>可用性ゾーン

[Availability Zones](../availability-zones/az-overview.md) は、リージョンのデータ センターを個別の物理的な場所のグループに分割することで、同じリージョン内で高可用性を実現します。 Cognitive Search の場合、個々のレプリカがゾーン割り当ての単位です。 検索サービスは 1 つのリージョン内で実行され、そのレプリカは異なるゾーンで実行されます。

検索サービスに 2 つ以上のレプリカを追加することで、Azure Cognitive Search で Availability Zones を利用することができます。 各レプリカは、リージョン内の異なる可用性ゾーンに配置されます。 Availability Zones よりも多くのレプリカがある場合、レプリカは Availability Zones 全体に可能な限り均等に配布されます。

現在、Azure Cognitive Search では、次のいずれかのリージョンで作成された Standard レベル以上の検索サービスの Availability Zones がサポートされます。

+ オーストラリア東部 (2021 年 1 月 30 日以降に作成)
+ カナダ中部 (2021 年 1 月 30 日以降に作成)
+ 米国中部 (2020 年 12 月 4 日以降に作成)
+ 米国東部 (2021 年 1 月 27 日以降に作成)
+ 米国東部 2 (2021 年 1 月 30 日以降に作成)
+ フランス中部 (2020 年 10 月 23 日以降に作成)
+ 東日本 (2021 年 1 月 30 日以降に作成)
+ 北ヨーロッパ (2021 年 1 月 28 日以降に作成)
+ 東南アジア (2021 年 1 月 31 日以降に作成)
+ 英国南部 (2021 年 1 月 30 日以降に作成)
+ 西ヨーロッパ (2021 年 1 月 29 日以降に作成)
+ 米国西部 2 (2021 年 1 月 30 日以降に作成)

Availability Zones は、[Azure Cognitive Search サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/search/v1_0/)には影響しません。 クエリの高可用性を実現するには、3 つ以上のレプリカが必要です。

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>地理的に分散したワークロードと Geo 冗長のためのスケーリング

地理的に分散したワークロードの場合、ホスト データ センターから遠い場所にいると、待機時間が長くなります。 1 つの方策は、これらのユーザーに近接するリージョンに複数の Search サービスをプロビジョニングすることです。

Azure Cognitive Search では、リージョンとの間で Azure Cognitive Search のインデックスを geo レプリケートする自動化された方法を現在は提供していませんが、実装と管理のためにこのプロセスを簡素化できる手法がいくつかあります。 以下のセクションでは、これらの手法の概要を説明します。

地理的に分散した一連の Search サービスの目的は、2 つかそれ以上のリージョンで 2 つかそれ以上のインデックスを利用できるようにすることです。そうすることで、次の例のように、待機時間が最も短い Azure Cognitive Search サービスまでの経路がユーザーに与えられます。

   ![リージョン別のサービスのクロス集計][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>複数のサービス間でデータの同期を維持する

分散した Search サービスの同期を維持するには 2 つのオプションがあり、[Azure Cognitive Search インデクサー](search-indexer-overview.md)か、Push API ([Azure Cognitive Search REST API](/rest/api/searchservice/) とも呼ばれます) を使用します。  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>複数のサービス上のコンテンツ更新のためのインデクサーの使用

あるサービスで既にインデクサーを使用している場合は、2 つ目のサービスに 2 つ目のインデクサーを構成し、同じデータ ソース オブジェクトを使用して同じ場所からデータをプルすることができます。 各リージョン内の各サービスにはその独自のインデクサーとターゲット インデックスがありますが (検索インデックスが共有されていないため、データが重複しています)、各インデクサーは同じデータ ソースを参照します。

このアーキテクチャの概観を次に示します。

   ![分散したインデクサーとサービスの組み合わせを使用する単一のデータ ソース][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>複数のサービスでコンテンツの更新をプッシュするための REST API を使用する

[Azure Cognitive Search インデックスの内容をプッシュ](/rest/api/searchservice/update-index)するために、Azure Cognitive Search REST API を使用している場合は、更新が必要になるたびにすべての Search サービスに変更をプッシュすることで、さまざまな Search サービスの同期を維持できます。 コードでは、ある Search サービスの更新が失敗した場合でも、他のサービスでの成功は必ず処理してください。

## <a name="leverage-azure-traffic-manager"></a>Azure Traffic Manager の活用

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) を使用すると、複数の Search サービスが支援する地理的に配置された複数の Web サイトに要求をルーティングできます。 Traffic Manager の利点の 1 つは、Azure Cognitive Search をプローブしてそれが利用可能であることを確実にし、ダウンタイムが発生した場合にユーザーを別の Search サービスにルーティングできることです。 また、Azure Web サイトを使用して検索要求をルーティングしている場合、Azure Traffic Manager を使用すると、Web サイトは稼働していても Azure Cognitive Search が稼働していない場合に負荷を分散できます。 Traffic Manager を活用したアーキテクチャの例を次に示します。

   ![リージョン別のサービスのクロス集計 (中央の Traffic Manager を使用)][3]

## <a name="next-steps"></a>次のステップ

価格レベルと各レベルのサービスの制限の詳細については、[サービスの制限](search-limits-quotas-capacity.md)に関するページをご覧ください。 パーティションとレプリカの組み合わせの詳細については、[キャパシティ プランニング](search-capacity-planning.md)に関する記事をご覧ください。

この記事で説明された手法のパフォーマンスとデモについて、次の動画をご覧ください。

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png