## <a name="configure-the-nodejs-simulated-device"></a>Node.js のシミュレーション対象デバイスの構成
1. リモート監視ダッシュボードで **[+ デバイスの追加]** をクリックし、"*カスタム デバイス*" を追加します。 IoT Hub のホスト名、デバイス ID、デバイス キーをメモします。 これらの情報は、後でこのチュートリアルの中で remote_monitoring.js デバイス クライアント アプリケーションを準備するときに必要となります。
2. 開発コンピューターに Node.js のバージョン 0.12.x 以降がインストールされていることを確認します。 バージョンを確認するには、コマンド プロンプトまたはシェルから「 `node --version` 」を実行します。 パッケージ マネージャーを使用して Linux に Node.js をインストールする方法については、[パッケージ マネージャーによる Node.js のインストール][node-linux]に関するページを参照してください。
3. Node.js をインストールしたら、ご使用の開発コンピューターに最新バージョンの [azure-iot-sdk-node][lnk-github-repo] リポジトリを複製します。 最新バージョンのライブラリとサンプルを入手するために、必ず **master** ブランチを使用してください。
4. ローカルにコピーした [azure-iot-sdk-node][lnk-github-repo] リポジトリの node/device/samples フォルダーから次の&2; つのファイルを開発コンピューター上の空のフォルダーにコピーします。
   
   * packages.json
   * remote_monitoring.js
5. remote_monitoring.js ファイルを開き、次の変数の定義を探します。
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. **[IoT Hub device connection string]** を、デバイスの接続文字列に置き換えます。 手順 1. でメモした IoT Hub のホスト名、デバイス ID、デバイス キーの値を使用してください。 デバイスの接続文字列は、次の形式にする必要があります。
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    IoT Hub のホスト名が **contoso**、デバイス ID が **mydevice** の場合、接続文字列は以下のようになります。
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. ファイルを保存します。 これらのファイルが格納されているフォルダーから、シェルまたはコマンド プロンプトで次のコマンドを実行して必要なパッケージをインストールし、サンプル アプリケーションを実行します。
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>作動中の動的テレメトリの観察
ダッシュボードには、既に実行されているシミュレーション対象デバイスから取得した温度と湿度のテレメトリが表示されます。

![The default dashboard][image1]

前のセクションで実行した Node.js のシミュレーション対象デバイスを選択した場合は、温度、湿度、外部温度のテレメトリが表示されます。

![Add external temperature to the dashboard][image2]

このリモート監視ソリューションは、その他の種類の外部温度テレメトリを自動的に検出し、ダッシュボード上のグラフに追加します。

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-send-external-temperature/image1.png
[image2]: media/iot-suite-send-external-temperature/image2.png

<!--HONumber=Dec16_HO3-->


