---
title: Metrics Advisor に関してよく寄せられる質問
titleSuffix: Azure Cognitive Services
description: Metrics Advisor サービスに関してよく寄せられる質問です。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0c4c296cb1454ed89eef102732533589b1c8ca0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420961"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Metrics Advisor に関してよく寄せられる質問

### <a name="what-is-the-cost-of-my-instance"></a>インスタンスのコストはどのくらいですか?

現在プレビュー期間中であり、インスタンスの使用にコストはかかりません。

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>リソースを作成できないのはなぜですか? "価格レベル" を表示することができず、"このサブスクリプションに対して 1 件の S0 を既に作成していますか?" と表示されます。

:::image type="content" source="media/pricing.png" alt-text="F0 リソースが既に存在する場合のメッセージ":::

パブリック プレビューの間、作成できる Metrics Advisor のインスタンスの数は、1 つのサブスクリプションで 1 つのリージョンにつき 1 つだけです。

同じリージョンで、同じサブスクリプションを使用して、既にインスタンスが作成されている場合は、別のリージョンまたは別のサブスクリプションで、新しいインスタンスの作成を試みることができます。 また、既存のインスタンスを削除して、新しいインスタンスを作成することもできます。

既存のインスタンスを既に削除してあるのにエラーが表示される場合は、リソースを削除して約 20 分待ってから、新しいインスタンスを作成してください。

## <a name="basic-concepts"></a>基本的な概念

### <a name="what-is-multi-dimensional-time-series-data"></a>多次元時系列データとは何ですか?

用語集で、[多次元メトリック](glossary.md#multi-dimensional-metric)の定義を参照してください。

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Metrics Advisor で異常検出を開始するために必要なデータ量はどのくらいですか?

データ ポイントが 1 つ以上あれば、異常検出をトリガーできます。 ただし、これによって最適な精度が得られるわけではありません。 このサービスでは、データ フィードの作成時に "fill-gap" ルールとして指定した値を使用する、以前のデータ ポイントのウィンドウがあるものと想定されています。

検出対象のタイムスタンプの前にデータがあるようにすることをお勧めします。
データの細分性に基づく、推奨されるデータ量は次のようにさまざまです。

| 粒度 | 検出に推奨されるデータ量 |
| ----------- | ------------------------------------- |
| 5 分未満 | 4 日分のデータ |
| 5 分から 1 日 | 28 日分のデータ |
| 1 日分より多く、31 日分まで | 4 年分のデータ |
| 31 日分より多い | 48 年分のデータ |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Metrics Advisor で履歴データからの異常検出が行われないのはなぜですか?

Metrics Advisor は、ライブ ストリーミング データの検出を目的に設計されています。 このサービスによって検索および異常検出が実行される履歴データの最大長には、制限があります。 これは、特定の最も古いタイムスタンプより後のデータ ポイントに対してのみ、異常検出の結果が示されることを意味します。 最も古いタイムスタンプは、データの細分性によって異なります。

データの細分性に基づく、異常検出の結果が含まれる履歴データの長さは次のとおりです。

| 粒度 | 異常検出の対象となる履歴データの最大長 |
| ----------- | ------------------------------------- |
| 5 分未満 | オンボード時間 - 13 時間 |
| 5 分から 1 時間未満 | オンボード時間 - 4 日間  |
| 1 時間から 1 日未満 | オンボード時間 - 14 日間  |
| 1 日 | オンボード時間 - 28 日間  |
| 1 日より長く、31 日間未満 | オンボード時間 - 2 年間  |
| 31 日分より多い | オンボード時間 - 24 年間   |

### <a name="more-concepts-and-technical-terms"></a>その他の概念と技術用語

詳細については、[用語集](glossary.md)のページも参照してください。

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>データを取り込むための有効なクエリを作成する方法  

Metrics Advisor でデータを取り込むには、1 つのタイムスタンプでデータのディメンションを返すクエリを作成する必要があります。 Metrics Advisor によってこのクエリが複数回実行されて、各タイムスタンプからデータが取得されます。 

クエリからは、指定したタイムスタンプで、ディメンションの各組み合わせに対して多くても 1 つのレコードが返される必要があることに注意してください。 返されるすべてのレコードで、タイムスタンプが同じになっている必要があります。 クエリによって重複するレコードが返されてはなりません。

たとえば、日単位のメトリックに対して次のクエリを作成したとします。 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

時系列に対して正しい粒度を使用してください。 時間単位のメトリックの場合は、以下を使用します。 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

これらのクエリからは 1 つのタイムスタンプのデータだけが返され、Metrics Advisor によって取り込まれるすべてのディメンションの組み合わせが含まれることに注意してください。 

:::image type="content" source="media/query-result.png" alt-text="1 つのタイムスタンプによるクエリの結果" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>急増と急減を異常として検出するには、どうすればよいですか?

ハードしきい値が事前に定義されている場合は、[[Anomaly Detection Configurations]\(異常検出の構成\)](how-tos/configure-metrics.md#anomaly-detection-methods) で "ハードしきい値" を手動で設定できます。
しきい値がない場合は、AI を利用した "スマート検出" を使用できます。 詳細については、[検出構成の調整](how-tos/configure-metrics.md#tune-the-detecting-configuration)に関する記事を参照してください。

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>通常 (季節) のパターンへの非準拠を異常として検出するには、どうすればよいですか?

"スマート検出" には、季節のパターンなど、データのパターンを学習する機能があります。 学習すると、通常のパターンに準拠していない、そのようなデータ ポイントを、異常として検出します。 詳細については、[検出構成の調整](how-tos/configure-metrics.md#tune-the-detecting-configuration)に関する記事を参照してください。

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>直線を異常として検出するには、どうすればよいですか?

データが通常は非常に不安定で変動が大きく、非常に安定したり直線になってしまったりするときには警告を表示したい場合、変化が非常にわずかであれば、そのようなデータ ポイントが検出されるように "しきい値の変更" を構成することができます。
詳細については、[異常検出の構成](how-tos/configure-metrics.md#anomaly-detection-methods)に関する記事を参照してください。

### <a name="how-to-set-up-email-settings-and-enable-alerting-by-email"></a>電子メールの設定を行って、電子メールによるアラートを有効にする方法

1.  サブスクリプション管理者またはリソース グループ管理者の権限を持つユーザーが、Azure portal に作成された Metrics Advisor リソースに移動し、 **[アクセス制御 (IAM)]** タブを選択する必要があります。 
2.  **[ロールの割り当ての追加]** を選択します
3.  **Cognitive Services Metrics Advisor 管理者** のロールを選択し、下の画像のように自分のアカウントを選択します。
4.  **[保存]** ボタンをクリックすると、Metrics Advisor リソースの管理者として正常に追加されます。 上記のアクションはすべて、サブスクリプション管理者またはリソース グループ管理者によって実行される必要があるので注意してください。 

:::image type="content" source="media/access-control.png" alt-text="アクセス制御 (IAM) のメニュー ページ。[ロールの割り当ての追加] が選択されており、[アクセスの割り当て先] がユーザーに選択され Cognitive Services Metrics Advisor 管理者のアクセス ロールが表示されているボックスがあり、UI の [保存] ボタンが選択されていて、ユーザーを検索して特定のレベルのアクセス許可を追加する手順を示している。" lightbox="media/access-control.png":::


5.  アクセス許可が反映されるまでに最大で 1 分かかる場合があります。 次に、自分の Metrics Advisor ワークスペースを選択し、左側のナビゲーション パネルで **[電子メール設定]** オプションを選択します。 必須の項目 (特に SMTP 関連の情報) を入力します。 
6.  **[保存]** を選択すると、電子メールの構成がすべて設定されます。 新しいフックを作成してメトリックの異常をサブスクライブし、準リアルタイムのアラートを受け取れます。 

## <a name="advanced-concepts"></a>高度な概念

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>多次元メトリックのインシデント ツリーは、Azure Advisor によってどのように構築されますか?

メトリックは、ディメンションによって複数の時系列に分割できます。 たとえば、メトリック `Response latency` は、チームが所有するすべてのサービスに対して監視されます。 `Service` カテゴリをディメンションとして使用してメトリックを強化し、`Service1`、`Service2` などによって `Response latency` を分割することができます。 各サービスは複数のデータ センター内の異なるコンピューターに展開できるので、メトリックは `Machine` や `Data center` によってさらに分割される可能性があります。

|サービス| データ センター| Machine  | 
|----|------|----------------   |
| S1 |  DC1 |   M1 |
| S1 |  DC1 |   M2 |
| S1 |  DC2 |   M3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   M1 |
| S2 |  DC1 |   M2 |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

合計の `Response latency` から始めて、`Service`、`Data center`、`Machine` でメトリックにドリルダウンできます。 ただし、サービス所有者にとってはパス `Service` -> `Data center` -> `Machine` を使用する方が意味がある場合、またインフラストラクチャ エンジニアにとってはパス `Data Center` -> `Machine` -> `Service` を使用する方が意味がある場合もあります。 これはすべて、ユーザーの個々のビジネス要件に依存します。 

Metric Advisor を使用すると、階層トポロジの 1 つのノードからドリルダウンまたはロールアップする任意のパスをユーザーが指定できます。 より正確には、階層のトポロジは、ツリー構造ではなく有向非巡回グラフです。 次のように、可能性のあるすべてのディメンションの組み合わせで構成される完全な階層トポロジがあります。 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="相互に接続された複数の頂点とエッジと、S、DC、M および対応する 1 から 6 の範囲の値でラベルが付けられた複数のディメンションで構成される、階層トポロジの図" lightbox="media/dimension-combinations-view.png":::

理論的には、ディメンション `Service` に `Ls` 個の個別値があり、ディメンション `Data center` に `Ldc` 個の個別値があり、ディメンション `Machine` に `Lm` 個の個別値がある場合、階層トポロジには `(Ls + 1) * (Ldc + 1) * (Lm + 1)` 個のディメンションの組み合わせが存在する可能性があります。 

ただし、通常は、すべてのディメンションの組み合わせが有効であることはないので、複雑さを大幅に軽減できます。 現在、ユーザー自身がメトリックを集計している場合、ディメンションの数は制限されません。 Metrics Advisor によって提供されるロールアップ機能を使用する必要がある場合、ディメンションの数を 6 より大きくすることはできません。 ただし、メトリックのディメンションによって展開される時系列の数は、10,000 未満に制限されています。

診断ページの **インシデント ツリー** ツールには、トポロジ全体ではなく、異常が検出されたノードのみが表示されます。 これは、現在の問題に注目できるようにするためです。 また、メトリック内の異常がすべて表示されるとは限らず、代わりに寄与度に基づいて上位の異常が表示されます。 このようにして、異常なデータの影響、スコープ、分散パスをすばやく確認できます。 これにより、注目する必要がある異常の数が大幅に減り、ユーザーが重要な問題を把握して見つけるのに役立ちます。 
 
たとえば、`Service = S2 | Data Center = DC2 | Machine = M5` で異常が発生した場合、異常の偏差はやはり異常が検出された親ノード `Service= S2` に影響を与えますが、異常は `DC2` のデータ センター全体と `M5` のすべてのサービスには影響しません。 インシデント ツリーは次のスクリーンショットのように構築されます。最上位の異常は `Service = S2` でキャプチャされ、根本原因はどちらも `Service = S2 | Data Center = DC2 | Machine = M5` に至る 2 つのパスで分析できます。

 :::image type="content" source="media/root-cause-paths.png" alt-text="5 つのラベル付き頂点と、エッジによって結合された 2 つの異なるパスと、S2 というラベルの付いた共通ノード。上位の異常は Service = S2 でキャプチャされ、根本原因はどちらも Service = S2 | Data Center = DC2 | Machine = M5 に至る 2 つのパスによって分析できます" lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>次の手順
- [Metrics Advisor の概要](overview.md)
- [Web ポータルを使用する](quickstarts/web-portal.md)