---
title: Azure IoT Connector for FHIR (プレビュー) - トラブルシューティング ガイドと操作方法
description: 一般的な Azure IoT Connector for FHIR (プレビュー) のエラー メッセージと状態のトラブルシューティング方法およびマッピング ファイルのコピー
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 3eef7354f7197f60e8abd1b5522393bf00a6203f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019351"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Azure IoT Connector for FHIR (プレビュー) のトラブルシューティング ガイド

この記事では、一般的な Azure IoT Connector for FHIR&#174; (高速ヘルスケア相互運用性リソース)* のエラー メッセージと状態をトラブルシューティングする手順について説明します。  

また、Azure IoT Connector for FHIR 変換マッピング JSON (例: デバイスと FHIR) のコピーを作成する方法について学習します。  

Azure portal 外での編集とアーカイブに、変換マッピング JSON コピーを使用することができます。  

> [!TIP]
> Azure IoT Connector for FHIR の [Azure テクニカル サポート](https://azure.microsoft.com/support/create-ticket/) チケットを開く場合は、トラブルシューティング プロセスに役立つように、必ず変換マッピング JSON のコピーを含めるようにしてください。

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー) のデバイスおよび FHIR 変換マッピング JSON テンプレートの検証
このセクションでは、デバイスおよび FHIR 変換マッピング JSON テンプレートを使用のために保存することを許可する前に、Azure IoT Connector for FHIR がそれを検証するために実行する検証プロセスについて説明します。  これらの要素は、デバイスおよび FHIR 変換マッピング JSON に必要です。

**デバイス マッピング**

|要素|必須|
|:-------|:------|
|TypeName|True|
|TypeMatchExpression|True|
|DeviceIdExpression|True|
|TimestampExpression|True|
|Values[].ValueName|True|
|Values[].ValueExpression|True|

> [!NOTE]
> Values[].ValueName および Values[].ValueExpression
>
> これらの要素は、配列に値エントリがある場合にのみ必要です。値がマップされていなくても有効です。 これは、送信されるテレメトリがイベントである場合に使用されます。 次に例を示します。ウェアラブル IoMT デバイスが装着または取り外された場合。 この要素には、Azure IoT Connector for FHIR がマッチングして出力する名前以外の値はありません。 FHIR 変換では、Azure IoT Connector for FHIR は、それをセマンティックの種類に基づいてコード可能な概念にマップします。実際の値は設定されません。

**FHIR マッピング**

|要素|必須|
|:------|:-------|
|TypeName|True|

> [!NOTE]
> これは、現時点で検証されている唯一の必須の FHIR マッピング要素です。

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

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Azure API for FHIR に Azure IoT Connector for FHIR (プレビュー) データが表示されないのはなぜですか?

|潜在的な問題|修正|
|----------------|-----|
|データがまだ処理中です。|データはバッチで (最大 15 分ごとに) Azure API for FHIR に送信されます。  データがまだ処理中である可能性があります。また、データを Azure API for FHIR で永続化するにはさらに時間がかかります。|
|デバイス変換マッピング JSON が構成されていません。|準拠するデバイス変換マッピング JSON を構成して保存します。|
|FHIR 変換マッピング JSON が構成されていません。|準拠する FHIR 変換マッピング JSON を構成して保存します。|
|デバイス マッピングで定義されている必要な式が、デバイス メッセージに含まれていません。|デバイス マッピングに定義されている JsonPath 式が、デバイス メッセージに定義されているトークンと一致することを確認します。|
|Azure API for FHIR にデバイス リソースが作成されていません (解決の種類: 参照のみ)*。|Azure API for FHIR に有効なデバイス リソースを作成します。 受信メッセージで指定されたデバイス ID と一致する識別子が、デバイス リソースに含まれていることを確認してください。|
|Azure API for FHIR で患者リソースが作成されていません (解決の種類: 参照のみ)*。|Azure API for FHIR に有効な患者リソースを作成します。|
|Device.patient 参照が設定されていないか、参照が無効です (解決の種類: 参照のみ)*。|患者リソースに対して有効な[参照](https://www.hl7.org/fhir/device-definitions.html#Device.patient)が、デバイス リソースに含まれていることを確認します。| 

\* [クイックスタート: Azure portal を使用する Azure IoT コネクタ (プレビュー) のデプロイ](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview)に関するページを参照し、Azure IoT Connector for FHIR の解決の種類 (例: 参照または作成) の機能説明を確認してください。

## <a name="use-metrics-to-troubleshoot-issues-in-azure-iot-connector-for-fhir-preview"></a>メトリックを使用して Azure IoT Connector for FHIR (プレビュー) の問題のトラブルシューティングを行う

データ フロー プロセスに関する分析情報を得るための複数のメトリックが Azure IoT Connector for FHIR によって生成されます。 サポートされているメトリックの 1 つは、"*エラー合計*" と呼ばれます。これは、Azure IoT Connector for FHIR の 1 つのインスタンス内で発生したすべてのエラーの数を示します。

各エラーは、関連するさまざまなプロパティと共にログに記録されます。 すべてのプロパティが、エラーに関するさまざまな側面を提供します。これは、問題の特定とトラブルシューティングに役立ちます。 このセクションでは、"*エラー合計*" のメトリックで各エラーについて取得されるさまざまなプロパティの一覧と、これらのプロパティに使用できる値を示します。

> [!NOTE]
> [Azure IoT Connector for FHIR (プレビュー) のメトリックに関するページ](iot-metrics-display.md)の説明に従って、Azure IoT Connector for FHIR (プレビュー) の "*エラー合計*" のメトリックに移動できます。

"*エラー合計*" グラフをクリックし、 *[フィルターの追加]* ボタンをクリックして、次に示すいずれかのプロパティを使用してエラー メトリックを詳細に分析します。

### <a name="the-operation-performed-by-the-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー) によって実行される操作

このプロパティは、エラー発生時に IoT コネクタで実行中の操作を表します。 通常、操作はデバイス メッセージ処理中のデータ フロー ステージを表します。 このプロパティの可能な値の一覧を次に示します。

> [!NOTE]
> Azure IoT Connector for FHIR (プレビュー) でのデータ フローのさまざまなステージについて詳しくは、[こちら](iot-data-flow.md)を参照してください。

|データ フロー ステージ|Description|
|---------------|-----------|
|セットアップ|IoT コネクタのインスタンスの設定に固有の操作|
|正規化|デバイス データが正規化されるデータ フロー ステージ|
|グループ化|正規化されたデータがグループ化されるデータ フローステージ|
|FHIRConversion|正規化されてグループ化されたデータが FHIR リソースに変換されるデータ フロー ステージ|
|Unknown|エラーが発生した場合、操作の種類は不明です|

### <a name="the-severity-of-the-error"></a>エラーの重大度

このプロパティは、発生したエラーの重大度を表します。 このプロパティの可能な値の一覧を次に示します。

|重大度|説明|
|---------------|-----------|
|警告|データ フロー プロセスにはいくつかの軽微な問題が存在しますが、デバイス メッセージの処理は停止しません|
|エラー|特定のデバイス メッセージの処理でエラーが発生し、他のメッセージは想定どおりに実行が継続される可能性があります|
|Critical|IoT コネクタにシステム レベルの問題がいくつか存在し、メッセージは処理されないと想定されます|

### <a name="the-type-of-the-error"></a>エラーの種類

このプロパティは、特定のエラーのカテゴリを示します。これは基本的に、種類が類似したエラーの論理的なグループを表します。 このプロパティの可能な値の一覧を次に示します。

|エラーの種類|Description|
|----------|-----------|
|DeviceTemplateError|デバイス マッピング テンプレートに関連したエラー|
|DeviceMessageError|特定のデバイス メッセージの処理中に発生したエラー|
|FHIRTemplateError|FHIR マッピング テンプレートに関連したエラー|
|FHIRConversionError|メッセージを FHIR リソースに変換中に発生したエラー|
|FHIRResourceError|IoT コネクタによって参照されている FHIR サーバー内の既存のリソースに関連したエラー|
|FHIRServerError|FHIR サーバーとの通信中に発生するエラー|
|GeneralError|その他のエラーの種類すべて|

### <a name="the-name-of-the-error"></a>エラーの名前

このプロパティは、特定のエラーの名前を示します。 すべてのエラー名、それらの説明、関連するエラーの種類、重大度、データ フロー ステージの一覧を次に示します。

|エラー名|Description|エラーの種類|エラーの重大度|データ フロー ステージ|
|----------|-----------|-------------|--------------|------------------|
|MultipleResourceFoundException|デバイス メッセージ内に存在する個々の ID に対して FHIR サーバーで患者またはデバイスのリソースが複数見つかったときに、エラーが発生しました|FHIRResourceError|エラー|FHIRConversion|
|TemplateNotFoundException|IoT コネクタのインスタンスにデバイスまたは FHIR マッピング テンプレートが構成されていません|DeviceTemplateError、FHIRTemplateError|Critical|正規化、FHIRConversion|
|CorrelationIdNotDefinedException|デバイス マッピング テンプレートに相関 ID が指定されていません。 CorrelationIdNotDefinedException は、FHIR Observation で相関 ID を使用してデバイスの測定値をグループ化する必要があるが、それが正しく構成されていない場合にのみ発生する条件付きエラーです|DeviceMessageError|エラー|正規化|
|PatientDeviceMismatchException|このエラーは、FHIR サーバー上のデバイス リソースから患者リソースへの参照があり、それがメッセージ内に存在する患者 ID と一致しない場合に発生します|FHIRResourceError|エラー|FHIRConversionError|
|PatientNotFoundException|デバイス メッセージ内に存在するデバイス ID に関連付けられた Device FHIR リソースによって参照されている Patient FHIR リソースはありません。 このエラーは、IoT コネクタ インスタンスに "*検索*" という解決の種類が構成されている場合にのみ発生することに注意してください|FHIRConversionError|エラー|FHIRConversion|
|DeviceNotFoundException|デバイス メッセージ内に存在するデバイス ID に関連付けられたデバイス リソースが FHIR サーバー上に存在しません|DeviceMessageError|エラー|正規化|
|PatientIdentityNotDefinedException|このエラーは、デバイス メッセージの患者 ID を解析する式がデバイス マッピング テンプレートに構成されていないか、デバイス メッセージ内に患者 ID が存在しない場合に発生します。 このエラーは、IoT コネクタの解決の種類が "*作成*" に設定されている場合にのみ発生することに注意してください|DeviceTemplateError|Critical|正規化|
|DeviceIdentityNotDefinedException|このエラーは、デバイス メッセージのデバイス ID を解析する式がデバイス マッピング テンプレートに構成されていないか、デバイス メッセージ内にデバイス ID が存在しない場合に発生します|DeviceTemplateError|Critical|正規化|
|NotSupportedException|サポートされていない形式のデバイス メッセージを受信したときにエラーが発生しました|DeviceMessageError|エラー|正規化|

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Azure IoT Connector for FHIR (プレビュー) 変換マッピング JSON のコピーの作成

Azure IoT Connector for FHIR マッピング ファイルのコピーは、Azure portal Web サイト外での編集とアーカイブに役立ちます。

トラブルシューティングに役立つように、サポート チケットを開くときにマッピング ファイルのコピーを Azure テクニカル サポートに提供する必要があります。

> [!NOTE]
> 現時点でデバイスおよび FHIR マッピング ファイルでサポートされている形式は、JSON のみです。

> [!TIP]
> Azure IoT Connector for FHIR の[デバイスおよび FHIR 変換マッピング JSON](iot-mapping-templates.md) の詳細を確認してください

1. Azure API for FHIR リソース ダッシュボードの左下にある、 **[アドイン]** セクションの **[IoT コネクタ (プレビュー)]** を選択します。

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT Connector1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 変換マッピング JSON のコピー元となる **コネクタ** を選択します。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> このプロセスは、 **[Configure FHIR mapping]\(FHIR マッピングの構成\)** JSON の内容をコピーして保存する場合にも使用できます。

3. **[Configure device mapping]\(デバイス マッピングの構成\)** を選択します。

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT Connector3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. JSON の内容を選択し、コピー操作を行います (例: Ctrl + c キーを選択する)。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 貼り付け操作 (例: Ctrl + v キーを選択する) をエディター (例: Visual Studio Code、メモ帳) 内の新しいファイルに対して行い、*.json 拡張子を付けてファイルを保存します。

> [!TIP]
> Azure IoT Connector for FHIR の [Azure テクニカル サポート](https://azure.microsoft.com/support/create-ticket/) チケットを開く場合は、トラブルシューティング プロセスに役立つように、必ず変換マッピング JSON のコピーを含めるようにしてください。

## <a name="next-steps"></a>次の手順

Azure IoT Connector for FHIR についてよく寄せられる質問を確認します。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR についてよく寄せられる質問](fhir-faq.md)

*Azure portal では、Azure IoT Connector for FHIR は IoT Connector (プレビュー) と呼ばれています。 FHIR は HL7 の登録商標であり、HL7 の許可を得て使用しています。