---
title: Azure IoT Connector for FHIR (プレビュー) のメトリックを表示および構成する
description: この記事では、Azure IoT Connector for FHIR (プレビュー) のメトリックを表示および構成する方法について説明します。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 831c6df3f50bfff9b411660d9efc4cd78ee5e8d9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019381"
---
# <a name="display-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Azure IoT Connector for FHIR (プレビュー) のメトリックを表示および構成する 

この記事では、Azure IoT Connector for FHIR#174;* (高速ヘルスケア相互運用性リソース) メトリックを表示および構成する方法について説明します。

> [!TIP]
> メトリック データのエクスポートを設定する方法については、「[診断設定を使用して Azure IoT Connector for FHIR (プレビュー) のメトリックをエクスポートする](iot-metrics-diagnostics-export.md)」のガイダンスを参照してください。

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー) のメトリックを表示する

1. Azure portal にサインインし、ご利用の Azure API for FHIR サービスを選択します。 

2. 左側のペインで、 **[メトリック]** を選択します。 

3. **[IoT Connector]\(IoT コネクタ\)** タブを選択します。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="受信したおよび正規化されたメッセージの数を示す折れ線グラフが表示された、[IoT Connector] ペインのスクリーンショット。" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. メトリックを表示する IoT Connector を選択します。 たとえば、この Azure API for FHIR サービスには、関連付けられている 4 つの IoT Connector ("*コネクタ 1*"、"*コネクタ 2*" など) があります。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="IoT Connector の 1、2、3、4 のタブが表示されている [IoT Connector] ペインのスクリーンショット。" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. 表示する IoT Connector メトリックの期間 (たとえば、 **[1 時間]** 、 **[24 時間]** 、 **[7 日]** 、 **[カスタム]** ) を選択します。 **[カスタム]** タブを選択すると、IoT Connector のメトリックを表示する特定の日付と時間の組み合わせを作成できます。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="'コネクタ 1' の '1 時間' の期間の折れ線グラフが表示されている、[IoT Connector] ペインのスクリーンショット。" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー) のメトリックの種類 

> [!TIP]
> Azure IoT Connector for FHIR のデータ フローについては、「[Azure IoT Connector for FHIR (プレビュー) のデータ フロー](iot-data-flow.md)」と「[Azure IoT Connector for FHIR (プレビュー) のトラブルシューティング ガイド](iot-troubleshoot-guide.md)」を参照して、エラー メッセージと修正の詳細を確認してください。

表示できる IoT Connector のメトリックを次の表に示します。

|メトリックの種類|メトリックの目的| 
|-----------|--------------|
|Number of Incoming Messages (受信メッセージの数)|受信した未加工の受信メッセージ (例: デバイス イベント) の数を表示します。|
|Number of Normalized Messages (正規化されたメッセージの数)|正規化されたメッセージの数を表示します。|
|Number of Message Groups (メッセージ グループの数)|指定された時間枠内にメッセージが集計されたグループの数を表示します。|
|Average Normalized Stage Latency (正規化されたステージの平均待機時間)|正規化ステージの平均待機時間を表示します。 正規化ステージにより、未加工の受信メッセージに対して正規化が実行されます。|
|Average Group Stage Latency (グループ ステージの平均待機時間)|グループ ステージの平均待機時間を表示します。 グループ ステージにより、正規化されたメッセージに対してバッファーリング、集計、グループ化が実行されます。| 
|Total Error Count|エラーの総数を表示します。| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Azure IoT Connector for FHIR (プレビュー) のメトリックに焦点を合わせて構成する

この例では、 **[Number of Incoming Messages]\(受信メッセージの数\)** メトリックに焦点を当てます。

1. 焦点を当てる時点を選択します。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="折れ線グラフで 1 つの時点が強調表示されている、[Number of Incoming Messages]\(受信メッセージの数\) メトリック ペインのスクリーンショット。" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. **[Number of Incoming Messages]\(受信メッセージの数\)** ペインで、 **[メトリックの追加]** 、 **[フィルターの追加]** 、または **[Apply splitting]\(分割の適用\)** を選択して、メトリックをさらにカスタマイズできます。 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="[メトリックの追加]、[フィルターの追加]、[Apply splitting]\(分割の適用\) の各ボタンが強調表示された、[Number of Incoming Messages]\(受信メッセージの数\) メトリック ペインのスクリーンショット。" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>まとめ 
データ プレーンのメトリックにアクセス可能であることは、監視とトラブルシューティングに欠かせません。 Azure IoT Connector for FHIR は、メトリックを使用してこのようなアクションを実行する場合に役立ちます。 

## <a name="next-steps"></a>次のステップ

Azure IoT Connector for FHIR についてよく寄せられる質問への回答を得ます。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR についてよく寄せられる質問](fhir-faq.md)

*Azure portal では、Azure IoT Connector for FHIR は IoT Connector (プレビュー) と呼ばれています。 FHIR は HL7 の登録商標であり、HL7 の許可を得て使用しています。 
