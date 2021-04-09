---
title: チュートリアル:Azure Functions をモジュールとして展開する - Azure IoT Edge
description: このチュートリアルでは、Azure Functions を IoT Edge モジュールとして開発した後、エッジ デバイスに展開します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 85bc54402237ff8e9c82c43ab9cdbe2dc6658af1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464107"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>チュートリアル:Azure Functions を IoT Edge モジュールとして展開する

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Functions を使用して、ビジネス ロジックを実装するコードを Azure IoT Edge デバイスに直接展開できます。 このチュートリアルでは、シミュレートされた IoT Edge デバイス上のセンサー データをフィルター処理する Azure Functions の作成と展開について段階的に説明します。 ここでは、クイックスタートで作成した、シミュレートされた IoT Edge デバイスを使用します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Visual Studio Code を使用して、Azure Functions を作成する。
> * VS Code と Docker を使用して Docker イメージを作成し、コンテナー レジストリに発行する。
> * コンテナー レジストリから IoT Edge デバイスにモジュールを配置する。
> * フィルター処理されたデータを表示する。

<center>

![図 - チュートリアルのアーキテクチャ: 関数モジュールのステージとデプロイ](./media/tutorial-deploy-function/functions-architecture.png)
</center>

このチュートリアルでは、デバイスによって生成される温度データをフィルター処理する Azure Functions を作成します。 この関数では、指定されたしきい値を温度が上回っているときにのみ、上流の Azure IoT Hub にメッセージを送信します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、前のチュートリアル「[Linux コンテナーを使用して IoT Edge モジュールを開発する](tutorial-develop-for-linux.md)」を完了して、Linux コンテナー開発用の開発環境を設定しておく必要があります。 このチュートリアルを完了すると、次の前提条件が満たされます。

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* Azure IoT Edge を実行しているデバイス。 クイックスタートを使用して、[Linux デバイス](quickstart-linux.md)または [Windows デバイス](quickstart.md)を設定できます。
* コンテナー レジストリ ([Azure Container Registry](../container-registry/index.yml) など)。
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して構成された [Visual Studio Code](https://code.visualstudio.com/)。
* Linux コンテナーを実行するように構成された [Docker CE](https://docs.docker.com/install/)。

Azure Functions を使用して IoT Edge モジュールを開発するには、開発用マシンに次の追加の前提条件をインストールします。

* [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [The .NET Core 2.1 SDK](https://www.microsoft.com/net/download)。

## <a name="create-a-function-project"></a>関数プロジェクトを作成する

前提条件としてインストールした Visual Studio Code 用 Azure IoT Tools は、いくつかのコード テンプレートと管理機能を提供します。 このセクションでは、Visual Studio Code を使用して、Azure Functions を含む IoT Edge ソリューションを作成します。

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

独自のコードでカスタマイズできる C# 関数ソリューション テンプレートを作成します。

1. 開発用マシンで Visual Studio Code を開きます。

2. **[表示]**  >  **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。

3. コマンド パレットで、**Azure IoT Edge:New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットに表示されるメッセージに従って、ソリューションを作成します。

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | **FunctionSolution** のように、ソリューションのわかりやすい名前を入力するか、既定値をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **[Azure Functions - C#]** を選択します。 |
   | Provide a module name (モジュール名の指定) | モジュールに **CSharpFunction** という名前を付けます。 |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 コンテナー イメージは、前の手順で事前設定されます。 **localhost:5000** を、Azure コンテナー レジストリの **ログイン サーバー** の値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 文字列は最終的に、\<registry name\>.azurecr.io/CSharpFunction のようになります。 |

   ![Docker イメージ リポジトリを指定する](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー レジストリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。

IoT Edge 拡張機能は、Azure からコンテナー レジストリの資格情報をプルし、それらを環境ファイルに取り込もうとします。 資格情報が既に含まれているかどうかを確認します。 含まれていない場合は、次のようにして追加します。

1. VS Code エクスプローラーで、.env ファイルを開きます。
2. ご自身の Azure コンテナー レジストリからコピーした **ユーザー名** と **パスワード** の値を使用して、フィールドを更新します。
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

## <a name="build-and-push-your-iot-edge-solution"></a>IoT Edge ソリューションをビルドしてプッシュする

前のセクションでは、IoT Edge ソリューションを作成し、**CSharpFunction** を変更しました。これにより、レポートされたマシンの温度が許容可能なしきい値を下回るメッセージがフィルターで除外されます。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。

1. **[表示]**  >  **[ターミナル]** を選択して、VS Code 統合ターミナルを開きます。

2. ターミナルで次のコマンドを入力して、Docker にサインインします。 自分の Azure コンテナー レジストリのユーザー名、パスワード、ログイン サーバーを使用してサインインします。 これらの値は、Azure portal でご自身のレジストリの **[アクセス キー]** セクションから取得できます。

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` の使用を推奨するセキュリティ警告が表示される場合があります。 このベスト プラクティスは、運用環境のシナリオを対象に推奨されていますが、それはこのチュートリアルの範囲外になります。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) のリファレンスをご覧ください。

3. VS Code エクスプローラーで、**deployment.template.json** ファイルを右クリックし、 **[Build and Push IoT Edge Solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。

   ビルドおよびプッシュ コマンドでは、3 つの操作を開始します。 最初に、デプロイ テンプレートと他のソリューション ファイルの情報からビルドされた完全な配置マニフェストを保持する、**config** という新しいフォルダーをソリューション内に作成します。 次に、`docker build` を実行して、お使いのターゲット アーキテクチャ用の適切な Dockerfile に基づいてコンテナー イメージをビルドします。 そして、`docker push` を実行して、イメージ リポジトリをコンテナー レジストリにプッシュします。

   このプロセスは、初回は数分間かかる可能性がありますが、次回これらのコマンドを実行するときは、それより速くなります。

## <a name="view-your-container-image"></a>コンテナー イメージの表示

コンテナー イメージがコンテナー レジストリにプッシュされると、Visual Studio Code によって成功メッセージが出力されます。 操作の成功を自分で確認したい場合は、レジストリのイメージを表示できます。

1. Azure portal で、Azure Container Registry に移動します。
2. **[リポジトリ]** を選択します。
3. **csharpfunction** リポジトリが一覧に表示されます。 このリポジトリを選択すると、さらに詳しい情報が表示されます。
4. **[タグ]** セクションには、**0.0.1-amd64** タグが表示されます。 このタグには、作成したイメージのバージョンとプラットフォームが示されます。 これらの値は、CSharpFunction フォルダーの module.json ファイルで設定されます。

## <a name="deploy-and-run-the-solution"></a>ソリューションの配置と実行

クイック スタートで行ったように、Azure portal を使用して関数モジュールを IoT Edge デバイスに展開できます。 また、Visual Studio Code 内からモジュールを配置して、監視することもできます。 以降のセクションでは、前提条件で示された VS Code 用の Azure IoT Tools を使用します。 この拡張機能をまだインストールしていない場合は、ここでインストールしてください。

1. Visual Studio Code エクスプローラーの **[Azure IoT Hub]** セクションで、 **[デバイス]** を展開して IoT デバイスの一覧を表示します。

2. IoT Edge デバイスの名前を右クリックし、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

3. **CSharpFunction** が含まれているソリューション フォルダーの場所を参照します。 config フォルダーを開き、**deployment.amd64.json** ファイルを選択して、 **[Select Edge Deployment Manifest]\(Edge 展開マニフェストの選択\)** を選択します。

4. お使いのデバイスの **[モジュール]** を展開し、デプロイされて実行中のモジュールの一覧を表示します。 更新ボタンをクリックします。 新しい **CSharpFunction** が、**SimulatedTemperatureSensor** モジュール、 **$edgeAgent** および **$edgeHub** と一緒に実行されていることがわかります。

    新しいモジュールが表示されるまでに、数分かかる場合があります。 IoT Edge デバイスは、その新しいデプロイ情報を IoT Hub から取得し、新しいコンテナーを起動した後、その状態を IoT Hub にレポートする必要があります。

   ![VS Code での配置されたモジュールの表示](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>生成されたデータを表示する

コマンド パレットで **Azure IoT Hub: Start Monitoring Built-in Event Endpoint** を実行することによって、IoT ハブに届くすべてのメッセージを確認できます。

また、特定のデバイスから IoT Hub に届くすべてのメッセージが表示されるよう、ビューをフィルター処理することもできます。 **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクション内でデバイスを右クリックして、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。

メッセージの監視を停止するには、コマンド パレットで **Azure IoT Hub: Stop Monitoring Built-in Event Endpoint** コマンドを実行します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードが含まれる Azure Functions モジュールを作成しました。

引き続き次のチュートリアルを実行すると、Azure IoT Edge を利用して、エッジでデータをビジネス上の分析情報に変える他の方法について学習できます。

> [!div class="nextstepaction"]
> [Azure Stream Analytics でフローティング ウィンドウを使用して平均値を見つける](tutorial-deploy-stream-analytics.md)
