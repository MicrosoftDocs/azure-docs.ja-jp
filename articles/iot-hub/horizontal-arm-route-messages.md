---
title: ARM テンプレートを使用して、Azure IoT Hub とストレージ アカウントを発行し、メッセージをルーティングする
description: ARM テンプレートを使用して、Azure IoT Hub とストレージ アカウントを発行し、メッセージをルーティングする
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: fc8ddba2ec9b7bc9f1c2db8673ab805810afe17e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99981291"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Azure IoT Hub とストレージ アカウントをデプロイする

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、メッセージを Azure Storage にルーティングする IoT Hub とそのメッセージを保持するストレージ アカウントを作成します。 仮想 IoT デバイスをハブに手動で追加してメッセージを送信した後、その接続情報を *arm-read-write* と呼ばれるアプリケーションで構成して、デバイスからハブにメッセージを送信するようにします。 ハブは、そのハブに届いたメッセージを自動的にストレージ アカウントへとルーティングするように構成されます。 このクイックスタートの最後には、ストレージ アカウントを開いて、送信されたメッセージを確認することができます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートでは、[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages)から入手した `101-iothub-auto-route-messages` というテンプレートを使用します。

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

テンプレートでは、次の 2 つの Azure リソースが定義されています。

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Devices/IotHubs](/azure/templates/microsoft.devices/iothubs)

## <a name="deploy-the-template-and-run-the-sample-app"></a>テンプレートをデプロイしてサンプル アプリを実行する

このセクションでは、テンプレートをデプロイして仮想デバイスを作成し、arm-read-write アプリケーションを実行してメッセージを送信する手順について説明します。

1. ARM テンプレートをデプロイしてリソースを作成します。

    > [!TIP]
    > 以下のボタンを選択すると、テンプレートのデプロイが開始されます。 それが行われている間に、arm-read-write アプリケーションを実行するための設定を行います。

    [![Azure へのデプロイ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. [IoT C# サンプル](/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/)をダウンロードして解凍します。

1. コマンド ウィンドウを開いて、IoT C# サンプルの解凍先フォルダーに移動します。 arm-read-write.csproj ファイルがあるフォルダーを見つけます。 このコマンド ウィンドウで環境変数を作成します。 [Azure portal](https://portal.azure.com) にログインしてキーを取得します。 **[リソース グループ]** を選択し、このクイックスタートに使用するリソース グループを選択してください。

   ![リソース グループの選択](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. ARM テンプレートをデプロイするときに作成された IoT Hub とストレージ アカウントが表示されます。 テンプレートが完全にデプロイされるのを待って続行してください。 次に、ご自分のリソース グループを選択してリソースを表示します。

   ![リソース グループ内のリソースを表示する](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. **ハブの名前** が必要です。 リソースの一覧でハブを選択します。 IoT Hub セクションの上部からハブの名前をコピーして Windows クリップボードに格納します。

   ![ハブの名前をコピーする](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    このコマンドのハブ名 (強調された部分) を実際のハブ名に置き換えて、コマンド ウィンドウで実行します。

    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   結果はこの例のようになります。

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. 次の環境変数は、IoT デバイス キーです。 ハブの [IoT Hub] メニューから **[IoT デバイス]** を選択して、ハブに新しいデバイスを追加します。

   ![[IoT デバイス] を選択する](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. 画面の右側にある **[+ 新規]** を選択して新しいデバイスを追加します。

   新しいデバイス名を入力します。 このクイックスタートでは、**Contoso-Test-Device** で始まる名前を使用します。 デバイスを保存し、その画面を再度開いてデバイス キーを取得します (このキーは、ペインを閉じたときに生成されます)。プライマリまたはセカンダリのキーを選択して Windows クリップボードにコピーします。 コマンド ウィンドウで、実行するコマンドを設定し、**Enter** キーを押します。 コマンドはこのようになっている必要があります。ただし、デバイス キーを貼り付けてください。

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. 最後の環境変数は **デバイス ID** です。 コマンド ウィンドウで、コマンドを設定して実行します。

   ```cmd
   SET IOT_DEVICE_ID=<device-id-goes-here>
   ```

   結果はこの例のようになります。

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. 定義した環境変数を確認するために、コマンド ラインで「SET」と入力し、**Enter** キーを押します。**IoT** で始まる行を探してください。

   ![環境変数を確認する](./media/horizontal-arm-route-messages/06-environment-variables.png)

    環境変数を設定できたところで、同じコマンド ウィンドウからアプリケーションを実行します。 同じウィンドウを使用しているため、アプリケーションを実行する際、変数にはメモリ内でアクセスできます。

1. アプリケーションを実行するには、コマンド ウィンドウで次のコマンドを入力し、**Enter** キーを押します。

    `dotnet run arm-read-write`

   アプリケーションがメッセージを生成し、それぞれのメッセージを IoT Hub に送信すると、メッセージはコンソールに表示されます。 ハブは、自動ルーティングを行うように ARM テンプレートで構成されています。 `level = storage` というテキストを含んだメッセージは、ストレージ アカウントに自動的にルーティングされます。 アプリを 10 分から 15 分実行したら、**Enter** キーを 1 回または 2 回押して実行を停止してください。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

1. [Azure portal](https://portal.azure.com) にログインし、[リソース グループ] を選択してストレージ アカウントを選択します。

1. ストレージ アカウントをドリルダウンしてファイルを見つけます。

   ![ストレージ アカウントのファイルを確認する](./media/horizontal-arm-route-messages/07-see-storage.png)

1. いずれかのファイルを選択して **[ダウンロード]** を選択し、後で見つけやすい場所にそのファイルをダウンロードします。 これには、数値の名前が付けられます (例: 47)。 末尾に " _.txt_" を追加し、ファイルをダブルクリックして開きます。

1. 開いたファイルの各行はそれぞれ異なるメッセージを表しており、また、各メッセージの本文は暗号化されています。 メッセージの本文に対してクエリを実行するためには、そのようになっている必要があります。

   ![送信されたメッセージを確認する](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > これらのメッセージは UTF-32 および Base64 でエンコードされています。 メッセージを復元して ASCII として読み取るためには、Base64 と UTF-32 からデコードする必要があります。 興味がある方は、ルーティングのチュートリアルで説明されている ReadOneRowFromFile メソッドを使用して、これらのメッセージ ファイルのうちから 1 つを読み取り、ASCII にデコードしてみてください。 ReadOneRowFromFile は、このクイックスタートで解凍した IoT C# サンプル リポジトリにあります。 このフォルダーの最上位を起点とするパスは、 *./iot-hub/Tutorials/Routing/SimulatedDevice/Program.cs* です。 ブール値の `readTheFile` は true に設定し、ディスク上のファイルのパスはハードコーディングしてください。これにより、ファイルの先頭行が開かれて変換されます。

IoT Hub とストレージ アカウントを作成する ARM テンプレートをデプロイし、そのハブにメッセージを送信するプログラムを実行しました。 そのメッセージは、確認を行える場所であるストレージ アカウントに自動的に格納されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで追加したリソースを削除するために、[Azure portal](https://portal.azure.com) にログインします。 **[リソース グループ]** を選択し、このクイックスタートに使用したリソース グループを見つけてください。 リソース グループを選択し、 *[削除]* を選択します。 これで、グループ内のすべてのリソースが削除されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:初めての ARM テンプレートを作成してデプロイする](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
