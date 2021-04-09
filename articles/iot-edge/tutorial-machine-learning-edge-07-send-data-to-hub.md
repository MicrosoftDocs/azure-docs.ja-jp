---
title: チュートリアル:透過的なゲートウェイ経由でデバイス データを送信する - Azure IoT Edge での Machine Learning
description: このチュートリアルでは、開発用マシンをシミュレートされた IoT Edge デバイスとして使用し、透過的なゲートウェイとして構成されたデバイスを経由することで、IoT Hub にデータを送信する方法を紹介します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 25320fb8cde0cc5a38f1edf4019a2f0e07cefe14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461098"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>チュートリアル:透過的なゲートウェイを介してデータを送信する

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事でも、シミュレートされたデバイスとして開発用 VM を使用します。 ただし、データを IoT Hub に直接送信する代わりに、デバイスは、透過的なゲートウェイとして構成された IoT Edge デバイスにデータを送信します。

シミュレートされたデバイスがデータを送信している間、IoT Edge デバイスの動作を監視します。 デバイスの実行が終了したら、ストレージ アカウント内のデータを確認し、すべてが期待どおりに機能したことを検証します。

この手順は、通常はクラウドまたはデバイス開発者によって実行されます。

チュートリアルのこのセクションで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * リーフ デバイスをビルドして実行する。
> * 生成されたデータが Azure BLOB ストレージに格納されていることを確認する。
> * 機械学習モデルによってデバイス データが分類されたことを検証する。

## <a name="prerequisites"></a>前提条件

この記事は、IoT Edge 上で Azure Machine Learning を使用するためのチュートリアルのシリーズの一部です。 シリーズの各記事は、前の記事の作業に基づいています。 この記事に直接アクセスしている場合は、シリーズの[最初の記事](tutorial-machine-learning-edge-01-intro.md)を参照してください。

## <a name="review-device-harness"></a>デバイス ハーネスを確認する

[DeviceHarness プロジェクト](tutorial-machine-learning-edge-03-generate-data.md)を再利用して、ダウンストリーム (またはリーフ) デバイスをシミュレートします。 透過的なゲートウェイに接続するには、2 つの追加処理が必要です。

* 証明書を登録して、ダウンストリーム IoT デバイスが、IoT Edge ランタイムによって使用されている証明機関を信頼するようにします。 このケースでのダウンストリーム デバイスは開発用 VM です。
* エッジ ゲートウェイの完全修飾ドメイン名 (FQDN) をデバイス接続文字列に追加します。

これら 2 つの項目を実装する方法をコードで見てみましょう。

1. 開発用コンピューターで Visual Studio Code を開きます。

1. **[ファイル]**  >  **[フォルダーを開く...]** を使用して、C:\\source\\IoTEdgeAndMlSample\\DeviceHarness を開きます。

1. Program.cs の InstallCertificate() メソッドに注目します。

1. 証明書のパスが見つかったら、コードは CertificateManager.InstallCACert メソッドを呼び出して証明書をコンピューターにインストールすることに注意してください。

1. 次に、TurbofanDevice クラスの GetIotHubDevice メソッドを見てみましょう。

1. ユーザーが "-g" オプションを使用してゲートウェイの FQDN を指定すると、その値は `gatewayFqdn` 変数としてこのメソッドに渡され、これがデバイス接続文字列に追加されます。

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>リーフ デバイスをビルドして実行する

1. DeviceHarness プロジェクトを Visual Studio Code で開いたまま、プロジェクトをビルドします。 **[ターミナル]** メニューの **[ビルド タスクの実行]** を選択し、 **[ビルド]** を選択します。

1. Azure portal で IoT Edge デバイス (Linux VM) に移動し、概要ページから **DNS 名** の値をコピーして、エッジ ゲートウェイの完全修飾ドメイン名 (FQDN) を見つけます。

1. IoT デバイス (Linux VM) を起動します (まだ実行されていない場合)。

1. Visual Studio Code ターミナルを開きます。 **[Terminal]\(ターミナル\)** メニューから **[New terminal]\(新しいターミナル\)** を選択し、次のコマンドを実行します。`<edge_device_fqdn>` は、IoT Edge デバイス (Linux VM) からコピーした DNS 名に置き換えてください。

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

1. アプリケーションは開発用コンピューターに証明書をインストールしようとします。 そのとき、セキュリティの警告を受け入れます。

1. IoT Hub 接続文字列の入力を求められたら、Azure IoT Hub デバイス パネル上の省略記号 ( **...** ) をクリックし、 **[Copy IoT Hub Connection String]\(IoT Hub 接続文字列をコピー\)** を選択します。 ターミナルに値を貼り付けます。

1. 次のような出力が表示されます。

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   デバイス接続文字列に "GatewayHostName" が追加されていることに注意してください。これにより、デバイスは IoT Edge の透過的なゲートウェイを介して IoT Hub と通信します。

## <a name="check-output"></a>出力を確認する

### <a name="iot-edge-device-output"></a>IoT Edge デバイス出力

avroFileWriter モジュールからの出力は、IoT Edge デバイスを調べることで簡単に確認できます。

1. IoT Edge 仮想マシンに SSH で接続します。

1. ディスクに書き込まれたファイルを探します。

   ```bash
   find /data/avrofiles -type f
   ```

1. コマンドの出力は次の例のようになります。

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   実行のタイミングによっては、複数のファイルが存在する場合があります。

1. タイム スタンプに注意してください。 最後の変更時刻からの経過時間が 10 分を超えると、avroFileWriter モジュールはファイルをクラウドにアップロードします (avroFileWriter モジュールの uploader.py 内の MODIFIED\_FILE\_TIMEOUT を参照)。

1. 10 分が経過すれば、モジュールはファイルをアップロードするはずです。 アップロードが成功した場合、ファイルがディスクから削除されます。

### <a name="azure-storage"></a>Azure Storage

リーフ デバイスのデータ送信の結果は、データのルーティング先と予想されるストレージ アカウントを調べることで確認できます。

1. 開発用コンピューターで Visual Studio Code を開きます。

1. エクスプローラー ウィンドウの [AZURE STORAGE] (AZURE ストレージ) パネルで、ツリーを操作して、お使いのストレージ アカウントを探します。

1. **[BLOB コンテナー]** ノードを展開します。

1. チュートリアルの前の部分で行った作業から、RUL を伴ったメッセージが **ruldata** コンテナーに含まれると考えられます。 **ruldata** ノードを展開します。

1. 次のような BLOB ファイル名が 1 つ以上表示されます: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`。

1. ファイルの 1 つを右クリックし、 **[Download Blob]\(BLOB のダウンロード\)** を選択して、開発用コンピューターにファイルを保存します。

1. 次に、**uploadturbofanfiles** ノードを展開します。 前の記事では、avroFileWriter モジュールによってアップロードされるファイルのターゲットとしてこの場所を設定しました。

1. ファイルを右クリックし、 **[Download Blob]\(BLOB のダウンロード\)** を選択して、開発用コンピューターに保存します。

### <a name="read-avro-file-contents"></a>Avro ファイルの内容を読み取る

Avro ファイルを読み取って、ファイル内のメッセージの JSON 文字列を返すための単純なコマンド ライン ユーティリティを含めました。 このセクションでは、それをインストールして実行します。

1. Visual Studio Code でターミナルを開きます ( **[Terminal]\(ターミナル\)**  >  **[New terminal]\(新しいターミナル\)** )。

1. hubavroreader をインストールします。

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

1. hubavroreader を使用して、**ruldata** からダウンロードした Avro ファイルを読み取ります。

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

1. メッセージの本文は予想どおりであり、デバイス ID と予測された RUL が含まれていることに注意してください。

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

1. **uploadturbofanfiles** からダウンロードした Avro ファイルを渡して、同じコマンドを実行します。

1. 予想どおり、これらのメッセージには、元のメッセージにあったすべてのセンサー データ設定および運用設定が含まれています。 このデータを使用してエッジ デバイスの RUL モデルを改善することができます。

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このエンドツーエンドのチュートリアルで使用されるリソースを調べる予定の場合、作成したリソースのクリーンアップが完了するまで待ってください。 それ以外の場合は、次の手順に従ってそれらを削除してください。

1. Dev VM、IoT Edge VM、IoT Hub、ストレージ アカウント、機械学習ワークスペース サービス (および、作成されたリソース: コンテナー レジストリ、Application Insights、キー コンテナー、ストレージ アカウント) を保持するために作成されたリソース グループを削除します。

1. [Azure Notebooks](https://notebooks.azure.com) の機械学習プロジェクトを削除します。

1. リポジトリをローカルで複製した場合は、そのローカル リポジトリを参照している PowerShell または VS Code ウィンドウをすべて閉じてから、リポジトリ ディレクトリを削除します。

1. ローカルの証明書を作成した場合は、フォルダー c:\\edgeCertificates を削除します。

## <a name="next-steps"></a>次のステップ

この記事では、開発用 VM を使用して、センサーおよび運用データを IoT Edge デバイスに送信するリーフ デバイスをシミュレートしました。 エッジ デバイスのリアルタイム動作を調べ、ストレージ アカウントにアップロードされたファイルに注目することによって、デバイス上のモジュールがデータをルーティング、分類、永続化、およびアップロードしたことを検証しました。

IoT Edge の機能について引き続き学習するには、次のチュートリアルを試してください。

> [!div class="nextstepaction"]
> [IoT Edge デバイスの階層を作成する (プレビュー)](tutorial-nested-iot-edge.md?view=iotedge-2020-11&preserve-view=true)