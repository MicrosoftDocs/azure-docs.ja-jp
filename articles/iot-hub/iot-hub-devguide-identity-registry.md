---
title: Azure IoT Hub ID レジストリについて | Microsoft Docs
description: 開発者ガイド - IoT Hub ID レジストリおよびこのレジストリを使用してデバイスを管理する方法の説明。 デバイス ID の一括でのインポートとエクスポートに関する情報が含まれています。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 2d9b0d97fa1823314f5109a1c7fc79054806c148
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146928"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>IoT Hub の ID レジストリを理解する

すべての IoT ハブには、その IoT ハブへの接続が許可されたデバイスおよびモジュールに関する情報を保存する ID レジストリがあります。 デバイスまたはモジュールを IoT ハブに接続できるようにするには、そのデバイスまたはモジュールのエントリが IoT ハブの ID レジストリに存在する必要があります。 さらに、ID レジストリに保存されている資格情報に基づいて、デバイスまたはモジュールが IoT ハブで認証される必要があります。

ID レジストリに保存されているデバイスまたはモジュール ID は、大文字と小文字が区別されます。

大まかに言うと、ID レジストリは、デバイスまたはモジュール ID リソースの REST 対応コレクションです。 この ID レジストリにエントリを追加すると、転送中の Cloud-to-device メッセージが含まれるキューなどの、デバイス単位のリソースが IoT Hub によって作成されます。

ID レジストリは、以下が必要な場合に使用します。

* IoT ハブに接続するデバイスまたはモジュールをプロビジョニングする。
* ハブのデバイスまたはモジュール向けエンドポイントに対するデバイスまたはモジュールごとのアクセスを制御する。

> [!NOTE]
> * ID レジストリには、アプリケーション固有のメタデータは含まれません。
> * モジュール ID とモジュール ツインはパブリック プレビュー中です。 以下の機能は、一般公開時にモジュール ID でサポートされます。
>

## <a name="identity-registry-operations"></a>ID レジストリの操作

IoT Hub の ID レジストリでは、次の操作が公開されています。

* デバイスまたはモジュール ID の作成
* デバイスまたはモジュール ID の更新
* ID ごとのデバイスまたはモジュール ID の取得
* デバイスまたはモジュール ID の削除
* 最大 1000 個の ID の一覧表示
* Azure Blob Storage へのデバイス ID のエクスポート
* Azure Blob Storage からのデバイス ID のインポート

これらすべての操作で、[RFC7232](https://tools.ietf.org/html/rfc7232) に指定されているオプティミスティック同時実行制御を使用できます。

> [!IMPORTANT]
> IoT Hub の ID レジストリにあるすべての ID を取得する唯一の方法は、[エクスポート](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)機能を使用することです。

IoT Hub の ID レジストリの特徴は次のとおりです。

* アプリケーションのメタデータは含まれていません。
* **deviceId** または **moduleId** をキーとして使用することで、ディクショナリと同様にアクセスできます。
* 詳細なクエリはサポートされていません。

通常、IoT ソリューションにはソリューション固有のストアがあり、各ストアにはアプリケーション固有のメタデータが含まれています。 たとえば、スマート ビルディング ソリューションのソリューション固有のストアでは、気温センサーをデプロイする部屋を記録します。

> [!IMPORTANT]
> ID レジストリは、デバイスの管理およびプロビジョニング操作でのみ使用します。 実行時の高スループット操作が、ID レジストリに対する操作の影響を受けないようにする必要があります。 たとえば、コマンドを送信する前に、デバイスの接続状態を確認するやり方は、サポートされていないパターンです。 ID レジストリの[スロットル率](iot-hub-devguide-quotas-throttling.md)と[デバイス ハートビート](iot-hub-devguide-identity-registry.md#device-heartbeat) パターンを必ず確認してください。

## <a name="disable-devices"></a>デバイスの無効化

ID レジストリで ID の **status** プロパティを更新することにより、デバイスを無効にすることができます。 通常、このプロパティは次の 2 つのシナリオで使用します。

* オーケストレーション プロセスのプロビジョニング中。 詳細については、[デバイスのプロビジョニング](iot-hub-devguide-identity-registry.md#device-provisioning)に関するページを参照してください。

* 何らかの理由で、デバイスが侵害されているか、未承認の状態になっていると考えられる場合。

この機能は、モジュールでは使用できません。

## <a name="import-and-export-device-identities"></a>デバイス ID のインポートとエクスポート

IoT ハブの ID レジストリからデバイス ID を一括エクスポートするには、[IoT Hub リソース プロバイダー エンドポイント](iot-hub-devguide-endpoints.md)に対する非同期操作を使用します。 エクスポートは、顧客が指定した BLOB コンテナーを使用して、デバイス ID レジストリから読み取ったデバイス ID データを保存する、長時間実行されるジョブです。

IoT ハブの ID レジストリにデバイス ID を一括インポートするには、[IoT Hub リソース プロバイダー エンドポイント](iot-hub-devguide-endpoints.md)に対する非同期操作を使用します。 インポートは、顧客が指定した BLOB コンテナー内のデータを使用してデバイス ID データを ID レジストリに書き込む、長時間実行ジョブです。

インポート API とエクスポート API の詳細については、[IoT Hub のリソースプロバイダー REST API](/rest/api/iothub/iothubresource) に関する記事を参照してください。 インポートおよびエクスポート ジョブの実行方法の詳細については、[IoT Hub デバイス ID の一括管理](iot-hub-bulk-identity-mgmt.md)に関するページを参照してください。

デバイス ID は、[REST API](/rest/api/iothub/service/jobs/createimportexportjob)、またはいずれかの IoT Hub [サービス SDK](./iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks) のどちらかを使用して、サービス API を介して IoT Hub からエクスポートおよびインポートすることもできます。

## <a name="device-provisioning"></a>デバイス プロビジョニング

特定の IoT ソリューションに格納されるデバイス データは、そのソリューションの具体的な要件によって異なりますが、 ソリューションには少なくともデバイスの ID と認証キーを格納する必要があります。 Azure IoT Hub には、各デバイスの ID、認証キー、状態コードなどの値を保存できる ID レジストリがあります。 ソリューションでは、Table Storage、Blob Storage、Cosmos DB などの他の Azure サービスを使用して、さらにデバイス データを保存できます。

*デバイス プロビジョニング* とは、最初のデバイス データをソリューション内のストアに追加するプロセスです。 新しいデバイスをハブに接続できるようにするには、デバイスの ID とキーを IoT Hub ID レジストリに追加する必要があります。 プロビジョニング プロセスの一環として、他のソリューション ストアにあるデバイス固有データの初期化が必要になる場合があります。 また、Azure IoT Hub Device Provisioning サービスを使用して、1 つまたは複数の IoT ハブに対してノータッチの Just-In-Time プロビジョニングを実現できるため、人の手を介する必要がなくなります。 詳しくは、[Provisioning Service のドキュメント](https://azure.microsoft.com/documentation/services/iot-dps)をご覧ください。

## <a name="device-heartbeat"></a>デバイスのハートビート

IoT Hub ID レジストリには、**connectionState** というフィールドが含まれています。 **connectionState** フィールドは、開発とデバッグ中にのみ使用します。 IoT ソリューションでは、実行時にフィールドに対してクエリを実行できません。 たとえば、Cloud-to-device メッセージや SMS を送信する前に、デバイスが接続されているかどうかを確認するために **connectionState** フィールドに対してクエリを実行することはできません。 Event Grid で [**デバイス切断** イベント](iot-hub-event-grid.md#event-types)をサブスクライブして、アラートを取得し、デバイスの接続状態を監視することをお勧めします。 IoT ソリューション内の IoT Hub からのデバイス接続イベントとデバイス切断イベントを統合する方法については、こちらの[チュートリアル](iot-hub-how-to-order-connection-state-events.md)をご覧ください。

IoT ソリューションでデバイスが接続されているかどうかを知る必要がある場合は、*ハートビート パターン* を実装できます。
ハートビート パターンでは、デバイスは一定の時間ごとに 1 回以上、D2C メッセージを送信します。(たとえば、1 時間ごとに 1 回以上)。 したがって、デバイスは送信するデータを保持していなくても、空の D2C メッセージを送信します (通常は、それをハートビートとして識別するプロパティが伴います)。 サービス側では、ソリューションは、各デバイスから受信した最後のハートビートのマップを保持します。 想定された時間内にデバイスからのハートビート メッセージを受け取らなかった場合、ソリューションはデバイスに問題があるとみなします。

より複雑な実装になると、[Azure Monitor](../azure-monitor/index.yml) と [Azure Resource Health](../service-health/resource-health-overview.md) からの情報を取り込むことで、接続または通信を試みているが失敗しているデバイスを識別することが可能です。 詳細については、[IoT Hub の監視](monitor-iot-hub.md)と[IoT Hub のリソースの正常性の確認](iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health)に関するページを参照してください。 ハートビート パターンを実装する場合は、[IoT Hub のクォータとスロットル](iot-hub-devguide-quotas-throttling.md)を必ず確認してください。

> [!NOTE]
> IoT ソリューションで Cloud-to-device メッセージを送信するかどうかを判断するためだけに接続状態が使用され、メッセージが多数のデバイスにブロードキャストされない場合は、より単純で *有効期限の短い* パターンの使用を検討してください。 この方法を使用すると、ハートビート パターンを使用してデバイス接続状態レジストリを維持するのと同じ結果を得られますが、効率が向上します。 メッセージの受信確認を要求すると、メッセージを受信できるデバイスとできないデバイスについての通知を IoT Hub から受け取ることができます。

## <a name="device-and-module-lifecycle-notifications"></a>デバイスまたはモジュールのライフサイクルの通知

IoT Hub は、ライフサイクルの通知を送信することで、ID がいつ作成されたか、またはいつ削除されたかを IoT ソリューションに通知できます。 そのためには、IoT ソリューションでルートを作成し、データ ソースの値を *DeviceLifecycleEvents* または *ModuleLifecycleEvents* に設定する必要があります。 既定では、ライフサイクルの通知は送信されません。つまり、このようなルートは事前に存在しません。 通知メッセージには、プロパティおよび本文が含まれます。

プロパティ: メッセージ システム プロパティは `$` 記号で始まります。

デバイスの通知メッセージ:

| 名前 | 値 |
| --- | --- |
|$content-type | application/json |
|$iothub-enqueuedtime |  通知が送信された時刻 |
|$iothub-message-source | deviceLifecycleEvents |
|$content-encoding | utf-8 |
|opType | **createDeviceIdentity** または **deleteDeviceIdentity** |
|hubName | IoT Hub の名前 |
|deviceId | デバイスの ID |
|operationTimestamp | 操作の ISO8601 タイムスタンプ |
|iothub-message-schema | deviceLifecycleNotification |

本文: このセクションは JSON 形式であり、作成されたデバイス ID のツインを表します。 たとえば、次のように入力します。

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
モジュールの通知メッセージ:

| 名前 | 値 |
| --- | --- |
$content-type | application/json |
$iothub-enqueuedtime |  通知が送信された時刻 |
$iothub-message-source | moduleLifecycleEvents |
$content-encoding | utf-8 |
opType | **createModuleIdentity** または **deleteModuleIdentity** |
hubName | IoT Hub の名前 |
moduleId | モジュールの ID |
operationTimestamp | 操作の ISO8601 タイムスタンプ |
iothub-message-schema | moduleLifecycleNotification |

本文: このセクションは JSON 形式であり、作成されたモジュール ID のツインを表します。 たとえば、次のように入力します。

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>デバイス ID のプロパティ

デバイス ID は、次のプロパティを持つ JSON ドキュメントとして表されます。

| プロパティ | オプション | 説明 |
| --- | --- | --- |
| deviceId |必須、読み取り専用 (更新時) |ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) と、特定の特殊文字 (`- . + % _ # * ? ! ( ) , : = @ $ '`)。 |
| generationId |必須、読み取り専用 |IoT Hub によって生成された、大文字と小文字が区別される文字列 (最大 128 文字)。 この値は、デバイスが削除されて再作成された場合に、同じ **deviceId** を持つデバイスを区別するために使用します。 |
| etag |必須、読み取り専用 |[RFC7232](https://tools.ietf.org/html/rfc7232) に準拠した、デバイス ID の弱い ETag を表す文字列。 |
| auth |オプション |認証情報とセキュリティのマテリアルを含む複合オブジェクト。 |
| auth.symkey |オプション |base64 形式でプライマリ キーとセカンダリ キーを格納する複合オブジェクト。 |
| status |必須 |アクセス インジケーター。 **[有効]** または **[無効]** のいずれか。 **[有効]** の場合、デバイスからの接続が許可されます。 **[無効]** の場合、このデバイスからデバイス向けのエンドポイントにアクセスできません。 |
| statusReason |オプション |デバイス ID の状態の理由を格納する、長さが 128 文字の文字列。 すべての UTF-8 文字を使用できます。 |
| statusUpdateTime |読み取り専用 |状態が最後に更新された日時を示す時間のインジケーター。 |
| connectionState |読み取り専用 |接続状態を示すフィールド。**Connected** または **Disconnected** のいずれか。 このフィールドは、デバイスの接続状態に関する IoT Hub ビューを表します。 **重要**: このフィールドは、開発およびデバッグ専用として使用してください。 接続状態は、MQTT または AMQP を使用するデバイスについてのみ更新されます。 また、この更新はプロトコル レベルの ping (MQTT ping または AMQP ping) に基づいており、遅延は最大でもわずか 5 分です。 このため、接続状態にあると報告されているが切断状態にあるデバイスのように、偽陽性を示す可能性があります。 |
| connectionStateUpdatedTime |読み取り専用 |前回接続状態が更新された日時を示す時間のインジケーター。 |
| lastActivityTime |読み取り専用 |前回デバイスが接続された日時またはメッセージを送受信した日時を示す時間のインジケーター。 このプロパティの一貫性は最終的には保たれますが、最大で 5 ～ 10 分遅れる可能性があります。 このため、運用環境のシナリオでは使用しないでください。 |

> [!NOTE]
> 接続状態が表すのは、その接続状態を示す IoT Hub ビューのみです。 ネットワークの状態と構成によっては、この状態の更新が遅延することがあります。

> [!NOTE]
> 現在、デバイス SDK では、**deviceId** での文字 `+` および `#` の使用はサポートされていません。

## <a name="module-identity-properties"></a>モジュール ID のプロパティ

モジュール ID は、次のプロパティを持つ JSON ドキュメントとして表されます。

| プロパティ | オプション | 説明 |
| --- | --- | --- |
| deviceId |必須、読み取り専用 (更新時) |ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) と、特定の特殊文字 (`- . + % _ # * ? ! ( ) , : = @ $ '`)。 |
| moduleId |必須、読み取り専用 (更新時) |ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) と、特定の特殊文字 (`- . + % _ # * ? ! ( ) , : = @ $ '`)。 |
| generationId |必須、読み取り専用 |IoT Hub によって生成された、大文字と小文字が区別される文字列 (最大 128 文字)。 この値は、デバイスが削除されて再作成された場合に、同じ **deviceId** を持つデバイスを区別するために使用します。 |
| etag |必須、読み取り専用 |[RFC7232](https://tools.ietf.org/html/rfc7232) に準拠した、デバイス ID の弱い ETag を表す文字列。 |
| auth |オプション |認証情報とセキュリティのマテリアルを含む複合オブジェクト。 |
| auth.symkey |オプション |base64 形式でプライマリ キーとセカンダリ キーを格納する複合オブジェクト。 |
| status |必須 |アクセス インジケーター。 **[有効]** または **[無効]** のいずれか。 **[有効]** の場合、デバイスからの接続が許可されます。 **[無効]** の場合、このデバイスからデバイス向けのエンドポイントにアクセスできません。 |
| statusReason |オプション |デバイス ID の状態の理由を格納する、長さが 128 文字の文字列。 すべての UTF-8 文字を使用できます。 |
| statusUpdateTime |読み取り専用 |状態が最後に更新された日時を示す時間のインジケーター。 |
| connectionState |読み取り専用 |接続状態を示すフィールド。**Connected** または **Disconnected** のいずれか。 このフィールドは、デバイスの接続状態に関する IoT Hub ビューを表します。 **重要**: このフィールドは、開発およびデバッグ専用として使用してください。 接続状態は、MQTT または AMQP を使用するデバイスについてのみ更新されます。 また、この更新はプロトコル レベルの ping (MQTT ping または AMQP ping) に基づいており、遅延は最大でもわずか 5 分です。 このため、接続状態にあると報告されているが切断状態にあるデバイスのように、偽陽性を示す可能性があります。 |
| connectionStateUpdatedTime |読み取り専用 |前回接続状態が更新された日時を示す時間のインジケーター。 |
| lastActivityTime |読み取り専用 |前回デバイスが接続された日時またはメッセージを送受信した日時を示す時間のインジケーター。 |

> [!NOTE]
> 現在、デバイス SDK では、**deviceId** および **moduleId** での文字 `+` および `#` の使用はサポートされていません。

## <a name="additional-reference-material"></a>参考資料

IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント](iot-hub-devguide-endpoints.md): 各 IoT Hub でランタイムと管理の操作のために公開される、さまざまなエンドポイントについて説明します。

* [スロットルとクォータ](iot-hub-devguide-quotas-throttling.md): IoT Hub サービスに適用されるクォータとスロットルの動作について説明します。

* [Azure IoT device SDK とサービス SDK](iot-hub-devguide-sdks.md): IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。

* [IoT Hub のクエリ言語](iot-hub-devguide-query-language.md): IoT Hub からデバイス ツインとジョブに関する情報を取得する際に使用できるクエリ言語について説明します。

* [IoT Hub の MQTT サポート](iot-hub-mqtt-support.md): IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次のステップ

IoT ハブの ID レジストリの使用方法を理解できたら、次の IoT Hub 開発者ガイドのトピックも参考にしてください。

* [IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)

* [デバイス ツインを使って状態と構成を同期する](iot-hub-devguide-device-twins.md)

* [デバイスでダイレクト メソッドを呼び出す](iot-hub-devguide-direct-methods.md)

* [複数デバイスでのジョブをスケジュール設定する](iot-hub-devguide-jobs.md)

この記事で説明した概念を試すには、次の IoT Hub のチュートリアルをご覧ください。

* [Azure IoT Hub を使ってみる](quickstart-send-telemetry-dotnet.md)

IoT Hub Device Provisioning サービスを使用してノータッチの Just-In-Time プロビジョニングを実現する方法については、次を参照してください。 

* [Azure IoT Hub Device Provisioning Service](https://azure.microsoft.com/documentation/services/iot-dps)