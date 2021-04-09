---
title: Azure IoT Central ソリューションでデバイス コマンドを使用する方法
description: Azure IoT Central ソリューションでデバイス コマンドを使用する方法。 このチュートリアルでは、デバイス開発者として、Azure IoT Central アプリケーションのクライアント アプリでデバイス コマンドを使用する方法を示します。
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e53bf377a7ef8f2293debd288ba25ef8f04ff4fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98611000"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Azure IoT Central ソリューションでコマンドを使用する方法

このハウツー ガイドでは、デバイス開発者として、デバイス テンプレートで定義されているコマンドを使用する方法を示します。

オペレーターは、IoT Central UI を使用して、デバイスでコマンドを呼び出すことができます。 コマンドによってデバイスの動作が制御されます。 たとえば、オペレーターはデバイスを再起動するか、または診断データを収集するためにコマンドを呼び出すことがあります。

デバイスは次のことを実行できます。

* 直ちにコマンドに応答する。
* コマンドを受信したときに IoT Central に応答し、その後、"*実行時間の長いコマンド*" が完了したら IoT Central に通知する。

既定では、コマンドはデバイスが接続されていることを予期し、デバイスに到達できない場合は失敗します。 デバイス テンプレート UI で **[オフラインの場合にキューに入れる]** オプションを選択した場合は、デバイスがオンラインになるまでコマンドがキューに入れられることがあります。 これらの "*オフライン コマンド*" は、この記事の後の方にある別のセクションで説明されています。

## <a name="define-your-commands"></a>コマンドを定義する

標準コマンドは、デバイスに何かを実行するよう指示するためにデバイスに送信されます。 コマンドには、追加情報を持つパラメーターを含めることができます。 たとえば、デバイスのバルブを開くためのコマンドには、バルブをどれだけ開くかを指定するパラメーターを含めることができます。 コマンドはまた、デバイスがコマンドを完了したときに戻り値を受信することもできます。 たとえば、デバイスに何らかの診断を実行するよう指示するコマンドは、診断レポートを戻り値として受信することができます。

コマンドは、デバイス テンプレートの一部として定義されます。 次のスクリーンショットは、 **[サーモスタット]** デバイス テンプレート内の **[最大-最小レポートの取得]** コマンドの定義を示しています。 このコマンドには、要求パラメーターと応答パラメーターの両方が含まれています。 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="[サーモスタット] デバイス テンプレート内の [最大-最小レポートの取得] コマンドを示すスクリーンショット":::

コマンド機能の構成設定を次の表に示します。

| フィールド             |説明|
|-------------------|-----------|
|表示名       |ダッシュボードとフォームで使用されるコマンド値。|
| 名前            | コマンドの名前。 IoT Central によって表示名からこのフィールドの値が生成されますが、必要に応じて独自の値を選択できます。 このフィールドは英数字である必要があります。 デバイス コードでは、この **[名前]** 値を使用します。|
| 機能の種類 | コマンド。|
| オフラインの場合にキューに入れる | このコマンドを "*オフライン*" コマンドにするかどうか。 |
| 説明     | コマンド機能の説明。|
| 解説     | コマンド機能に関するコメント。|
| Request     | デバイス コマンドのペイロード。|
| Response     | デバイス コマンドの応答のペイロード。|

次のスニペットは、デバイス モデルでのコマンドの JSON 表現を示しています。 この例では、応答値は、複数のフィールドを持つ複雑な **オブジェクト** の種類です。

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> デバイス モデルは、[デバイス テンプレート] ページからエクスポートできます。

次のフィールドを使用して、このコマンド定義を UI のスクリーンショットに関連付けることができます。

* 機能の種類 `Command` を指定する `@type`
* コマンド値の `name`。

表示名や説明などのオプション フィールドを使用すると、インターフェイスや機能に詳細を追加できます。

## <a name="standard-commands"></a>標準コマンド

このセクションでは、デバイスがコマンドを受信後にすぐに応答値を送信する方法を示します。

次のコード スニペットは、デバイスが直ちにコマンドに応答して成功コードを送信する方法を示しています。

> [!NOTE]
> この記事では、簡単にするために Node.js を使用します。 他の言語の例については、「[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](tutorial-connect-device.md)」のチュートリアルを参照してください。

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

`onDeviceMethod` の呼び出しによって `commandHandler` メソッドが設定されます。 このコマンド ハンドラーは、次の処理を行います。

1. コマンドの名前をチェックします。
1. `getMaxMinReport` コマンドの場合は、`getMaxMinReportObject` を呼び出して、返されるオブジェクトに含める値を取得します。
1. `sendCommandResponse` を呼び出して、IoT Central に応答を返送します。 この応答には、成功を示す `200` 応答コードが含まれます。

次のスクリーンショットは、成功したコマンドの応答が IoT Central UI にどのように表示されるかを示しています。

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="標準コマンドのコマンド ペイロードを表示する方法を示すスクリーンショット":::

## <a name="long-running-commands"></a>実行時間の長いコマンド

このセクションでは、デバイスがコマンドの完了を示す確認の送信を遅延させる方法を示します。

次のコード スニペットは、デバイスが実行時間の長いコマンドを実装する方法を示しています。

> [!NOTE]
> この記事では、簡単にするために Node.js を使用します。 他の言語の例については、「[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](tutorial-connect-device.md)」のチュートリアルを参照してください。

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

`onDeviceMethod` の呼び出しによって `commandHandler` メソッドが設定されます。 このコマンド ハンドラーは、次の処理を行います。

1. コマンドの名前をチェックします。
1. `sendCommandResponse` を呼び出して、IoT Central に応答を返送します。 この応答には、保留中の結果を示す `202` 応答コードが含まれます。
1. 実行時間の長い操作を完了します。
1. コマンドと同じ名前を持つ報告されるプロパティを使用して、IoT Central にコマンドが完了したことを通知します。

次のスクリーンショットは、IoT Central UI が 202 応答コードを受信したときにコマンド応答がどのように表示されるかを示しています。

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="デバイスからの即座の応答を示すスクリーンショット":::

次のスクリーンショットは、コマンドが完了したことを示すプロパティの更新を受信したときの IoT Central UI を示しています。

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="実行時間の長いコマンドが完了したことを示すスクリーンショット":::

## <a name="offline-commands"></a>オフライン コマンド

このセクションでは、デバイスがオフライン コマンドを処理する方法を示します。 デバイスはオンラインである場合、受信したオフライン コマンドを直ちに処理できます。 デバイスはオフラインである場合、次回 IoT Central に接続したときにオフライン コマンドを処理します。 デバイスは、オフライン コマンドに応答して戻り値を送信することができません。

> [!NOTE]
> この記事では、簡単にするために Node.js を使用します。

次のスクリーンショットは、**GenerateDiagnostics** という名前のオフライン コマンドを示しています。 要求パラメーターは、**StartTime** という名前の datetime プロパティと **Bank** という名前の整数列挙型プロパティを持つオブジェクトです。

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="オフライン コマンドの UI を示すスクリーンショット":::

次のコード スニペットは、クライアントがオフライン コマンドをリッスンし、メッセージの内容を表示する方法を示しています。

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

前のコード スニペットからの出力は、**StartTime** 値と **Bank** 値を持つペイロードを示しています。 このプロパティ リストでは、**method-name** のリスト項目にコマンド名が含まれています。

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

> [!NOTE]
> オフライン コマンドの既定の有効期間は 24 時間であり、その後、メッセージは期限切れになります。

## <a name="next-steps"></a>次のステップ

これで、Azure IoT Central アプリケーションでコマンドを使用する方法を学習しました。次に、コマンド パラメーターの詳細については[ペイロード](concepts-telemetry-properties-commands.md)に関するページを、またさまざまな言語での完全なコード サンプルを確認するには「[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](tutorial-connect-device.md)」を参照してください。
