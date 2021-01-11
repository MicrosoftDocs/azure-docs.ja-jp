---
title: Azure IoT Connector for FHIR (プレビュー) - トラブルシューティング ガイドと操作方法
description: 一般的な Azure IoT Connector for FHIR (プレビュー) のエラー メッセージと状態のトラブルシューティング方法およびマッピング ファイルのコピー
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: jasteppe
ms.openlocfilehash: 088d1e409f14fdba02311d1ff17eb655f6e41ad3
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053458"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Azure IoT Connector for FHIR (プレビュー) のトラブルシューティング ガイド

この記事では、一般的な Azure IoT Connector for FHIR* のエラー メッセージと状態をトラブルシューティングする手順について説明します。  

また、Azure IoT Connector for FHIR 変換マッピング JSON (例: デバイスと FHIR) のコピーを作成する方法について学習します。  

Azure portal 外での編集とアーカイブに、変換マッピング JSON コピーを使用することができます。  

> [!TIP]
> Azure IoT Connector for FHIR の [Azure テクニカル サポート](https://azure.microsoft.com/support/create-ticket/) チケットを開く場合は、トラブルシューティング プロセスに役立つように、必ず変換マッピング JSON のコピーを含めるようにしてください。

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー) のエラー メッセージと修正

|Message|表示|条件|Fix| 
|-------|---------|---------|---|
|マッピング名が無効です。マッピング名はデバイスまたは FHIR である必要があります。|API|指定されたマッピングの種類がデバイスでも FHIR でもありません。|サポートされている 2 つのマッピングの種類のいずれか (例: デバイスまたは FHIR) を使用します。|
|検証に失敗しました。 必要な情報が見つからないか、無効です。|API と Azure portal|変換マッピングを保存しようとしましたが、必要な情報または要素が欠落しています。|欠落している変換マッピング情報または要素を追加して、変換マッピングをもう一度保存します。|
|キーの再生成パラメーターが定義されていません。|API|キーの再生成要求。|キーの再生成要求にパラメーターを含めます。|
|このサブスクリプションでプロビジョニングできる IoT コネクタ インスタンスの最大数に達しました。|API と Azure portal|Azure IoT Connector for FHIR のサブスクリプション クォータに達しました (サブスクリプションあたりの既定値は (2) です)。|Azure IoT Connector for FHIR の既存のインスタンスの 1 つを削除します。  サブスクリプション クォータに達していない別のサブスクリプションを使用します。  サブスクリプション クォータを増やすように要求します。|
|Azure API for FHIR リソースが有効になっている IoT コネクタでは、リソースの移動はサポートされません。|API と Azure portal|Azure IoT Connector for FHIR の 1 つまたは複数のインスタンスを含む Azure API for FHIR リソースで、移動操作を実行しようとしています。|Azure IoT Connector for FHIR の既存のインスタンスを削除して、移動操作を実行します。|
|IoT コネクタがプロビジョニングされていません。|API|親 (Azure IoT Connector for FHIR) がプロビジョニングされていないときに、子サービスを使用 (接続とマッピング) しようとしています。|Azure IoT Connector for FHIR をプロビジョニングします。|
|要求はサポートされていません。|API|特定の API 要求がサポートされていません。|適切な API 要求を使用します。|
|アカウントが存在しません。|API|Azure IoT Connector for FHIR を追加しようとしています。Azure API for FHIR リソースは存在しません。|Azure API for FHIR リソースを作成してから、操作を再試行します。|
|Azure API for FHIR リソースの FHIR バージョンが、IoT コネクタでサポートされていません。|API|互換性のないバージョンの Azure API for FHIR リソースで、Azure IoT Connector for FHIR を使用しようとしています。|新しい Azure API for FHIR リソース (バージョン R4) を作成するか、既存の Azure API for FHIR リソース (バージョン R4) を使用します。

##  <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Azure API for FHIR に Azure IoT Connector for FHIR (プレビュー) データが表示されないのはなぜですか?

|潜在的な問題  |修正            |
|------------------|-----------------|
|データがまだ処理中です。|データはバッチで (最大 15 分ごとに) Azure API for FHIR に送信されます。  データがまだ処理中である可能性があります。また、データを Azure API for FHIR で永続化するにはさらに時間がかかります。|
|デバイス変換マッピング JSON が構成されていません。|準拠するデバイス変換マッピング JSON を構成して保存します。|
|FHIR 変換マッピング JSON が構成されていません。|準拠する FHIR 変換マッピング JSON を構成して保存します。|
|デバイス マッピングで定義されている必要な式が、デバイス メッセージに含まれていません。|デバイス マッピングに定義されている JsonPath 式が、デバイス メッセージに定義されているトークンと一致することを確認します。|
|Azure API for FHIR にデバイス リソースが作成されていません (解決の種類: 参照のみ)*。|Azure API for FHIR に有効なデバイス リソースを作成します。 受信メッセージで指定されたデバイス ID と一致する識別子が、デバイス リソースに含まれていることを確認してください。|
|Azure API for FHIR で患者リソースが作成されていません (解決の種類: 参照のみ)*。|Azure API for FHIR に有効な患者リソースを作成します。|
|Device.patient 参照が設定されていないか、参照が無効です (解決の種類: 参照のみ)*。|患者リソースに対して有効な[参照](https://www.hl7.org/fhir/device-definitions.html#Device.patient)が、デバイス リソースに含まれていることを確認します。| 

\* [クイックスタート: Azure portal を使用する Azure IoT コネクタ (プレビュー) のデプロイ](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview)に関するページを参照し、Azure IoT Connector for FHIR の解決の種類 (例: 参照または作成) の機能説明を確認してください。

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Azure IoT Connector for FHIR (プレビュー) 変換マッピング JSON のコピーの作成
Azure IoT Connector for FHIR マッピング ファイルのコピーは、Azure portal Web サイト外での編集とアーカイブに役立ちます。

トラブルシューティングに役立つように、サポート チケットを開くときにマッピング ファイルのコピーを Azure テクニカル サポートに提供する必要があります。

> [!NOTE]
> 現時点でデバイスおよび FHIR マッピング ファイルでサポートされている形式は、JSON のみです。

> [!TIP]
> Azure IoT Connector for FHIR の[デバイスおよび FHIR 変換マッピング JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates) の詳細を確認してください

1. Azure API for FHIR リソース ダッシュボードの左下にある、 **[アドイン]** セクションの **[IoT コネクタ (プレビュー)]** を選択します。

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT コネクタ" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 変換マッピング JSON のコピー元となる**コネクタ**を選択します。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT コネクタ" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> このプロセスは、 **[Configure FHIR mapping]\(FHIR マッピングの構成\)** JSON の内容をコピーして保存する場合にも使用できます。

3. **[Configure device mapping]\(デバイス マッピングの構成\)** を選択します。

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT コネクタ" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. JSON の内容を選択し、コピー操作を行います (例: Ctrl + c キーを選択する)。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT コネクタ" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 貼り付け操作 (例: Ctrl + v キーを選択する) をエディター (例: Visual Studio Code、メモ帳) 内の新しいファイルに対して行い、*.json 拡張子を付けてファイルを保存します。

> [!TIP]
> Azure IoT Connector for FHIR の [Azure テクニカル サポート](https://azure.microsoft.com/support/create-ticket/) チケットを開く場合は、トラブルシューティング プロセスに役立つように、必ず変換マッピング JSON のコピーを含めるようにしてください。

## <a name="next-steps"></a>次の手順

Azure IoT Connector for FHIR についてよく寄せられる質問を確認します。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR についてよく寄せられる質問](fhir-faq.md#azure-iot-connector-for-fhir-preview)

*Azure portal では、Azure IoT Connector for FHIR は IoT Connector (プレビュー) と呼ばれています。

FHIR は HL7 の登録商標であり、HL7 の許可を得て使用しています。
