---
title: Azure IoT Hub ジョブについて | Microsoft Docs
description: 開発者ガイド - IoT Hub に接続されている複数のデバイスで実行するジョブのスケジュール設定。 ジョブはタグと必要なプロパティを更新でき、複数のデバイス上でダイレクト メソッドを呼び出すことができます。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/09/2018
ms.openlocfilehash: aacb0ab69dad45f9ca7655daaae0c2acff0403f5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59044374"
---
# <a name="schedule-jobs-on-multiple-devices"></a>複数デバイスでのジョブをスケジュール設定する

Azure IoT Hub では、[デバイス ツインのプロパティとタグ](iot-hub-devguide-device-twins.md)や[ダイレクト メソッド](iot-hub-devguide-direct-methods.md)のような多数の構成要素を使用できます。 通常、デバイス管理者とオペレーターは、バックエンド アプリを使用して、IoT デバイスの更新と対話を、指定した時刻に一括で実行できます。 ジョブは、指定した時刻に一連のデバイスに対してデバイス ツインの更新とダイレクト メソッドを実行します。 たとえば、オペレーターは、ビル 43 の 3 階にある一連のデバイスを、ビルの運用に悪影響を与えることがない時刻に再起動するジョブを開始して追跡するバックエンド アプリを使用します。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

一連のデバイスで次のアクティビティのスケジュールを設定し、その進行状況を追跡する必要があるときは、ジョブの使用を検討します。

* 必要なプロパティを更新する
* タグを更新する
* ダイレクト メソッドを呼び出す

## <a name="job-lifecycle"></a>ジョブのライフサイクル

ジョブは、ソリューションのバック エンドによって開始され、IoT Hub によって管理されます。 ジョブは、サービス向け URI (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`) を通して開始でき、実行中のジョブの進行状況は、サービス向け URI (`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`) を通して照会できます。 ジョブが開始された後で実行中のジョブの状態を更新するには、ジョブ クエリを実行します。

> [!NOTE]
> ジョブを呼び出すとき、プロパティ名と値には `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT` を除く US-ASCII 印刷可能英数字のみを使用できます。

## <a name="jobs-to-execute-direct-methods"></a>ダイレクト メソッドを実行するジョブ

次のスニペットでは、ジョブを使って一連のデバイス上で[ダイレクト メソッド](iot-hub-devguide-direct-methods.md)を実行するための HTTPS 1.1 要求の詳細を示します。

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleDirectMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

クエリ条件は、1 つのデバイス ID に対するものであっても、次の例で示すようにデバイス ID のリストに対するものであってもかまいません。

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

IoT Hub クエリ言語の詳細については、[IoT Hub クエリ言語](iot-hub-devguide-query-language.md)に関するページをご覧ください。

## <a name="jobs-to-update-device-twin-properties"></a>デバイス ツインのプロパティを更新するジョブ

次のスニペットでは、ジョブを使ってデバイス ツインのプロパティを更新するための HTTPS 1.1 要求の詳細を示します。

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleTwinUpdate",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

## <a name="querying-for-progress-on-jobs"></a>ジョブの進行状況の照会

次のスニペットでは、ジョブを照会するための HTTPS 1.1 要求の詳細を示します。

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>
```

ContinuationToken は、応答から提供されます。

それぞれのデバイスにおけるジョブの実行状態は、[デバイス ツイン、ジョブ、メッセージ ルーティングの IoT Hub クエリ言語](iot-hub-devguide-query-language.md)を使用してクエリを実行することができます。

## <a name="jobs-properties"></a>ジョブのプロパティ

次の一覧は、ジョブまたはジョブの結果を照会するときに使うことができるプロパティとその説明です。

| プロパティ | 説明 |
| --- | --- |
| **jobId** |アプリケーションが提供するジョブの ID。 |
| **startTime** |アプリケーションが提供するジョブの開始時刻 (ISO 8601)。 |
| **endTime** |IoT Hub が提供するジョブの完了時の日付 (ISO 8601)。 ジョブが 'completed' 状態に達した後でのみ有効です。 |
| **type** |ジョブの種類: |
| | **scheduledUpdateTwin**: 必要なプロパティまたはタグのセットを更新するために使用するジョブ。 |
| | **scheduledDeviceMethod**: デバイス ツインのセットに対してデバイス メソッドを呼び出すために使用するジョブ。 |
| **status** |ジョブの現在の状態。 状態の可能値: |
| | **pending**: スケジュールが設定され、ジョブ サービスによって選択されるのを待機しています。 |
| | **scheduled**: 将来の時刻のスケジュールが設定されています。 |
| | **running**: 現在アクティブなジョブです。 |
| | **canceled**: ジョブが取り消されました。 |
| | **failed**: ジョブが失敗しました。 |
| | **completed**: ジョブが完了しました。 |
| **deviceJobStatistics** |ジョブの実行に関する統計情報。 |
| | **deviceJobStatistics** プロパティ: |
| | **deviceJobStatistics.deviceCount**: ジョブ内のデバイスの数。 |
| | **deviceJobStatistics.failedCount**: ジョブが失敗したデバイスの数。 |
| | **deviceJobStatistics.succeededCount**: ジョブが成功したデバイスの数。 |
| | **deviceJobStatistics.runningCount**: 現在ジョブが実行中であるデバイスの数。 |
| | **deviceJobStatistics.pendingCount**: 現在ジョブの実行が保留されているデバイスの数。 |

### <a name="additional-reference-material"></a>参考資料

IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント](iot-hub-devguide-endpoints.md): 各 IoT Hub でランタイムと管理の操作のために公開される、さまざまなエンドポイントについて説明します。

* [調整とクォータ](iot-hub-devguide-quotas-throttling.md): IoT Hub サービスに適用されるクォータと、サービスを使用するときに想定される調整の動作について説明します。

* [Azure IoT device SDK とサービス SDK](iot-hub-devguide-sdks.md): IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。

* [デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語](iot-hub-devguide-query-language.md): IoT Hub クエリ言語について説明します。 このクエリ言語を使用して、IoT Hub からデバイス ツインとジョブに関する情報を取得します。

* [IoT Hub の MQTT サポート](iot-hub-mqtt-support.md): IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次の手順

この記事で説明した概念を試すには、次の IoT Hub のチュートリアルをご覧ください。

* [ジョブのスケジュールとブロードキャスト](iot-hub-node-node-schedule-jobs.md)