> [!div class="op_single_selector"]
> * [デバイス: Node.js サービス: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [デバイス: Node.js サービス: C#](../articles/iot-hub/iot-hub-csharp-node-device-management-get-started.md)
> * [デバイス: C# サービス: C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [デバイス: Java サービス: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)

バックエンド アプリは、Azure IoT Hub のプリミティブ ([デバイス ツイン][lnk-devtwin]や[ダイレクト メソッド][lnk-c2dmethod]など) を使用して、デバイス上でデバイス管理アクションをリモートで開始して監視できます。 このチュートリアルでは、バックエンド アプリとデバイス アプリをどのように連携させると、IoT Hub を使用してデバイスの再起動をリモートで開始して監視できるかについて示します。

ダイレクト メソッドを使用して、クラウド内のバックエンド アプリケーションからデバイス管理操作 (再起動、出荷時の設定に戻す、ファームウェアの更新など) を開始します。 デバイスは次の操作を担当します。

* IoT Hub から送信されたメソッド要求の処理。
* デバイスでの対応するデバイス固有の操作の開始。
* "*報告されるプロパティ*" を介した IoT Hub への状態更新の提供。

クラウドでバックエンド アプリを使用してデバイス ツインのクエリを実行することで、デバイス管理操作の進行状況を報告できます。

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
