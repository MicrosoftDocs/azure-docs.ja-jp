> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md)

[Simulated Device Cloud Upload サンプル]のこのチュートリアルでは、[Azure IoT Edge][lnk-sdk] を使って、シミュレーション対象デバイスから IoT Hub に D2C テレメトリを送信する方法を示します。

このチュートリアルでは、次の項目について説明します。

* **アーキテクチャ**: [Simulated Device Cloud Upload サンプル]のアーキテクチャに関する情報。
* **ビルドおよび実行**: サンプルをビルドして実行するために必要な手順。

## <a name="architecture"></a>アーキテクチャ

[Simulated Device Cloud Upload サンプル]では、シミュレートされたデバイスから IoT hub にテレメトリを送信するゲートウェイを作成する方法を示します。 次の理由のため、デバイスが IoT Hub に直接接続できないことがあります。

* IoT Hub で認識される通信プロトコルを使っていない。
* IoT Hub によって割り当てられる ID を記憶できない。

IoT Edge ゲートウェイは、次の方法でこれらの問題を解決することができます。

* デバイスで使われるプロトコルを理解し、デバイスから D2C テレメトリを受け取り、そのメッセージを IoT Hub で認識されるプロトコルを使って IoT Hub に転送します。

* ゲートウェイは、IoT Hub の ID をデバイスにマップし、デバイスが IoT Hub にメッセージを送信するときにプロキシとして機能します。

次の図は、IoT Edge モジュールを含む、サンプルの主要なコンポーネントを示しています。

![図 - シミュレートされたデバイスのメッセージはゲートウェイを通って IoT Hub に送られる][1]

このサンプルには、ゲートウェイを構成する 3 つのモジュールが含まれます。
1. プロトコル インジェスト モジュール
1. MAC &lt;-&gt; IoT Hub ID モジュール
1. IoT Hub 通信モジュール

モジュールが他のモジュールに直接メッセージを渡すことはありません。 モジュールが内部のブローカーにメッセージを発行し、メッセージ バスが他のモジュールにメッセージを配信します。これにはサブスクリプション メカニズムが使用されます。 詳細については、[Azure IoT Edge の概要][lnk-gw-getstarted]に関するページを参照してください。

![図 - ゲートウェイ モジュールはブローカーと通信する][2]

### <a name="protocol-ingestion-module"></a>プロトコル インジェスト モジュール

プロトコル インジェスト モジュールは、デバイスから取得されたデータがゲートウェイを経由してクラウドに送られるプロセスの出発点になります。 

サンプルのこのモジュールは次のことを行います。

1. シミュレートされた温度データを作成する。 物理デバイスを使う場合、モジュールはそれらの物理デバイスからデータを読み取ります。
1. メッセージを作成する。
1. シミュレートされた温度データをメッセージ コンテンツに追加する。
1. 偽の MAC アドレスを含むプロパティをメッセージに追加する。
1. チェーン内の次のモジュールでメッセージを利用できるようにする。

プロトコル インジェスト モジュールは、ソース コードでは **simulated_device.c** です。

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt;-&gt; IoT Hub ID モジュール

MAC &lt;-&gt; IoT Hub ID モジュールは、トランスレーターとして機能します。 このサンプルでは、MAC アドレスを一意のデバイス識別子として使用し、IoT Hub デバイス ID と関連付けていますが、 別の一意識別子を使用する独自のモジュールを記述することもできます。 たとえば、デバイスに設定されている一意のシリアル番号や、テレメトリ データに含まれる一意の埋め込みデバイス名などです。

サンプルのこのモジュールは次のことを行います。

1. MAC アドレスのプロパティを持つメッセージをスキャンします。
1. MAC アドレスがある場合は、IoT Hub デバイス キーを含む別のプロパティをメッセージに追加します。 
1. チェーン内の次のモジュールでメッセージを利用できるようにする。

開発者は、MAC アドレスと IoT Hub ID の間にマッピングを設定し、シミュレートされたデバイスを IoT Hub デバイスの ID と関連付けます。 開発者は、モジュール構成の一部として手動でマッピングを追加します。

MAC &lt;-&gt; IoT Hub ID モジュールは、ソース コードでは **identitymap.c** です。 

### <a name="iot-hub-communication-module"></a>IoT Hub 通信モジュール

IoT Hub 通信モジュールは、ゲートウェイから IoT Hub に 1 つの HTTP 接続を開きます。 HTTP は、IoT Hub で認識される 3 つのプロトコルのうちの 1 つです。 このモジュールにより、1 つの接続を介したすべてのデバイスからの接続を多重化することで、デバイスごとに接続を開く必要がなくなります。 この方法では、1 つのゲートウェイで多くのデバイスに接続できます。 

サンプルのこのモジュールは次のことを行います。

1. 前のモジュールによって割り当てられた、IoT Hub デバイス キー プロパティを含むメッセージを受け取ります。 
1. HTTP プロトコルを使って IoT Hub にメッセージ コンテンツを送信します。 

IoT Hub 通信モジュールは、ソース コードでは **iothub.c** です。

## <a name="before-you-get-started"></a>開始する前に

作業を開始する前に、以下を行う必要があります。

* Azure サブスクリプションで [IoT Hub を作成][lnk-create-hub]します。 このサンプル チュートリアル用のハブの名前が必要です。 アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます。
* 2 つのデバイスを IoT Hub に追加し、デバイスの ID とデバイス キーをメモしておきます。 [デバイス エクスプローラー][lnk-device-explorer]または [iothub-explorer][lnk-iothub-explorer] ツールを使って、IoT Hub にデバイスを追加し、キーを取得します。


<!-- Images -->
[1]: media/iot-hub-iot-edge-simulated-selector/image1.png
[2]: media/iot-hub-iot-edge-simulated-selector/image2.png

<!-- Links -->
[Simulated Device Cloud Upload サンプル]: https://github.com/Azure/iot-edge/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/iot-edge
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-create-hub]: ../articles/iot-hub/iot-hub-create-through-portal.md