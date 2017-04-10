---
title: "Azure IoT Hub のデバイス メソッドについて | Microsoft Docs"
description: "開発者ガイド - ダイレクト メソッドを使用して、サービス アプリからデバイス上のコードを呼び出す。"
services: iot-hub
documentationcenter: .net
author: nberdy
manager: timlt
editor: 
ms.assetid: 9f0535f1-02e6-467a-9fc4-c0950702102d
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: ce7860e291c71e825561caf3ee7eafe904101799
ms.lasthandoff: 03/10/2017


---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>IoT Hub からのダイレクト メソッドの呼び出しについて
## <a name="overview"></a>概要
IoT Hub には、クラウドからデバイス上のダイレクト メソッドを呼び出す機能が備わっています。 ダイレクト メソッドは、デバイスとの要求/応答型通信を表し、すぐに要求の成功または失敗が確定する (ユーザーが指定したタイムアウト後) という点で HTTP 呼び出しに似ています。 これは、デバイスがオフラインの場合に SMS ウェイクアップを送信するような、デバイスが応答できるかによって、一連の即時のアクションが異なってくるシナリオで便利です (SMS はメソッドの呼び出しよりもコストがかかります)。

各デバイス メソッドは、1 つのデバイスをターゲットとします。 [Jobs][lnk-devguide-jobs] を使用すると、複数のデバイス上のダイレクト メソッドを呼び出すことができ、接続されていないデバイスに対するメソッドの呼び出しをスケジュール設定できます。

IoT Hub で**サービス接続**のアクセス許可を持っていれば、誰でもデバイスでメソッドを呼び出すことができます。

### <a name="when-to-use"></a>使用時の注意
ダイレクト メソッドは要求/応答型パターンに従うメソッドであり、すぐに確認する必要がある通信向けであり、通常はデバイスを対話式で制御する (例: ファンをオンにする) ときに使用されます。

必要なプロパティ、ダイレクト メソッド、または cloud-to-device メッセージの使用方法の詳細については、「[cloud-to-device 通信に関するガイダンス][lnk-c2d-guidance]」を参照してください。

## <a name="method-lifecycle"></a>メソッドのライフサイクル
ダイレクト メソッドはデバイス上に実装され、正しくインスタンス化するには、メソッドのペイロードに&0; 個以上の入力が必要になります。 ダイレクト メソッドは、サービス向け URI (`{iot hub}/twins/{device id}/methods/`) を通じて呼び出します。 デバイスは、デバイス固有の MQTT トピック (`$iothub/methods/POST/{method name}/`) からダイレクト メソッドを受け取ります。 今後、ダイレクト メソッドに対応するデバイス側のネットワーク プロトコルが追加される予定です。

> [!NOTE]
> デバイス上のダイレクト メソッドを呼び出す場合、プロパティ名と値には ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}`` を除く US-ASCII 印刷可能英数字のみを使用できます。
> 
> 

ダイレクト メソッドは同期型であり、タイムアウト期間 (既定では 30 秒、最長 3600 秒に設定可能) 後に成功または失敗します。 ダイレクト メソッドは、デバイスがオンラインでコマンドを受け取っている場合のみデバイスを操作する対話型のシナリオ (携帯電話から照明を付けるなどの操作) で便利です。 このようなシナリオでは、成功か失敗かを即座に確認し、クラウド サービスがその結果に対してできるだけ早く対応することが必要になります。 デバイスはメッセージの結果として何らかのメッセージ本文を返すことがありますが、メソッドにはその機能は必要ありません。 順番の保証や、メソッドの呼び出しの同時実行セマンティクスはありません。

ダイレクト メソッドは、クラウド側からは HTTP のみに、デバイス側からは MQTT のみになります。

メソッドの要求および応答のペイロードは、最大 8 KB の JSON ドキュメントになります。

## <a name="reference-topics"></a>参照トピック:
以下の参照トピックは、ダイレクト メソッドの使用に関する詳細情報を提供しています。

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>バックエンド アプリからダイレクト メソッドを呼び出す
### <a name="method-invocation"></a>メソッドの呼び出し
デバイスでのダイレクト メソッドの呼び出しは HTTP 呼び出しであり、次の項目で構成されます。

* デバイスに固有の *URI* (`{iot hub}/twins/{device id}/methods/`)
* POST *メソッド*
* *ヘッダー*。認証、要求 ID、コンテンツの種類、コンテンツのエンコーディングを含む
* 透過的な JSON *本文*。次の形式になります。

```
{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

タイムアウトは秒単位です。 タイムアウトが設定されていない場合の既定値は 30 秒です。

### <a name="response"></a>応答
バックエンド アプリは次の項目で構成される応答を受け取ります。

* *HTTP 状態コード*。接続されていないデバイスの 404 エラーを含む、IoT Hub からのエラーに使用される
* *ヘッダー*。ETag、要求 ID、コンテンツの種類、コンテンツのエンコーディングを含む
* JSON *本文*。次の形式になります。

```
{
    "status" : 201,
    "payload" : {...}
}
```

   `status` と `body` の両方ともデバイスによって提供され、デバイス自身の状態コードまたは説明とともに応答する場合に使用されます。

## <a name="handle-a-direct-method-on-a-device"></a>デバイスでダイレクト メソッドを処理する
### <a name="method-invocation"></a>メソッドの呼び出し
デバイスは MQTT トピックに関するダイレクト メソッド要求を受け取ります。`$iothub/methods/POST/{method name}/?$rid={request id}`

デバイスが受け取る本文は次の形式になります。

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

メソッドの要求は QoS 0 です。

### <a name="response"></a>Response
デバイスは応答を `$iothub/methods/res/{status}/?$rid={request id}` に送信します。この場合、

* `status` プロパティは、デバイスから提供される、メソッド実行の状態です。
* `$rid` プロパティは、IoT Hub から受け取るメソッド呼び出しの要求 ID です。

本文はデバイスごとに設定され、任意の状態を設定できます。

## <a name="additional-reference-material"></a>参考資料
IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント][lnk-endpoints]: 各 IoT Hub でランタイムと管理の操作のために公開される、さまざまなエンドポイントについて説明します。
* [調整とクォータ][lnk-quotas]: IoT Hub サービスに適用されるクォータと、サービスを使用するときに想定される調整の動作について説明します。
* [Azure IoT device SDK とサービス SDK][lnk-sdks]: IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。
* [IoT Hub のツインおよびジョブ向けのクエリ言語][lnk-query]: IoT Hub からデバイス ツインおよびジョブに関する情報を取得する際に使用できる IoT Hub のクエリ言語について説明します。
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]: IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次のステップ
ダイレクト メソッドの使用方法を理解できたら、次の IoT Hub 開発者ガイド トピックも参考にしてください。

* [複数デバイスでのジョブをスケジュール設定する][lnk-devguide-jobs]

この記事で説明した概念を試す場合は、次の IoT Hub のチュートリアルをご利用ください。

* [ダイレクト メソッドを使用する][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md

