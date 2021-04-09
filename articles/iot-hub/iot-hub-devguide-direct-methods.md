---
title: Azure IoT Hub のデバイス メソッドについて | Microsoft Docs
description: 開発者ガイド - ダイレクト メソッドを使用して、サービス アプリからデバイス上のコードを呼び出す。
author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: philmea
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: b75e859fc1237bc88bee464cef423b7289810fa8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147803"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>IoT Hub からのダイレクト メソッドの呼び出しについて

IoT Hub には、クラウドからデバイス上のダイレクト メソッドを呼び出す機能が備わっています。 ダイレクト メソッドは、デバイスとの要求/応答型通信を表し、すぐに要求の成功または失敗が確定する (ユーザーが指定したタイムアウト後) という点で HTTP 呼び出しに似ています。 この方法は、デバイスが応答できるかどうかに応じて即座に実行するアクションが異なるシナリオで便利です。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

各デバイス メソッドは、1 つのデバイスをターゲットとします。 「[複数デバイスでのジョブをスケジュール設定する](iot-hub-devguide-jobs.md)」では、複数のデバイス上のダイレクト メソッドを呼び出し、接続されていないデバイスに対するメソッドの呼び出しをスケジュール設定する方法を紹介しています。

IoT Hub で **サービス接続** のアクセス許可を持っていれば、誰でもデバイスでメソッドを呼び出すことができます。

ダイレクト メソッドは要求/応答型パターンに従うメソッドであり、結果をすぐに確認する必要がある通信向けです。 たとえばデバイスを対話式で制御する (例: ファンをオンにする) ときに使用されます。

必要とされるプロパティ、ダイレクト メソッド、または C2D メッセージの使用方法の詳細については、「[cloud-to-device 通信に関するガイダンス](iot-hub-devguide-c2d-guidance.md)」を参照してください。

## <a name="method-lifecycle"></a>メソッドのライフサイクル

ダイレクト メソッドはデバイス上に実装され、正しくインスタンス化するには、メソッドのペイロードに 0 個以上の入力が必要になります。 ダイレクト メソッドは、サービス向け URI (`{iot hub}/twins/{device id}/methods/`) を通じて呼び出します。 デバイスは、デバイス固有の MQTT トピック (`$iothub/methods/POST/{method name}/`) または AMQP リンク (`IoThub-methodname` および `IoThub-status` アプリケーション プロパティ) を通じてダイレクト メソッドを受け取ります。

> [!NOTE]
> デバイス上のダイレクト メソッドを呼び出す場合、プロパティ名と値には ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}`` を除く US-ASCII 印刷可能英数字のみを使用できます。
> 

ダイレクト メソッドは同期型であり、タイムアウト期間後に成功または失敗します (既定では30 秒、5 - 300 秒の範囲で設定可能)。 ダイレクト メソッドは、デバイスがオンラインでコマンドを受け取っている場合のみデバイスを操作する対話型のシナリオで便利です。 たとえば、携帯電話からの照明を点灯させる場合です。 このようなシナリオでは、成功か失敗かを即座に確認し、クラウド サービスがその結果に対してできるだけ早く対応することが必要になります。 デバイスはメッセージの結果として何らかのメッセージ本文を返すことがありますが、メソッドにはその機能は必要ありません。 順番の保証や、メソッドの呼び出しのコンカレンシー セマンティクスはありません。

ダイレクト メソッドは、クラウド側からは HTTPS のみになります。また、デバイス側からは MQTT、AMQP、MQTT over WebSocket、または AMQP over WebSocket になります。

メソッドの要求および応答のペイロードは、最大 128 KB の JSON ドキュメントになります。

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>バックエンド アプリからダイレクト メソッドを呼び出す

次に、バックエンド アプリからダイレクト メソッドを呼び出します。

### <a name="method-invocation"></a>メソッドの呼び出し

デバイスでのダイレクト メソッドの呼び出しは HTTPS 呼び出しであり、次で構成されます。

* [API バージョン](/rest/api/iothub/service/devices/invokemethod)が適合するデバイスに固有の *要求の URI*:

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* POST *メソッド*

* *ヘッダー*。承認、要求 ID、コンテンツの種類、コンテンツのエンコーディングを含む。

* 透過的な JSON *本文*。次の形式になります。

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

要求で `responseTimeoutInSeconds` として指定された値は、IoT Hub サービスがデバイスでのダイレクト メソッドの実行が完了するまで待機する必要がある時間です。 このタイムアウトは、少なくとも、デバイスによるダイレクト メソッドの予想実行時間と同じ長さに設定してください。 タイムアウトが指定されていない場合は、既定値の 30 秒が使用されます。 `responseTimeoutInSeconds` の最小値は 5 秒で、最大値は 300 秒です。

要求で `connectTimeoutInSeconds` として指定された値は、ダイレクト メソッドの呼び出し時に、IoT Hub サービスが接続されていないデバイスがオンラインになるまで待機する必要がある時間です。 既定値は 0 です。これは、ダイレクト メソッドの呼び出し時にデバイスが既にオンラインである必要があることを意味します。 `connectTimeoutInSeconds` の最大値は 300 秒です。

#### <a name="example"></a>例

この例では、Azure IoT Hub に登録されている IoT デバイス上でダイレクト メソッドを呼び出すための要求を安全に開始することができます。

まず、[Azure CLI 用の Microsoft Azure IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)を使用して、SharedAccessSignature を作成します。

```bash
az iot hub generate-sas-token -n <iothubName> -du <duration>
```

次に、Authorization ヘッダーを、新しく生成された SharedAccessSignature に置き換えます。次に、以下の `curl` コマンドの例の実装に一致するように、`iothubName`、`deviceId`、`methodName`、および `payload` パラメーターを変更します。  

```bash
curl -X POST \
  https://<iothubName>.azure-devices.net/twins/<deviceId>/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

変更したコマンドを実行して、指定したダイレクト メソッドを呼び出します。 要求が成功すると、HTTP 200 状態コードが返されます。

> [!NOTE]
> 上の例は、デバイスでダイレクト メソッドを呼び出す方法を示しています。  IoT Edge モジュールでダイレクト メソッドを呼び出す場合は、次に示すように URL 要求を変更する必要があります。

```bash
https://<iothubName>.azure-devices.net/twins/<deviceId>/modules/<moduleName>/methods?api-version=2018-06-30
```
### <a name="response"></a>Response

バックエンド アプリは、次の項目で構成されている応答を受け取ります。

* *HTTP 状態コード*:
  * 200 は、ダイレクト メソッドが正常に実行されたことを示します。
  * 404 は、デバイス ID が無効であること、またはダイレクト メソッドの呼び出し時とその後 `connectTimeoutInSeconds` の間、デバイスがオンラインになっていなかったことを示します (根本原因を把握するには、付随するエラー メッセージを使用してください)。
  * 504 は、`responseTimeoutInSeconds` 内のダイレクト メソッドの呼び出しにデバイスが応答していないことが原因で、ゲートウェイのタイムアウトが発生したことを示します。

* *ヘッダー*。ETag、要求 ID、コンテンツの種類、コンテンツのエンコーディングを含む。

* JSON *本文*。次の形式になります。

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    `status` と `body` の両方ともデバイスによって提供され、デバイス自身の状態コードまたは説明とともに応答する場合に使用されます。

### <a name="method-invocation-for-iot-edge-modules"></a>IoT Edge モジュールのメソッド呼び出し

モジュール ID を使用したダイレクト メソッドの呼び出しは、[IoT Service Client C# SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) でサポートされています。

この目的で、`ServiceClient.InvokeDeviceMethodAsync()` メソッドを使用し、`deviceId` と `moduleId` をパラメーターとして渡します。

## <a name="handle-a-direct-method-on-a-device"></a>デバイスでダイレクト メソッドを処理する

IoT デバイスでダイレクト メソッドを処理する方法を見てみましょう。

### <a name="mqtt"></a>MQTT

次のセクションでは、MQTT プロトコルを扱います。

#### <a name="method-invocation"></a>メソッドの呼び出し

デバイスは MQTT トピックに関するダイレクト メソッド要求を受け取ります (`$iothub/methods/POST/{method name}/?$rid={request id}`)。 デバイスごとのサブスクリプションの数は 5 に制限されます。 そのため、各ダイレクト メソッドを個別にサブスクライブしないことをお勧めします。 その代わり、`$iothub/methods/POST/#` をサブスクライブし、届けられたメッセージをメソッド名に基づいてフィルター処理することを検討してください。

デバイスが受け取る本文は次の形式になります。

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

メソッドの要求は QoS 0 です。

#### <a name="response"></a>Response

デバイスは応答を `$iothub/methods/res/{status}/?$rid={request id}` に送信します。この場合、

* `status` プロパティは、デバイスから提供される、メソッド実行の状態です。

* `$rid` プロパティは、IoT Hub から受け取るメソッド呼び出しの要求 ID です。

本文はデバイスごとに設定され、任意の状態を設定できます。

### <a name="amqp"></a>AMQP

次のセクションでは、AMQP プロトコルを扱います。

#### <a name="method-invocation"></a>メソッドの呼び出し

デバイスは、アドレス `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` に受信リンクを作成することによってダイレクト メソッド要求を受け取ります。

AMQP メッセージは、メソッド要求を表す受信リンクに到着します。 次のセクションが含まれます。

* 関連付け ID プロパティ。対応するメソッド応答で戻される要求 ID が含まれています。

* `IoThub-methodname` という名前のアプリケーション プロパティ。呼び出されるメソッドの名前が含まれています。

* AMQP メッセージ本文。JSON としてメソッド ペイロードが含まれています。

#### <a name="response"></a>Response

デバイスは、メソッド応答を返す送信リンクをアドレス `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` に作成します。

メソッドの応答は送信リンクで返され、以下のもので構成されています。

* 関連付け ID プロパティ。メソッドの要求メッセージで渡される要求 ID が含まれています。

* `IoThub-status` という名前のアプリケーション プロパティ。ユーザーが指定したメソッド状態が含まれています。

* AMQP メッセージ本文。JSON としてメソッド応答が含まれています。

## <a name="additional-reference-material"></a>参考資料

IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント](iot-hub-devguide-endpoints.md): 各 IoT Hub でランタイムと管理の操作のために公開される、さまざまなエンドポイントについて説明します。

* [調整とクォータ](iot-hub-devguide-quotas-throttling.md): IoT Hub の使用時に適用されるクォータと想定される調整の動作について説明します。

* [Azure IoT device SDK とサービス SDK](iot-hub-devguide-sdks.md): IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。

* [デバイス ツイン、ジョブ、メッセージ ルーティングの IoT Hub クエリ言語](iot-hub-devguide-query-language.md): デバイス ツインとジョブに関する情報を IoT Hub から取得するために使用できる IoT Hub クエリ言語について説明します。

* [IoT Hub の MQTT サポート](iot-hub-mqtt-support.md): IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次のステップ

ダイレクト メソッドの使用方法を理解できたら、次の IoT Hub 開発者ガイドの記事も参考にしてください。

* [複数デバイスでのジョブをスケジュール設定する](iot-hub-devguide-jobs.md)

この記事で説明した概念を試す場合は、次の IoT Hub のチュートリアルをご利用ください。

* [ダイレクト メソッドの使用](quickstart-control-device-node.md)
* [VS Code 用の Azure IoT Tools を使用したデバイス管理](iot-hub-device-management-iot-toolkit.md)