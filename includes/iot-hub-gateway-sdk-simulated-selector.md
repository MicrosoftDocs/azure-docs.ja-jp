> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-simulated-device.md)
> 
> 

[Simulated Device Cloud Upload サンプル]のチュートリアルでは、[Azure IoT Gateway SDK][lnk-sdk] を使用して、シミュレーション対象デバイスから IoT Hub に D2C テレメトリを送信する方法を示します。

このチュートリアルでは、次の項目について説明します。

1. **アーキテクチャ**: Simulated Device Cloud Upload サンプルの重要なアーキテクチャに関する情報。
2. **ビルドおよび実行**: サンプルををビルドして実行するために必要な手順。

## <a name="architecture"></a>アーキテクチャ
Simulated Device Cloud Upload サンプルでは、SDK を使用して、シミュレートされたデバイスから IoT hub にテレメトリを送信するゲートウェイを作成する方法を示します。 シミュレートされたデバイスが直接 IoT Hub に接続することはできません。それには、以下の理由があります。

* シミュレートされたデバイスは IoT Hub で認識される通信プロトコルを使用していない。
* シミュレートされたデバイスは IoT Hub によって割り当てられる ID を記憶できない。

このシミュレートされたデバイスの問題は、ゲートウェイが次の方法で解決します。

* シミュレートされたデバイスで使用されるプロトコルを理解し、デバイスから D2C テレメトリを受け取り、そのメッセージを IoT Hub で認識されるプロトコルを使用して IoT Hub に転送する。
* シミュレートされたデバイスに代わって IoT Hub の ID を格納し、シミュレートされたデバイスから IoT Hub にメッセージを送信するときにプロキシとして動作する。

次の図は、ゲートウェイ モジュールを含む、サンプルの主要なコンポーネントを示しています。

![][1]

> [!NOTE]
> モジュールが他のモジュールに直接メッセージを渡すことはありません。 下の図に示すように、モジュールが内部のブローカーにメッセージを発行し、メッセージ バスが他のモジュールにメッセージを配信します。これにはサブスクリプション メカニズムが使用されます。 詳細については、[IoT Gateway SDK の概要][lnk-gw-getstarted]に関する記事を参照してください。
> 
> 

### <a name="protocol-ingestion-module"></a>プロトコル インジェスト モジュール
このモジュールは、データをデバイスから取得し、ゲートウェイを経由してクラウドに送信する流れの出発点となります。 このサンプルでは、プロトコル インジェスト モジュールは次の&4; つのタスクを実行します。

1. シミュレートされた温度データを作成する。 実際のデバイスを使用する場合は、それらの物理デバイスからデータが読み取られることに注意してください。
2. シミュレートされた温度データをメッセージに追加する。
3. シミュレートされた温度データを含むメッセージに、偽の MAC アドレスを持つプロパティを追加する。
4. チェーン内の次のモジュールでメッセージを利用できるようにする。

> [!NOTE]
> 上の図で "**プロトコル X インジェスト**" と呼ばれているモジュールは、ソース コードでは "**シミュレートされたデバイス**" と呼ばれます。
> 
> 

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt;-&gt; IoT Hub ID モジュール
このモジュールは、メッセージをスキャンして、プロトコル インジェスト モジュールによって追加された、シミレーション対象デバイス アプリの MAC アドレスを持つプロパティが含まれているかどうかを調べます。 そのようなプロパティが見つかると、IoT Hub デバイス キーを持つ別のプロパティをメッセージに追加し、メッセージをチェーン内の次のモジュールで利用できるようにします。 この方法で、IoT Hub デバイス ID をシミュレートされたデバイスに関連付けます。 開発者は、モジュールを構成する際に手動で MAC アドレスと IoT Hub ID のマッピングを設定します。 

> [!NOTE]
> このサンプルでは、MAC アドレスを一意のデバイス識別子として使用し、IoT Hub デバイス ID と関連付けていますが、 別の一意識別子を使用する独自のモジュールを記述することもできます。 たとえば、デバイスの一意のシリアル番号やテレメトリ データに一意のデバイス名が埋め込まれており、それを IoT Hub デバイス ID の確認に使用できる場合などです。
> 
> 

### <a name="iot-hub-communication-module"></a>IoT Hub 通信モジュール
このモジュールは、前のモジュールによって割り当てられた IoT Hub デバイス ID を持つメッセージを受け取り、HTTP を使用してそのメッセージの内容を IoT Hub に送信します。 HTTP は、IoT Hub で認識される&3; つのプロトコルのうちの&1; つです。

このモジュールは、シミュレーション対象デバイス アプリごとに IoT Hub への接続を開く代わりに、ゲートウェイから IoT Hub への単一の HTTP 接続を開き、その接続を介するすべてのシミュレートされたデバイスからの接続を多重化します。 これにより、デバイスごとに一意の接続が開かれている場合に比べ、1 つのゲートウェイでより多くの (シミュレートされた) デバイスに接続することができます。

![][2]

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-simulated-selector/image1.png
[2]: media/iot-hub-gateway-sdk-simulated-selector/image2.png

<!-- Links -->
[Simulated Device Cloud Upload サンプル]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md