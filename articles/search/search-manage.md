---
title: ポータルでの Azure Search のサービス管理 - Azure Search
description: Azure portal を使用して、Microsoft Azure のホスト型クラウド検索サービスである Azure Search を管理します。
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d5820c927b88eba37eaf092dfd4b209180bfc8eb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775596"
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Azure Portal での Azure Search のサービス管理
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [ポータル](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Search は、高度な検索エクスペリエンスをカスタムアプリに組み込むために使用される、フル マネージドのクラウドベースの検索サービスです。 この記事では、既にプロビジョニング済みの検索サービスに対して [Azure portal](https://portal.azure.com) で実行できるサービス管理タスクについて説明します。 サービス管理は軽量設計で、次のタスクに限定されています。

> [!div class="checklist"]
> * サービスへの読み取りまたは書き込みアクセスに使用される "*API キー*" へのアクセスを管理する。
> * パーティションとレプリカの割り当てを変更することにより、サービスの容量を調整する。
> * リソース使用量をサービス レベルの上限と比較して監視する。

管理タスクに*アップグレード*は表示されません。 リソースはサービスがプロビジョニングされたときに割り当てられるため、別のレベルに移動するには新しいサービスが必要です。 詳細については、[Azure Search サービスの作成](search-create-service-portal.md)に関するページを参照してください。

> [!Tip]
> 検索トラフィックまたはクエリのパフォーマンスを分析するには、 クエリの量、ユーザーが検索する用語、ユーザーがインデックス内の特定のドキュメントに誘導されるときに検索結果が成功する度合いを監視できます。 詳細については、[Azure Search の検索トラフィックの分析](search-traffic-analytics.md)、[使用状況とクエリ メトリックの監視](search-monitor-usage.md)、[パフォーマンスと最適化](search-performance-optimization.md)に関する各ページを参照してください。

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>管理者権限
サービス自体のプロビジョニングまたは使用停止は、Azure サブスクリプションの管理者または共同管理者が実行できます。

サービス内では、サービスの URL と管理 API キーにアクセスできるすべてのユーザーがサービスへの読み取り/書き込みアクセス権を持ちます。 読み取り/書き込みアクセス権は、[RBAC で定義されたロール](search-security-rbac.md)によって実装されるサーバー オブジェクト (API キー、インデックス、インデクサー、データ ソース、スケジュール、ロール割り当てなど) の追加、削除、変更の権限を提供します。

Azure Search に対するすべてのユーザー操作は、サービスへの読み取り/書き込みアクセス (管理者権限) か、またはサービスへの読み取り専用アクセス (クエリ権限) のいずれかのモードに分類されます。 詳細については、「 [API キーを管理する](search-security-api-keys.md)」をご覧ください。

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>ログとシステム情報
Azure Search は、ポータルまたはプログラム インターフェイスを通じて個々のサービスのログ ファイルを公開しません。 Basic レベル以上では、サービス レベル アグリーメント (SLA) に従って、すべての Azure Search サービスが 99.9% の可用性を実現するようにマイクロソフトによって監視されます。 サービスの速度が低下した場合や、要求のスループットが SLA のしきい値を下回った場合は、サポート チームが利用可能なログ ファイルを確認して問題に対処します。

サービスに関する一般的な情報は、以下の方法で入手できます。

* ポータルのサービス ダッシュボードで、プロパティ、およびステータス メッセージを確認する。
* [PowerShell](search-manage-powershell.md) または[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) を使用して[サービスのプロパティを取得](https://docs.microsoft.com/rest/api/searchmanagement/services)するか、インデックス リソース使用率のステータスを確認する。
* 既に説明したように、 [検索トラフィックの分析](search-traffic-analytics.md)を使用する。

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>リソース使用量を監視する
ダッシュボードでは、リソース監視が、サービス ダッシュボードに表示される情報と、サービスのクエリで取得できるいくつかのメトリックに限定されます。 サービス ダッシュボードの [使用] セクションで、パーティション リソース レベルが自分のアプリケーションに適しているかどうかをすばやく決定できます。 ログに記録されたイベントをキャプチャして保持する必要がある場合は、Azure 監視などの外部のリソースをプロビジョニングできます。 詳細については、[Azure Search の監視](search-monitor-usage.md)に関するページをご覧ください。

Search Service REST API を使用して、プログラムでドキュメントとインデックスの数を取得できます。 

* [インデックス統計の取得](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [ドキュメントのカウント](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>障害復旧とサービスの停止

Microsoft はユーザーのデータを回収できますが、Azure Search では、クラスターまたはデータ センターのレベルで障害が発生した場合のサービスの即時フェールオーバーは提供されません。 データ センターでクラスターの障害が発生した場合、運用チームは障害を検出してサービスの復元を行います。 サービスの復元中にダウンタイムが発生しますが、[サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) に従い、サービス使用不可に対する補償としてサービス クレジットを要求できます。 

Microsoft の管理が及ばない外部の壊滅的災害の際にサービスを継続する必要がある場合は、異なるリージョンに[追加のサービスをプロビジョニング](search-create-service-portal.md)し、geo レプリケーション戦略を導入して、インデックスがすべてのサービスで完全に冗長になるようにする必要があります。

インデックスの作成と更新に[インデクサー](search-indexer-overview.md)を使っているお客様は、同じデータ ソースを利用する geo 固有のインデクサーによってディザスター リカバリーを処理できます。 異なるリージョンにある 2 つのサービスそれぞれでインデクサーを実行し、同じデータ ソースのインデックスを作成すると、geo 冗長性を実現できます。 geo 冗長性も備えたデータ ソースからインデックスを作成する場合、Azure Search インデクサーは、プライマリ レプリカからの増分インデックス作成のみを実行できることに注意してください。 フェールオーバー イベントでは、新しいプライマリ レプリカをポイントするように再度インデクサーを設定してください。 

インデクサーを使用しない場合は、アプリケーションのコードを使ってオブジェクトとデータを異なる検索サービスに並列にプッシュします。 詳細については、「 [Performance and optimization in Azure Search](search-performance-optimization.md)」(Azure Search のパフォーマンスと最適化) をご覧ください。

## <a name="backup-and-restore"></a>バックアップと復元

Azure Search は主要なデータ ストレージ ソリューションではないため、セルフサービスのバックアップと復元のための正式なメカニズムは提供されません。 インデックスの作成と設定に使われるアプリケーション コードが、誤ってインデックスを削除した場合の事実上の復元オプションです。 

インデックスを再構築するには、インデックスを削除し (存在する場合)、サービスでインデックスを再作成して、プライマリ データ ストアからデータを取得することによって再読み込みします。


<a id="scale"></a>

## <a name="scale-up-or-down"></a>拡大または縮小
すべての検索サービスは、最小限の 1 つのレプリカと 1 つのパーティションで開始されます。 [専用リソースを提供するレベル](search-limits-quotas-capacity.md)にサインアップしている場合は、サービス ダッシュボードの **[スケール]** タイルをクリックし、リソースの使用状況を調整します。

いずれかのリソースで容量を追加すると、サービスで自動的に使用されます。 それ以外のアクションは必要ありませんが、新しいリソースの影響が現れるまでわずかに遅延が発生します。 追加のリソースのプロビジョニングには 15 分以上かかる場合があります。

 ![][10]

### <a name="add-replicas"></a>レプリカの追加
1 秒あたりのクエリ (QPS) の増加や高可用性の実現は、レプリカの追加により達成できます。 各レプリカにはインデックスのコピーが 1 つあるため、レプリカを 1 つ追加することは、サービス クエリ要求の処理に使用できるインデックスを 1 つ追加することです。 高可用性を実現するには少なくとも 3 つのレプリカが必要です (詳細については、「 [Capacity Planning](search-capacity-planning.md) 」(容量計画) をご覧ください。

より多くのレプリカを持つ検索サービスでは、より多くのインデックスに対してクエリ要求を負荷分散できます。 特定のクエリの数量のレベルで、要求のサービスで利用できるインデックスのコピーが多いほど、クエリ スループットはより高速になります。 クエリの遅延が発生した場合は、追加のレプリカをオンラインにすることでパフォーマンスへの良い影響を期待できます。

レプリカを追加するとクエリのスループットは向上しますが、サービスにレプリカを追加することにより厳密に 2 倍や 3 倍になるわけではありません。 すべての検索アプリケーションは、クエリ パフォーマンスに影響を与える外部要因の影響を受けます。 クエリの応答時間を変動させる 2 つの要因として、複雑なクエリおよびネットワークの遅延があります。

### <a name="add-partitions"></a>パーティションの追加
ほとんどのサービス アプリケーションには、パーティション数ではなくレプリカ数の増加への固有のニーズがあります。 ドキュメント数を増やす必要がある場合、Standard サービスにサインアップしていればパーティションを追加できます。 Basic レベルでは、パーティションは追加できません。

Standard レベルでは、パーティションが 12 の倍数単位 (具体的には、1、2、3、4、6、12) で追加されます。 これは、シャーディングのアーティファクトです。 インデックスは 12 個のシャードで作成され、これらはすべて 1 個のパーティションまたは均等に 2、3、4、6、12 個のパーティションに格納されます (パーティションごとに 1 つのシャード)。

### <a name="remove-replicas"></a>レプリカの削除
クエリの数量が多い期間の後 (たとえば、休日のセールの終了後)、検索クエリの負荷が正常化したら、スライダーを使用してレプリカを減らすことができます。 それ以外の手順は必要ありません。 レプリカの数を減らすと、データ センター内の仮想マシンが解放されます。 クエリとデータ インジェストの操作は、以前よりも少ない数の VM で実行されます。 レプリカの最小要件は 1 つです。

### <a name="remove-partitions"></a>パーティションの削除
追加の手順を必要としないレプリカの削除に比べ、削除できる量よりも多くのストレージを使用している場合の手順は少し複雑です。 たとえば、3 つのパーティションを使用しているソリューションでは、パーティションを 1 つまたは 2 つに減らすと、新しいストレージ領域がインデックスのホストに必要な量よりも少ない場合にエラーが発生します。 ご推察のとおり、その場合はインデックスまたは関連付けられたインデックス内のドキュメントを削減して領域を解放するか、現在の構成を維持するかを選択できます。

特定のパーティションにどのインデックス シャードが格納されているかを検出する方法はありません。 各パーティションで約 25 GB のストレージが提供されるため、現在のパーティションの数で対応できるサイズまでストレージを削減する必要があります。 1 つのパーティションに戻す場合は、12 個のシャードをすべて収める必要があります。

将来の計画を立てるには、ストレージをチェックして (「 [Get Index Statistics (Azure Search API) (インデックス統計の取得 (Azure Search API))](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)」を使用) 実際にどのくらい使用したかを確認します。 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>スケールとデプロイメントに関するベスト プラクティス
この 30 分間のビデオでは、地理的に分散したワークロードを含む、高度なデプロイメント シナリオのベスト プラクティスを確認します。 同じ点を説明したヘルプ ページとして、「 [Performance and optimization in Azure Search](search-performance-optimization.md) 」(Azure Search のパフォーマンスと最適化) もご覧ください。

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>次の手順
サービス管理の背後にある概念を把握できたら、[PowerShell](search-manage-powershell.md) を使ってタスクを自動化することを検討してください。

また、[パフォーマンスと最適化の記事](search-performance-optimization.md)を確認することをお勧めします。

さらに、前のセクションで紹介したビデオを見ることもお勧めします。 ビデオでは、このセクションで触れたテクニックが詳しく説明されています。

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



