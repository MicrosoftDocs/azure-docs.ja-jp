---
title: センサー コンソール ダッシュボードを操作する
description: ダッシュボードを使用すると、ネットワークのセキュリティの状態をすばやく表示できます。 タイムライン上のシステム全体に対する脅威の概要と、関連デバイスに関する情報が提供されます。
ms.date: 11/03/2020
ms.topic: article
ms.openlocfilehash: e9bc650e9c306d12ef63994852816c96e1bdf40a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781536"
---
# <a name="the-dashboard"></a>ダッシュボード

ダッシュボードを使用すると、ネットワークのセキュリティの状態をすばやく表示できます。 タイムライン上のシステム全体に対する脅威の概要と、関連デバイスに関する次のような情報が提供されます。

- さまざまな重要度レベルのアラート:

- 重要

- メジャー

- Minor

- 警告

- ページの中央にある 2 つのインジケーターは、1 秒あたりのパケット数 (PPS) と未確認アラート数 (UA) を示しています。 **PPS** は、システムによって 1 秒あたりに確認されたパケットの数です。 **UA** は、まだ確認されていないアラートの数です。

- 未確認アラートの一覧とその説明。

- アラートの説明を含むタイムライン。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="ダッシュボード":::

## <a name="viewing-the-latest-alerts"></a>最新のアラートの表示

ページの中央にある未確認アラート数 (UA) ゲージは、このようなアラートの数を示しています。 アラートの一覧を表示するには、ダッシュボード ページの下部にある **[More Alerts]\(その他のアラート\)** を選択するか、サイド メニューの **[アラート]** を選択します。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="未確認アラート":::

## <a name="status-boxes"></a>状態ボックス

このセクションでは各状態ボックスについて説明します。

| 状態ボックスとゲージ | [説明] |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="重大なアラート"::: | **重大なアラート** - ページの上部中央にあるボックスに、重大なアラートの数が示されます。 このボックスを選択すると、タイムライン上とゲージの下の一覧にアラートの説明が表示されます (存在する場合)。                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="メジャー アラート"::: | **メジャー アラート** - ページの右上にあるボックスに、メジャー アラートの数が表示されます。 このボックスを選択すると、タイムライン上とゲージの下の一覧にアラートの説明が表示されます (存在する場合)。                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="マイナー アラート"::: | **マイナー アラート** - ページの左下にあるボックスに、マイナー アラートの数が表示されます。 このボックスを選択すると、タイムライン上とゲージの下の一覧にアラートの説明が表示されます (存在する場合)。                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="警告アラート"::: | **警告アラート** - ページの下部中央にあるボックスに、警告アラートの数が表示されます。 このボックスを選択すると、タイムライン上とゲージの下の一覧にアラートの説明が表示されます (存在する場合)。                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="すべてのアラート"::: | **すべてのアラート** - ページの右下にあるボックスに、重大、メジャー、マイナー、警告のアラートの合計数が示されます。 このボックスを選択すると、タイムライン上とゲージの下の一覧にアラートの説明が表示されます (存在する場合)。 |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="1 秒あたりのパケット数"::: | **1秒あたりのパケット数 (PPS)** - PPS メトリックは、ネットワークのパフォーマンスの指標です。 |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="未確認イベント (UA)"::: | **未確認のイベント** - このメトリックは、未確認のイベントの数を示します。

## <a name="using-the-timeline"></a>タイムラインの使用

アラートは、日付と時刻の情報を含む垂直タイムラインに沿って表示されます。

タイムラインにグラフィカルに表示されるもの:

- 重大なアラート

- メジャー アラート

- マイナー アラート

- 警告アラート

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="タイムライン グラフ":::

## <a name="viewing-alerts"></a>アラートの表示

アラート ボックスの下部にある下矢印 **"V"** を選択して、アラートのエントリとデバイスの情報を表示します。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="アラートのエントリとデバイスの情報":::

- デバイスを選択して、物理モード マップを表示します。 対象デバイスが強調表示されます。

- アラート ボックス内の任意の場所をクリックすると、そのアラートに関する詳細情報が表示されます。 ポップアップに以下のように表示されます。

- アラートに関する CSV ファイルをエクスポートするには、:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="[Excel]"::: を選択します。

- 管理者およびセキュリティ アナリストのみ - 関連付けられているアラートを **すべて確認** するには :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="[Acknowledge all]\(すべて確認\)"::: を選択します。

- アラート レポートを PDF ファイルとしてダウンロードするには :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="[PDF]"::: を選択します。

- アラートをピン留めするかピン留めを外すには、:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="[ピン留め]"::: を選択します。 ピン留めを選択すると、 **[アラート]** 画面の **[Pinned Alerts]\(ピン留めされたアラート\)** に追加されます。

- ネットワーク プロトコル分析を含む、関連する PCAP ファイルをダウンロードしてアラートを調査するには :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="[ダウンロード]"::: を選択します。

- :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="[クラウド]"::: を選択すると、アラート関連パケットだけを含むフィルター済みの関連する PCAP ファイルがダウンロードされます。これにより、出力ファイルのサイズが減少し、より焦点を絞った分析が可能になります。 [Wireshark](https://www.wireshark.org/)を使用してこれを表示できます。

- 要求されたアラートの時刻のイベント タイムラインに移動するには :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="[ナビゲーション]"::: を選択します。 これにより、特定のアラートの付近に発生している可能性のあるその他のイベントを評価できます。

- 管理者およびセキュリティ アナリストのみ - アラートの状態を未確認から確認済みに変更します。 [詳細] を選択して検出されたアクティビティを承認します。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="不正なインターネット接続が検出されました":::

## <a name="see-also"></a>関連項目

[センサー上でのアラートの操作](how-to-work-with-alerts-on-your-sensor.md)
