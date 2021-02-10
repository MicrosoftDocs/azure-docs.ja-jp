---
title: 傾向と統計のレポートを生成する
description: Defender for IoT の傾向と統制のウィジェットを使用し、ネットワークのアクティビティ、統計、傾向に関する分析情報を取得します。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 8bfbacc77843c7978bcb1d364bd93f5e09381144
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811569"
---
# <a name="sensor-trends-and-statistics-reports"></a>センサーの傾向と統計のレポート

## <a name="about-sensor-trends-and-statistics-reports"></a>センサーの傾向と統計のレポートについて

ウィジェットのグラフと円グラフを作成して、ネットワークの傾向と統計に関する分析情報を得ることができます。 ウィジェットは、ユーザー定義ダッシュボードの下にグループ化できます。

> [!NOTE]
> 管理者とセキュリティ アナリストは傾向と統制のレポートを作成できます。

ダッシュボードは、次の種類の情報をグラフィカルに説明するウィジェットで構成されています。

- ポート別のトラフィック
- チャネル帯域幅
- 合計帯域幅
- アクティブ TCP 接続
- デバイス:
  - 新しいデバイス
  - ビジー状態のデバイス
  - ベンダー別のデバイス
  - OS 別のデバイス
  - 切断されたデバイス
- 時間別の接続の切断
- 種類別のインシデントのアラート
- データベース テーブルへのアクセス
- プロトコル解析ウィジェット
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

## <a name="see-also"></a>関連項目

[リスク評価レポート](how-to-create-risk-assessment-reports.md)
[センサー データ マイニング クエリ](how-to-create-data-mining-queries.md)
[攻撃ベクトル レポート](how-to-create-attack-vector-reports.md)