---
title: "Azure Search のパフォーマンスと最適化に関する考慮事項 | Microsoft Docs"
description: "Azure Search のパフォーマンスを調整し、最適なスケールを構成します。"
services: search
documentationcenter: 
author: LiamCavanagh
manager: pablocas
editor: 
ms.assetid: 4d3cd864-29d2-4921-be0d-a3f1a819de46
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/17/2016
ms.author: liamca
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9024c47e7d483129d66105012e0d67cfa9700cb9


---
# <a name="azure-search-performance-and-optimization-considerations"></a>Azure Search のパフォーマンスと最適化に関する考慮事項
優れた検索機能は、多くのモバイル アプリケーションと Web アプリケーションの成功の鍵となります。 不動産から中古車市場、オンライン カタログまで、高速検索と適切な結果はカスタマー エクスペリエンスに影響を及ぼします。 このドキュメントは、特に、スケーラビリティ、複数の言語のサポート、またはカスタム ランキングの非常に複雑な要件がある高度なシナリオで、Azure Search を最大限に活用する方法のベスト プラクティスを示すことを目的としています。  また、内部の概要を説明し、実際の顧客アプリで効果的に機能する方法についても説明します。

## <a name="performance-and-scale-tuning-for-search-services"></a>Search サービスのパフォーマンスとスケールの調整
Bing や Google などの検索エンジンとこれらが提供する高いパフォーマンスにだれもが慣れています。  そのため、顧客は検索に対応する Web アプリケーションやモバイル アプリケーションを使用するときに、同様のパフォーマンス特性を求めます。  検索のパフォーマンスに最適化する場合、最良の方法の 1 つは、待機時間 (クエリが完了し、結果を返すまでの所要時間) に重点を置くことです。  検索の待機時間に最適化する場合、次の点が重要となります。

1. 標準的な検索要求が完了するまでに要する対象待機時間 (または最長時間) を選択します。
2. 実際のワークロードを作成し、現実的なデータセットを使って Search サービスでワークロードをテストして待機時間の割合を測定します。
3. 少数の QPS (1 秒あたりのクエリ数) から始め、クエリの待機時間が定義済みの対象待機時間を下回るまで、テストで実行されるクエリ数を増やし続けます。  これは、アプリケーションの普及に伴ってスケールの計画を立てる際に役立つ重要なベンチマークです。
4. 可能な限り、HTTP 接続を再利用します。  Azure Search .NET SDK を使用している場合、これはインスタンスまたは [SearchIndexClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx) インスタンスを再利用することを意味します。REST API を使用している場合は、1 つの HttpClient を再利用します。

これらのテスト ワークロードの作成時に留意すべき Azure Search の特性がいくつかあります。

1. 一度に非常に多くの検索クエリを発行すると、Azure Search サービスで使用できるリソースの上限に達する可能性があります。  この場合、HTTP 503 応答コードが表示されます。  そのため、さまざまな検索要求から始め、検索要求を追加したときの待機時間の割合の差を確認することをお勧めします。
2. コンテンツを Azure Search にアップロードすると、Azure Search サービスの全体的なパフォーマンスと待機時間に影響します。  ユーザーが検索を実行している間にデータを送信する場合は、テストでこのワークロードを考慮することが重要です。
3. すべての検索クエリが同じパフォーマンス レベルで実行されるわけではありません。  たとえば、文書検索や検索候補は、多数のファセットとフィルターを含むクエリよりも通常は高速で実行されます。  テストを作成するときに、予想されるさまざまなクエリを考慮することをお勧めします。  
4. 同じ検索要求を継続的に実行すると、データのキャッシュによって、異なるクエリ セットを使用した場合よりもパフォーマンスが高く見えるようになるため、検索要求にバリエーションを持たせることが重要です。

> [!NOTE]
> [Visual Studio Load Testing](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) では、Azure Search に対するクエリを実行するために必要な HTTP 要求を実行することができ、要求の並列処理が可能になるため、ベンチマーク テストを実行するのに最適です。
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>高いクエリ レートと調整済みの要求に対応するための Azure Search のスケーリング
受信する調整済みの要求が多すぎる場合や、クエリ負荷の増加によって対象待機時間の割合を超えた場合は、次の 2 つの方法のいずれかで待機時間の割合を減らすことができます。

1. **レプリカを増やす:** レプリカはデータのコピーのようなものです。レプリカにより、Azure Search は複数のコピーに対して要求の負荷を分散させることができます。  複数のレプリカでの負荷分散とデータのレプリケーションはすべて Azure Search によって管理されます。サービスに割り当てられたレプリカの数はいつでも変更できます。  Standard Search サービスでは最大 12 個、Basic Search サービスでは最大 3 個のレプリカを割り当てることができます。  レプリカは、[Azure Portal](search-create-service-portal.md) または [Azure Search 管理 API](search-get-started-management-api.md) を使用して調整できます。
2. **Search のレベルを上げる:** Azure Search には[複数のレベル](https://azure.microsoft.com/pricing/details/search/)が用意されており、レベルごとに異なるパフォーマンス レベルを提供します。  クエリの数が非常に多いため、レプリカを最大数まで増やしても、現在のレベルでは待機時間の割合を十分に低くしておくことができない場合があります。  この場合、多数のドキュメントと負荷がきわめて高いクエリ ワークロードを使用するシナリオに最適な Azure Search S3 など、Search のより高いレベルのいずれかを利用することを検討します。

## <a name="scaling-azure-search-for-slow-individual-queries"></a>個々の低速クエリに対応するための Azure Search のスケーリング
待機時間の割合が高くなる可能性があるもう 1 つの理由は、1 つのクエリが完了するまでに時間がかかりすぎることです。  この場合、レプリカを追加しても待機時間の割合は改善されません。  この状況に対応するには、次の 2 つの方法があります。

1. **パーティションを増やす:** パーティションとは、追加のリソース間でデータを分割するためのメカニズムです。  そのため、2 つ目のパーティションを追加すると、データが 2 つに分割されます。  3 つ目のパーティションにより、インデックスが 3 つに分割されます。これは、計算の並列処理によって低速クエリの実行を高速化する場合にも効果があります。  選択度の低いクエリを含むクエリで、この並列処理が大きな効果を発揮した例がいくつかあります。  たとえば、多数のドキュメントと一致するクエリや、ファセットで多数のドキュメントの数をカウントする必要がある場合です。  ドキュメントの関連性を評価したり、ドキュメントの数をカウントしたりするために多数の計算が必要となるため、パーティションを追加することで追加の計算を提供できます。  
   
   Standard Search サービスでは最大 12 個のパーティション、Basic Search サービスでは 1 つのパーティションを使用できます。  パーティションは、[Azure Portal](search-create-service-portal.md) または [Azure Search 管理 API](search-get-started-management-api.md) を使用して調整できます。
2. **高基数フィールドを制限する:** 高基数フィールドは、多数の一意の値を含むファセット可能フィールドまたはフィルター可能フィールドで構成されます。そのため、結果を計算するために多くのリソースを必要とします。   たとえば、製品 ID フィールドや説明フィールドをファセット可能/フィルター可能として設定すると、ドキュメント間でほとんどの値が一意であるため、高基数になります。 可能な限り、高基数フィールドの数を制限します。
3. **Search のレベルを上げる:** 低速クエリのパフォーマンスを向上させるもう 1 つの方法として、Azure Search のレベルを上げます。  レベルを上げると、CPU が高速化され、メモリ容量も増えるので、クエリのパフォーマンスが向上する可能性があります。

## <a name="scaling-for-availability"></a>可用性のスケーリング
レプリカにより、クエリの待機時間が短縮されるだけでなく、高可用性も実現できます。  レプリカが 1 つの場合、ソフトウェアの更新後や発生する他のメンテナンス イベントのためのサーバーの再起動により、定期的なダウンタイムが発生することを想定しておく必要があります。  そのため、アプリケーションで検索 (クエリ) と書き込み (インデックス作成イベント) の高可用性が必要かどうかを検討することが重要です。  Azure Search では、次の属性を持つすべての有料検索サービスで SLA オプションを提供しています。

* 読み取り専用ワークロード (クエリ) の高可用性を実現するには 2 つのレプリカ
* 読み取り/書き込みワークロード (クエリとインデックス作成) の高可用性を実現するには 3 つ以上のレプリカ

この詳細については、 [Azure Search のサービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/search/v1_0/)に関するページをご覧ください。

レプリカはデータのコピーであるため、複数のレプリカを用意することで、Azure Search は一度に 1 つのレプリカでコンピューターの再起動やメンテナンスを実行し、それ以外のレプリカでクエリを引き続き実行することができます。  したがって、データのコピーが 1 つ少ない状態でクエリを実行しなければならなくなった場合に、このダウンタイムがクエリに及ぼす可能性のある影響を考慮することも必要になります。

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>地理的に分散したワークロードのスケーリングと Geo 冗長の提供
地理的に分散したワークロードでは、Azure Search サービスをホストするデータ センターから離れた場所のユーザーは、待機時間の割合が高くなります。  そのため、多くの場合、これらのユーザーに近接するリージョンで複数の Search サービスを用意することが重要になります。  Azure Search では、リージョン間で Azure Search インデックスを geo レプリケートする自動化された方法を現在は提供していませんが、実装と管理のためにこのプロセスを簡素化できる手法がいくつかあります。 以下のセクションでは、これらの手法の概要を説明します。

地理的に分散した一連の Search サービスの目的は、次の例に示すように、待機時間が最も短い Azure Search サービスにユーザーをルーティングする複数のリージョンで、複数のインデックスを使用できるようにすることです。

   ![リージョン別のサービスのクロス集計][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>複数の Azure Search サービス間でのデータの同期の維持
分散した Search サービスの同期を維持するには、[Azure Search インデクサー](search-indexer-overview.md)を使用するか、Push API ([Azure Search REST API](https://msdn.microsoft.com/library/dn798935.aspx) とも呼ばれます) を使用します。  

### <a name="azure-search-indexers"></a>Azure Search インデクサー
Azure Search インデクサーを使用している場合は、Azure SQL DB や DocumentDB などの中央のデータストアからデータの変更を既にインポートしています。 新しい Search サービスを作成するときは、この同じデータストアを参照する、そのサービス用の新しい Azure Search インデクサーも作成します。 これにより、新しい変更がデータストアに書き込まれるたびに、さまざまなインデクサーによってインデックスが作成されます。  

このアーキテクチャの例を次に示します。

   ![分散したインデクサーとサービスの組み合わせを使用する単一のデータ ソース][2]

### <a name="push-api"></a>Push API
[Azure Search インデックスの内容を更新](https://msdn.microsoft.com/library/dn798930.aspx)するために、Azure Search Push API を使用している場合は、更新が必要になるたびにすべての Search サービスに変更をプッシュすることで、さまざまな Search サービスの同期を維持できます。  これを実行するときには、1 つの Search サービスの更新が失敗し、1 つ以上の更新が成功した場合に確実に処理できるようにする必要があります。

## <a name="leveraging-azure-traffic-manager"></a>Azure Traffic Manager の活用
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) を使用すると、複数の Azure Search サービスが支援する地理的に配置された複数の Web サイトに要求をルーティングできます。  Traffic Manager の利点の 1 つは、Azure Search をプローブしてサービスが利用可能であることを確認し、ダウンタイムが発生した場合にユーザーを別の Search サービスにルーティングできることです。  また、Azure Websites を使用して検索要求をルーティングしている場合、Azure Traffic Manager を使用すると、Websites は稼働していても Azure Search が稼働していない場合に負荷を分散できます。  Traffic Manager を活用したアーキテクチャの例を次に示します。

   ![リージョン別のサービスのクロス集計 (中央の Traffic Manager を使用)][3]

## <a name="monitoring-performance"></a>パフォーマンス監視
Azure Search では、[検索トラフィックの分析 (STA)](search-traffic-analytics.md) を使用してサービスのパフォーマンスを分析および監視できます。 STA により、必要に応じて、個々の検索操作や Azure Storage アカウントの集計メトリックをログに記録できます。ログに記録された集計メトリックは、Power BI で分析または視覚化するために処理できます。  STA メトリックを使用することで、平均クエリ数やクエリの応答時間などのパフォーマンス統計情報を確認できます。  また、操作のログでは、特定の検索操作の詳細を調べることができます。

STA は、その Azure Search の観点から待機時間の割合を把握するのに役立つツールです。  ログに記録されたクエリのパフォーマンス メトリックは、Azure Search でクエリが完全に処理されるまでの所要時間 (クエリが要求された時点から送信されるまでの時間) に基づいているので、これを使用して、待機時間の問題が Azure Search サービス側とサービスの外部 (ネットワーク待ち時間など) のどちらで発生しているのかを特定できます。  

## <a name="next-steps"></a>次のステップ
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



<!--HONumber=Feb17_HO3-->


