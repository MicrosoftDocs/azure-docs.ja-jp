---
title: Azure Time Series Insights プレビューでの診断とトラブルシューティング | Microsoft Docs
description: Azure Time Series Insights プレビューでの診断とトラブルシューティングの方法を説明します。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: eb1c302bf1954492ba2a7a78d16fc697fdf4b687
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58080439"
---
# <a name="diagnose-and-troubleshoot"></a>診断とトラブルシューティング

この記事では、Azure Time Series Insights プレビュー環境を使用しているときに発生する可能性があるいくつかの一般的な問題をまとめます。 また、各問題の考えられる原因と解決策についても説明します。

## <a name="problem-i-cant-find-my-environment-in-the-time-series-insights-preview-explorer"></a>問題:Time Series Insights プレビュー エクスプローラーで自分の環境が見つからない

この問題は、Time Series Insights 環境にアクセスするための権限がない場合に発生する可能性があります。 Time Series Insights 環境を表示するには、ユーザーに閲覧者レベルのアクセス ロールが必要です。 現在のアクセス レベルを確認し、追加のアクセス権を付与するには、[Azure portal](https://portal.azure.com/) で Time Series Insights リソースの [データ アクセス ポリシー] セクションを使用します。

  ![環境][1]

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>問題:Time Series Insights プレビュー エクスプローラーにデータが表示されない

[Azure Time Series Insights プレビュー エクスプローラー](https://insights.timeseries.azure.com/preview)でデータが表示されない場合、一般的な理由がいくつかあります。

- イベント ソースでデータが受信されていない可能性があります。

    イベント ハブまたは IoT ハブであるイベント ソースで、タグまたはインスタンスからのデータが受信されていることを確認します。 確認するには、Azure portal でリソースの概要ページに移動します。

    ![ダッシュボード - 分析情報][2]

- イベント ソースのデータが JSON 形式ではありません。

    Time Series Insights では、JSON データのみがサポートされています。 JSON のサンプルについては、「[サポートされている JSON 構造](./how-to-shape-query-json.md)」を参照してください。

- イベント ソース キーに必要なアクセス許可がありません。

  * IoT Hub の場合は、**サービス接続**アクセス許可を持つキーを指定する必要があります。

    ![構成][3]

  * 上の図に示すように、ポリシー **iothubowner** と **service** は両方とも**サービス接続**アクセス許可が設定されているため、どちらも動作します。
  * イベント ハブの場合は、**リッスン** アクセス許可を持つキーを指定する必要があります。
  
    ![アクセス許可][4]

  * 上の図に示すように、**read** ポリシーと **manage** ポリシーは両方とも**リッスン** アクセス許可が設定されているため、どちらも動作します。

- お使いのコンシューマー グループが Time Series Insights 専用ではありません。

    IoT ハブまたはイベント ハブの登録時に、データを読み取るために使用するコンシューマー グループを指定します。 そのコンシューマー グループを共有しないでください。 コンシューマー グループを共有すると、基盤となるイベント ハブによってリーダーのいずれかがランダムに自動的に切断されます。 Time Series Insights が読み取る一意のコンシューマー グループを指定します。

- プロビジョニング時に指定したタイム シリーズ ID プロパティが、正しくないか、不足しているか、または null です。

    この問題は、環境のプロビジョニング時に、タイム シリーズ ID プロパティを誤って構成した場合に発生する可能性があります。 詳しくは、「[Best practices for choosing a Time Series ID](./time-series-insights-update-how-to-id.md)」(タイム シリーズ ID の選択のベスト プラクティス) をご覧ください。 現時点では、別のタイム シリーズ ID を使うように既存の Time Series Insights 環境を更新することはできません。

## <a name="problem-some-data-shows-but-some-is-missing"></a>問題:表示されるデータと表示されないデータがある

タイム シリーズ ID を持たないデータを送信している場合があります。

- この問題は、ペイロードにタイム シリーズ ID フィールドのないイベントを送信すると発生する可能性があります。 詳しくは、[サポートされている JSON の形式](./how-to-shape-query-json.md)に関する記事をご覧ください。

- この問題は、お使いの環境が調整されているために発生する可能性があります。

    > [!NOTE]
    > 現時点では、Time Series Insights でサポートされる最大インジェスト速度は 6 Mbps です。

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>問題:イベント ソースのタイムスタンプ プロパティ名の設定が機能しない

名前と値が次の規則に準拠していることを確認してください。

* タイムスタンプ プロパティ名では大文字と小文字が区別されます。
* イベント ソースから JSON 文字列として取得されるタイムスタンプ プロパティの値は、`yyyy-MM-ddTHH:mm:ss.FFFFFFFK` という形式です。 たとえば、`“2008-04-12T12:53Z”` のような文字列です。

タイムスタンプ プロパティ名がキャプチャされて正しく動作していることを確認する最も簡単な方法は、Azure Time Series Insights プレビュー エクスプローラーを使用することです。 Time Series Insights プレビュー エクスプローラーでグラフを使用して、タイムスタンプ プロパティの名前を入力した後の期間を選択します。 選択内容を右クリックして、**[イベントの探索]** オプションを選択します。 最初の列の見出しが、タイムスタンプ プロパティの名前です。 `Timestamp` という単語の隣に、以下のような値ではなく、`($ts)` と表示されている必要があります。

* `(abc)`: Time Series Insights が文字列としてデータ値を読み取っていることを示します。
* カレンダー アイコン: Time Series Insights が日時としてデータ値を読み取っていることを示します。
* `#`: Time Series Insights が整数としてデータ値を読み取っていることを示します。

タイムスタンプ プロパティが明示的に指定されていない場合は、イベントが IoT ハブまたはイベント ハブにエンキューされた時刻が、既定のタイムスタンプとして使用されます。

## <a name="problem-i-cant-edit-or-view-my-time-series-model"></a>問題:自分のタイム シリーズ モデルを表示または編集できない

- Time Series Insights S1 または S2 環境にアクセスしている可能性があります。

   タイム シリーズ モデルは、PAYG 環境でのみサポートされます。 Time Series Insights プレビュー エクスプローラーから S1/S2 環境にアクセスする方法について詳しくは、「[Visualize data in the explorer](./time-series-insights-update-explorer.md)」(エクスプローラーでデータを視覚化する) をご覧ください。

   ![Access][5]

- モデルを表示および編集するためのアクセス許可がない可能性があります。

   タイム シリーズ モデルを編集および表示するには、ユーザーに共同作成者レベルのアクセス権が必要です。 現在のアクセス レベルを確認し、追加のアクセス権を付与するには、Azure portal で Time Series Insights リソースの [データ アクセス ポリシー] セクションを使用します。

## <a name="problem-all-my-instances-in-the-time-series-insights-preview-explorer-dont-have-a-parent"></a>問題:Time Series Insights プレビュー エクスプローラーのすべてのインスタンスに親がない

この問題は、環境でタイム シリーズ モデルの階層が定義されていない場合に発生する可能性があります。 詳しくは、「[Work with Time Series Models](./time-series-insights-update-how-to-tsm.md)」(タイム シリーズ モデルを使用する) をご覧ください。

  ![タイム シリーズ モデル][6]

## <a name="next-steps"></a>次の手順

- 「[Work with Time Series Models](./time-series-insights-update-how-to-tsm.md)」(タイム シリーズ モデルを使用する) を読む。
- [サポートされている JSON の形式](./how-to-shape-query-json.md)に関する記事を読む。

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/environment.png
[2]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[3]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[4]: media/v2-update-diagnose-and-troubleshoot/permissions.png
[5]: media/v2-update-diagnose-and-troubleshoot/access.png
[6]: media/v2-update-diagnose-and-troubleshoot/tsm.png