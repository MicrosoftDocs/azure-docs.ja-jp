---
title: "Azure IoT Hub のダイレクト メソッドの使用 (.NET/Node) | Microsoft Docs"
description: "Azure IoT Hub のダイレクト メソッドの使用方法。 Azure IoT device SDK for Node.js を使用して、ダイレクト メソッドを含むシミュレートされたデバイス アプリを実装し、Azure IoT service SDK for .NET を使用して、ダイレクト メソッドを呼び出すサービス アプリを実装します。"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: e7d7065513f69bac5f759bfd5f4b3965b26bab5d
ms.lasthandoff: 03/14/2017


---
# <a name="use-direct-methods-netnode"></a>ダイレクト メソッドを使用する (.NET/Node)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

このチュートリアルでは、.NET と Node.js のコンソール アプリを開発します。

* **CallMethodOnDevice.sln**。.NET バックエンド アプリ。シミュレートされたデバイス アプリでメソッドを呼び出し、応答を表示します。
* **SimulatedDevice.js**: Node.js アプリ。作成済みのデバイス ID を使用して IoT ハブに接続し、クラウドによって呼び出されたメソッドに応答するデバイスをシミュレートします。

> [!NOTE]
> デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの開発に利用できる各種 Azure IoT SDK については、[Azure IoT SDK][lnk-hub-sdks] に関する記事を参照してください。
> 
> 

このチュートリアルを完了するには、次のものが必要です。

* Visual Studio 2015 または Visual Studio 2017。
* Node.js バージョン 0.10.x 以降。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>シミュレーション対象デバイス アプリの作成
このセクションでは、ソリューション バックエンドによって呼び出されたメソッドに応答する Node.js コンソール アプリを作成します。

1. **simulateddevice**という名前の新しい空のフォルダーを作成します。 コマンド プロンプトで次のコマンドを使用して、**simulateddevice** フォルダー内に新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**simulateddevice** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** パッケージと **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
        npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. テキスト エディターを使用して **simulateddevice** フォルダーにファイルを作成し、**SimulatedDevice.js** という名前を付けます。
4. **SimulatedDevice.js** ファイルの先頭に、次の `require` ステートメントを追加します。
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. **connectionString** 変数を追加し、それを使用して **DeviceClient** インスタンスを作成します。 **{device connection string}** を「*デバイス ID の作成*」セクションで生成したデバイス接続文字列に置き換えます。
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. デバイスにダイレクト メソッドを実装する次の関数を追加します。
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. IoT Hub への接続を開き、メソッド リスナーを初期化します。
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. **SimulatedDevice.js** ファイルを保存して閉じます。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「[Transient Fault Handling (一時的な障害の処理)][lnk-transient-faults]」で推奨されているように、再試行ポリシー (接続の再試行など) を実装することをお勧めします。
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>デバイスのダイレクト メソッドを呼び出す
このセクションでは、シミュレート対象デバイス アプリのメソッドを呼び出し、その応答を表示する .NET コンソール アプリを作成します。

1. Visual Studio で、 **[コンソール アプリケーション]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 .NET Framework のバージョンが 4.5.1 以降であることを確認します。 プロジェクトに **CallMethodOnDevice** という名前を付けます。
   
    ![New Visual C# Windows Classic Desktop project][10]
2. ソリューション エクスプローラーで **CallMethodOnDevice** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
3. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices** を検索します。**[インストール]** を選択して **Microsoft.Azure.Devices** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT service SDK][lnk-nuget-service-sdk] NuGet パッケージへの参照とその依存関係が追加されます。
   
    ![NuGet Package Manager window][11]

4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで作成したハブの IoT Hub 接続文字列に置き換えてください。
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. **Program** クラスに次のメソッドを追加します。
   
        private static async Task InvokeMethod()
        {
            var methodInvocation = new CloudToDeviceMethod("writeLine") { ResponseTimeout = TimeSpan.FromSeconds(30) };
            methodInvocation.SetPayloadJson("'a line to be written'");

            var response = await serviceClient.InvokeDeviceMethodAsync("myDeviceId", methodInvocation);

            Console.WriteLine("Response status: {0}, payload:", response.Status);
            Console.WriteLine(response.GetPayloadAsJson());
        }
   
    このメソッドは、`myDeviceId` デバイスの `writeLine` という名前のダイレクト メソッドを呼び出します。 その後、デバイスによって提供された応答をコンソールに書き込みます。 デバイスが応答するまでのタイムアウト値の指定方法に注意してください。
7. 最後に、**Main** メソッドに次の行を追加します。
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

## <a name="run-the-applications"></a>アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. Visual Studio のソリューション エクスプローラーでソリューションを右クリックし、**[スタートアップ プロジェクトの設定]** をクリックします。 **[シングル スタートアップ プロジェクト]** を選択し、ドロップダウン メニューから **[CallMethodOnDevice]** プロジェクトを選択します。

2. コマンド プロンプトで、**simulateddevice** フォルダーに移動し、次のコマンドを実行して、IoT Hub からのメソッドの呼び出しのリッスンを開始します。
   
    ```
    node SimulatedDevice.js
    ```
   シミュレートされたデバイスが開くまで待ちます。 ![][7]
3. デバイスが接続され、メソッドの呼び出しのために待機しています。.NET **CallMethodOnDevice** アプリを実行して、シミュレート対象デバイスのアプリを呼び出します。 デバイスの応答がコンソールに書き込まれます。
   
    ![][8]
4. メソッドへの応答として、デバイスから次のメッセージが出力されます。
   
    ![][9]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 シミュレーション対象デバイス アプリでクラウドから呼び出したメソッドに対応できるようにするために、このデバイス ID を使用しました。 また、デバイスでメソッドを呼び出し、デバイスからの応答を表示するアプリも作成しました。 

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [IoT Hub の使用]
* [複数デバイスで実行されるジョブのスケジュール設定][lnk-devguide-jobs]

IoT ソリューションの拡張と複数のデバイスでのメソッドの呼び出しをスケジュールする方法については、「[ジョブのスケジュールとブロードキャスト][lnk-tutorial-jobs]」チュートリアルを参照してください。

<!-- Images. -->
[7]: ./media/iot-hub-csharp-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-csharp-node-direct-methods/netserviceapp.png
[9]: ./media/iot-hub-csharp-node-direct-methods/methods-output.png

[10]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp1.png
[11]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp2.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[IoT Hub の使用]: iot-hub-node-node-getstarted.md

