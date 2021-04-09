---
title: セキュリティ エージェントを構成する
description: Defender for IoT セキュリティ サービスで Defender for IoT セキュリティ エージェントが使用されるように構成する方法について説明します。
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 5ac9a5cdb4cc13ed91c0e5e29447b9cdbe71bcce
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784409"
---
# <a name="tutorial-configure-security-agents"></a>チュートリアル:セキュリティ エージェントを構成する

この記事では、Defender for IoT セキュリティ エージェントと、それらの変更および構成を実行する方法の詳細について説明します。

> [!div class="checklist"]
> * セキュリティ エージェントを構成する
> * ツインのプロパティを編集してエージェントの動作を変更する
> * 既定の構成を検出する

## <a name="agents"></a>エージェント

Defender for IoT セキュリティ エージェントでは、IoT デバイスからデータが収集され、検出された脆弱性を軽減するためのセキュリティ アクションが実行されます。 セキュリティ エージェント構成は、カスタマイズ可能なモジュール ツイン プロパティのセットを使用して制御することができます。 一般に、これらのプロパティに対する第 2 更新は、めったに起こりません。

Defender for IoT セキュリティ エージェント ツインの構成オブジェクトは、JSON 形式のオブジェクトです。 この構成オブジェクトは制御可能な一連のプロパティで、エージェントの動作を制御するために定義できます。

これらの構成は、エージェントを必要なシナリオごとにカスタマイズするのに役立ちます。 たとえば、これらのプロパティを構成することで、いくつかのイベントを自動的に除外したり、消費電力を最小限のレベルに抑えたりすることができます。

変更を加えるには、Defender for IoT セキュリティ エージェントの構成[スキーマ](https://aka.ms/iot-security-github-module-schema)を使用します。

## <a name="configuration-objects"></a>構成オブジェクト

Defender for IoT セキュリティ エージェントに関連するプロパティは、**azureiotsecurity** モジュールの (目的のプロパティ セクション内の) エージェント構成オブジェクトに配置されます。

構成を変更するには、**azureiotsecurity** モジュール ツイン ID 内でこのオブジェクトを作成して変更します。

**azureiotsecurity** モジュール ツインにエージェント構成オブジェクトが存在しない場合は、セキュリティ エージェントのすべてのプロパティ値が既定値に設定されます。

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>構成スキーマと検証

必ず、この[スキーマ](https://aka.ms/iot-security-github-module-schema)に照らしてエージェント構成を検証してください。 構成オブジェクトがスキーマと一致しない場合、エージェントは起動しません。

エージェントの実行中に構成オブジェクトが無効な構成 (スキーマに一致しない構成) に変更された場合、エージェントは無効な構成を無視して、現在の構成を引き続き使用します。

### <a name="configuration-validation"></a>構成の検証

Defender for IoT セキュリティ エージェントでは、**azureiotsecurity** モジュール ツイン ID の報告されるプロパティ セクションの中に、現在の構成が報告されます。
エージェントは使用可能なすべてのプロパティを報告します。プロパティがユーザーによって設定されていない場合、エージェントは既定の構成を報告します。

構成を検証するには、目的のセクションに設定されている値と、報告されたセクションで報告された値を比較します。

目的のプロパティと報告されたプロパティが一致しない場合、エージェントは構成を解析できませんでした。

[スキーマ](https://aka.ms/iot-security-github-module-schema)に対して目的のプロパティを検証し、エラーを修正して、必要なプロパティを再度設定します。

> [!NOTE]
> エージェントが目的の構成を解析できなかった場合に、エージェントから構成エラーアラートが発生します。
> 報告されたセクションと目的のセクションを比較して、アラートがまだ適用されているかどうかを理解します

## <a name="editing-a-property"></a>プロパティの編集

すべてのカスタム プロパティは、**azureiotsecurity** モジュール ツイン内のエージェント構成オブジェクト内に設定する必要があります。
既定のプロパティ値を使用するには、構成オブジェクトからプロパティを削除します。

### <a name="setting-a-property"></a>プロパティの設定

1. IoT Hub で、変更するデバイスを見つけて選択します。

1. デバイスをクリックし、次に **[azureiotsecurity]** をクリックします。

1. **[モジュール ID ツイン]** をクリックします。

1. Defender-IoT-micro-agent で変更するプロパティを編集します。

   たとえば、接続イベントを優先度が高いイベントとして構成し、7 分おきに優先度が高いイベントを収集するには、次の構成を使用します。

    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. **[保存]** をクリックします。

### <a name="using-a-default-value"></a>既定値の使用

既定のプロパティ値を使用するには、構成オブジェクトからプロパティを削除します。

## <a name="default-properties"></a>既定のプロパティ

次の表には、Defender for IoT セキュリティ エージェントの制御可能なプロパティが含まれています。

既定値は、[GitHub](https\://aka.ms/iot-security-module-default) の適切なスキーマにあります。

| 名前| Status | 有効な値| 既定値| 説明 |
|----------|--------|--|-------|----|
|highPriorityMessageFrequency|必須: false |有効な値: ISO 8601 形式の期間 |既定値:PT7M |優先度の高いメッセージが送信されるまでの最大時間間隔。|
|lowPriorityMessageFrequency |必須: false|有効な値: ISO 8601 形式の期間 |既定値:PT5H |優先度の低いメッセージが送信されるまでの最大時間。|
|snapshotFrequency |必須: false|有効な値: ISO 8601 形式の期間 |既定値: PT13H |デバイス状態のスナップショットを作成する時間間隔。|
|maxLocalCacheSizeInBytes |必須: false |有効な値: |既定値:2560000 (8192 より大きい値) | エージェントのメッセージ キャッシュで許容される最大ストレージ (バイト単位)。 メッセージが送信される前に、デバイス上にメッセージを保存するために許可される領域の上限。|
|maxMessageSizeInBytes |必須: false |有効な値: 8192 より大きく、262144 より小さい正の数 |既定値:204800 |クラウドにメッセージを送信するエージェントの最大許容サイズ。 この設定は、各メッセージで送信されるデータの最大量を制御します。 |
|eventPriority${EventName} |必須: false |有効な値: High、Low、Off |既定値: |エージェントが生成した各イベントの優先順位 |

### <a name="supported-security-events"></a>サポートされるセキュリティ イベント

|イベント名| PropertyName | Default value| スナップショット イベント| 詳細の状態  |
|----------|-|---------|----|----|
|診断イベント|eventPriorityDiagnostic| Off| False| エージェント関連の診断イベント。 このイベントは、詳細ログ記録のために使用します。|
|構成エラー |eventPriorityConfigurationError |低 |False |構成の解析に失敗したエージェント。 構成をスキーマに照らして検証します。|
|削除されたイベントの統計 |eventPriorityDroppedEventsStatistics |低 |True|エージェント関連イベントの統計。 |
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
|

## <a name="next-steps"></a>次のステップ

- [Defender for IoT の推奨事項を理解する](concept-recommendations.md)
- [Defender for IoT アラートを探索する](concept-security-alerts.md)
- [未加工のセキュリティ データにアクセスする](how-to-security-data-access.md)
