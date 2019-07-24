---
title: 'チュートリアル: Azure 関数をデバイスに展開する - Azure IoT Edge | Microsoft Docs'
description: このチュートリアルでは、Azure 関数を IoT Edge モジュールとして開発した後、エッジ デバイスにそれを展開します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 1fba2c4e5191d4c827035362a8eb6876fcbb67cc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58081748"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>チュートリアル: Azure 関数を IoT Edge モジュールとして展開する

Azure Functions を使用して、ビジネス ロジックを実装するコードを Azure IoT Edge デバイスに直接展開できます。 このチュートリアルでは、シミュレートされた IoT Edge デバイス上のセンサー データをフィルター処理する Azure 関数の作成と展開について段階的に説明します。 [Windows](quickstart.md) または [Linux](quickstart-linux.md) のシミュレートされたデバイスに Azure IoT Edge をデプロイするクイック スタートで作成した、シミュレートされた IoT Edge デバイスを使用します。 このチュートリアルでは、以下の内容を学習します。     

> [!div class="checklist"]
> * Visual Studio Code を使用して、Azure 関数を作成する。
> * VS Code と Docker を使用して Docker イメージを作成し、コンテナー レジストリに発行する。
> * コンテナー レジストリから IoT Edge デバイスにモジュールを配置する。
> * フィルター処理されたデータを表示する。

<center>

![図 - チュートリアルのアーキテクチャ、関数モジュールのステージングと展開](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Azure IoT Edge 上の Azure Function モジュールは、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)段階にあります。 

このチュートリアルで作成する Azure 関数は、デバイスによって生成される温度データをフィルター処理します。 この関数は、指定されたしきい値を温度が上回っているときにのみ、上流の Azure IoT Hub にメッセージを送信します。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux デバイス](quickstart-linux.md) または [Windows デバイス](quickstart.md)のクイック スタートに記載された手順に従って開発マシンまたは仮想マシンをエッジ デバイスとして設定できます。

クラウド リソース:

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 

開発リソース:

* [Visual Studio Code](https://code.visualstudio.com/)。 
* [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Visual Studio Code 用 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。 
* [The .NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
* [Docker CE](https://docs.docker.com/install/)。 

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

このチュートリアルでは、Visual Studio Code 用の Azure IoT Tools を使用してモジュールをビルドし、ファイルから**コンテナー イメージ**を作成します。 その後、このイメージを**レジストリ**にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。  

コンテナー イメージは、Docker と互換性のある任意のレジストリを使用して格納できます。 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。 

1. [Azure portal](https://portal.azure.com) で、**[リソースの作成]** > **[コンテナー]** > **[コンテナー レジストリ]** の順に選択します。

    ![Azure portal を使用したコンテナー レジストリの作成](./media/tutorial-deploy-function/create-container-registry.png)

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

## <a name="create-a-function-project"></a>関数プロジェクトを作成する

前提条件としてインストールした Visual Studio Code 用 Azure IoT Tools は、いくつかのコード テンプレートと管理機能を提供します。 このセクションでは、Visual Studio Code を使用して、Azure 関数を含む IoT Edge ソリューションを作成します。 

1. 開発用マシンで Visual Studio Code を開きます。

2. **[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。

3. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットに表示されるメッセージに従って、ソリューションを作成します。

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | **FunctionSolution** のように、ソリューションのわかりやすい名前を入力するか、既定値をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **[Azure Functions - C#]** を選択します。 |
   | Provide a module name (モジュール名の指定) | モジュールに **CSharpFunction** という名前を付けます。 |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 コンテナー イメージは、前の手順で事前設定されます。 **localhost:5000** を、Azure コンテナー レジストリのログイン サーバーの値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 文字列は最終的に、\<レジストリ名\>.azurecr.io/CSharpFunction のようになります。 |

   ![Docker イメージ リポジトリを指定する](./media/tutorial-deploy-function/repository.png)

4. VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペース (\.vscode フォルダー、modules フォルダー、配置マニフェスト テンプレート ファイル、 \.env ファイル) が読み込まれます。 VS Code エクスプローラーで、**[モジュール]** > **[CSharpFunction]** > **[CSharpFunction.cs]** の順に開きます。

5. **CSharpFunction.cs** ファイルの内容を次のコードに置き換えます。 このコードは、周囲温度とマシン温度に関するテレメトリを受け取り、定義されたしきい値をマシン温度が超えた場合にのみ、IoT Hub にメッセージを転送します。

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

6. ファイルを保存します。

## <a name="build-your-iot-edge-solution"></a>IoT Edge ソリューションのビルド

前のセクションでは、IoT Edge ソリューションを作成し、**CSharpFunction** にコードを追加しました。これにより、報告されるマシンの温度が許容可能なしきい値を下回っている場合のメッセージがフィルターで除外されます。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。

このセクションでは、コンテナー レジストリの資格情報を 2 回指定します。 1 回目では、開発用マシンからローカルにサインインして、Visual Studio Code がイメージをレジストリにプッシュできるようにします。 2 回目は IoT Edge ソリューションの **.env** ファイル内で行います。これにより、レジストリからイメージを取得するアクセス許可が IoT Edge デバイスに与えられます。 

1. **[表示]** > **[ターミナル]** を選択して、VS Code 統合ターミナルを開きます。 

2. 統合ターミナルで次のコマンドを入力して、コンテナー レジストリにサインインします。 前に Azure Container Registry からコピーしたユーザー名とログイン サーバーを使用します。
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    パスワードの入力を求めるメッセージが表示されたら、コンテナー レジストリのパスワードを貼り付けて **Enter** キーを押します。

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. VS Code のエクスプローラーで、ご自身の IoT Edge ソリューション ワークスペースの **deployment.template.json** ファイルを開きます。 このファイルでは、IoT Edge ランタイムによってデバイスに配置されるモジュールが指定されます。 関数モジュール **CSharpFunction** が、テスト データを提供する **tempSensor** モジュールと共に一覧に表示されていることに注意してください。 配置マニフェストの詳細については、[IoT Edge モジュールを使用、構成、再利用する方法の確認](module-composition.md)に関するページをご覧ください。

   ![配置マニフェストのモジュールを表示する](./media/tutorial-deploy-function/deployment-template.png)

3. IoT Edge ソリューション ワークスペース内の **.env** ファイルを開きます。 この git で無視されるファイルにコンテナー レジストリの資格情報が格納されているため、配置マニフェスト テンプレートには資格情報を入れる必要はありません。 コンテナー レジストリの**ユーザー名**と**パスワード**を入力してください。 

5. このファイルを保存します。

6. VS Code エクスプローラーで、deployment.template.json ファイルを右クリックし、**[Build and Push IoT Edge solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。 

ソリューションのビルドを指示すると、最初に Visual Studio Code によって配置テンプレートの情報が読み取られて、**config** という名前の新しいフォルダーに deployment.json ファイルが生成されます。次に、`docker build` と `docker push` の 2 つのコマンドが統合ターミナルで実行されます。 これらの 2 つのコマンドによって、コードがビルドされ、関数がコンテナー化されたうえで、ソリューションを初期化したときに指定したコンテナー レジストリにコードがプッシュされます。 

## <a name="view-your-container-image"></a>コンテナー イメージの表示

コンテナー イメージがコンテナー レジストリにプッシュされると、Visual Studio Code によって成功メッセージが出力されます。 操作の成功を自分で確認したい場合は、レジストリのイメージを表示できます。 

1. Azure portal で、Azure Container Registry に移動します。 
2. **[リポジトリ]** を選択します。
3. **csharpfunction** リポジトリが一覧に表示されます。 このリポジトリを選択すると、さらに詳しい情報が表示されます。
4. **[タグ]** セクションには、**0.0.1-amd64** タグが表示されます。 このタグには、作成したイメージのバージョンとプラットフォームが示されます。 これらの値は、CSharpFunction フォルダーの module.json ファイルで設定されます。 

## <a name="deploy-and-run-the-solution"></a>ソリューションの配置と実行

クイック スタートで行ったように、Azure portal を使用して関数モジュールを IoT Edge デバイスに展開できます。 また、Visual Studio Code 内からモジュールを配置して、監視することもできます。 以降のセクションでは、前提条件で示された VS Code 用の Azure IoT Tools を使用します。 この拡張機能をまだインストールしていない場合は、ここでインストールしてください。 

1. **[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。

2. **Azure: Sign in** コマンドを検索して、実行します。 手順に従って Azure アカウントにサインインします。 

3. コマンド パレットで、**Azure IoT Hub: Select IoT Hub** コマンドを検索して実行します。 

4. 自分の IoT Hub が含まれているサブスクリプションを選択し、アクセス先の IoT Hub を選択します。

5. VS Code エクスプローラーで、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。 

6. IoT Edge デバイスの名前を右クリックし、**[単一デバイスの展開を作成する]** を選択します。 

7. **CSharpFunction** が含まれているソリューション フォルダーの場所を参照します。 config フォルダーを開き、**deployment.json** ファイルを選択して、**[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** を選択します。

8. **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを更新します。 新しい **CSharpFunction** が、**TempSensor** モジュール、**$edgeAgent** および **$edgeHub** と一緒に実行されていることがわかります。 新しいモジュールが表示されるまでに、数分かかる場合があります。 IoT Edge デバイスは、その新しいデプロイ情報を IoT Hub から取得し、新しいコンテナーを起動した後、その状態を IoT Hub にレポートする必要があります。 

   ![VS Code での配置されたモジュールの表示](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>生成されたデータを表示する

コマンド パレットで **Azure IoT Hub: Start Monitoring D2C Message** を実行することで、IoT ハブに届くすべてのメッセージを確認できます。

また、特定のデバイスから IoT Hub に届くすべてのメッセージが表示されるよう、ビューをフィルター処理することもできます。 **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションのデバイスを右クリックして、**Start Monitoring D2C Messages** を選択します。

メッセージの監視を停止するには、コマンド パレットで **Azure IoT Hub: Stop monitoring D2C message** コマンドを実行します。 


## <a name="clean-up-resources"></a>リソースのクリーンアップ

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。 

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む Azure 関数モジュールを作成しました。 独自のモジュールをビルドする準備ができたら、[Visual Studio Code 用の Azure IoT Edge で Azure 関数を開発する](how-to-develop-csharp-function.md)方法の詳細をご覧ください。 

引き続き次のチュートリアルを実行すると、Azure IoT Edge を利用して、エッジでデータをビジネス上の分析情報に変える他の方法について学習できます。

> [!div class="nextstepaction"]
> [Azure Stream Analytics でフローティング ウィンドウを使用して平均値を見つける](tutorial-deploy-stream-analytics.md)

