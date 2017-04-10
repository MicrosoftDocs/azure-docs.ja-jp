## <a name="create-a-device-identity"></a>デバイス ID の作成
このセクションでは、[IoT Hub Explorer][iot-hub-explorer] という名前の Node.js ツールを使用して、このチュートリアル用のデバイス ID を作成します。

1. コマンドライン環境で次のコマンドを実行します。
   
    ```
    npm install -g iothub-explorer@latest
    ```
2. 次に、次のコマンドを実行してハブにログインします。このとき、`{iot hub connection string}` を、前にコピーしておいた IoT Hub 接続文字列に置き換えます。

    ```
    iothub-explorer login "{iot hub connection string}"
    ```
3. 最後に、次のコマンドを実行して、`myDeviceId` という新しいデバイス ID を作成します。
   
    ```
    iothub-explorer create myDeviceId --connection-string
    ```

結果として得られたデバイスの接続文字列をメモしておきます。 このデバイス接続文字列は、デバイス アプリからデバイスとして IoT Hub に接続する際に使用します。

![][img-identity]

デバイスの ID をプログラムで作成する方法については、[IoT Hub の概要][lnk-getstarted]に関するページを参照してください。

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
