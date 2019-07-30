---
title: Azure Security Center for IoT エージェントを構成する (プレビュー) | Microsoft Docs
description: Azure Security Center for IoT で使用するエージェントを構成する方法について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 39539bb14877208e5f6af957e735a136b077f16a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618272"
---
# <a name="tutorial-configure-security-agents"></a>チュートリアル:セキュリティ エージェントを構成する

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Azure Security Center (ASC) for IoT セキュリティ エージェントを構成する方法、ツインのプロパティを編集してそれらの動作を変更する方法、および既定の構成を検出する方法について説明します。

> [!div class="checklist"]
> * セキュリティ エージェントを構成する
> * ツインのプロパティを編集してエージェントの動作を変更する
> * 既定の構成を検出する

## <a name="agents"></a>エージェント

ASC for IoT セキュリティ エージェントは、IoT デバイスからデータを収集し、検出された脆弱性を軽減するためにセキュリティ アクションを実行します。 セキュリティ エージェント構成は、カスタマイズ可能なモジュール ツイン プロパティのセットを使用して制御することができます。 一般に、これらのプロパティに対する第 2 更新は、めったに起こりません。  

ASC for IoT セキュリティ エージェント ツインの構成オブジェクトは、.json 形式のオブジェクトです。 この構成オブジェクトは制御可能な一連のプロパティで、エージェントの動作を制御するために定義できます。 

これらの構成は、エージェントを必要なシナリオごとにカスタマイズするのに役立ちます。 たとえば、これらのプロパティを構成することで、いくつかのイベントを自動的に除外したり、消費電力を最小限のレベルに抑えたりすることができます。  

変更を加えるには、ASC for IoT セキュリティ エージェントの構成[スキーマ](https://aka.ms/iot-security-github-module-schema)を使用します。  

## <a name="configuration-objects"></a>構成オブジェクト 

ASC for IoT セキュリティ エージェントに関連する各プロパティは、**azureiotsecurity** モジュールのエージェント構成オブジェクトの、目的のプロパティのセクション内にあります。 

構成を変更するには、**azureiotsecurity** モジュール ツイン ID 内でこのオブジェクトを作成して変更します。 

**azureiotsecurity** モジュール ツインにエージェント構成オブジェクトが存在しない場合は、セキュリティ エージェントのすべてのプロパティ値が既定値に設定されます。 

```json
"desired": {
  "azureiot*com^securityAgentConfiguration^1*0*0": {
  } 
}
```

必ず、この[スキーマ](https://aka.ms/iot-security-github-module-schema)に照らしてエージェント構成の変更を検証してください。
構成オブジェクトがスキーマと一致しない場合、エージェントは起動しません。

## <a name="configuration-schema-and-validation"></a>構成スキーマと検証 

必ず、この[スキーマ](https://aka.ms/iot-security-github-module-schema)に照らしてエージェント構成を検証してください。 構成オブジェクトがスキーマと一致しない場合、エージェントは起動しません。

 
エージェントの実行中に構成オブジェクトが無効な構成 (スキーマに一致しない構成) に変更された場合、エージェントは無効な構成を無視して、現在の構成を引き続き使用します。 

## <a name="editing-a-property"></a>プロパティの編集 

すべてのカスタム プロパティは、**azureiotsecurity** モジュール ツイン内のエージェント構成オブジェクト内に設定する必要があります。
既定のプロパティ値を使用するには、構成オブジェクトからプロパティを削除します。

### <a name="setting-a-property"></a>プロパティの設定

1. IoT Hub で、変更するデバイスを見つけて選択します。

1. デバイスをクリックし、次に **[azureiotsecurity]** をクリックします。

1. **[モジュール ID ツイン]** をクリックします。

1. セキュリティ モジュールの必要なプロパティを編集します。
   
   たとえば、接続イベントを優先度が高いイベントとして構成し、7 分おきに優先度が高いイベントを収集するには、次の構成を使用します。
   
   ```json
    "desired": {
      "azureiot*com^securityAgentConfiguration^1*0*0": {
        "highPriorityMessageFrequency": "PT7M",    
        "eventPriorityConnectionCreate": "High" 
      } 
    }, 
    ```

1. **[Save]** をクリックします。

### <a name="using-a-default-value"></a>既定値の使用

既定のプロパティ値を使用するには、構成オブジェクトからプロパティを削除します。

## <a name="default-properties"></a>既定のプロパティ 

次の表には、ASC for IoT セキュリティ エージェントの制御可能なプロパティが含まれています。

既定値は、[Github](https://aka.ms/iot-security-module-default) の適切なスキーマにあります。

| Name| Status | 有効な値| 既定値| 説明 |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|必須: false |有効な値: ISO 8601 形式の期間 |既定値:PT7M |優先度の高いメッセージが送信されるまでの最大時間。|
|lowPriorityMessageFrequency |必須: false|有効な値: ISO 8601 形式の期間 |既定値:PT5H |低優先度のメッセージが送信されるまでの最大時間。| 
|snapshotFrequency |必須: false|有効な値: ISO 8601 形式の期間 |既定値: PT13H |デバイス状態のスナップショットを作成する時間間隔。| 
|maxLocalCacheSizeInBytes |必須: false |有効な値: |既定値:2560000 (8192 より大きい値) | エージェントのメッセージ キャッシュで許容される最大ストレージ (バイト単位)。 メッセージが送信される前に、デバイス上にメッセージを保存するために許可される領域の上限。| 
|maxMessageSizeInBytes |必須: false |有効な値: 8192 より大きく、262144 より小さい正の数 |既定値:204800 |クラウドにメッセージを送信するエージェントの最大許容サイズ。 この設定は、各メッセージで送信されるデータの最大量を制御します。 |
|eventPriority${EventName} |必須: false |有効な値: High、Low、Off |既定値: |エージェントが生成した各イベントの優先順位 | 

### <a name="supported-security-events"></a>サポートされるセキュリティ イベント

|イベント名| PropertyName | Default value| スナップショット イベント| 詳細の状態  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|診断イベント|eventPriorityDiagnostic| オフ| False| エージェント関連の診断イベント。 このイベントは、詳細ログ記録のために使用します。| 
|構成エラー |eventPriorityConfigurationError |低 |False |構成の解析に失敗したエージェント。 構成をスキーマに照らして検証します。| 
|削除されたイベントの統計 |eventPriorityDroppedEventsStatistics |低 |True|エージェント関連イベントの統計。 |
|メッセージの統計|eventPriorityMessageStatistics |低 |True |エージェント関連メッセージの統計。 |
|接続されているハードウェア|eventPriorityConnectedHardware |低 |True |デバイスに接続されているすべてのハードウェアのスナップショット。|
|ポートのリッスン|eventPriorityListeningPorts |高 |True |デバイス上のオープンしているすべてのリスニング ポートのスナップショット。|
|プロセスの作成 |eventPriorityProcessCreate |低 |False |デバイス上のプロセスの作成を監査します。|
|プロセスの終了|eventPriorityProcessTerminate |低 |False |デバイス上のプロセスの終了を監査します。| 
|システム情報 |eventPrioritySystemInformation |低 |True |システム情報のスナップショット (例:OS または CPU)。| 
|ローカル ユーザー| eventPriorityLocalUsers |高 |True|システム内の登録済みローカル ユーザーのスナップショット。 |
|ログイン|  eventPriorityLogin |高|False|デバイスへのログイン イベントを監査します (ローカル ログインとリモート ログイン)。|
|接続の作成 |eventPriorityConnectionCreate|低|False|デバイスとの間で作成された TCP 接続を監査します。 |
|ファイアウォールの構成| eventPriorityFirewallConfiguration|低|True|デバイスのファイアウォール構成 (ファイアウォール規則) のスナップショット。 |
|OS ベースライン| eventPriorityOSBaseline| 低|True|デバイスの OS ベースライン チェックのスナップショット。|
 

## <a name="next-steps"></a>次の手順

- [ASC for IoT の推奨事項について](concept-recommendations.md)
- [ASC for IoT アラートを確認する](concept-security-alerts.md)
- [未加工のセキュリティ データにアクセスする](how-to-security-data-access.md)
