---
title: "Azure IoT Edge モジュールを C# で作成する | Microsoft Docs"
description: "このチュートリアルでは、BLE データ コンバーター モジュールを最新の Azure IoT Edge NuGet パッケージ、Visual Studio Code、および C# を使用して記述する方法を紹介します。"
services: iot-hub
author: jeffreyCline
manager: timlt
keywords: "Azure, iot, チュートリアル, モジュール, NuGet, vscode, csharp, Edge"
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: jcline
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 91ff3c96e4d7928131eba83c0e8c1951782447be
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017

---
# <a name="create-an-azure-iot-edge-module-with-cx23"></a>Azure IoT Edge モジュールを C&#x23; で作成する

このチュートリアルでは、`Azure IoT Edge` 用のモジュールを `Visual Studio Code` と `C#` を使用して作成する方法を紹介します。

このチュートリアルでは、環境をセットアップし、`Azure IoT Edge NuGet` パッケージを使用して [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) データ コンバーター モジュールを記述する方法を示します。 

>[!NOTE]
このチュートリアルでは、クロスプラットフォームの互換性をサポートしている `.NET Core SDK` を使用しています。 次のチュートリアルは、`Windows 10` オペレーティング システムを使用して記述されています。 このチュートリアルで使用されているいくつかのコマンドは、`development environment`によって異なる可能性があります。 

## <a name="prerequisites"></a>前提条件

このセクションでは、`Azure IoT Edge` モジュールを開発するための環境をセットアップします。 **64 ビット 版の Windows** オペレーティング システムと **64 ビット版の Linux (Ubuntu/Debian 8)** オペレーティング システムの両方に適用されます。

次のソフトウェアが必要です。

- [Git クライアント](https://git-scm.com/downloads)
- [.NET Core SDK](https://www.microsoft.com/net/core#windowscmd)
- [Visual Studio Code](https://code.visualstudio.com/)

このサンプル用のリポジトリを複製する必要はありません。ただし、このチュートリアルで説明するすべてのサンプル コードは、次のリポジトリに配置されます。

- `git clone https://github.com/Azure-Samples/iot-edge-samples.git`
- `cd iot-edge-samples/dotnetcore/simulated_ble`

## <a name="getting-started"></a>使用の開始

1. `.NET Core SDK` をインストールします。
2. Visual Studio Code Marketplace から `Visual Studio Code` と `C# extension`をインストールします。

`Visual Studio Code` と `.NET Core SDK` の使用を開始する方法については、こちらの[クイック ビデオ チュートリアル](https://channel9.msdn.com/Blogs/dotnet/Get-started-VSCode-Csharp-NET-Core-Windows)を参照してください。

## <a name="creating-the-azure-iot-edge-converter-module"></a>Azure IoT Edge コンバーター モジュールの作成

1. 新しい `.NET Core` クラス ライブラリの C# プロジェクトを初期化します。
    - コマンド プロンプトを開きます (`Windows + R` -> `cmd` -> `enter`)。
    - `C#` プロジェクトを作成するフォルダーに移動します。
    - 「**dotnet new classlib -o IoTEdgeConverterModule -f netstandard1.3**」と入力します。 
    - このコマンドは、`Class1.cs` と呼ばれる空のクラスをプロジェクト ディレクトリに作成します。
2. 「**cd IoTEdgeConverterModule**」と入力して、先ほどクラス ライブラリ プロジェクトを作成したフォルダーに移動します。
3. **code** と入力して、`Visual Studio Code` でプロジェクトを開きます。
4. `Visual Studio Code` でプロジェクトが開いたら、**IoTEdgeConverterModule.csproj** をクリックして、次の図のようにファイルを開きます。

    ![Visual Studio Code の編集ウィンドウ](media/iot-hub-iot-edge-create-module/vscode-edit-csproj.png)

5. 次のコード スニペットに示されている `XML` BLOB を、`PropertyGroup` の終了タグと `Project` の終了タグの間 (上の図の 6 行目) に挿入し、`Ctrl` + `S` キーを押してファイルを保存します。

   ```xml
     <ItemGroup>
       <PackageReference Include="Microsoft.Azure.Devices.Gateway.Module.NetStandard" Version="1.0.5" />
       <PackageReference Include="System.Threading.Thread" Version="4.3.0" />
       <PackageReference Include="Newtonsoft.Json" Version="10.0.2" />
     </ItemGroup> 
   ```

6. `.csproj` ファイルを保存すると、次の図に示すように、`Visual Studio Code` によって `unresolved dependencies` ダイアログが表示されます。 

    ![Visual Studio Code の依存関係復元ダイアログ](media/iot-hub-iot-edge-create-module/vscode-restore.png)

    a) 追加した `PackageReferences` を含むプロジェクトの `.csproj` ファイル内のすべての参照を復元するには、`Restore` をクリックします。 

    b) `Visual Studio Code` によって、プロジェクトの `obj` フォルダーに `project.assets.json` が自動的に作成されます。 このファイルには、以降の復元を迅速に行うためのプロジェクトの依存関係に関する情報が含まれています。
 
    >[!NOTE]
    `.NET Core Tools` は、MSBuild ベースになりました。 つまり、`project.json` の代わりに`.csproj` プロジェクト ファイルが作成されます。

    - `Visual Studio Code` によって OK にならない場合は、手動で実行できます。 `Ctrl` + `backtick` キーを押すか `View` -> `Integrated Terminal` メニューを使用して、`Visual Studio Code` 統合ターミナル ウィンドウを開きます。
    - `Integrated Terminal` ウィンドウで、「**dotnet restore**」と入力します。
    
7. `Class1.cs` ファイルの名前を `BleConverterModule.cs` に変更します。 

    a) ファイルの名前を変更するには、ファイルをクリックした後、`F2` キーを押します。
    
    b) 次の図に示すように、新しい名前 **BleConverterModule** を入力します。

    ![Visual Studio Code でのクラス名の変更](media/iot-hub-iot-edge-create-module/vscode-rename.png)

8. 次のコード スニペットをコピーして `BleConverterModule.cs` ファイルに貼り付けることで、`BleConverterModule.cs` ファイルの既存のコードを置き換えます。

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Globalization;
   using System.Linq;
   using System.Text;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace IoTEdgeConverterModule
   {
       public class BleConverterModule : IGatewayModule, IGatewayModuleStart
       {
           private Broker broker;
           private string configuration;
           private int messageCount;

           public void Create(Broker broker, byte[] configuration)
           {
               this.broker = broker;
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Start()
           {
           }

           public void Destroy()
           {
           }

           public void Receive(Message received_message)
           {
               string recMsg = Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               BleData receivedData = JsonConvert.DeserializeObject<BleData>(recMsg);

               float temperature = float.Parse(receivedData.Temperature, CultureInfo.InvariantCulture.NumberFormat); 
               Dictionary<string, string> receivedProperties = received_message.Properties;
            
               Dictionary<string, string> properties = new Dictionary<string, string>();
               properties.Add("source", receivedProperties["source"]);
               properties.Add("macAddress", receivedProperties["macAddress"]);
               properties.Add("temperatureAlert", temperature > 30 ? "true" : "false");
   
               String content = String.Format("{0} \"deviceId\": \"Intel NUC Gateway\", \"messageId\": {1}, \"temperature\": {2} {3}", "{", ++this.messageCount, temperature, "}");
               Message messageToPublish = new Message(content, properties);
   
               this.broker.Publish(messageToPublish);
           }
       }
   }
   ```

9. `Ctrl` + `S` キーを押してファイルを保存します。

10. 次の図に示すように、`Ctrl` + `N` キーを押して、`Untitled-1` という名前の新しいファイルを作成します。

    ![Visual Studio Code の新しいファイル](media/iot-hub-iot-edge-create-module/vscode-new-file.png)

11. シミュレートされた `BLE` デバイスから受け取る `JSON` オブジェクトを逆シリアル化するために、次のコードを `Untitled-1` ファイル コード エディター ウィンドウにコピーします。 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace IoTEdgeConverterModule
   {
       public class BleData
       {
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

12. `Ctrl` + `Shift` + `S` キーを押して、ファイルを `BleData.cs` として保存します。
    - 次の図に示すように、[名前を付けて保存] ダイアログ ボックスで、`Save as Type` ドロップダウン メニューの `C# (*.cs;*.csx)` を選択します。

    ![Visual Studio Code の [名前を付けて保存] ダイアログ](media/iot-hub-iot-edge-create-module/vscode-save-as.png)

13. `Ctrl` + `N` キーを押して、`Untitled-1` という名前の新しいファイルを作成します。

14. 以下のコード スニペットをコピーして`Untitled-1` ファイルに貼り付けます。 このクラスは `Azure IoT Edge`モジュールであり、`BleConverterModule` から受信したデータを出力するために使用します。

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace PrinterModule
   {
       public class DotNetPrinterModule : IGatewayModule
       {
           private string configuration;
           public void Create(Broker broker, byte[] configuration)
           {
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Destroy()
           {
           }
   
           public void Receive(Message received_message)
           {
               string recMsg = System.Text.Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               Dictionary<string, string> receivedProperties = received_message.Properties;
               
               BleConverterData receivedData = JsonConvert.DeserializeObject<BleConverterData>(recMsg);
   
               Console.WriteLine();
               Console.WriteLine("Module           : [DotNetPrinterModule]");
               Console.WriteLine("received_message : {0}", recMsg);
   
               if(received_message.Properties["source"] == "bleTelemetry")
               {
                   Console.WriteLine("Source           : {0}", receivedProperties["source"]);
                   Console.WriteLine("MAC address      : {0}", receivedProperties["macAddress"]);
                   Console.WriteLine("Temperature Alert: {0}", receivedProperties["temperatureAlert"]);
                   Console.WriteLine("Deserialized Obj : [BleConverterData]");
                   Console.WriteLine("  DeviceId       : {0}", receivedData.DeviceId);
                   Console.WriteLine("  MessageId      : {0}", receivedData.MessageId);
                   Console.WriteLine("  Temperature    : {0}", receivedData.Temperature);
               }
   
               Console.WriteLine();
           }
       }
   }
   ```

15. `Ctrl` + `Shift` + `S` キーを押して、ファイルを `DotNetPrinterModule.cs` として保存します。
    - [名前を付けて保存] ダイアログ ボックスで、`Save as Type` ドロップダウン メニューの `C# (*.cs;*.csx)` を選択します。

16. `Ctrl` + `N` キーを押して、`Untitled-1` という名前の新しいファイルを作成します。

17. `BleConverterModule` から受け取る `JSON` オブジェクトを逆シリアル化するために、次のコード スニペットをコピーして `Untitled-1` ファイルに貼り付けます。 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace PrinterModule
   {
       public class BleConverterData
       {
           [JsonProperty(PropertyName = "deviceId")]
           public string DeviceId { get; set; }
   
           [JsonProperty(PropertyName = "messageId")]
           public string MessageId { get; set; }
   
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

18. `Ctrl` + `Shift` + `S` キーを押して、ファイルを `BleConverterData.cs` として保存します。
    - [名前を付けて保存] ダイアログ ボックスで、`Save as Type` ドロップダウン メニューの `C# (*.cs;*.csx)` を選択します。

19. `Ctrl` + `N` キーを押して、`Untitled-1` という名前の新しいファイルを作成します。

20. 以下のコード スニペットをコピーして`Untitled-1` ファイルに貼り付けます。

   ```json
   {
       "loaders": [
           {
               "type": "dotnetcore",
               "name": "dotnetcore",
               "configuration": {
                   "binding.path": "dotnetcore.dll",
                   "binding.coreclrpath": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\coreclr.dll",
                   "binding.trustedplatformassemblieslocation": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\"
               }
           }
       ],
          "modules": [
           {
               "name": "simulated_device",
               "loader": {
                   "name": "native",
                   "entrypoint": {
                       "module.path": "simulated_device.dll"
                   }
               },
               "args": {
                   "macAddress": "01:02:03:03:02:01",
                   "messagePeriod": 500
               }
           },
           {
               "name": "ble_converter_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "IoTEdgeConverterModule.BleConverterModule"
                   }
               },
               "args": ""
           },
           {
               "name": "printer_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "PrinterModule.DotNetPrinterModule"
                   }
               },
               "args": ""
           }
       ],
       "links": [
           {
               "source": "simulated_device", "sink": "ble_converter_module"
           },
           {
               "source": "ble_converter_module", "sink": "printer_module"
           }
   ]
   }
   ```

21. `Ctrl` + `Shift` + `S` キーを押して、ファイルを `gw-config.json` として保存します。
    - [名前を付けて保存] ダイアログ ボックスで、`Save as Type` ドロップダウン メニューの `JSON (*.json;*.bowerrc;*.jshintrc;*.jscsrc;*.eslintrc;*.babelrc;*webmanifest)` を選択します。

22. 構成ファイルの出力ディレクトリへのコピーを有効にするために、`IoTEdgeConverterModule.csproj` を次の XML BLOB で更新します。

   ```xml
     <ItemGroup>
       <None Update="gw-config.json" CopyToOutputDirectory="PreserveNewest" />
     </ItemGroup>
   ```
    
   - 更新された `IoTEdgeConverterModule.csproj` ファイルは次の図のようになります。

    ![Visual Studio Code の更新された .csproj ファイル](media/iot-hub-iot-edge-create-module/vscode-update-csproj.png)

23. `Ctrl` + `N` キーを押して、`Untitled-1` という名前の新しいファイルを作成します。

24. 以下のコード スニペットをコピーして`Untitled-1` ファイルに貼り付けます。

   ```powershell
   Copy-Item -Path $env:userprofile\.nuget\packages\microsoft.azure.devices.gateway.native.windows.x64\1.1.3\runtimes\win-x64\native\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.formatters\4.3.0\lib\netstandard1.4\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.primitives\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\newtonsoft.json\10.0.2\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.componentmodel.typeconverter\4.3.0\lib\netstandard1.5\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.nongeneric\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.specialized\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   ```

25. `Ctrl` + `Shift` + `S` キーを押して、ファイルを `binplace.ps1` として保存します。
    - [名前を付けて保存] ダイアログ ボックスで、`Save as Type` ドロップダウン メニューの `PowerShell (*.ps1;*.psm1;*.psd1;*.pssc;*.psrc)` を選択します。

26. `Ctrl` + `Shift` + `B` キーを押して、プロジェクトをビルドします。 プロジェクトを初めてビルドすると、次の図に示すように、`Visual Studio Code` によって、`No build task defined.` ダイアログが表示されます。

    ![Visual Studio Code のビルド タスク ダイアログ](media/iot-hub-iot-edge-create-module/vscode-build-task.png)

    a) `Configure Build Task` ボタンをクリックします。

    b) `Select a Task Runner` ダイアログ ドロップダウン メニューで、 次の図に示すように、`.NET Core` を選択します。 

    ![Visual Studio Code のタスク選択ダイアログ](media/iot-hub-iot-edge-create-module/vscode-build-task-runner.png)

    c) `.NET Core` 項目をクリックすると、`tasks.json` ファイルが `.vscode` ディレクトリに作成され、そのファイルが `code editor` ウィンドウに開きます。 このファイルは変更する必要はないため、そのままタブを閉じます。

27.  `Ctrl` + `backtick` キーを押すか `View` -> `Integrated Terminal` メニューを使用して `Visual Studio Code` 統合ターミナルウィンドウを開き、`PowerShell` コマンド プロンプトに「**.\binplace.ps1**」と入力します。 このコマンドは、すべての依存関係を出力ディレクトリにコピーします。

28. `Integrated Terminal` ウィンドウで「**cd .\bin\Debug\netstandard1.3**」と入力して、プロジェクト出力ディレクトリに移動します。

29. `Integrated Terminal` ウィンドウプロンプトに 「**.\gw.exe gw-config.json**」と入力して、サンプル プロジェクトを実行します。 
    - このチュートリアルの手順をそのまま実行している場合は、次の図に示すように、`Azure IoT Edge BLE Data Converter Module` サンプル プロジェクトが実行されることを確認できます。
    
        ![Visual Studio Code で実行されるシミュレートされたデバイスの例](media/iot-hub-iot-edge-create-module/vscode-run.png)
    
    - アプリケーションを終了する場合は、`<Enter>` キーを押します。

>[!IMPORTANT]
`Ctrl` + `C` キーを使用して`IoT Edge` ゲートウェイ アプリケーション (**gw.exe**) を終了することはお勧めしません。 この操作は、プロセスを異常終了させる可能性があるためです。


