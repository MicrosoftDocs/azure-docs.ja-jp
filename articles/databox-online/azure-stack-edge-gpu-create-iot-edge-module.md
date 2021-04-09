---
title: GPU 搭載の Azure Stack Edge Pro 向け C# IoT Edge モジュール | Microsoft Docs
description: Azure Stack Edge Pro GPU デバイスにデプロイできる C# IoT Edge モジュールの開発方法について学習します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: d2ab96fcfa33301f0bd1212b23f9418fa39d0134
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102637107"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro 上のファイルを移動する C# IoT Edge モジュールを開発する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro デバイスにデプロイするために、IoT Edge モジュールを作成する方法を説明します。 Azure Stack Edge Pro は、データを処理してネットワーク経由で Azure に送信できるストレージ ソリューションです。

Azure IoT Edge モジュールを Azure Stack Edge Pro と共に使用して、データを変換し、Azure に移動することができます。 この記事で使用されるモジュールによって、Azure Stack Edge Pro デバイス上でファイルをローカル共有からクラウド共有にコピーするロジックが実装されます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * モジュールを格納して管理するコンテナー レジストリを作成する (Docker イメージ)。
> * IoT Edge モジュールを作成して Azure Stack Edge Pro デバイスにデプロイする。


## <a name="about-the-iot-edge-module"></a>IoT Edge モジュールについて

Azure Stack Edge Pro デバイスでは、IoT Edge モジュールをデプロイして実行できます。 Edge モジュールは基本的には Docker コンテナーであり、デバイスからのメッセージの取り込み、メッセージの変換、IoT Hub へのメッセージの送信など、特定のタスクを実行できます。 この記事では、Azure Stack Edge Pro デバイス上でファイルをローカル共有からクラウド共有にコピーするモジュールを作成します。

1. ファイルは、Azure Stack Edge Pro デバイス上のローカル共有に書き込まれます。
2. ファイル イベント ジェネレーターは、ローカル共有に書き込まれる各ファイルに対して、ファイル イベントを作成します。 ファイル イベントは、ファイルが変更されたときも生成されます。 その後ファイル イベントは (IoT Edge ランタイムの) IoT Edge Hub に送信されます。
3. IoT Edge のカスタム モジュールは、ファイル イベントを処理して、ファイルへの相対パスも含むファイル イベント オブジェクトを作成します。 モジュールは、相対ファイル パスを使用して絶対パスを生成し、ファイルをローカル共有からクラウド共有にコピーします。 その後、モジュールはファイルをローカル共有から削除します。

![Azure Stack Edge Pro での Azure IoT Edge モジュールのしくみ](./media/azure-stack-edge-gpu-create-iot-edge-module/how-module-works-1.png)

ファイルがクラウド共有に移動すると、ユーザーの Azure Storage アカウントに自動的にアップロードされます。

## <a name="prerequisites"></a>前提条件

開始する前に、次のものがあることを確認します。

- 実行中の Azure Stack Edge Pro デバイス。

    - デバイスには、関連付けられた IoT Hub リソースもある。
    - デバイスで Edge コンピューティング ロールが構成されている。
    詳細については、Azure Stack Edge Pro での「[コンピューティングの構成](azure-stack-edge-j-series-deploy-configure-compute.md#configure-compute)」を参照してください。<!--Update link?-->

- 次の開発リソース。

    - [Visual Studio Code](https://code.visualstudio.com/)。
    - [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
    - [Visual Studio Code 用の Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows)。 ソフトウェアをダウンロードしてインストールするには、アカウントの作成が必要になる場合があります。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

Azure Container Registry は、プライベート Docker コンテナー イメージを保存および管理する Azure のプライベート Docker レジストリです。 クラウドで使用できる 2 つの一般的な Docker レジストリ サービスは、Azure Container Registry と Docker Hub です。 この記事では、Container Registry を使用します。

1. Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。
2. **[Create a resource] (リソースの作成) > [コンテナー] > [Container Registry]** を選択します。 **Create** をクリックしてください。
3. 次を指定します。

   1. 5 - 50 文字の英数字を含む、Azure 内で一意の **レジストリ名**。
   2. **[サブスクリプション]** を選択します。
   3. 新しい **リソース グループ** を作成するか、既存のリソース グループを選択します。
   4. **[場所]** を選択します。 この場所は、Azure Stack Edge リソースと関連付けられているのと同じ場所にすることをお勧めします。
   5. **[管理者ユーザー]** を **[有効]** に切り替えます。
   6. SKU を **[Basic]** に設定します。

      ![コンテナー レジストリを作成する](./media/azure-stack-edge-gpu-create-iot-edge-module/create-container-registry-1.png)
 
4. **［作成］** を選択します
5. コンテナー レジストリが作成されたら、その場所を参照し、 **[アクセス キー]** を選択します。

    ![アクセス キーを取得する](./media/azure-stack-edge-gpu-create-iot-edge-module/get-access-keys-1.png)
 
6. **ログイン サーバー**、**ユーザー名**、および **パスワード** の値をコピーします。 これらの値は、後で Docker イメージをレジストリに発行し、レジストリの資格情報を Azure IoT Edge ランタイムに追加する際に使用します。


## <a name="create-an-iot-edge-module-project"></a>IoT Edge モジュール プロジェクトを作成する

次のステップでは、.NET Core 2.1 SDK に基づいて IoT Edge モジュール プロジェクトを作成します。 プロジェクトでは、Visual Studio Code と Azure IoT Edge の拡張機能を使用します。

### <a name="create-a-new-solution"></a>新しいソリューションの作成

独自のコードでカスタマイズできる C# ソリューション テンプレートを作成します。

1. Visual Studio Code で、 **[表示] > [コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。
2. コマンド パレットで、**Azure: Sign in** コマンドを入力して実行し、手順に従って Azure アカウントにサインインします。 既にサインインしている場合、この手順は省略できます。
3. コマンド パレットで、**Azure IoT Edge:New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットで、次の情報を指定してソリューションを作成します。

    1. ソリューションの作成先フォルダーを選択します。
    2. ソリューションの名前を指定するか、既定の **EdgeSolution** をそのまま使用します。
    
        ![新しいソリューションの作成 1](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-1.png)

    3. モジュール テンプレートとして **C# Module** を選択します。
    4. 既定のモジュール名を指定する名前で置き換えます。この場合は **FileCopyModule** です。
    
        ![新しいソリューションの作成 2](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-2.png)

    5. 前のセクションで作成したコンテナー レジストリを、最初のモジュールのイメージ リポジトリとして指定します。 **localhost:5000** を、コピーしたログイン サーバーの値に置き換えます。

        最終的な文字列は、`<Login server name>/<Module name>` のようになります。 この例では、文字列は `mycontreg2.azurecr.io/filecopymodule` です。

        ![新しいソリューションの作成 3](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-3.png)

4. **[ファイル] > [フォルダーを開く]** に移動します。

    ![新しいソリューションの作成 4](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-4.png)

5. 先に作成した **[EdgeSolution]** フォルダーを選択します。 VS Code ウィンドウに、IoT Edge ソリューション ワークスペースと 5 つの上位レベル コンポーネントが表示されます。 この記事では、`.vscode` フォルダー、 **.gitignore** ファイル、 **.env** ファイル、deployment.template.json** は編集しません。
    
    変更するコンポーネントは、モジュール フォルダーだけです。 このフォルダーには、モジュールの C# コードと、モジュールをコンテナー イメージとしてビルドするための Docker ファイルが含まれています。

    ![新しいソリューションの作成 5](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

1. VS Code エクスプローラーで、 **[モジュール] > [FileCopyModule] > [Program.cs]** の順に開きます。
2. **[FileCopyModule]** 名前空間の上部で、後で使用する型として次の using ステートメントを追加します。 **Microsoft.Azure.Devices.Client.Transport.Mqtt** は、メッセージを IoT Edge Hub に送信するためのプロトコルです。

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. **InputFolderPath** と **OutputFolderPath** 変数を Program クラスに追加します。

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. 前の手順の直後に、**FileEvent** クラスを追加してメッセージ本文を定義します。

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. **Init メソッド** では、コードによって **ModuleClient** オブジェクトが作成され、構成されます。 このオブジェクトにより、モジュールは MQTT プロトコルを使用してローカルの Azure IoT Edge ランタイムに接続し、メッセージを送受信することができます。 Init メソッドで使用される接続文字列は、IoT Edge ランタイムによってモジュールに提供されます。 コードによって、IoT Edge ハブから **input1** エンドポイントを介してメッセージを受信するための FileCopy コールバックが登録されます。 **Init メソッド** を次のコードに置き換えます。

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. **PipeMessage メソッド** のコードを削除し、その代わりに **FileCopy** のコードを挿入します。

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub.
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. このファイルを保存します。
8. このプロジェクト用の[既存のコード サンプルをダウンロード](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable)することもできます。 このサンプルでは、**program.cs** ファイルに対して保存したファイルを検証できます。

## <a name="build-your-iot-edge-solution"></a>IoT Edge ソリューションのビルド

前のセクションで IoT Edge ソリューションを作成して、ファイルをローカル共有からクラウド共有にコピーするためにコードを FileCopyModule に追加しました。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。

1. VSCode で、[Terminal] (ターミナル) > [New Terminal] (新しいターミナル) に移動して、新しい Visual Studio Code 統合ターミナルを開きます。
2. 統合ターミナルで次のコマンドを入力して、Docker にサインインします。

    `docker login <ACR login server> -u <ACR username>`

    コンテナー レジストリからコピーしたログイン サーバーとユーザー名を使用します。

    ![IoT Edge ソリューションをビルドしてプッシュする](./media/azure-stack-edge-gpu-create-iot-edge-module/build-iot-edge-solution-1.png)

2. 入力を求められたら、パスワードを入力します。 ログイン サーバー、ユーザー名、パスワードは、Azure portal のコンテナー レジストリ内の **[アクセス キー]** から取得することもできます。
 
3. 資格情報を提供すると、モジュール イメージを Azure Container Registry にプッシュできます。 VS Code エクスプローラーで、**module.json** ファイルを右クリックし、 **[Build and Push IoT Edge solution] (IoT Edge ソリューションのビルドとプッシュ)** を選択します。

    ![IoT Edge ソリューションをビルドしてプッシュする 2](./media/azure-stack-edge-gpu-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Visual Studio Code でソリューションをビルドすると、統合ターミナルで 2 つのコマンドが実行されます。docker build と docker push です。 この 2 つのコマンドによって、ご自身のコードがビルドされ、CSharpModule.dll がコンテナー化されたうえで、ソリューションを初期化したときに指定したコンテナー レジストリにコードがプッシュされます。

    モジュール プラットフォームを選択するように求められます。 Linux に対応する *amd64* を選択します。

    ![プラットフォームの選択](./media/azure-stack-edge-gpu-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Linux のモジュールのみがサポートされています。

    次の警告が表示されますが、無視できます。

    *Program.cs(77,44): warning CS1998:This async method lacks 'await' operators and will run synchronously.Consider using the 'await' operator to await non-blocking API calls, or 'await Task.Run(...)' to do CPU-bound work on a background thread.* (この非同期メソッドには 'await' 演算子がないため、同期的に実行されます。'await' 演算子を使用して非ブロッキング API 呼び出しを待機するか、'await Task.Run(...)' を使用してバックグラウンドのスレッドに対して CPU 主体の処理を実行することを検討してください。)

4. タグを含む完全なコンテナー イメージ アドレスは、VS Code 統合ターミナルで確認できます。 イメージ アドレスは、`<repository>:<version>-<platform>` の形式で、module.json ファイルの情報から作成されます。 この記事では、`mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64` のようになります。

## <a name="next-steps"></a>次のステップ

Azure Stack Edge Pro でこのモジュールをデプロイして実行するには、「[モジュールの追加](azure-stack-edge-j-series-deploy-configure-compute.md#add-a-module)」の手順を参照してください。<!--Update link?-->
