---
title: モジュール ツインの監視 - Azure IoT Edge
description: デバイス ツインとモジュール ツインを解釈して、接続性と正常性を判断する方法。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0b7013979199eefa873a651d99e87dc8b2c47856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201600"
---
# <a name="monitor-module-twins"></a>モジュール ツインの監視

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Hub のモジュール ツインを利用すると、IoT Edge デプロイの接続性と正常性の監視が可能になります。 モジュール ツインでは、実行中のモジュールのパフォーマンスに関する有益な情報が、IoT ハブに格納されます。 [IoT Edge エージェント](iot-edge-runtime.md#iot-edge-agent)および [IoT Edge ハブ](iot-edge-runtime.md#iot-edge-hub)の各ランタイム モジュールでは、モジュール ツインである `$edgeAgent` と `$edgeHub` が保持されています。

* `$edgeAgent` には、IoT Edge エージェントと IoT Edge ハブの両方のランタイム モジュールとカスタム モジュールに関する、正常性と接続性のデータが含まれています。 IoT Edge エージェントでは、モジュールのデプロイとそれらの監視、および Azure IoT ハブに対する接続状態のレポートの作成が行われます。
* `$edgeHub` には、デバイス上とお使いの Azure IoT ハブ上で実行されている IoT Edge ハブ間での通信に関するデータが含まれています。 これには、ダウンストリーム デバイスからの受信メッセージの処理が含まれます。 IoT Edge ハブでは、Azure IoT ハブと IoT Edge デバイスおよびモジュール間での通信処理が行われます。

データはメタデータ、タグに加えて、モジュール ツインの JSON 構造内での目的のおよび報告されたプロパティ セットに編成されます。 deployment.json ファイルに指定した目的のプロパティは、モジュール ツインにコピーされます。 IoT Edge エージェントと IoT Edge ハブではそれぞれ、モジュールに対する報告されたプロパティが更新されます。

同様に、deployment.json ファイル内でカスタム モジュールに対して指定された目的のプロパティが、モジュール ツインにコピーされます。ただし、報告されたプロパティ値の指定は、ソリューションによって行われます。

この記事では、Azure portal、Azure CLI、および Visual Studio Code でのモジュール ツインの確認方法について説明します。 デバイスによるデプロイの受信状況を監視する方法については、「[IoT Edge デプロイの監視](how-to-monitor-iot-edge-deployments.md)」を参照してください。 モジュール ツインの概念の概要については、「[IoT Hub のモジュール ツインの理解と使用](../iot-hub/iot-hub-devguide-module-twins.md)」を参照してください。

> [!TIP]
> IoT Edge デバイスが IoT ハブから切断された場合、ランタイム モジュールの報告されたプロパティが古くなる可能性があります。 `$edgeAgent` モジュールに [ping](how-to-edgeagent-direct-method.md#ping) を実行して、接続が失われたかどうかを判断できます。

## <a name="monitor-runtime-module-twins"></a>ランタイム モジュール ツインを監視する

デプロイの接続の問題をトラブルシューティングするには、IoT Edge エージェントと IoT Edge ハブのランタイム モジュール ツインを確認してから、その他のモジュールにドリルダウンします。

### <a name="monitor-iot-edge-agent-module-twin"></a>IoT Edge エージェントのモジュール ツインを監視する

次の JSON は、Visual Studio Code 内の `$edgeAgent` モジュール ツインを示しています。ほとんどの JSON セクションは折りたたまれた状態になっています。

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

JSON は上から順に、次のセクションで表すことができます。

* Metadata - 接続データが含まれます。 興味深いことに、IoT Edge エージェントの接続状態は常に、切断の状態である `"connectionState": "Disconnected"` になっています。 そのような接続状態になっている理由は、デバイスからクラウドへ (D2C) のメッセージに関連しており、IoT Edge エージェントでは D2C メッセージの送信は行われません。
* Properties - `desired` および `reported` サブセクションが含まれます。
* Properties.desired - (折りたたまれて表示されています) deployment.json ファイル内に、演算子で設定されたプロパティ値が必要です。
* Properties.reported - IoT Edge エージェントによって報告された最新のプロパティ値。

`properties.desired` および `properties.reported` セクションは両方ともよく似た構造になっており、スキーマ、バージョン、およびランタイム情報に対する追加のメタデータが含まれます。 また、カスタム モジュール (`SimulatedTemperatureSensor` など) に対する `modules` セクションと、`$edgeAgent` および `$edgeHub` ランタイム モジュールに対する `systemModules` セクションも含まれます。

報告されたプロパティ値を目的の値と比較することで、不一致を判定して切断を特定し、トラブルシューティングの問題に役立てることができます。 これらの比較を行う場合は、調査しているプロパティの `metadata` セクションにある `$lastUpdated` の報告された値を確認します。

トラブルシューティングの際には、次のプロパティを調べることが重要になります。

* **exitcode** - ゼロ以外の値はすべて、モジュールがエラーによって停止されたことを示します。 ただし、モジュールが意図的に停止状態に設定された場合は、エラーコード 137 または 143 が使用されます。

* **lastStartTimeUtc** - コンテナーが最後に開始された **DateTime** が表示されます。 コンテナーが開始されていなかった場合、この値は 0001-01-01T00:00:00Z になります。

* **lastExitTimeUtc** - コンテナーが最後に終了された **DateTime** が表示されます。 コンテナーが実行中で、停止されなかった場合、この値は 0001-01-01T00:00:00Z になります。

* **runtimeStatus** - 次のいずれかの値になります。

    | 値 | 説明 |
    | --- | --- |
    | unknown | デプロイが作成されるまでの既定の状態。 |
    | backoff | モジュールは、開始するようにスケジュールされていますが、現在は実行されていません。 再起動中に状態の変更を行っているモジュールには、この値が適しています。 障害が発生しているモジュールにおいて、クールオフ期間中に再起動を待機している場合、そのモジュールは backoff 状態になります。 |
    | 実行中 | モジュールが現在実行されていることを示します。 |
    | unhealthy | 正常性プローブ チェックがエラーになったか、またはタイムアウトになったことを示します。 |
    | stopped | モジュールが正常に終了したことを示します (終了コードはゼロです)。 |
    | 失敗 | モジュールがエラーの終了コード (ゼロ以外) で終了したことを示します。 有効な再起動ポリシーに応じて、モジュールがこの状態から backoff に戻される場合があります。 この状態は、モジュール上で回復不能なエラーが発生したことを示している可能性があります。 Microsoft Monitoring Agent (MMA) によってモジュールを再生できなくなり、新しいデプロイが必要になったときに、エラーが発生します。 |

詳細については、「[EdgeAgent の報告されるプロパティ](module-edgeagent-edgehub.md#edgeagent-reported-properties)」を参照してください。

### <a name="monitor-iot-edge-hub-module-twin"></a>IoT Edge ハブのモジュール ツインを監視する

次の JSON は、Visual Studio Code 内の `$edgeHub` モジュール ツインを示しています。ほとんどの JSON セクションは折りたたまれた状態になっています。

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

JSON は上から順に、次のセクションで表すことができます。

* Metadata - 接続データが含まれます。

* Properties - `desired` および `reported` サブセクションが含まれます。
* Properties.desired - (折りたたまれて表示されています) deployment.json ファイル内に、演算子で設定されたプロパティ値が必要です。
* Properties.reported - IoT Edge ハブによって報告された最新のプロパティ値。

ダウンストリーム デバイスで問題が発生している場合、まずは、このデータから調べることをお勧めします。

## <a name="monitor-custom-module-twins"></a>カスタム モジュール ツインを監視する

カスタムモジュールの接続に関する情報は、IoT Edge エージェントのモジュール ツイン内に保持されます。 カスタム モジュールのモジュール ツインは、主にソリューションのデータを保持するために使用されます。 deployment.json ファイル内にご自身で定義した目的のプロパティがモジュール ツインに反映され、モジュール上では必要に応じて報告されたプロパティ値が更新されます。

[Azure IoT Hub のデバイス SDK](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) に任意のプログラミング言語を使用して、モジュールのアプリケーション コードに基づいてモジュール ツイン内の報告されたプロパティ値を更新することができます。 次の手順では、これを行うために Azure SDK for .NET を使用し、[SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) モジュールからのコードを利用しています。

1. [CreateFromEnvironmentAysnc](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync) メソッドを使って、[ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) のインスタンスを作成します。

1. [GetTwinAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync) メソッドを使って、モジュール ツインのプロパティのコレクションを取得します。

1. [SetDesiredPropertyUpdateCallbackAsync](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync) メソッドを使って、目的のプロパティへの変更をキャッチするリスナー (コールバックを渡す) を作成します。

1. コールバック メソッド内で、設定するプロパティ値の [TwinCollection](/dotnet/api/microsoft.azure.devices.shared.twincollection) を渡し、[UpdateReportedPropertiesAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient) メソッドを使ってモジュール ツイン内の報告されたプロパティを更新します。

## <a name="access-the-module-twins"></a>モジュール ツインにアクセスする

Azure IoT Hub 上、Visual Studio Code 上で、また、Azure CLI を使用して、モジュール ツインの JSON を確認できます。

### <a name="monitor-in-azure-iot-hub"></a>Azure IoT Hub 上で監視する

モジュール ツインの JSON を表示するには:

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。
1. 左ペインのメニューで、 **[IoT Edge]** を選択します。
1. **[IoT Edge デバイス]** タブ上で、監視する対象のモジュールを含むデバイスの **[デバイス ID]** を選択します。
1. **[モジュール]** タブからモジュール名を選択して、上部のメニュー バーから **[モジュール ID ツイン]** を選択します。

  ![Azure portal 上で表示するモジュール ツインを選択します](./media/how-to-monitor-module-twins/select-module-twin.png)

"このモジュールのモジュール ID は存在しません" というメッセージが表示された場合、このエラーは、ID を作成した元のソリューションが利用できなくなっていることを示します。

### <a name="monitor-module-twins-in-visual-studio-code"></a>Visual Studio Code 上でモジュール ツインを監視する

モジュール ツインを確認して編集するには:

1. まだインストールしていない場合は、Visual Studio Code 向けの [Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)をインストールします。
1. **[エクスプローラー]** 上で、 **[Azure IoT Hub]** を展開して、監視する対象のモジュールを含むデバイスを展開します。
1. モジュールを右クリックして、 **[Edit Module Twin]\(モジュール ツインの編集\)** を選択します。 モジュール ツインの一時ファイルがお使いのコンピューターにダウンロードされ、Visual Studio Code 上に表示されます。

  ![Visual Studio Code 上で編集するモジュール ツインを取得する](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

変更を行う場合は、エディター内のコードの上にある **[Update Module Twin]\(モジュール ツインの更新\)** を選択して、IoT ハブへの変更を保存します。

  ![Visual Studio Code 上でモジュール ツインを更新する](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Azure CLI 上でモジュール ツインを監視する

IoT Edge が実行中かどうかを確認するには、[az iot hub invoke-module-method](how-to-edgeagent-direct-method.md#ping) を使用して IoT Edge エージェントに ping を実行します。

[az iot hub module-twin](/cli/azure/ext/azure-iot/iot/hub/module-twin) 構造では、以下のコマンドが提供されています。

* **az iot hub module-twin show** - モジュール ツインの定義を表示します。
* **az iot hub module-twin update** - モジュール ツインの定義を更新します。
* **az iot hub module-twin replace** - モジュール ツインの定義を対象の JSON に置き換えます。

## <a name="next-steps"></a>次のステップ

[組み込みのダイレクト メソッドを使用して EdgeAgent と通信する](how-to-edgeagent-direct-method.md)方法について学習します。
