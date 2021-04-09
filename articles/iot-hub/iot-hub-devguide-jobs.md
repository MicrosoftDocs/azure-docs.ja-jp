---
title: Azure IoT Hub ジョブについて | Microsoft Docs
description: 開発者ガイド - IoT Hub に接続されている複数のデバイスで実行するジョブのスケジュール設定。 ジョブはタグと必要なプロパティを更新でき、複数のデバイス上でダイレクト メソッドを呼び出すことができます。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: mqtt
ms.openlocfilehash: 5c14e8cfcbf8df86b0f71d6b12025594d2e648c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "81730103"
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

次のスニペットでは、ジョブを使ってデバイス上で[ダイレクト メソッド](iot-hub-devguide-direct-methods.md)を実行するための HTTPS 1.1 要求の詳細を示します。

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
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

次のスニペットは、contoso-hub-1 のすべてのデバイスで testMethod という名前のダイレクト メソッドを呼び出すようにスケジュールされたジョブの要求と応答を示しています。

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>デバイス ツインのプロパティを更新するジョブ

次のスニペットでは、ジョブを使ってデバイス ツインのプロパティを更新するための HTTPS 1.1 要求の詳細を示します。

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> *updateTwin* プロパティでは、`etag="*"` など、有効な etag と一致する必要があります。

次のスニペットは、contoso-hub-1 の test-device のデバイス ツイン プロパティを更新するようにスケジュールされたジョブの要求と応答を示しています。

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>ジョブの進行状況の照会

次のスニペットでは、ジョブを照会するための HTTPS 1.1 要求の詳細を示します。

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

ContinuationToken は、応答から提供されます。

それぞれのデバイスにおけるジョブの実行状態は、[デバイス ツイン、ジョブ、メッセージ ルーティングの IoT Hub クエリ言語](iot-hub-devguide-query-language.md)を使用して照会することができます。

## <a name="jobs-properties"></a>ジョブのプロパティ

次の一覧は、ジョブまたはジョブの結果を照会するときに使うことができるプロパティとその説明です。

| プロパティ | 説明 |
| --- | --- |
| **jobId** |アプリケーションが提供するジョブの ID。 |
| **startTime** |アプリケーションが提供するジョブの開始時刻 (ISO 8601)。 |
| **endTime** |IoT Hub が提供するジョブの完了時の日付 (ISO 8601)。 ジョブが 'completed' 状態に達した後でのみ有効です。 |
| **type** |ジョブの種類: |
| | **scheduleUpdateTwin**: 必要なプロパティまたはタグを更新するために使用するジョブ。 |
| | **scheduleDeviceMethod**: デバイス ツインでデバイス メソッドを呼び出すために使用するジョブ。 |
| **status** |ジョブの現在の状態。 状態の可能値: |
| | **pending**: スケジュールが設定され、ジョブ サービスによって選択されるために待機しています。 |
| | **scheduled**: 将来の時刻のスケジュールが設定されています。 |
| | **running**: 現在アクティブなジョブです。 |
| | **canceled**: ジョブは取り消されました。 |
| | **failed**: ジョブは失敗しました。 |
| | **completed**: ジョブは完了しています。 |
| **deviceJobStatistics** |ジョブの実行に関する統計情報。 |
| | **deviceJobStatistics** プロパティ: |
| | **deviceJobStatistics.deviceCount**: ジョブ内のデバイスの数。 |
| | **deviceJobStatistics.failedCount**: ジョブが失敗したデバイスの数。 |
| | **deviceJobStatistics.succeededCount**: ジョブが成功したデバイスの数。 |
| | **deviceJobStatistics.runningCount**: 現在ジョブが実行中であるデバイスの数。 |
| | **deviceJobStatistics.pendingCount**: ジョブの実行が保留中であるデバイスの数。 |

### <a name="additional-reference-material"></a>参考資料

IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント](iot-hub-devguide-endpoints.md): 各 IoT Hub でランタイムと管理の操作のために公開される、さまざまなエンドポイントについて説明します。

* [調整とクォータ](iot-hub-devguide-quotas-throttling.md): IoT Hub サービスに適用されるクォータと、サービスを使用するときに想定される調整の動作について説明します。

* [Azure IoT device SDK とサービス SDK](iot-hub-devguide-sdks.md): IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。

* [デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語](iot-hub-devguide-query-language.md): IoT Hub クエリ言語について説明します。 このクエリ言語を使用して、IoT Hub からデバイス ツインとジョブに関する情報を取得します。

* [IoT Hub の MQTT サポート](iot-hub-mqtt-support.md): IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次のステップ

この記事で説明した概念を試すには、次の IoT Hub のチュートリアルをご覧ください。

* [ジョブのスケジュールとブロードキャスト](iot-hub-node-node-schedule-jobs.md)