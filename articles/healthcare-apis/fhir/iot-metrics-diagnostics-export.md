---
title: 診断設定を使用して Azure IoT Connector for FHIR (プレビュー) のメトリックをエクスポートする
description: この記事では、診断設定を使用して Azure IoT Connector for FHIR (プレビュー) のメトリックをエクスポートする方法について説明します
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 3b350de81e07f30f4f0f105c4e833a3b4450e558
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019411"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>診断設定を使用して Azure IoT Connector for FHIR (プレビュー) のメトリックをエクスポートする

この記事では、Azure IoT Connector for FHIR&#174; (高速ヘルスケア相互運用性リソース)* メトリック ログをエクスポートする方法について説明します。 メトリック ログ記録を有効にする機能は、Azure portal の [ **[診断設定]**](../../azure-monitor/essentials/diagnostic-settings.md) に相当します。 

> [!TIP]
> [Azure API for FHIR と Azure IoT Connector for FHIR の診断ログを有効にする](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir)ことに関するページのガイダンスに従って、監査ログを設定します。

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー) のメトリック ログ記録を有効にする
1. Azure IoT Connector for FHIR のメトリック ログ記録を有効にするには、Azure portal でお使いの Azure API for FHIR サービスを選択します 

2. **[診断設定]** に移動します 

3. **[+ 診断設定を追加する]** を選択します

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. **[診断設定の名前]** ダイアログ ボックスに名前を入力します。

5. 診断ログへのアクセスに使用する方法を選択します。

    1. 監査や手動での検査のために、**ストレージ アカウントにアーカイブ** します。 使用するストレージ アカウントは既に作成済みである必要があります。
    2. サード パーティのサービスやカスタム分析ソリューションで取り込むために、**イベント ハブにストリーム配信** します。 この手順を構成する前に、イベント ハブの名前空間とイベント ハブのポリシーを作成する必要があります。
    3. Azure Monitor の **Log Analytics ワークスペースにストリーム配信** します。 このオプションを選択する前に、Log Analytics ワークスペースを作成する必要があります。

6. Azure IoT Connector for FHIR の **[エラー]、[トラフィック]、[待機時間]** を選択します。  Azure API for FHIR でキャプチャする追加のメトリック カテゴリを選択します。

7. **[保存]** を選びます。

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> 選択したリポジトリに最初のメトリック ログが表示されるまでに最大 15 分かかることがあります。  
 
診断ログの使用方法の詳細については、[Azure リソース ログのドキュメント](../../azure-monitor/essentials/platform-logs-overview.md)を参照してください

## <a name="conclusion"></a>まとめ 
メトリック ログにアクセス可能であることは、監視とトラブルシューティングに欠かせません。  Azure IoT Connector for FHIR を使用すると、メトリック ログを通してこれらのアクションを行うことができます。 

## <a name="next-steps"></a>次の手順

Azure IoT Connector for FHIR についてよく寄せられる質問を確認します。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR についてよく寄せられる質問](fhir-faq.md)

*Azure portal では、Azure IoT Connector for FHIR は IoT Connector (プレビュー) と呼ばれています。 FHIR は HL7 の登録商標であり、HL7 の許可を得て使用しています。