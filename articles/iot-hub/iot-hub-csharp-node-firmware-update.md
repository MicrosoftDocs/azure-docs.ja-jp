---
title: "Azure IoT Hub を使用したデバイス ファームウェアの更新 (.NET/Node) | Microsoft Docs"
description: "Azure IoT Hub でデバイス管理を使用してデバイス ファームウェアの更新を開始する方法。 Azure IoT device SDK for Node.js を使用して、シミュレートされたデバイス アプリを実装し、Azure IoT service SDK for .NET を使用して、ファームウェアの更新をトリガーするサービス アプリを実装します。"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2017
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: 4ba60cee8848079935111ed3de480081a4aa58f6
ms.openlocfilehash: a586d437ed7636874d324c9d3fc5274fe9001627
ms.lasthandoff: 02/06/2017


---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>デバイス管理を使用してデバイス ファームウェアの更新を開始する (.NET/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>はじめに
「[デバイス管理の開始][lnk-dm-getstarted]」チュートリアルでは、[デバイス ツイン][lnk-devtwin]および[ダイレクト メソッド][lnk-c2dmethod] プリミティブを使用してリモートでデバイスを再起動する方法を説明しました。 このチュートリアルでは、同じ IoT Hub プリミティブを使用して、エンド ツー エンドでシミュレートされたファームウェア更新を実行する方法を示します。  このパターンは、[Raspberry Pi デバイスの実装サンプル][lnk-rpi-implementation]のファームウェア更新の実装で使用されます。

このチュートリアルでは、次の操作方法について説明します。

* シミュレート対象デバイス アプリで firmwareUpdate ダイレクト メソッドを IoT Hub 経由で呼び出す .NET コンソール アプリを作成します。
* **firmwareUpdate** ダイレクト メソッドを実装するシミュレート対象デバイス アプリを作成します。 このメソッドは、ファームウェア イメージをダウンロードするまで待機し、ファームウェア イメージをダウンロードし、最後にファームウェア イメージを適用する、多段階のプロセスを開始します。 更新の各段階中、デバイスは、報告されるプロパティを使用して進捗状況を報告します。

このチュートリアルが終わると、次の Node.js コンソール デバイス アプリと .NET (C#) コンソール バックエンド アプリが完成しています。

**dmpatterns_fwupdate_service.js**: シミュレート対象デバイス アプリでダイレクト メソッドを呼び出し、応答を表示して、更新された報告されるプロパティを定期的に表示します (500 ミリ秒ごと)。

**TriggerFWUpdate**: IoT Hub とすでに作成したデバイス ID をつなげ、farmwareUpdate ダイレクト メソッドを受信し、イメージのダウンロードの待機、新しいイメージのダウンロード、イメージの適用というマルチステートメント プロセスを経由してファームウェアの更新をシミュレーションします。

このチュートリアルを完了するには、以下が必要です。

* Microsoft Visual Studio 2015
* Node.js バージョン 0.12.x 以降。 <br/>  「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」では、このチュートリアルのために Node.js を Windows または Linux にインストールする方法が説明されています。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

「[デバイス管理の開始](iot-hub-csharp-node-device-management-get-started.md)」の記事を参照して、IoT Hub を作成し、IoT Hub 接続文字列を取得します。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>ダイレクト メソッドを使用して、デバイス上でリモート ファームウェア更新を開始する
このセクションでは、デバイスでファームウェアのリモート更新を開始する .NET コンソール アプリケーションを作成します (C# を使用します)。 アプリケーションは、ダイレクト メソッドを使用して更新を開始し、デバイス ツイン クエリを使用してアクティブなファームウェア更新の状態を定期的に取得します。

1. Visual Studio で、 **[コンソール アプリケーション]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 プロジェクトに **TriggerFWUpdate** という名前を付けます。

    ![New Visual C# Windows Classic Desktop project][img-createapp]

2. ソリューション エクスプローラーで **TriggerFWUpdate** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
3. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices** を検索します。**[インストール]** を選択して **Microsoft.Azure.Devices** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT service SDK][lnk-nuget-service-sdk] NuGet パッケージへの参照とその依存関係が追加されます。

    ![NuGet Package Manager window][img-servicenuget]
4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
5. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで作成した Hub の IoT Hub 接続文字列とデバイスの ID に置き換えてください。
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
6. **Program** クラスに次のメソッドを追加します。
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. **Program** クラスに次のメソッドを追加します。

        public static async Task StartFirmwareUpdate()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);
            method.SetPayloadJson(
                @"{
                    fwPackageUri : 'https://someurl'
                }");

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. 最後に、 **Main** メソッドに次の行を追加します。
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. ソリューションをビルドします。

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>アプリの実行
これで、アプリを実行する準備が整いました。

1. コマンド プロンプトで、**manageddevice** フォルダーに移動し、次のコマンドを実行して再起動のダイレクト メソッドのリッスンを開始します。
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. Visual Studio で、**TriggerFWUpdate** プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順に選択します。

3. ダイレクト メソッドに対するデバイスの応答がコンソールに表示されます。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、ダイレクト メソッドを使用してデバイス上でリモートのファームウェア更新を開始し、報告されるプロパティを使用してファームウェア更新の進捗状況を確認しました。

IoT ソリューションの拡張と複数のデバイスでのメソッドの呼び出しをスケジュールする方法については、「[ジョブのスケジュールとブロードキャスト][lnk-tutorial-jobs]」チュートリアルを参照してください。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
