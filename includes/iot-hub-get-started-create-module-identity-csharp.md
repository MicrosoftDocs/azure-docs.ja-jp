---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883850"
---
## <a name="create-a-module-identity"></a>モジュール ID を作成する

このセクションでは、ご自身のハブの ID レジストリにデバイス ID とモジュール ID を作成する .NET コンソール アプリを作成します。 ハブに接続するデバイスまたはモジュールは、あらかじめ ID レジストリに登録されている必要があります。 詳細については、[IoT Hub 開発者ガイドの ID レジストリ](../articles/iot-hub/iot-hub-devguide-identity-registry.md)に関するセクションを参照してください。

このコンソール アプリを実行すると、デバイスとモジュール両方に対して一意のデバイス ID とキーが生成されます。 デバイスとモジュールでは、IoT ハブに device-to-cloud メッセージを送信するときにこれらの値を使用して自分自身を識別します。 ID には大文字と小文字の区別があります。

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** で、 **[コンソール アプリ (.NET Framework)]** を選択します。

1. **[次へ]** を選択して、 **[新しいプロジェクトの構成]** を開きます。 プロジェクトに *CreateIdentities* という名前を付け、ソリューションに *IoTHubGetStarted* という名前を付けます。 .NET Framework のバージョンが 4.6.1 以降であることを確認します。

    ![Visual Studio ソリューションの名前とフレームワークの入力](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. Visual Studio で、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[ソリューションの NuGet パッケージの管理]** を開きます。 **[参照]** タブを選択します。

1. **Microsoft.Azure.Devices** を検索します。 これを選択してから、 **[インストール]** を選択します。

    ![Azure IoT Hub .NET service SDK の現在のバージョンをインストールする](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで作成したハブの IoT Hub 接続文字列に置き換えてください。

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. 次の操作を行うコードを **Main** クラスに追加します。

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. **Program** クラスに次のメソッドを追加します。

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    `AddDeviceAsync` メソッドでは、**myFirstDevice** という ID でデバイス ID が作成されます。 そのデバイス ID が既に ID レジストリに存在する場合は、コードにより単にその既存のデバイス情報が取得されます。 続けてその ID のプライマリ キーが表示されます。 シミュレーション対象デバイス アプリ内でこのキーを使用して、ハブに接続します。

    `AddModuleAsync` メソッドでは、デバイス **myFirstDevice** の下で、**myFirstModule** という ID でモジュール ID が作成されます。 そのモジュール ID が既に ID レジストリに存在する場合は、単にその既存のモジュール情報を取得します。 続けてその ID のプライマリ キーが表示されます。 シミュレートされたモジュール アプリ内でこのキーを使用し、ハブに接続します。

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. このアプリを実行し、デバイス キーとモジュール キーを書き留めておきます。

> [!NOTE]
> IoT ハブの ID レジストリには、ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID とモジュール ID のみが格納されます。 ID レジストリには、セキュリティ資格情報として使用されるデバイス ID とキーが格納されます。 ID レジストリには、そのデバイスのアクセスを無効にするために使用できる各デバイスの有効/無効フラグも格納されます。 その他デバイス固有のメタデータをアプリで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。 モジュール ID 用の有効/無効フラグはありません。 詳細については、[IoT Hub 開発者ガイド](../articles/iot-hub/iot-hub-devguide-identity-registry.md)をご覧ください。
