> [!div class="op_single_selector"]
> * [Node.JS](../articles/iot-hub/iot-hub-node-node-direct-methods.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-node-direct-methods.md)
> 
> 

## <a name="introduction"></a>はじめに
Azure IoT Hub は、何百万ものデバイスとソリューション バック エンド間で、セキュリティで保護された信頼性のある双方向通信を実現する、完全に管理されたサービスです。 前のチュートリアル ([IoT Hub の使用]と [IoT Hub を使用したクラウドからデバイスへのメッセージの送信]に関するチュートリアル) では、IoT Hub のデバイスからクラウドおよびクラウドからデバイスへののメッセージングについて、基本的な機能の使用方法を説明しています。 さらに、IoT Hub には、クラウドからデバイスに持続性のないメソッドを呼び出す機能も備わっています。 メソッドは、デバイスとの要求/応答を表しています。これは、成功または失敗の直後 (ユーザーが指定したタイムアウト後) に、呼び出しのステータスをユーザーに伝える HTTP 呼び出しと同様のものです。 [デバイスでのダイレクト メソッドの呼び出し][lnk-devguide-methods]に関する記事では、メソッドの詳細を説明し、メソッドやクラウドからデバイスへのメッセージを使用するタイミングに関するガイダンスを提供します。

このチュートリアルでは、次の操作方法について説明します。

* Azure Portal を使用して IoT Hub を作成し、IoT Hub でデバイス ID を作成します。
* クラウドから呼び出すことができるダイレクト メソッドを持つ、シミュレーション対象デバイス アプリを作成します。
* IoT Hub を介してシミュレーション対象デバイス アプリでダイレクト メソッドを呼び出すコンソール アプリを作成します。

> [!NOTE]
> 現在のところ、ダイレクト メソッドには MQTT プロトコルを使用して IoT Hub に接続するデバイスからのみアクセスできます。 既存のデバイス アプリを変換して MQTT を使用する方法については、[MQTT サポート][lnk-devguide-mqtt]の記事を参照してください。
> 
> 




[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[IoT Hub の使用]: ../articles/iot-hub/iot-hub-node-node-getstarted.md

<!--HONumber=Dec16_HO1-->


