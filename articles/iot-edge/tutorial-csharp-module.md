---
title: 'チュートリアル: カスタム C# モジュールを作成する - Azure IoT Edge | Microsoft Docs'
description: このチュートリアルでは、C# コードで IoT Edge モジュールを作成し、Edge デバイスに展開する方法について説明します。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: f4afd83e31cf724e734b4e86045f8404e65753c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58088028"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>チュートリアル: C# IoT Edge モジュールを開発して、シミュレートされたデバイスにデプロイする

Azure IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。 [Windows](quickstart.md) または [Linux](quickstart-linux.md) のシミュレートされたデバイスに Azure IoT Edge をデプロイするクイック スタートで作成した、シミュレートされた IoT Edge デバイスを使用します。 このチュートリアルでは、以下の内容を学習します。    

> [!div class="checklist"]
> * Visual Studio Code を使用して .NET Core 2.1 SDK に基づく IoT Edge モジュールを作成する。
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、レジストリに発行する。
> * モジュールを IoT Edge デバイスに展開する。
> * 生成されたデータを表示する。

>[!NOTE]
>[Visual Studio 2017 を使用して IoT Edge モジュールを開発、デバッグ、およびデプロイ](how-to-visual-studio-develop-csharp-module.md)することもできます。

このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux デバイス](quickstart-linux.md) または [Windows デバイス](quickstart.md)のクイック スタートに記載された手順に従って開発マシンまたは仮想マシンをエッジ デバイスとして使用できます。

クラウド リソース:

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 

開発リソース:

* [Visual Studio Code](https://code.visualstudio.com/)。 
* [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* Visual Studio Code 用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
* [Docker CE](https://docs.docker.com/install/)


## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

このチュートリアルでは、Visual Studio Code 用の Azure IoT Tools を使用してモジュールをビルドし、ファイルから**コンテナー イメージ**を作成します。 その後、このイメージを**レジストリ**にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。  

コンテナー イメージは、Docker と互換性のある任意のレジストリを使用して格納できます。 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。 

コンテナー レジストリがまだない場合は、次の手順に従って、Azure に新しいコンテナー レジストリを作成します。

1. [Azure portal](https://portal.azure.com) で、**[リソースの作成]** > **[コンテナー]** > **[コンテナー レジストリ]** の順に選択します。

2. コンテナー レジストリを作成するには、以下の値を指定します。

   | フィールド | 値 | 
   | ----- | ----- |
   | レジストリ名 | 一意の名前を指定します。 |
   | サブスクリプション | ドロップダウン リストで、サブスクリプションを選択します。 |
   | リソース グループ | IoT Edge のクイック スタートおよびチュートリアルで作成するすべてのテスト リソースに、同じリソース グループを使用することをお勧めします。 たとえば、**IoTEdgeResources** を使用します。 |
   | Location | 近くの場所を選択します。 |
   | 管理者ユーザー | **[有効]** に設定します。 |
   | SKU | **[Basic]** を選択します。 | 

5. **作成**を選択します。

6. コンテナー レジストリが作成されたら、その場所を参照し、**[アクセス キー]** を選択します。 

7. **ログイン サーバー**、**ユーザー名**、および**パスワード**の値をコピーします。 これらの値は、このチュートリアルで後ほどコンテナー レジストリへのアクセスを提供するために使用します。 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge モジュール プロジェクトを作成する
以下の手順では、Visual Studio Code と Azure IoT Tools を使用して、.NET Core 2.0 SDK に基づく IoT Edge モジュール プロジェクトを作成します。

### <a name="create-a-new-solution"></a>新しいソリューションの作成

独自のコードでカスタマイズできる C# ソリューション テンプレートを作成します。 

1. Visual Studio Code で、**[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。 

2. コマンド パレットで、**Azure: Sign in** コマンドを入力して実行し、手順に従って Azure アカウントにサインインします。 既にサインインしている場合、この手順は省略できます。

3. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットに表示されるメッセージに従って、ソリューションを作成します。

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | ソリューションのためにわかりやすい名前を入力するか、既定値の **EdgeSolution** をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **C# モジュール**を選択します。 |
   | Provide a module name (モジュール名の指定) | ご自身のモジュールに **CSharpModule** と名前を付けます。 |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 前の手順で指定した名前がコンテナー イメージに事前設定されます。 **localhost:5000** を、Azure コンテナー レジストリのログイン サーバーの値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 <br><br>最終的なイメージ リポジトリは、\<registry name\>.azurecr.io/csharpmodule のようになります。 |
 
   ![Docker イメージ リポジトリを指定する](./media/tutorial-csharp-module/repository.png)

VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペースが読み込まれます。 ソリューション ワークスペースには、最上位の 5 つのコンポーネントが含まれています。 **modules** フォルダーには、モジュール用の C# コードと、モジュールをコンテナー イメージとして構築するための Dockerfiles が含まれています。 **\.env** ファイルには、コンテナー レジストリの資格情報が格納されています。 **deployment.template.json** ファイルには、デバイスにモジュールをデプロイするために IoT Edge ランタイムが使用する情報が含まれています。 また、**deployment.debug.template.json** ファイルには、モジュールのデバッグ バージョンが含まれています。 このチュートリアルでは、**\.vscode** フォルダーまたは **\.gitignore** ファイルは編集しません。

ソリューションの作成時にコンテナー レジストリを指定せず、既定値の localhost:5000 のままにすると、\.env ファイルは作成されません。 

<!--
   ![C# solution workspace](./media/tutorial-csharp-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー レジストリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。 

1. VS Code エクスプローラーで、.env ファイルを開きます。 
2. ご自身の Azure コンテナー レジストリからコピーした**ユーザー名**と**パスワード**の値を使用して、フィールドを更新します。 
3. このファイルを保存します。 

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

1. VS Code エクスプローラーで、**[モジュール]** > **[CSharpModule]** > **[Program.cs]** の順に開きます。

2. **[CSharpModule]** 名前空間の上部で、後で使用する型として 3 つの **using** ステートメントを追加します。

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. **temperatureThreshold** 変数を **Program** クラスに追加します。 この変数により、データが IoT Hub に送信される基準値が設定されます。データは、測定温度がこの値を超えると送信されます。 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. **MessageBody**、**Machine**、**Ambient** の各クラスを **Program** クラスに追加します。 これらのクラスは、受信メッセージの本文に対して予期されるスキーマを定義します。

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
    ```

5. **Init** メソッドでは、コードによって **ModuleClient** オブジェクトが作成され、構成されます。 このオブジェクトにより、モジュールはローカルの Azure IoT Edge ランタイムに接続し、メッセージを送受信することができます。 **Init** メソッドで使用される接続文字列は、IoT Edge ランタイムによってモジュールに提供されます。 **ModuleClient** の作成後、コードによって、モジュール ツインの目的のプロパティから **temperatureThreshold** が読み取られ、 IoT Edge ハブから **input1** エンドポイントを介してメッセージを受信するためのコールバックが登録されます。 **SetInputMessageHandlerAsync** メソッドを新しいメソッドで置き換え、対象プロパティの更新のために **SetDesiredPropertyUpdateCallbackAsync** メソッドを追加します。 この変更を行うには、**Init** メソッドの最後の行を次のコードに置き換えます。

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. **onDesiredPropertiesUpdate** メソッドを **Program** クラスに追加します。 このメソッドは、モジュール ツインから対象プロパティの更新を受け取り、それに合わせて **temperatureThreshold** 変数を更新します。 すべてのモジュールに独自のモジュール ツインがあり、これにより、モジュール内で実行されているコードをクラウドから直接構成できます。

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. **PipeMessage** メソッドを **FilterMessages** メソッドに置き換えます。 このメソッドは、モジュールが IoT Edge ハブからメッセージを受け取るたびに呼び出されます。 これにより、モジュール ツインで設定されているしきい値を下回る温度を報告するメッセージは除外されます。 また、**MessageType** プロパティを、値が **Alert** に設定されたメッセージに追加します。 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await moduleClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Program.cs ファイルを保存します。

9. VS Code のエクスプローラーで、ご自身の IoT Edge ソリューション ワークスペースの **deployment.template.json** ファイルを開きます。 このファイルは、IoT Edge エージェントにどのモジュール (この場合は **tempSensor** および **CSharpModule**) をデプロイするかを指示し、IoT Edge ハブにそれらの間でメッセージをルーティングする方法を指示します。 デプロイ テンプレートで必要なほとんどの情報は Visual Studio Code 拡張機能によって自動的に入力されますが、ソリューションに関してすべての情報が正確であることを確認します。 

   1. IoT Edge の既定のプラットフォームは、VS Code のステータス バーで **amd64** に設定されています。つまり、**CSharpModule** は Linux amd64 バージョンのイメージに設定されています。 IoT Edge デバイスのアーキテクチャに応じて、ステータス バーで既定のプラットフォームを **amd64** から **arm32v7** または **windows-amd64** に変更します。 

      ![モジュール イメージ プラットフォームを更新する](./media/tutorial-csharp-module/image-platform.png)

   2. テンプレートで、モジュール名が既定の **SampleModule** という名前ではなく、IoT Edge ソリューションの作成時に変更した正しい名前になっていることを確認します。

   3. IoT Edge エージェントがモジュール イメージを取得できるように、**registryCredentials** セクションには Docker レジストリの認証情報が格納されています。 実際のユーザー名とパスワードの組み合わせは、.env ファイル (Git では無視されます) に格納されます。 まだ行っていない場合は、.env ファイルに資格情報を追加します。  

   4. デプロイ マニフェストの詳細については、「[IoT Edge にモジュールをデプロイしてルートを確立する方法について説明します。](module-composition.md)」を参照してください。

10. **CSharpModule** モジュール ツインを配置マニフェストに追加します。 次の JSON コンテンツを **modulesContent** セクションの下部、**$edgeHub** モジュール ツインの後に挿入します。 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![モジュール ツインをデプロイ テンプレートに追加する](./media/tutorial-csharp-module/module-twin.png)

11. deployment.template.json ファイルを保存します。


## <a name="build-your-iot-edge-solution"></a>IoT Edge ソリューションのビルド

前のセクションでは、IoT Edge ソリューションを作成し、**CSharpModule** にコードを追加しました。これにより、許容されるしきい値をマシンの温度が下回ることを報告するメッセージが除外されます。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。 

1. Visual Studio Code 統合ターミナルで次のコマンドを入力して、Docker にサインインします。 これで、必要なモジュール イメージを Azure Container Registry にプッシュすることができます。
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   最初のセクションで Azure Container Registry からコピーしたユーザー名、パスワード、ログイン サーバーを使用します。 または、Azure portal でご自身のレジストリの **[アクセス キー]** セクションからこれらの値を取得することもできます。

2. VS Code エクスプローラーで、deployment.template.json ファイルを右クリックし、**[Build and Push IoT Edge solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。 

ソリューションのビルドを指示すると、最初に Visual Studio Code によって配置テンプレートの情報が読み取られて、**config** という名前の新しいフォルダーに deployment.json ファイルが生成されます。次に、`docker build` と `docker push` の 2 つのコマンドが統合ターミナルで実行されます。 この 2 つのコマンドによって、ご自身のコードがビルドされ、CSharpModule.dll がコンテナー化されたうえで、ソリューションを初期化したときに指定したコンテナー レジストリにコードがプッシュされます。 

タグを含む完全なコンテナー イメージ アドレスは、VS Code 統合ターミナルで確認できます。 イメージ アドレスは、\<リポジトリ\>:\<バージョン\>-\<プラットフォーム\> の形式で、module.json ファイルの情報から作成されます。 このチュートリアルでは、registryname.azurecr.io/csharpmodule:0.0.1-amd64 になります。

>[!TIP]
>モジュールをビルドしてプッシュしようとするときにエラーが発生した場合は、次の点を確認してください。
>* Visual Studio Code からお使いのコンテナー レジストリの資格情報を使用して Docker にサインインしているか。 これらの資格情報は、Azure portal にサインインする際に使用する情報とは異なります。
>* コンテナー リポジトリは正しいか。 **[modules]** > **[cmodule]** > **[module.json]** の順に開いて、**[repository]** フィールドを見つけます。 イメージ リポジトリは、**\<registryname\>.azurecr.io/csharpmodule** のようになっている必要があります。 
>* 開発マシンで実行されているものと同じタイプのコンテナーをビルドしているか。 Visual Studio Code の既定のコンテナーは Linux amd64 です。 お使いの開発マシンで Windows コンテナーまたは Linux arm32v7 コンテナーが実行されている場合は、実際のコンテナーのプラットフォームに合わせて、VS Code ウィンドウ下部の青色のステータス バーでプラットフォームを更新してください。

## <a name="deploy-and-run-the-solution"></a>ソリューションの配置と実行

IoT Edge デバイスの設定に使用したクイック スタートの記事では、Azure portal を使用してモジュールをデプロイしました。 また、Visual Studio Code 用の Azure IoT Hub Toolkit 拡張機能 (旧称 Azure IoT Toolkit 拡張機能) を使用して、モジュールをデプロイすることもできます。 シナリオ用の配置マニフェストである **deployment.json** ファイルは、既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

1. VS Code コマンド パレットで、**Azure IoT Hub: Select IoT Hub** を実行します。 

2. 構成する IoT Edge デバイスが含まれているサブスクリプションと IoT ハブを選択します。 

3. VS Code エクスプローラーで、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。 

4. IoT Edge デバイスの名前を右クリックして、**[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。 

   ![単一デバイスのデプロイを作成する](./media/tutorial-csharp-module/create-deployment.png)

5. **config**フォルダーで **deployment.json** ファイルを選択し、**[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。 deployment.template.json ファイルは使用しないでください。 

6. 更新ボタンをクリックします。 新しい **CSharpModule** が、**TempSensor** モジュール、**$edgeAgent** および **$edgeHub** と一緒に実行されていることが表示されます。  

## <a name="view-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスに配置マニフェストを適用すると、デバイス上の IoT Edge ランタイムが新しいデプロイ情報を収集し、そこで実行を開始します。 デバイス上で動作しているモジュールのうち、配置マニフェストに含まれていないものはすべて停止されます。 デバイスから不足しているモジュールがあればすべて開始されます。 

IoT Edge デバイスのステータスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを使用して確認できます。 デバイスの詳細を展開すると、デプロイされて実行中のモジュールが一覧表示されます。 

デプロイ モジュールのステータスは、IoT Edge デバイス自体で `iotedge list` コマンドを使用すると確認できます。 2 つの IoT Edge ランタイム モジュールと tempSensor、さらに、このチュートリアルで作成したカスタム モジュールの 4 つのモジュールが表示されると思います。 すべてのモジュールが開始されるまでには数分かかると考えられます。最初に表示されないモジュールがある場合は、コマンドを再実行してください。 

モジュールによって生成されているメッセージを確認するには、`iotedge logs <module name>` コマンドを使用します。 

IoT ハブに到着したメッセージは、Visual Studio Code を使用して確認できます。 

1. IoT Hub に到着するデータを監視するには、省略記号 (**...**) を選択し、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
2. 特定のデバイスの D2C のメッセージを監視するには、一覧でデバイスを右クリックし、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
3. データの監視を停止するには、コマンド パレットで **Azure IoT Hub:Stop monitoring D2C message** コマンドを実行します。 
4. モジュール ツインを表示または更新するには、一覧でモジュールを右クリックし、**[Edit module twin]\(モジュール ツインの編集\)** を選択します。 モジュール ツインを更新するには、ツインの JSON ファイルを保存し、編集領域を右クリックして、**[Update Module Twin]\(モジュール ツインの更新\)** を選択します。
5. Docker のログを表示するために、[Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) for VS Code をインストールします。 ローカルで実行されているモジュールを Docker エクスプローラーで確認できます。 コンテキスト メニューで、**[ログを表示する]** を選択し、統合ターミナルで表示します。
 
## <a name="clean-up-resources"></a>リソースのクリーンアップ 

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。 

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 独自のモジュールをビルドする準備ができたら、[Visual Studio Code 用の Azure IoT Edge で C# モジュールを開発する](how-to-develop-csharp-module.md)方法の詳細をご覧ください。 引き続き次のチュートリアルに進み、Azure IoT Edge が、エッジでデータをビジネス上の分析情報に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [SQL Server データベースを使用してエッジでデータを格納する](tutorial-store-data-sql-server.md)
