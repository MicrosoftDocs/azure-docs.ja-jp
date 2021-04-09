---
title: 操作とアクティビティを監視する
titleSuffix: Azure Cognitive Search
description: ログ記録を有効にし、クエリ アクティビティのメトリック、リソースの使用状況、およびその他のシステム データを Azure Cognitive Search サービスから取得します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3f348a5005f28fdd1d02be706ca2742d77368288
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592369"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Azure Cognitive Search の操作とアクティビティを監視する

この記事では、Azure Cognitive Search の監視の概念とツールの概要について説明します。 総合的な監視を行うため、組み込みの機能と Azure Monitor のようなアドオン サービスを組み合わせて使用することができます。

要するに、以下の追跡を行うことができます。

* サービス: 正常性または可用性とサービス構成の変更。
* ストレージ: 使用済みと使用可能の両方、サービス層で許可されているクォータに対する各コンテンツの種類の数。
* クエリ アクティビティ: ボリューム、待機時間、調整されたまたは削除されたクエリ。 ログに記録されるクエリ要求には、[Azure Monitor](#add-azure-monitor) が必要です。
* インデックス作成アクティビティ: Azure Monitor を使用した[診断ログ](#add-azure-monitor)が必要。

検索サービスでは、ユーザー単位の認証がサポートされていないため、ログでは ID 情報は見つかりません。

## <a name="built-in-monitoring"></a>組み込みの監視

組み込みの監視とは、検索サービスによってログに記録されるアクティビティを指します。 診断を除き、このレベルの監視には構成は必要ありません。

Azure Cognitive Search は、サービスの正常性とクエリのメトリックに関するレポートを作成するために、30 日周期のスケジュールで内部データを保持します。このデータは、ポータルまたはこれらの [REST API](#monitoring-apis) を通じて見つけることができます。

次のスクリーンショットは、ポータルで監視情報を見つけるのに役立ちます。 サービスの使用を開始するとすぐにデータが使用できるようになります。 ポータルのページは、数分ごとに更新されます。

* **[監視]** タブの [概要] ページには、クエリのボリューム、待機時間、およびサービスに負荷がかかっているかどうかが表示されます。
* 左側のナビゲーション ウィンドウの **[アクティビティ ログ]** は、Azure Resource Manager に接続されています。 アクティビティ ログでは、Resource Manager によって実行されたアクション (サービスの可用性と状態、容量 (レプリカとパーティション)、API キーに関連するアクティビティ) が報告されます。
* 下の方にある **[監視]** 設定では、構成可能なアラート、メトリック、診断ログが提供されます。 これらは必要に応じて作成します。 データが収集されて保存されたら、その情報を照会したり視覚化したりすることで分析情報を得ることができます。

![Search Service への Azure Monitor の統合](./media/search-monitor-usage/azure-monitor-search.png
 "Search Service への Azure Monitor の統合")

> [!NOTE]
> ポータル ページは数分ごとに更新されるため、報告される数値は概算であり、システムが要求をどの程度適切に処理しているかを大まかに把握することを目的としています。 1 秒あたりのクエリ数 (QPS: Queries Per Second) など実際のメトリックは、ページに表示される値を上回る場合もあれば下回る場合もあります。 精度が必要な場合は、API の使用を検討してください。

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>監視に役立つ API

次の API を使用して、ポータルの [監視] タブと [使用状況] タブにあるのと同じ情報を取得できます。

* [サービス統計情報の取得](/rest/api/searchservice/get-service-statistics)
* [インデックス統計の取得](/rest/api/searchservice/get-index-statistics)
* [ドキュメント数の取得](/rest/api/searchservice/count-documents)
* [インデクサーの状態の取得](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>アクティビティ ログとサービス正常性

ポータルの [ **[アクティビティ ログ]**](../azure-monitor/essentials/activity-log.md#view-the-activity-log) ページでは、Azure Resource Manager から情報を収集し、サービス正常性の変化をレポートします。 アクティビティ ログを監視して、サービス正常性に関連したクリティカル、エラー、警告の各状態を把握することができます。

共通エントリには、API キー ("*管理者キーの取得*" や "*クエリ キーの取得*" など、情報提供を目的とした一般的な通知) への参照が含まれています。 これらのアクティビティは、クエリ キーまたは、管理者キーを使用して作成された要求 (オブジェクトの作成または削除) を示しますが、要求自体は表示されません。 この粒度の情報については、診断ログを構成する必要があります。

**アクティビティ ログ** には、左側のナビゲーション ウィンドウ、上部ウィンドウのコマンド バーの通知、または **[問題の診断と解決]** ページからアクセスすることができます。

### <a name="monitor-storage-in-the-usage-tab"></a>[Usage]\(使用状況\) タブでストレージを監視する

ポータルで視覚的に監視できるように、 **[Usage]\(使用状況\)** タブには、リソースの空き状況が、サービス レベルによる現在の [制限](search-limits-quotas-capacity.md)と比較して表示されます。 [運用環境のワークロードに使用するレベル](search-sku-tier.md)について、または[アクティブなレプリカとパーティションの数を調整する](search-capacity-planning.md)かどうかについて最終的に判断する場合、これらのメトリックを見ると、リソースが消費される速さや、現在の構成で既存の負荷がどの程度うまく処理されているかがわかるので、これらの決定を下すのに役立ちます。

次の図は、オブジェクトが種類ごとに 3 つ、ストレージが 50 MB に制限されている Free サービスを示したものです。 Basic または Standard サービスでは上限がこれより高く、パーティションの数を増やすと最大ストレージがそれに比例して増加します。

![レベルの制限を基準とした使用状況](./media/search-monitor-usage/usage-tab.png
 "レベルの制限を基準とした使用状況")

> [!NOTE]
> ストレージに関連したアラートは現在利用できません。ストレージ消費量は集計されず、Azure Monitor の **AzureMetrics** テーブルにも記録されません。 ストレージのアラートを取得するには、リソースに関連した通知を生成する[カスタム ソリューションを作成](../azure-monitor/insights/solutions.md)する必要があります。そこでは、独自のコードでストレージ サイズを調べて応答を処理します。

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Azure Monitor を使用したアドオンの監視

Azure Cognitive Search を含む多くのサービスは、追加のアラート、メトリック、および診断データのログ記録のために、[Azure Monitor](../azure-monitor/index.yml) と統合されます。 

データの収集と保存を制御する場合は、検索サービスに[診断ログを有効](search-monitor-logs.md)にします。 Azure Monitor によってキャプチャされたログに記録されたイベントは、**AzureDiagnostics** テーブルに格納され、クエリとインデックス作成に関連する操作データで構成されます。

Azure Monitor にはいくつかのストレージ オプションが用意されており、どれを選択するかによってデータの使用方法が決まります。

* Power BI レポートで[ログ データを視覚化](search-monitor-logs-powerbi.md)する場合は、[Azure Blob Storage] を選択します。
* Kusto クエリを使用してデータを探索する場合は、[Log Analytics] を選択します。

Azure Monitor には独自の課金体系があり、このセクションで参照される診断ログには関連コストがあります。 詳細については、[Azure Monitor での使用量と推定コスト](../azure-monitor//usage-estimated-costs.md)に関する記事を参照してください。

## <a name="monitor-user-access"></a>ユーザー アクセスの監視

検索インデックスは、より大きなクライアント アプリケーションのコンポーネントであるため、インデックスへのアクセスをユーザーごとに制御または監視するために組み込まれた手法はありません。 要求は、管理またはクエリ要求を目的として、クライアント アプリケーションから送信されたものと見なされます。 管理目的の読み取り/書き込み操作には、サービス全体を対象としたオブジェクトの作成、更新、削除が含まれます。 読み取り専用の操作としては、単一のインデックスに範囲が限定されたドキュメント コレクションに対するクエリがあります。 

その意味で、アクティビティ ログに表示されるのは、管理者キーまたはクエリ キーを使用した呼び出しの参照といえます。 クライアント コードから送信される要求には、適切なキーが含まれています。 このサービスには、ID トークンや偽装を処理するしくみが備わっていません。

ビジネス要件がユーザーごとの承認のために存在する場合は、Azure Active Directory との統合をお勧めします。 $filter とユーザー ID を使用すると、ユーザーに表示すべきでない、ドキュメントの[検索結果をトリミングする](search-security-trimming-for-azure-search-with-aad.md)ことができます。 

$filter パラメーターを含んだクエリ文字列とは別に、この情報をログに記録することはできません。 クエリ文字列のレポートの詳細については、[クエリの監視](search-monitor-queries.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Azure Cognitive Search などのリソースを含む Azure サービスを監視するうえで、Azure Monitor を使いこなすことが欠かせません。 Azure Monitor を十分に理解していない場合は、時間を取ってリソースに関連した記事を確認してください。 チュートリアルのほか、次の記事からお読みいただくことをお勧めします。

> [!div class="nextstepaction"]
> [Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)