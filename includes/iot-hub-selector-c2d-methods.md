> [!div class="op_single_selector"]
> * [Node.JS](../articles/iot-hub/iot-hub-node-node-direct-methods.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-direct-methods.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-direct-methods.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-direct-methods.md)

Azure IoT Hub は、何百万ものデバイスとソリューション バック エンド間で、セキュリティで保護された信頼性のある双方向通信を実現する、完全に管理されたサービスです。 前のチュートリアル ([IoT Hub の使用]と [IoT Hub を使用したクラウドからデバイスへのメッセージの送信]に関するチュートリアル) では、IoT Hub のデバイスからクラウドおよびクラウドからデバイスへののメッセージングについて、基本的な機能の使用方法を説明しています。 さらに、IoT Hub には、クラウドからデバイスに持続性のないメソッドを呼び出す機能も備わっています。 ダイレクト メソッドは、デバイスとの要求/応答を表します。これは、(ユーザーが指定したタイムアウト後) すぐに成功または失敗してその状態をユーザーに知らせるという点で HTTP 呼び出しに似ています。 [デバイスでのダイレクト メソッドの呼び出し][lnk-devguide-methods]に関する記事では、ダイレクト メソッドの詳細を説明し、クラウドからデバイスへのメッセージや必要なプロパティではなくダイレクト メソッドを使用するタイミングに関するガイダンスを提供します。

このチュートリアルでは、次の操作方法について説明します。

* Azure Portal を使用して IoT Hub を作成し、IoT Hub でデバイス ID を作成します。
* クラウドから呼び出すことができるダイレクト メソッドを持つ、シミュレーション対象デバイス アプリを作成します。
* IoT Hub を介してシミュレーション対象デバイス アプリでダイレクト メソッドを呼び出すコンソール アプリを作成します。


[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[IoT Hub の使用]: ../articles/iot-hub/iot-hub-node-node-getstarted.md