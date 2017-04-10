> [!div class="op_single_selector"]
> * [Node.JS](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)
> 
> 

## <a name="introduction"></a>はじめに
デバイス ツインは、デバイスの状態に関する情報 (メタデータ、構成、状態) を格納する JSON ドキュメントです。 IoT Hub は、IoT Hub に接続する各デバイスにデバイス ツインを保持します。

次の場合にデバイス ツインを使用します。

* ソリューション バックエンドからデバイス メタデータを格納する。
* デバイス アプリで利用できる機能や状態 (たとえば、使用される接続方法) などの現在の状態に関する情報を報告する。
* デバイス アプリとバックエンド アプリの間で実行時間の長いワークフロー (ファームウェアや構成の更新など) の状態を同期する。
* デバイス メタデータ、構成、または状態を照会する。

> [!NOTE]
> デバイス ツインは、同期のほか、デバイスの構成と状態の照会に対応しています。 デバイス ツインを使用するタイミングの詳細については、「[デバイス ツインについて][lnk-twins]」を参照してください。
> 
> 

デバイス ツインは IoT ハブに格納され、次のものを含みます。

* "*タグ*": ソリューション バックエンドからのみアクセスできるデバイス メタデータです。
* "*必要なプロパティ*": ソリューション バックエンドから変更でき、デバイス アプリから監視できる JSON オブジェクトです。
* "*報告されるプロパティ*": デバイス アプリから変更でき、ソリューション バックエンドから読み取り可能な JSON オブジェクトです。 タグとプロパティに配列を含めることはできませんが、オブジェクトは入れ子にできます。

![][img-twin]

さらに、ソリューション バックエンドは、上記のすべてのデータに基づいてデバイス ツインに対してクエリを実行できます。
デバイス ツインの詳細については「[デバイス ツインについて][lnk-twins]」を、クエリについては [IoT Hub クエリ言語][lnk-query]のリファレンスを参照してください。

> [!NOTE]
> 現在のところ、デバイス ツインには MQTT プロトコルを使用して IoT Hub に接続するデバイスからのみアクセスできます。 既存のデバイス アプリを変換して MQTT を使用する方法については、[MQTT サポート][lnk-devguide-mqtt]の記事を参照してください。
> 
> 

このチュートリアルでは、次の操作方法について説明します。

* デバイス ツインに "*タグ*" を追加するバックエンド アプリのほか、接続チャネルをデバイス ツインの "*報告されるプロパティ*" として報告するシミュレーション対象デバイス アプリを作成します。
* 以前に作成したタグとプロパティのフィルターを使用して、バックエンド アプリからデバイスに対してクエリを実行します。

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

<!--HONumber=Dec16_HO1-->


