## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成
このセクションでは、次の作業を行います。

* クラウドによって呼び出されたダイレクト メソッドに応答する Node.js コンソール アプリを作成します。
* シミュレートされたファームウェアの更新をトリガーする
* 報告されるプロパティを使用して、デバイス ツイン クエリで、デバイスと最後にファームウェアの更新が完了した日時を識別できるようにする

1. **manageddevice** という名前の空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、**manageddevice** フォルダー内に新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**manageddevice** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** と **azure-iot-device-mqtt** Device SDK パッケージをインストールします。
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. テキスト エディターを使用して、**manageddevice** フォルダーに **dmpatterns_fwupdate_device.js** ファイルを作成します。

4. **dmpatterns_fwupdate_device.js** ファイルの先頭に、次の 'require' ステートメントを追加します。
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. **connectionString** 変数を追加し、それを使用して **Client** インスタンスを作成します。 `{yourdeviceconnectionstring}`プレース ホルダーを、前に「デバイス ID の作成」セクションでメモしておいた接続文字列に置き換えます。
   
    ```
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. 報告されるプロパティを更新するために使用する次の関数を追加します。
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported: ' + firmwareUpdateValue.status);
      });
    };
    ```
7. ファームウェア イメージのダウンロードと適用をシミュレートする次の関数を追加します。
   
    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
   
      console.log("Downloading image from " + imageUrl);
   
      callback(error, image);
    }
   
    var simulateApplyImage = function(imageData, callback) {
      var error = null;
   
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
   
      callback(error);
    }
    ```
8. 報告されるプロパティを介してファームウェアの更新状態を **waiting** に更新する次の関数を追加します。 通常、利用可能な更新プログラムがあるとデバイスに通知され、管理者が定義したポリシーによって更新のダウンロードと適用が開始されます。 この関数で、そのポリシーを実行するためのロジックが実行されます。 わかりやすくするため、サンプルはファームウェア イメージのダウンロードに進む前に&4; 秒間待ちます。
   
    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
   
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
9. 報告されるプロパティを介してファームウェアの更新状態を **downloading** に更新する次の関数を追加します。 次に関数はファームウェアのダウンロードをシミュレートし、最後にファームウェアの更新状態を **downloadFailed** または **downloadComplete** に更新します。
   
    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
   
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
   
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
   
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
   
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
   
      }, 4000);
    }
    ```
10. 報告されるプロパティを介してファームウェアの更新状態を **applying** に更新する次の関数を追加します。 次に関数はファームウェア イメージの適用をシミュレートし、最後にファームウェアの更新状態を **applyFailed** または **applyComplete** に更新します。
    
    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
    
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
    
      setTimeout(function() {
    
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
    
          }
        });
    
        setTimeout(callback, 4000);
    
      }, 4000);
    }
    ```
11. **firmwareUpdate** ダイレクト メソッドを処理してマルチステージのファームウェア更新プロセスを開始する次の関数を追加します。
    
    ```
    var onFirmwareUpdate = function(request, response) {
    
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });
    
      // Get the parameter from the body of the method request
      var fwPackageUri = request.payload.fwPackageUri;
    
      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
    
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });
    
        }
      });
    }
    ```
12. 最後に、IoT Hub に接続する次のコードを追加します。
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate);
    });
    ```

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「[Transient Fault Handling (一時的な障害処理)][lnk-transient-faults]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

<!--HONumber=Feb17_HO1-->


