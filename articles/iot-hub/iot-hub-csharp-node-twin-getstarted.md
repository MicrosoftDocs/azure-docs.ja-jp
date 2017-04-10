---
title: "Azure IoT Hub デバイス ツインの使用 (.NET/Node) | Microsoft Docs"
description: "Azure IoT Hub デバイス ツインを使用してタグを追加し、IoT Hub クエリを使用する方法。 Azure IoT device SDK for Node.js を使用して、シミュレートされたデバイス アプリを実装し、Azure IoT service SDK for .NET を使用して、タグを追加し、IoT Hub クエリを実行するサービス アプリを実装します。"
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: f233f75d464ec2796d02f6760ef07512abfe3b2a


---
# <a name="get-started-with-device-twins-netnode"></a>デバイス ツインの概要 (.NET/Node)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

このチュートリアルの最後には、.NET および Node.js コンソール アプリが完成します。

* **AddTagsAndQuery.sln**: .NET バックエンド アプリで、タグを追加してデバイス ツインのクエリを実行します。
* **TwinSimulatedDevice.js**: 以前作成したデバイス ID を使用して IoT Hub に接続するデバイスをシミュレートする Node.js アプリで、接続の状態を報告します。

> [!NOTE]
> デバイス アプリとバックエンド アプリ両方の作成に利用できる Azure IoT SDK に関する情報は、「[Azure IoT SDK][lnk-hub-sdks]」の記事で取り上げています。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Microsoft Visual Studio 2015
* Node.js バージョン 0.10.x 以降。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>サービス アプリケーションを作成する
このセクションでは、**myDeviceId** に関連付けられたデバイス ツインに場所のメタデータを追加する Node.js コンソール アプリを作成します。 その後、米国にあるデバイスで、携帯ネットワーク接続を報告しているものを選択して、IoT Hub に格納されているデバイス ツインに対してクエリを実行します。

1. Visual Studio で、 **[コンソール アプリケーション]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 プロジェクトに **AddTagsAndQuery** という名前を付けます。
   
    ![New Visual C# Windows Classic Desktop project][img-createapp]
2. ソリューション エクスプローラーで **AddTagsAndQuery** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
3. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices** を検索します。**[インストール]** を選択して **Microsoft.Azure.Devices** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT service SDK][lnk-nuget-service-sdk] NuGet パッケージへの参照とその依存関係が追加されます。
   
    ![NuGet Package Manager window][img-servicenuget]
4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.Devices;
5. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで作成したハブの IoT Hub 接続文字列に置き換えてください。
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
6. **Program** クラスに次のメソッドを追加します。
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    **RegistryManager** クラスに、サービスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 前のコードでは、まず **registryManager** オブジェクトを初期化し、**myDeviceId** のデバイス ツインを取得して、最後にタグを目的の位置情報で更新します。
   
    更新終了後、2 つのクエリを実行します。1 つ目のクエリで **Redmond43** 工場にあるデバイスのデバイス ツインのみを選択し、2 つ目のクエリで携帯ネットワーク経由で接続しているデバイスのみを選択するように絞り込みます。
   
    前のコードでは、**query** オブジェクトの作成時に返されるドキュメントの最大数を指定します。 **query** オブジェクトには、**GetNextAsTwinAsync** メソッドを複数回呼び出してすべての結果を取得する際に使用できる **HasMoreResults** のブール型プロパティが含まれます。 **GetNextAsJson** というメソッドは、集計クエリの結果など、デバイス ツインの結果ではない場合に使用できます。
7. 最後に、 **Main** メソッドに次の行を追加します。
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();
8. このアプリケーションを実行します。**Redmond43** にあるすべてのデバイスを照会するクエリの結果には 1 件のデバイスが表示され、携帯ネットワークを使用するデバイスに絞り込んだ結果には 0 件のデバイスが表示されます。
   
    ![ウィンドウに表示されたクエリの結果][img-addtagapp]

次のセクションでは、接続情報を報告し、前のセクションのクエリの結果を変更するデバイス アプリを作成します。

## <a name="create-the-device-app"></a>サービス アプリを作成する
このセクションでは、**myDeviceId** としてハブに接続し、報告されるプロパティに携帯ネットワークを使用しているという情報を含めるよう更新する Node.js コンソール アプリを作成します。

1. **reportconnectivity** という名前の新しい空のフォルダーを作成します。 **reportconnectivity** フォルダー内に、コマンド プロンプトで次のコマンドを使用して新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**reportconnectivity** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** と **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. テキスト エディターを使用して、**reportconnectivity** フォルダーに新しい**ReportConnectivity.js** ファイルを作成します。
4. **ReportConnectivity.js** ファイルに次のコードを追加し、**{device connection string}** プレースホルダーを、**myDeviceId** のデバイス IDの作成時にコピーしたデバイス接続文字列で置き換えます。
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    **Client** オブジェクトに、デバイスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 前のコードでは、**Client** オブジェクトを初期化した後、**myDeviceId** のデバイス ツインを取得して、報告されるプロパティに接続情報を含めるよう更新します。
5. デバイス アプリを実行する
   
        node ReportConnectivity.js
   
    `twin state reported` というメッセージが表示されます。
6. これで、デバイスが接続情報を報告したため、両方のクエリで表示されるようになります。 .NET **AddTagsAndQuery** アプリを実行して、クエリをもう一度実行します。 今回は、**myDeviceId** が両方のクエリ結果に表示されるはずです。
   
    ![][img-addtagapp2]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 バックエンド アプリからデバイスのメタデータをタグとして追加し、シミュレート対象デバイス アプリでデバイス ツインのデバイスの接続情報を報告するよう記述しました。 さらに、SQL に似た IoT Hub クエリ言語を使用してこの情報を照会する方法も学習しました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要][lnk-iothub-getstarted]に関するチュートリアルでデバイスからテレメトリを送信する。
* 「[目的のプロパティを使用してデバイスを構成する][lnk-twin-how-to-configure]」チュートリアルで、デバイス ツインの必要なプロパティを使用してデバイスを構成する。
* 「[ダイレクト メソッドの使用][lnk-methods-tutorial]」チュートリアルで、デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど)。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md




<!--HONumber=Dec16_HO1-->


