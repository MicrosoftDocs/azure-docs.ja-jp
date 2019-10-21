---
title: 'チュートリアル: Azure 関数をデバイスに展開する - Azure IoT Edge | Microsoft Docs'
description: このチュートリアルでは、Azure 関数を IoT Edge モジュールとして開発した後、エッジ デバイスにそれを展開します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: b0007f578efa1c9f8653e1ab6295b81e554245bd
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123356"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>チュートリアル: Azure 関数を IoT Edge モジュールとして展開する

Azure Functions を使用して、ビジネス ロジックを実装するコードを Azure IoT Edge デバイスに直接展開できます。 このチュートリアルでは、シミュレートされた IoT Edge デバイス上のセンサー データをフィルター処理する Azure 関数の作成と展開について段階的に説明します。 [Windows](quickstart.md) または [Linux](quickstart-linux.md) のシミュレートされたデバイスに Azure IoT Edge をデプロイするクイック スタートで作成した、シミュレートされた IoT Edge デバイスを使用します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
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

このチュートリアルを開始する前に、前のチュートリアルを完了して、Linux コンテナー開発用の開発環境を設定しておく必要があります。[Linux のデバイス用の IoT Edge モジュールを開発する](tutorial-develop-for-linux.md)。 このチュートリアルを完了すると、次の前提条件が満たされます。 

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* [Azure IoT Edge を実行している Linux デバイス](quickstart-linux.md)
* コンテナー レジストリ ([Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) など)。
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して構成された [Visual Studio Code](https://code.visualstudio.com/)。
* Linux コンテナーを実行するように構成された [Docker CE](https://docs.docker.com/install/)。

Azure Functions を使用して IoT Edge モジュールを開発するには、開発用マシンに次の追加の前提条件をインストールします。 

* [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [The .NET Core 2.1 SDK](https://www.microsoft.com/net/download)。

## <a name="create-a-function-project"></a>関数プロジェクトを作成する

前提条件としてインストールした Visual Studio Code 用 Azure IoT Tools は、いくつかのコード テンプレートと管理機能を提供します。 このセクションでは、Visual Studio Code を使用して、Azure 関数を含む IoT Edge ソリューションを作成します。 

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

独自のコードでカスタマイズできる C# 関数ソリューション テンプレートを作成します。

1. 開発用マシンで Visual Studio Code を開きます。

2. **[表示]**  >  **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。

3. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットに表示されるメッセージに従って、ソリューションを作成します。

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | **FunctionSolution** のように、ソリューションのわかりやすい名前を入力するか、既定値をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **[Azure Functions - C#]** を選択します。 |
   | Provide a module name (モジュール名の指定) | モジュールに **CSharpFunction** という名前を付けます。 |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 コンテナー イメージは、前の手順で事前設定されます。 **localhost:5000** を、Azure コンテナー レジストリのログイン サーバーの値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 文字列は最終的に、\<レジストリ名\>.azurecr.io/CSharpFunction のようになります。 |

   ![Docker イメージ リポジトリを指定する](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー レジストリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。

1. VS Code エクスプローラーで、.env ファイルを開きます。
2. ご自身の Azure コンテナー レジストリからコピーした**ユーザー名**と**パスワード**の値を使用して、フィールドを更新します。
3. このファイルを保存します。

### <a name="select-your-target-architecture"></a>ターゲット アーキテクチャを選択する

現在、Visual Studio Code では、Linux AMD64 および Linux ARM32v7 デバイス用の C モジュールを開発できます。 ソリューションごとにターゲットとするアーキテクチャを選択する必要があります。これは、アーキテクチャの種類によって、コンテナーのビルド方法と実行方法が異なるためです。 既定値は Linux AMD64 です。 

1. コマンド パレットを開き、次を検索します: 「**Azure IoT Edge: Set Default Target Platform for Edge Solution (Azure IoT Edge: Edge ソリューションの既定のターゲット プラットフォームの設定)** 」。または、ウィンドウの下部にあるサイド バーで、ショートカット アイコンを選択します。 

2. コマンド パレットで、オプションの一覧からターゲット アーキテクチャを選択します。 このチュートリアルでは、Ubuntu 仮想マシンを IoT Edge デバイスとして使用するため、既定値の **amd64** のままにします。 

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

モジュールがメッセージを IoT Hub に転送する前に、エッジでそれらを処理できるように、追加のコードを追加してみましょう。

1. Visual Studio Code で、 **[モジュール]**  >  **[CSharpFunction]**  >  **[CSharpFunction.cs]** の順に開きます。

1. **CSharpFunction.cs** ファイルの内容を次のコードに置き換えます。 このコードは、周囲温度とマシン温度に関するテレメトリを受け取り、定義されたしきい値をマシン温度が超えた場合にのみ、IoT Hub にメッセージを転送します。

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
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
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

1. ファイルを保存します。

## <a name="build-your-iot-edge-solution"></a>IoT Edge ソリューションのビルド

前のセクションでは、IoT Edge ソリューションを作成し、**CSharpFunction** にコードを追加しました。これにより、報告されるマシンの温度が許容可能なしきい値を下回っている場合のメッセージがフィルターで除外されます。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。

このセクションでは、Visual Studio Code からレジストリにイメージをプッシュできるように、お使いの開発用マシンからローカルでサインインして、2 回目にはコンテナー レジストリの資格情報を指定します (最初は IoT Edge ソリューションの **.env**  ファイル内にありました)

1. **[表示]**  >  **[ターミナル]** を選択して、VS Code 統合ターミナルを開きます。 

2. 統合ターミナルで次のコマンドを入力して、コンテナー レジストリにサインインします。 前に Azure Container Registry からコピーしたユーザー名とログイン サーバーを使用します。

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    パスワードの入力を求めるメッセージが表示されたら、コンテナー レジストリのパスワード (ターミナル ウィンドウには表示されません) を貼り付けて **Enter** キーを押します。

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. VS Code エクスプローラーで、deployment.template.json ファイルを右クリックし、 **[Build and Push IoT Edge solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。 

ソリューションのビルドを指示すると、最初に Visual Studio Code によって配置テンプレートの情報が読み取られて、**config** という名前の新しいフォルダーに deployment.json ファイルが生成されます。次に、`docker build` と `docker push` の 2 つのコマンドが統合ターミナルで実行されます。 これらの 2 つのコマンドによって、コードがビルドされ、関数がコンテナー化されたうえで、ソリューションを初期化したときに指定したコンテナー レジストリにコードがプッシュされます。 

## <a name="view-your-container-image"></a>コンテナー イメージの表示

コンテナー イメージがコンテナー レジストリにプッシュされると、Visual Studio Code によって成功メッセージが出力されます。 操作の成功を自分で確認したい場合は、レジストリのイメージを表示できます。 

1. Azure portal で、Azure Container Registry に移動します。 
2. **[リポジトリ]** を選択します。
3. **csharpfunction** リポジトリが一覧に表示されます。 このリポジトリを選択すると、さらに詳しい情報が表示されます。
4. **[タグ]** セクションには、**0.0.1-amd64** タグが表示されます。 このタグには、作成したイメージのバージョンとプラットフォームが示されます。 これらの値は、CSharpFunction フォルダーの module.json ファイルで設定されます。 

## <a name="deploy-and-run-the-solution"></a>ソリューションの配置と実行

クイック スタートで行ったように、Azure portal を使用して関数モジュールを IoT Edge デバイスに展開できます。 また、Visual Studio Code 内からモジュールを配置して、監視することもできます。 以降のセクションでは、前提条件で示された VS Code 用の Azure IoT Tools を使用します。 この拡張機能をまだインストールしていない場合は、ここでインストールしてください。 

1. VS Code エクスプローラーで、 **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。 

2. IoT Edge デバイスの名前を右クリックし、 **[単一デバイスの展開を作成する]** を選択します。 

3. **CSharpFunction** が含まれているソリューション フォルダーの場所を参照します。 config フォルダーを開き、**deployment.json** ファイルを選択して、 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** を選択します。

4. **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを更新します。 新しい **CSharpFunction** が、**SimulatedTemperatureSensor** モジュール、 **$edgeAgent** および **$edgeHub** と一緒に実行されていることがわかります。 新しいモジュールが表示されるまでに、数分かかる場合があります。 IoT Edge デバイスは、その新しいデプロイ情報を IoT Hub から取得し、新しいコンテナーを起動した後、その状態を IoT Hub にレポートする必要があります。 

   ![VS Code での配置されたモジュールの表示](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>生成されたデータを表示する

コマンド パレットで **Azure IoT Hub: Start Monitoring Built-in Event Endpoint** を実行することによって、IoT ハブに届くすべてのメッセージを確認できます。

また、特定のデバイスから IoT Hub に届くすべてのメッセージが表示されるよう、ビューをフィルター処理することもできます。 **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクション内でデバイスを右クリックして、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。

メッセージの監視を停止するには、コマンド パレットで **Azure IoT Hub: Stop Monitoring Built-in Event Endpoint** コマンドを実行します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。 

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む Azure 関数モジュールを作成しました。 独自のモジュールをビルドする準備ができたら、[Visual Studio Code を使用した Azure IoT Edge 用の開発](how-to-vs-code-develop-module.md)方法の詳細をご覧ください。 

引き続き次のチュートリアルを実行すると、Azure IoT Edge を利用して、エッジでデータをビジネス上の分析情報に変える他の方法について学習できます。

> [!div class="nextstepaction"]
> [Azure Stream Analytics でフローティング ウィンドウを使用して平均値を見つける](tutorial-deploy-stream-analytics.md)
