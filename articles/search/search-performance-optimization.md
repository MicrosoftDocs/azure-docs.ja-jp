---
title: パフォーマンスの最適化のためのデプロイ戦略とベスト プラクティス - Azure Search
description: Azure Search のパフォーマンスを調整し、最適なスケールを構成するためのベスト プラクティスについて説明します。
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 8a07657d04cee57cb69c9f5f7862fed3e7965716
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404469"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>Azure Search でのパフォーマンスの最適化のためのデプロイ戦略とベスト プラクティス

この記事では、スケーラビリティと可用性に対する高度な要件を持つ高度なシナリオのベスト プラクティスについて説明します。 

## <a name="develop-baseline-numbers"></a>ベースラインの数を作り出す
検索のパフォーマンスを最適化するときは、クエリの実行時間を減らすことに注力してください。 そのためには、一般的なクエリの負荷がどのようになるか把握する必要があります。 次のガイドラインにより、ベースラインのクエリ数に達することができます。

1. 標準的な検索要求が完了するまでに要する対象待機時間 (または最長時間) を選択します。
2. 実際のワークロードを作成し、現実的なデータセットを使って Search サービスでワークロードをテストして待機時間の割合を測定します。
3. 少数の QPS (1 秒あたりのクエリ数) から始め、クエリの待機時間が定義済みの対象待機時間を下回るまで、テストで実行されるクエリ数を徐々に増やします。 これは、アプリケーションの普及に伴ってスケールの計画を立てる際に役立つ重要なベンチマークです。
4. 可能な限り、HTTP 接続を再利用します。 Azure Search .NET SDK を使用している場合、これはインスタンスまたは [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) インスタンスを再利用することを意味します。REST API を使用している場合は、1 つの HttpClient を再利用します。
5. インデックスのさまざまな部分で検索が行われるように、クエリ要求の内容を変更します。 同じ検索要求を継続的に実行すると、データのキャッシュによって、異なるクエリ セットを使用した場合よりもパフォーマンスが高く見えるようになるため、バリエーションを持たせることが重要です。
6. クエリ要求の構造を、さまざまなクエリの種類を取得するように変更します。 すべての検索クエリが同じレベルで実行されるわけではありません。 たとえば、文書検索や検索候補は、多数のファセットとフィルターを含むクエリよりも通常は高速です。 テスト構成には、運用環境で想定される比率とほぼ同じ比率で、さまざまなクエリを含める必要があります。  

これらのテスト ワークロードの作成時に留意すべき Azure Search の特性がいくつかあります。

+ 一度にプッシュする検索クエリが多すぎると、サービスに負荷がかかる可能性があります。 この場合、HTTP 503 応答コードが表示されます。 テスト中に 503 が表示されないようにするには、さまざまな検索要求から始めて、検索要求を追加したときの待機時間の割合の差を確認します。

+ Azure Search はインデックス作成タスクをバックグラウンドでは実行しません。 サービスがクエリとインデックス作成のワークロードを同時に処理する場合は、インデックス作成ジョブをクエリ テストに組み込むか、ピーク時を避けてインデックス作成ジョブを実行するオプションを調べることで、この点を考慮してください。

> [!NOTE]
> [Visual Studio Load Testing](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) では、Azure Search に対するクエリを実行するために必要な HTTP 要求を実行することができ、要求の並列処理が可能になるため、ベンチマーク テストを実行するのに最適です。
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>高いクエリ料と調整済みの要求に対応するためのスケーリング
受信する調整済みの要求が多すぎる場合や、クエリ負荷の増加によって対象待機時間の割合を超えた場合は、次の 2 つの方法のいずれかで待機時間の割合を減らすことができます。

1. **レプリカを増やす:** レプリカはデータのコピーのようなものです。レプリカにより、Azure Search は複数のコピーに対して要求の負荷を分散させることができます。  複数のレプリカでの負荷分散とデータのレプリケーションはすべて Azure Search によって管理されます。サービスに割り当てられたレプリカの数はいつでも変更できます。  Standard Search サービスでは最大 12 個、Basic Search サービスでは最大 3 個のレプリカを割り当てることができます。 レプリカは [Azure Portal](search-create-service-portal.md) または [PowerShell](search-manage-powershell.md) から調整できます。
2. **Search のレベルを上げる:** Azure Search には[複数のレベル](https://azure.microsoft.com/pricing/details/search/)が用意されており、レベルごとに異なるパフォーマンス レベルを提供します。  クエリの数が非常に多いため、レプリカを最大数まで増やしても、現在のレベルでは待機時間の割合を十分に低くしておくことができない場合があります。この場合、多数のドキュメントと負荷がきわめて高いクエリ ワークロードを使用するシナリオに最適な Azure Search S3 など、Search のより高いレベルのいずれかを利用することを検討します。

## <a name="scaling-for-slow-individual-queries"></a>個々の低速クエリに対応するためのスケーリング
待機時間の割合が高くなるもう 1 つの理由は、1 つのクエリが完了するまでに時間がかかりすぎることです。 この場合、レプリカを追加しても役に立ちません。 役に立つ可能性のある 2 つのオプションは、次のとおりです。

1. **パーティションを増やす:** パーティションとは、追加のリソース間でデータを分割するためのメカニズムです。 2 つ目のパーティションを追加するとデータが 2 つに分割され、3 つ目のパーティションを追加すると 3 つに分割されます。 有益な副作用の 1 つは、並列コンピューティングのために、場合によっては低速クエリが高速で実行されることです。 多数のドキュメントと一致するクエリや、多数のドキュメントの数をカウントするファセットなど、選択度の低いクエリの並列処理に注目しました。 ドキュメントの関連性を評価したり、ドキュメントの数をカウントしたりするために、膨大な量の計算が必要となるため、パーティションを追加することでクエリをより速く完了できます。  
   
   Standard Search サービスでは最大 12 個のパーティション、Basic Search サービスでは 1 つのパーティションを使用できます。  パーティションは [Azure Portal](search-create-service-portal.md) または [PowerShell](search-manage-powershell.md) から調整できます。

2. **高カーディナリティ フィールドを制限する:** 高カーディナリティ フィールドは、多数の一意の値を含むファセット可能フィールドまたはフィルター可能フィールドで構成されます。そのため、結果を計算するときに膨大な量のリソースを消費します。 たとえば、製品 ID フィールドや説明フィールドをファセット可能/フィルター可能として設定すると、ドキュメント間でほとんどの値が一意であるため、高カーディナリティとしてカウントされます。 可能な限り、高カーディナリティ フィールドの数を制限します。

3. **Search のレベルを上げる:** 低速クエリのパフォーマンスを向上させるもう 1 つの方法として、Azure Search のレベルを上げます。 レベルを上げると、CPU が高速化され、メモリ容量が増えるので、その両方によりクエリのパフォーマンスが向上します。

## <a name="scaling-for-availability"></a>可用性のスケーリング
レプリカにより、クエリの待機時間が短縮されるだけでなく、高可用性も実現できます。 レプリカが 1 つの場合、ソフトウェアの更新後や発生する他のメンテナンス イベントのためのサーバーの再起動により、定期的なダウンタイムが発生することを想定しておく必要があります。  そのため、アプリケーションで検索 (クエリ) と書き込み (インデックス作成イベント) の高可用性が必要かどうかを検討することが重要です。 Azure Search では、次の属性を持つすべての有料検索サービスで SLA オプションを提供しています。

* 読み取り専用ワークロード (クエリ) の高可用性を実現するには 2 つのレプリカ
* 読み取り/書き込みワークロード (クエリとインデックス作成) の高可用性を実現するには 3 つ以上のレプリカ

この詳細については、 [Azure Search のサービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/search/v1_0/)に関するページをご覧ください。

レプリカはデータのコピーであるため、複数のレプリカを用意することで、Azure Search は一度に 1 つのレプリカでコンピューターの再起動やメンテナンスを実行でき、それ以外のレプリカでクエリを引き続き実行することができます。 逆に、レプリカを取り除くと、それらのレプリカは使用率が低いリソースだったと仮定して、クエリのパフォーマンスが低下します。

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>地理的に分散したワークロードと Geo 冗長のスケーリング
地理的に分散したワークロードでは、Azure Search をホストするデータ センターから離れた場所のユーザーは、待機時間の割合が高くなります。 1 つの方策は、これらのユーザーに近接するリージョンに複数の Search サービスをプロビジョニングすることです。 Azure Search では、リージョン間で Azure Search インデックスを geo レプリケートする自動化された方法を現在は提供していませんが、実装と管理のためにこのプロセスを簡素化できる手法がいくつかあります。 以下のセクションでは、これらの手法の概要を説明します。

地理的に分散した一連の Search サービスの目的は、次の例に示すように、待機時間が最も短い Azure Search サービスにユーザーをルーティングする複数のリージョンで、複数のインデックスを使用できるようにすることです。

   ![リージョン別のサービスのクロス集計][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>複数の Azure Search サービス間でのデータの同期の維持
分散した Search サービスの同期を維持するには、[Azure Search インデクサー](search-indexer-overview.md)を使用するか、Push API ([Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/) とも呼ばれます) を使用します。  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>複数のサービス上のコンテンツ更新のためのインデクサーの使用

あるサービスで既にインデクサーを使用している場合は、2 つ目のサービスに 2 つ目のインデクサーを構成し、同じデータ ソース オブジェクトを使用して同じ場所からデータをプルすることができます。 各リージョン内の各サービスにはその独自のインデクサーとターゲット インデックスがありますが (検索コーパスが共有されていないため、データが重複しています)、各インデクサーは同じデータ ソースを参照します。

このアーキテクチャの概観を次に示します。

   ![分散したインデクサーとサービスの組み合わせを使用する単一のデータ ソース][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>複数のサービスでコンテンツの更新をプッシュするための REST API を使用する
[Azure Search インデックスの内容をプッシュ](https://docs.microsoft.com/rest/api/searchservice/update-index)するために、Azure Search REST API を使用している場合は、更新が必要になるたびにすべての Search サービスに変更をプッシュすることで、さまざまな Search サービスの同期を維持できます。 コードでは、ある Search サービスの更新が失敗した場合でも、他のサービスでの失敗は必ず処理してください。

## <a name="leverage-azure-traffic-manager"></a>Azure Traffic Manager の活用
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) を使用すると、複数の Azure Search サービスが支援する地理的に配置された複数の Web サイトに要求をルーティングできます。 Traffic Manager の利点の 1 つは、Azure Search をプローブしてサービスが利用可能であることを確認し、ダウンタイムが発生した場合にユーザーを別の Search サービスにルーティングできることです。 また、Azure Websites を使用して検索要求をルーティングしている場合、Azure Traffic Manager を使用すると、Websites は稼働していても Azure Search が稼働していない場合に負荷を分散できます。 Traffic Manager を活用したアーキテクチャの例を次に示します。

   ![リージョン別のサービスのクロス集計 (中央の Traffic Manager を使用)][3]

## <a name="monitor-performance"></a>パフォーマンスの監視
Azure Search では、[検索トラフィックの分析](search-traffic-analytics.md)を使用してサービスのパフォーマンスを分析および監視できます。 この機能を有効にし、クライアント アプリにインストルメンテーションを追加すると、必要に応じて、個々の検索操作や Azure Storage アカウントの集計メトリックをログに記録できます。ログに記録された集計メトリックは、Power BI で分析または視覚化するために処理できます。 この方法でメトリックをキャプチャすることで、平均クエリ数やクエリの応答時間などのパフォーマンス統計情報が提供されます。 また、操作のログでは、特定の検索操作の詳細を調べることができます。

トラフィック分析は、その Azure Search の観点から待機時間の割合を把握するのに役立ちます。 ログに記録されたクエリのパフォーマンス メトリックは、Azure Search でクエリが完全に処理されるまでの所要時間 (クエリが要求された時点から送信されるまでの時間) に基づいているので、これを使用して、待機時間の問題が Azure Search サービス側とサービスの外部 (ネットワーク待ち時間など) のどちらで発生しているのかを特定できます。  

## <a name="next-steps"></a>次の手順
価格レベルと各レベルのサービスの制限の詳細については、「 [Azure Search サービスの制限](search-limits-quotas-capacity.md)」をご覧ください。

パーティションとレプリカの組み合わせの詳細については、 [キャパシティ プランニング](search-capacity-planning.md) に関する記事をご覧ください。

パフォーマンスの詳細と、この記事で説明した最適化の実装方法については、次のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
