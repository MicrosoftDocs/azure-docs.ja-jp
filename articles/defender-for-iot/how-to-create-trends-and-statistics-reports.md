---
title: 傾向と統計のレポートを生成する
description: Defender for IoT の Trends and Statistics (傾向と統計) ウィジェットを使用し、ネットワークのアクティビティ、統計、傾向に関する分析情報を取得します。
ms.date: 2/21/2021
ms.topic: how-to
ms.openlocfilehash: b4539e20ff485f1b6be69fee8e6849298540adcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779003"
---
# <a name="sensor-trends-and-statistics-reports"></a>センサーの傾向と統計のレポート

ウィジェットのグラフと円グラフを作成して、ネットワークの傾向と統計に関する分析情報を得ることができます。 ウィジェットは、ユーザー定義ダッシュボードの下にグループ化できます。

> [!NOTE]
> 管理者とセキュリティ アナリストは傾向と統制のレポートを作成できます。

ダッシュボードは、次の種類の情報をグラフィカルに説明するウィジェットで構成されています。

- ポート別のトラフィック
- ポート別の上位トラフィック
- チャネル帯域幅
- 合計帯域幅
- アクティブ TCP 接続
- VLAN 別の上位の帯域幅
- デバイス:
  - 新しいデバイス
  - ビジー状態のデバイス
  - ベンダー別のデバイス
  - OS 別のデバイス
  - VLAN あたりのデバイス数
  - 切断されたデバイス
- 時間別の接続の切断
- 種類別のインシデントのアラート
- データベース テーブルへのアクセス
- プロトコル解析ウィジェット
- DELTAV
  - DeltaV roc 操作の分布
  - 名前別の DeltaV roc イベント
  - 時間別の DeltaV イベント
- AMS
  - サーバー ポート別の AMS トラフィック
  - コマンド別の AMS トラフィック
- イーサネットと IP アドレス:
  - CIP サービス別のイーサネットと IP アドレスのトラフィック
  - CIP クラス別のイーサネットと IP アドレスのトラフィック
  - コマンド別のイーサネットと IP アドレスのトラフィック
- OPC:
  - OPC の上位管理操作
  - OPC の上位 I/O 操作
- Siemens S7:
  - 制御機能別の S7 トラフィック
  - サブ機能別の S7 トラフィック
- VLAN
  - VLAN あたりのデバイス数
  - VLAN 別の上位の帯域幅
- 60870-5-104
  - ASDU 別の IEC-60870 トラフィック
- BACNET
  - BACnet サービス
- DNP3
  - 機能別の DNP3 トラフィック
- SRTP:
  - サービス コード別の SRTP トラフィック
  - 日別の SRTP エラー
- SuiteLink:
  - SuiteLink のクエリされた上位のタグ
  - SuiteLink の数値タグの動作
- ASDU 別の IEC-60870 トラフィック
- 機能別の DNP3 トラフィック
- サービス別の MMS トラフィック
- 機能別の Modbus トラフィック
- サービス別の OPC-UA トラフィック

> [!NOTE]
>  ウィジェットの時刻は、センサーの時刻に従って設定されます。

## <a name="create-reports"></a>レポートを作成します

ダッシュボードとウィジェットを見るには:

サイド メニューで **[傾向と統計]** を選択します。

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="調査のスクリーンショット。":::

既定では、過去 7 日間の検出結果が表示されます。 フィルター ツールを使用してこの範囲を変更できます。 たとえば、フリー テキスト検索です。

## <a name="create-a-dashboard"></a>ダッシュボードを作成する

**[ダッシュボード]** ドロップダウン メニューを選択して、新しいダッシュボードを作成します。 任意の数のウィジェットを作成してダッシュボードに追加できます。

次のオプションを使用して、カスタマイズされたダッシュボードを作成できます。

- ウィジェットをダッシュボードに追加する

- ダッシュボードからのウィジェットの削除

- ウィジェットのフィルターを変更する

- ウィジェットのサイズを変更する

- ウィジェットの場所を変更する

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="ウィジェットの場所を変更します。":::

カスタマイズしたダッシュボードを作成するには:

1. 左側のパネルから **[Trends and Statistics]\(傾向と統計\)** を選択します。

1. **[ダッシュボードの選択]** ドロップダウン メニューを選択し、 **[ダッシュボードの作成]** ボタンを選択します。

1. 新しいダッシュボードにわかりやすい名前を入力し、 **[作成]** を選択します。

1. ページの左上にある **[ウィジェットの追加]** を選択します。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="ウィジェット ストアからウィジェットを選択します。":::

1. **[セキュリティ]** および **[Operational]\(運用時\)** ウィジェットは、ウィンドウの右上にあります。 さまざまなカテゴリとプロトコルから選択できます。 簡単な説明と小さいグラフィックが各ウィジェットに表示されます。 使用できるすべてのウィジェットを表示するには、スクロール バーを使用します。

1. **[クリックして追加]** ボタンを使用してウィジェットを選択します。 ウィジェットがダッシュボードにすぐに表示されます。

ダッシュボードを削除するには:

1. ドロップダウン メニューからダッシュボードの名前を選択します。

1. **[削除]** アイコンを選択し、 **[OK]** を選択します。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="[削除] アイコンを選択してダッシュボードを削除します。":::

ダッシュボード名を編集するには:

1. ドロップダウン メニューからダッシュボードの名前を選択します。

1. **[編集]** アイコンを選択します。
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="[編集] アイコンを選択してダッシュボードの名前を編集します。":::

1. ダッシュボードの新しい名前を入力し、 **[保存]** を選択します。
 
既定のダッシュボードを設定するには:

1. ドロップダウン メニューからダッシュボードの名前を選択します。

1. **[星]** アイコンを選択して、既定のダッシュボードとして設定するダッシュボードを選択します。

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="[星] アイコンを選択して既定のダッシュボードを選択します。"::: 

ウィジェットのデータのフィルター処理を変更するには:

1. **[フィルター]** アイコンを選択します。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="[フィルター] アイコンを選択してウィジェットのパラメーターを設定します。":::

1. 必要なパラメーターを編集します。

1. **[OK]** を選択します。

ウィジェットを削除するには:

- :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false"::: アイコンを選択します。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="[X] を選択してウィジェットを削除します。":::

## <a name="creating-widgets"></a>ウィジェットの作成 

ウィジェット ストアを使用すると、カテゴリとプロトコルを指定してウィジェットを選択できます。 使用できる **[セキュリティ]** または **[Operational]\(運用時\)** ウィジェットを選択して表示できます。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="ウィジェット ストアからウィジェットを選択します。":::

各ウィジェットには、システムのトラフィック、ネットワークの統計情報、プロトコルの統計情報、デバイス、およびアラート情報に関する具体的な情報が含まれています。 ウィジェットのデータがない場合はメッセージが表示されます。

円グラフの円からセクションを削除して、残りのスライスの相対的な重要性をより明確に確認することができます。 これを行うには、画面下部の凡例でスライスの名前を選択します。

以下のセクションでは、いくつかのウィジェットのユース ケースの例を示します。

### <a name="busy-devices-widget"></a>Busy Devices (ビジー デバイス) ウィジェット

このウィジェットを使用すると、ビジー デバイスの上位 5 つを一覧表示できます。 **[Edit]\(編集\)** モードでは、既知のプロトコルでフィルター処理できます。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="Busy Devices (ビジー デバイス) ウィジェットのビュー。":::

### <a name="total-bandwidth-widget"></a>Total Bandwidth (合計帯域幅) ウィジェット

このウィジェットを使用すると、帯域幅を Mbps (メガビット/秒) 単位で追跡できます。 帯域幅は Y 軸に示され、日付は X 軸に示されます。 **[Edit]\(編集\)** モードでは、クライアント、サーバー、サーバー ポート、またはサブネットに従って結果をフィルター処理できます。 グラフの上にカーソルを置くと、ツール ヒントが表示されます。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="Total Bandwidth (合計帯域幅) ウィジェットのビュー。":::

### <a name="channels-bandwidth-widget"></a>Channels Bandwidth (チャネル帯域幅) ウィジェット

このウィジェットには、上位 5 つのトラフィック チャネルが表示されます。 アドレスでフィルター処理し、表示される結果の数を設定できます。 下矢印を選択すると、さらにチャネルが表示されます。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="Channels Bandwidth (チャネル帯域幅) ウィジェットのビュー。":::

### <a name="traffic-by-port-widget"></a>Traffic By Port (ポート別トラフィック) ウィジェット

このウィジェットには、ポートごとにトラフィックが表示されます。これは、各ポートに別の色が指定された円グラフで示されます。 各ポートのトラフィック量は、円グラフのその部分のサイズに比例します。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="Traffic By Port (ポート別トラフィック) ウィジェットのビュー。":::

### <a name="new-devices-widget"></a>New Devices (新しいデバイス) ウィジェット

このウィジェットには、特定の日に検出された新しいデバイスの数を示す、新しいデバイスの棒グラフが表示されます。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="New Devices (新しいデバイス) ウィジェットのビュー。":::

### <a name="protocol-dissection-widgets"></a>プロトコル解析ウィジェット

このウィジェットには、関数コードとサービスによって解析された、プロトコルごとのトラフィックを確認できる円グラフが表示されます。 円グラフの各スライスのサイズは、他のスライスを基準としたトラフィックの量に比例します。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="Protocol Dissection (プロトコル解析) ウィジェットのビュー。":::

### <a name="active-tcp-connections-widget"></a>Active TCP Connections (アクティブな TCP 接続) ウィジェット

このウィジェットには、システム内のアクティブな TCP 接続数を示すグラフが表示されます。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="Active TCP Connections (アクティブな TCP 接続) ウィジェットのビュー。":::

### <a name="incident-by-type-widget"></a>Incident By Type (種類別インシデント) ウィジェット

このウィジェットには、種類別のインシデント数を示す円グラフが表示されます。 これは、定義された期間に各エンジンによって生成されたアラートの数です。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="Incident By Type (種類別インシデント) ウィジェットのビュー。":::

## <a name="devices-by-vendor-widget"></a>Devices By Vendor (ベンダー別デバイス) ウィジェット

このウィジェットには、ベンダー別のデバイス数を示す円グラフが表示されます。 特定のベンダーのデバイスの数は、他のデバイス ベンダーを基準とした、ディスクのそのデバイスのベンダー部分のサイズに比例します。

## <a name="number-of-devices-per-vlan-widget"></a>Number of devices per VLAN (VLAN あたりのデバイス数) ウィジェット

このウィジェットには、VLAN あたりの検出されたデバイス数を示す円グラフが表示されます。 円グラフの各スライスのサイズは、他のスライスを基準として検出されたデバイスの数に比例します。

各 VLAN は、センサーによって割り当てられた VLAN タグ、または手動で追加した名前と共に表示されます。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="Number of devices (デバイス数) ウィジェットのビュー。":::

### <a name="top-bandwidth-by-vlan-widget"></a>Top Bandwidth by VLAN (VLAN 別の上位の帯域幅) ウィジェット

このウィジェットには、VLAN 別の帯域幅の使用量が表示されます。 既定で、このウィジェットには、帯域幅の使用量が多い上位 5 つの VLAN が表示されます。

ウィジェットに表示される期間でデータをフィルター処理できます。 下矢印を選択すると、さらに結果が表示されます。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="Top Bandwidth by VLAN (VLAN 別の上位の帯域幅) ウィジェットのビュー。":::

## <a name="system-report"></a>システム レポート

システム レポートをダウンロードするには: 

1. サイド メニューで **[傾向と統計]** を選択します。

1. 右上隅にある **[System Report]\(システム レポート\)** を選択します。 レポートが自動的にダウンロードされます。

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="[System Report]\(システム レポート\) ボタンを選択して、システム レポートのコピーをダウンロードします。":::

システム レポートは、システム内のすべてのデータが含まれる PDF ファイルです。

  - デバイス

  - 警告

  - ネットワーク ポリシー情報

## <a name="devices-in-a-system-report"></a>システム レポートのデバイス 

システム レポートには、すべてのデバイスとその情報の一覧が表示されます。 たとえば、種類、名前、使用されているプロトコルです。 システム レポートには、ベンダーごとのデバイスの一覧も表示されます。

## <a name="alerts-in-system-report"></a>システム レポートのアラート 

システム レポートには、日付や重要度などの情報と共にすべてのアラートの一覧が表示されます。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="システム レポートのアラートのビュー。":::

## <a name="network-information-in-system-report"></a>システム レポートのネットワーク情報 

システム レポートには、ネットワークのベースラインが詳細に表示されます。 たとえば、DNP3 関数コード、接続ごとの開いているポートです。

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="システム レポートの DNP3 関数のビュー。":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="接続ごとの開いているポートのレポートのビュー。":::

## <a name="see-also"></a>関連項目

[リスク評価レポート](how-to-create-risk-assessment-reports.md)
[センサー データ マイニング クエリ](how-to-create-data-mining-queries.md)
[攻撃ベクトル レポート](how-to-create-attack-vector-reports.md)
