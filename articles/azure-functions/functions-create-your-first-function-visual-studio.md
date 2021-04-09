---
title: クイック スタート:Visual Studio を使用して Azure で初めての関数を作成する
description: このクイックスタートでは、Visual Studio を使用して、HTTP によってトリガーされる Azure Function を作成して発行する方法について学習します。
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
ms.openlocfilehash: d691ee60f624f75c89e44e905e5343bf9c71fd4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701417"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>クイック スタート:Visual Studio を使用して Azure で初めての関数を作成する

この記事では、Visual Studio を使用して、HTTP 要求に応答する C# クラス ライブラリベースの関数を作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。  

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、まず、[Visual Studio 2019](https://azure.microsoft.com/downloads/) をインストールします。 インストール時に **[Azure の開発]** ワークロードを確実に選択してください。 代わりに Visual Studio 2017 を使用して Azure Functions プロジェクトを作成する場合は、まず、[最新の Azure Functions ツール](functions-develop-vs.md#check-your-tools-version)をインストールする必要があります。

![Azure 開発ワークロードと共に Visual Studio をインストールする](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[Azure サブスクリプション](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/dotnet/)を作成してください。

## <a name="create-a-function-app-project"></a>関数アプリ プロジェクトを作成する

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio によってプロジェクトとクラスが作成されます。クラスの中には、HTTP トリガー関数型のスケルトン コードが含まれています。 スケルトン コードは、要求本文またはクエリ文字列の値を含む HTTP 応答を送信します。 `HttpTrigger`属性は、関数が HTTP 要求によってトリガーされることを指定します。 

## <a name="rename-the-function"></a>フォルダーの名前を変更する

`FunctionName` メソッド属性は、関数の名前を設定します。これは、既定では `Function1` として生成されます。 このツールでは、プロジェクトを作成するときに既定の関数名をオーバーライドすることはできないため、ここで関数クラス、ファイル、およびメタデータに対してより適切な名前を指定します。

1. **エクスプローラー** で Function1.cs ファイルを右クリックし、`HttpExample.cs` という名前に変更します。

1. コードで Function1 クラスの名前を "HttpExample" に変更します。

1. `Run` という名前の `HttpTrigger` メソッドで、`FunctionName` メソッド属性の名前を `HttpExample` に変更します。

関数の名前の変更が済んだので、この関数をローカル コンピューターでテストできるようになりました。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

完全な Azure Functions ランタイムを使用してローカルで関数をテストできるように、Visual Studio は Azure Functions Core Tools と統合されます。  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

関数がローカル コンピューター上で正常に動作することを確認したら、プロジェクトを Azure に発行します。

## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する

プロジェクトを発行するには、Azure サブスクリプションに関数アプリが存在する必要があります。 初めてプロジェクトを発行するときに、Visual Studio の発行機能によって、関数アプリが自動的に作成されます。

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Azure で関数をテストする

1. Cloud Explorer では、新しい関数アプリが選択されているはずです。 そうでない場合は、自分のサブスクリプション > **[App Services]** の順に展開して、新しい関数アプリを選択します。

1. 関数アプリを右クリックし、 **[ブラウザーで開く]** を選択します。 これにより、関数アプリのルートが既定の Web ブラウザーで開かれ、関数アプリが実行されていることを示すページが表示されます。 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="実行されている関数アプリ":::

1. ブラウザーのアドレス バーで、ベース URL に文字列 `/api/HttpExample?name=Functions` を追加し、要求を実行します。

    HTTP トリガー関数を呼び出す URL は、次の形式になります。

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. この URL に移動し、関数によって返されたリモート GET 要求に対する応答がブラウザーに表示されます。次の例のような内容です。

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="ブラウザーでの関数の応答":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このコレクションの他のクイックスタートは、このクイックスタートに基づいています。 クイック スタート、チュートリアル、またはこのクイック スタートで作成したサービスのいずれかでの作業を引き続き行う場合は、リソースをクリーンアップしないでください。

Azure の "*リソース*" とは、関数アプリ、関数、ストレージ アカウントなどのことを指します。 これらは "*リソース グループ*" に分類されており、グループを削除することでグループ内のすべてのものを削除できます。 

これらのクイックスタートを完了するためにリソースを作成しました。 これらのリソースには、[アカウントの状態](https://azure.microsoft.com/account/)と[サービスの価格](https://azure.microsoft.com/pricing/)に応じて課金される場合があります。 リソースの必要がなくなった場合にそれらを削除する方法を、次に示します。

1. Cloud Explorer で、自分のサブスクリプション > **[App Services]** の順に展開し、関数アプリを右クリックして、 **[ポータルで開く]** を選択します。 

1. 関数アプリのページで、 **[概要]** タブを選択してから、 **[リソース グループ]** の下にあるリンクを選択します。

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="関数アプリのページで削除するリソース グループを選択する":::

2. **[リソース グループ]** ページで、含まれているリソースの一覧を確認し、削除するものであることを確認します。
 
3. **[リソース グループの削除]** を選択し、指示に従います。

   削除には数分かかることがあります。 実行されると、通知が数秒間表示されます。 ページの上部にあるベルのアイコンを選択して、通知を表示することもできます。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Visual Studio を使用して、シンプルな HTTP トリガー関数を含む C# 関数アプリを作成し、Azure に発行しました。 

次の記事に進み、Azure Storage キュー バインドを関数に追加する方法を学習してください。
> [!div class="nextstepaction"]
> [関数に Azure Storage キュー バインドを追加する](functions-add-output-binding-storage-queue-vs.md)

