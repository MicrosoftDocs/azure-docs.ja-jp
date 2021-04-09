---
title: Metrics Advisor サービスに異常なフィードバックを提供する
titleSuffix: Azure Cognitive Services
description: Metrics Advisor インスタンスによって検出された異常に関するフィードバックを送信し、結果を調整する方法について説明します。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96184971"
---
# <a name="provide-anomaly-feedback"></a>異常に関するフィードバックを提供する

ユーザー フィードバックは、異常検出システム内の欠陥を検出するための最も重要な方法の 1 つです。 ここでは、ユーザーが誤った検出結果を時系列に直接マークし、すぐにフィードバックを適用する方法について説明します。 これにより、ユーザーは異常検出システムに対し、アクティブなやり取りを通じて、特定の時系列についての異常検出を行う方法を指示することができます。 

> [!NOTE]
> 現時点では、フィードバックが反映されるのは **スマート検出** による異常検出結果だけであり、**ハードしきい値** および **しきい値の変更** には反映されません。

## <a name="how-to-give-time-series-feedback"></a>時系列のフィードバックを提供する方法

任意の系列のメトリック詳細ページからフィードバックを提供できます。 任意のポイントを選択すると、以下のフィードバック ダイアログが表示されます。 選択した系列のディメンションが表示されます。 ディメンションの値を選択し直したり、一部を削除して時系列データのバッチを取得したりすることもできます。 時系列を選択した後、 **[追加]** ボタンを選択してフィードバックを追加します。4 種類のフィードバックを提供できます。 複数のフィードバック項目を追加するには、注釈を入力した後、 **[保存]** ボタンをクリックします。

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="青い線の時系列データとさまざまなポイントの赤い点が示されているグラフ。1 つの点は赤いボックスで囲まれ、次のように書かれています: 任意のポイントを選択してください":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="2 つのディメンションと、ディメンションの選択または削除、およびフィードバックの追加のためのオプションが含まれる [フィードバックの追加] ダイアログ ボックス。":::

### <a name="mark-the-anomaly-point-type"></a>異常なポイントの種類をマークする

次の画像で示すように、フィードバック ダイアログには、選択したポイントのタイムスタンプが自動的に入力されますが、この値は編集できます。 次に、この項目を `Anomaly`、`NotAnomaly`、`AutoDetect` のいずれとして識別するかを選択します。

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="Anomaly、NotAnomaly、AutoDetect の選択肢が含まれるドロップダウン メニュー":::

選択すると、同じ系列の今後の異常検出処理にフィードバックが適用されます。 処理されたポイントは再計算されません。 つまり、異常を NotAnomaly としてマークした場合、それ以降同様の異常は表示されなくなります。また、`NotAnomaly` のポイントを `Anomaly` としてマークした場合は、それ以降も同様のポイントを `Anomaly` として検出します。 `AutoDetect` を選択した場合は、同じポイントでの以前のフィードバックはすべて無視されます。

## <a name="provide-feedback-for-multiple-continuous-points"></a>複数の連続したポイントに関するフィードバックを提供する 

複数の連続するポイントに対して異常のフィードバックを同時に行う場合は、注釈を付けるポイントのグループを選択します。 異常に関するフィードバックを提供すると、選択した時間範囲が自動的に入力されます。

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="異常が選択され、特定の時間範囲が設定されている異常フィードバック メニュー":::

個々のポイントに異常フィードバックが反映されているかどうかを確認するには、時系列を表示して 1 つのポイントを選択します。 その異常検出結果がフィードバックによって変更されている場合、ヒントに **[Affected by feedback: true]\(フィードバックによる影響: true\)** と表示されます。 **[Affected by feedback: false]\(フィードバックによる影響: false\)** と表示される場合は、このポイントに対して異常フィードバックの計算が実行されましたが、異常検出の結果は変更されていないことを意味します。

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="ヒントの表示: [Affected by feedback: true]\(フィードバックによる影響: true\)、赤で強調表示":::

フィードバックを提供することが推奨されない状況がいくつかあります。

- 休日が原因で異常が発生している。 この種の誤ったアラームを解決するには、より正確であるため、事前設定イベントを使用することをお勧めします。
- 既知のデータ ソースの変更によって異常が発生している。 たとえば、上流のシステムの変更がその時点で発生しました。 このような状況では、値の変更の原因が何であったか、および同様の値の変更がいつ再び発生するかはシステムでわからないため、異常アラートが発生することが予想されます。 したがって、この種の問題には `NotAnomaly` の注釈を付けないことをお勧めします。

## <a name="change-points"></a>変化点

データの傾向の変化が異常検出の結果に影響することがあります。 ポイントが異常であるかどうかに関する判断が行われるとき、履歴データの最新の期間が考慮されます。 時系列に傾向の変化がある場合、正確な変化点をマークできます。これは、将来の分析での異常検出に役立ちます。

次の図に示すように、フィードバックの種類として `ChangePoint` を選択し、プルダウン リストから `ChangePoint`、`NotChangePoint`、または `AutoDetect` を選択できます。

:::image type="content" source="../media/feedback/changepoint.png" alt-text="変化点メニューの、ChangePoint、NotChangePoint、AutoDetect のオプションが含まれるドロップダウン":::

> [!NOTE]
> データの変化が続いている場合、`ChangePoint` としてマークする必要があるポイントは 1 つだけなので、`timerange` をマークした場合、最後のポイントのタイムスタンプと時刻が自動的に設定されます。 この場合、注釈は 12 ポイント後の異常検出結果にのみ影響します。

## <a name="seasonality"></a>季節性

季節性のデータの場合、異常検出を実行するときに、1 つのステップで時系列の期間 (季節性) が推定され、それが異常検出フェーズに適用されます。 場合によっては、正確な期間を特定することが困難であり、期間が変わることもあります。 期間が正しく定義されていないと、異常検出の結果に副作用が生じる可能性があります。 現在の期間は、ヒントに含まれる `Min Period` という名前から見つけることができます。

:::image type="content" source="../media/feedback/min-period.png" alt-text="Period という単語と 7 という数字が赤で強調表示されたヒントのオーバーレイ。":::

期間に関するフィードバックを提供して、この種の異常検出エラーを修正できます。 図で示されているように、期間の値を設定できます。 単位の `interval` は、1 つの粒度を意味します。 ここで、間隔 0 はデータが季節性でないことを意味します。 また、以前のフィードバックを取り消し、パイプラインによって期間が自動的に検出されるようにする場合は、`AutoDetect` を選択します。 
 
> [!NOTE]
> 期間を設定するときに、タイムスタンプまたは時間範囲を割り当てる必要はありません。期間は、フィードバックを提供した時点以降の時系列全体に対する異常検出に反映されます。


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="自動検出期間が 28 に設定され、間隔が季節性でないことを示す 0 に設定されたメニュー。":::

## <a name="provide-comment-feedback"></a>コメントのフィードバックを提供する

注釈のコメントを追加して、データにコンテキストを提供することもできます。 コメントを追加するには、時間範囲を選択して、コメントのテキストを追加します。

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="時間範囲を設定できるメニューと、テキスト ベースのコメントを追加するためのボックス":::

## <a name="time-series-batch-feedback"></a>時系列のバッチ フィードバック

前に説明したように、フィードバック モーダルを使用すると、ディメンションの値を再選択または削除して、ディメンション フィルターによって定義される時系列のバッチを取得できます。 左側のパネルからフィードバックの [+] ボタンをクリックしてこのモーダルを開き、ディメンションとディメンションの値を選択することもできます。

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="[フィードバック] の横の青いプラス記号が赤で強調表示されたメニュー":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="Dim1 と Dim2 によって示される 2 つのディメンションが含まれるフィードバック追加メニュー":::

## <a name="how-to-view-feedback-history"></a>フィードバックの履歴を表示する方法

フィードバックの履歴を表示するには 2 つの方法があります。 左側のパネルからフィードバック履歴ボタンを選択すると、フィードバック一覧のモーダルが表示されます。 そこには、単一の系列またはディメンション フィルターについて前に指定したすべてのフィードバックの一覧が表示されます。

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="フィードバック一覧メニュー":::

フィードバック履歴を表示するもう 1 つの方法は、系列からです。 各系列の右上隅には複数のボタンが表示されています。 フィードバック表示ボタンを選択すると、線が異常ポイントの表示からフィードバック エントリの表示に切り替わります。 緑色のフラグは変更点を表し、青い点は他のフィードバック ポイントです。 それらを選択して、このポイントに対して提供されたフィードバックの詳細の一覧を示すフィードバック リスト モーダルを表示することもできます。

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="フィードバック履歴グラフ":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="2 つのディメンションを含むフィードバック一覧メニュー":::

> [!NOTE]
> メトリックにアクセスできるすべてのユーザーがフィードバックを提供できるので、他のデータ フィード所有者によって提供されたフィードバックが表示される場合があります。 他のユーザーと同じポイントを編集すると、そのフィードバックによって前のフィードバック エントリが上書きされます。       

## <a name="next-steps"></a>次のステップ
- [インシデントを診断する](diagnose-incident.md)
- [メトリックを構成して検出構成を微調整する](configure-metrics.md)
- [フックを使用してアラートを構成し、通知を取得する](../how-tos/alerts.md)