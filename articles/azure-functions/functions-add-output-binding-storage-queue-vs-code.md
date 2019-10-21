---
title: Visual Studio Code を使用して関数を Azure Storage に接続する
description: Visual Studio Code を使用して、出力バインドを追加して関数を Azure Storage に接続する方法を説明します。
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: quickstart
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: 951e48e591f490ea6321329352fd798fea58855d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329697"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Visual Studio Code を使用して関数を Azure Storage に接続する

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

この記事では、Visual Studio Code を使用して、[前のクイックスタートの記事](functions-create-first-function-vs-code.md)で作成した関数を Azure Storage に接続する方法を説明します。 この関数に追加する出力バインドは、HTTP 要求のデータを Azure Queue storage キュー内のメッセージに書き込みます。 

ほとんどのバインドでは、バインドされているサービスにアクセスするために関数が使用する、保存されている接続文字列が必要です。 作業を簡単にするために、関数アプリで作成したストレージ アカウントを使用します。 このアカウントへの接続は、既に `AzureWebJobsStorage` という名前のアプリ設定に保存されています。  

## <a name="prerequisites"></a>前提条件

この記事を始める前に、以下の要件を満たす必要があります。

* [Visual Studio Code 用の Azure Storage 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)をインストールする。
* [Azure Storage Explorer](https://storageexplorer.com/) をインストールする。 Storage Explorer は、出力バインドによって生成されるキュー メッセージの調査に使用するツールです。 Storage Explorer は、macOS、Windows、Linux ベースのオペレーティング システムでサポートされます。
* [.NET Core CLI ツール](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)をインストールする (C# プロジェクトのみ)。
* [Visual Studio Code のクイックスタートのパート 1](functions-create-first-function-vs-code.md) の手順を完了する。 

この記事では、Visual Studio Code から Azure サブスクリプションに既にサインインしていることを前提としています。 コマンド パレットから `Azure: Sign In` を実行するとサインインできます。 

## <a name="download-the-function-app-settings"></a>関数アプリの設定をダウンロードする

[前のクイックスタートの記事](functions-create-first-function-vs-code.md)では、必要なストレージ アカウントと共に Azure で関数アプリを作成しました。 このアカウントの接続文字列は、Azure のアプリ設定に安全に格納されています。 この記事では、同じアカウントのストレージ キューにメッセージを書き込みます。 関数をローカルで実行しているときにストレージ アカウントに接続するには、アプリ設定を local.settings.json ファイルにダウンロードする必要があります。 

1. F1 キーを押してコマンド パレットを開き、コマンド `Azure Functions: Download Remote Settings....` を検索して実行します。 

1. 前の記事で作成した関数アプリを選択します。 **[すべてはい]** を選択して既存のローカル設定を上書きします。 

    > [!IMPORTANT]  
    > local.settings.json ファイルは、機密情報が含まれているため、公開されることはなく、ソース管理から除外されます。

1. ストレージ アカウントの接続文字列値のキーである `AzureWebJobsStorage` をコピーします。 この接続を使用して、出力バインドが期待どおりに動作することを確認します。

## <a name="register-binding-extensions"></a>バインディング拡張機能を登録する

Queue storage の出力バインドを使用しているため、このプロジェクトを実行する前に Storage のバインド拡張機能をインストールしておく必要があります。 

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

HTTP トリガーとタイマー トリガーを除き、バインドは拡張機能パッケージとして実装されます。 ターミナル ウィンドウで次の [dotnet add package](/dotnet/core/tools/dotnet-add-package) コマンドを実行して、Storage 拡張機能パッケージをプロジェクトに追加します。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```
---
これで、Storage の出力バインドをプロジェクトに追加できるようになります。

## <a name="add-an-output-binding"></a>出力バインディングを追加する

Functions では、各種のバインドで、`direction`、`type`、および固有の `name` が function.json ファイル内で定義される必要があります。 これらの属性を定義する方法は、関数アプリの言語によって異なります。

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

## <a name="add-code-that-uses-the-output-binding"></a>出力バインディングを使用するコードを追加する

バインドが定義されたら、そのバインドの `name` を使用して、関数シグネチャの属性としてアクセスできます。 出力バインドを使用すると、認証、キュー参照の取得、またはデータの書き込みに、Azure Storage SDK のコードを使用する必要がなくなります。 Functions ランタイムおよびキューの出力バインドが、ユーザーに代わってこれらのタスクを処理します。

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

出力バインディングを最初に使用するときに、**outqueue** という名前の新しいキューが、Functions ランタイムによってストレージ アカウントに作成されます。 このキューが新しいメッセージと共に作成されたことを確認するために、Storage Explorer を使用します。

### <a name="connect-storage-explorer-to-your-account"></a>ストレージ エクスプローラーをアカウントに接続する

既に Azure Storage Explorer をインストールして Azure アカウントに接続している場合は、このセクションをスキップしてください。

1. [Azure Storage Explorer] ツールを実行し、左側の接続アイコンを選択して、 **[アカウントの追加]** を選択します。

    ![Microsoft Azure Storage Explorer に Azure アカウントを追加する](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. **[接続]** ダイアログで、 **[Add an Azure account]\(Azure アカウントを追加する\)** を選択し、お使いの **Azure 環境**を選択して、 **[サインイン]** を選択します。 

    ![Azure アカウントへのサインイン](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

自分のアカウントへのサインインが成功すると、そのアカウントに関連付けられている Azure サブスクリプションがすべて表示されます。

### <a name="examine-the-output-queue"></a>出力キューを確認する

1. Visual Studio Code で、F1 キーを押してコマンド パレットを開き、コマンド `Azure Storage: Open in Storage Explorer` を検索して実行し、自分のストレージ アカウント名を選択します。 Azure Storage Explorer で自分のストレージ アカウントが開きます。  

1. **[キュー]** ノードを展開して、**outqueue** という名前のキューを選択します。 

   このキューには、HTTP によってトリガーされる関数を実行したときにキューの出力バインディングが作成されたというメッセージが含まれます。 *Azure* の既定の `name` 値で関数を呼び出した場合、キュー メッセージは「*Name passed to the function: Azure*」(関数に渡された名前: Azure) になります。

    ![Azure Storage Explorer に表示されたキュー メッセージ](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. 関数を再度実行し、別の要求を送信すると、キューに新しいメッセージが表示されます。  

ここで、更新された関数アプリを Azure に再発行します。

## <a name="redeploy-and-verify-the-updated-app"></a>更新したアプリを再デプロイして検証する

1. Visual Studio Code で、F1 キーを押してコマンド パレットを開きます。 コマンド パレットで、`Azure Functions: Deploy to function app...` を検索して選択します。

1. 最初の記事で作成した関数アプリを選択します。 同じアプリにプロジェクトを再デプロイしているため、 **[デプロイ]** を選択して、ファイルの上書きに関する警告を無視します。

1. デプロイが完了したら、もう一度 curl またはブラウザーを使用して、再デプロイされた関数をテストすることができます。 次の例のように、前の手順と同様にクエリ文字列 `&name=<yourname>` を URL に追加します。

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. もう一度[ストレージ キューのメッセージを表示](#examine-the-output-queue)して、出力バインドによってキューに新しいメッセージが再生成されていることを確認します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure の*リソース*とは、Function App、関数、ストレージ アカウントなどのことを指します。 これらは*リソース グループ*に分類されており、グループを削除することでグループ内のすべてのものを削除できます。

これらのクイックスタートを完了するためにリソースを作成しました。 これらのリソースには、[アカウントの状態](https://azure.microsoft.com/account/)と[サービスの価格](https://azure.microsoft.com/pricing/)に応じて課金される場合があります。 リソースの必要がなくなった場合にそれらを削除する方法を、次に示します。

1. Visual Studio Code で、F1 キーを押してコマンド パレットを開きます。 コマンド パレットで、`Azure Functions: Open in portal` を検索して選択します。

1. 関数アプリを選択し、Enter キーを押します。 その関数アプリのページが [Azure portal](https://portal.azure.com) で開きます。

1. **[概要]** タブの **[リソース グループ]** で名前付きリンクを選択します。

    ![Function App ページから削除するリソース グループを選択します。](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. **[リソース グループ]** ページで、含まれているリソースの一覧を確認し、削除するものであることを確認します。
 
1. **[リソース グループの削除]** を選択し、指示に従います。

   削除には数分かかることがあります。 実行されると、通知が数秒間表示されます。 ページの上部にあるベルのアイコンを選択して、通知を表示することもできます。

## <a name="next-steps"></a>次の手順

HTTP によってトリガーされる関数を、ストレージ キューにデータを書き込むように更新しました。 Functions の開発の詳細については、「[Visual Studio Code を使用して Azure Functions を開発する](functions-develop-vs-code.md)」を参照してください。

次に、関数アプリに対して Application Insights の監視を有効にする必要があります。

> [!div class="nextstepaction"]
> [Application Insights との統合を有効にする](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
