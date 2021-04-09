---
title: Visual Studio Code を使用して Azure Functions を Azure Storage に接続する
description: Visual Studio Code プロジェクトに出力バインディングを追加して Azure Functions を Azure Storage キューに接続する方法を説明します。
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-js
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 96384d2c50e7d5b4b5b6e652d01c4a89cd519573
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493392"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Visual Studio Code を使用して Azure Functions を Azure Storage に接続する

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

この記事では、Visual Studio Code を使用して、前のクイックスタート記事で作成した関数に Azure Storage を接続する方法について説明します。 この関数に追加する出力バインドは、HTTP 要求のデータを Azure Queue storage キュー内のメッセージに書き込みます。 

ほとんどのバインドでは、バインドされているサービスにアクセスするために関数が使用する、保存されている接続文字列が必要です。 作業を簡単にするために、関数アプリで作成したストレージ アカウントを使用します。 このアカウントへの接続は、既に `AzureWebJobsStorage` という名前のアプリ設定に保存されています。  

## <a name="configure-your-local-environment"></a>ローカル環境を構成する

この記事を始める前に、以下の要件を満たす必要があります。

* [Visual Studio Code 用の Azure Storage 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)をインストールする。

* [Azure Storage Explorer](https://storageexplorer.com/) をインストールする。 Storage Explorer は、出力バインドによって生成されるキュー メッセージの調査に使用するツールです。 Storage Explorer は、macOS、Windows、Linux ベースのオペレーティング システムでサポートされます。

::: zone pivot="programming-language-csharp"
* [.NET Core CLI ツール](/dotnet/core/tools/?tabs=netcore2x)をインストールします。
::: zone-end

::: zone pivot="programming-language-csharp"  
* [Visual Studio Code のクイックスタートのパート 1](create-first-function-vs-code-csharp.md) の手順を完了する。 
::: zone-end  
::: zone pivot="programming-language-javascript"  
* [Visual Studio Code のクイックスタートのパート 1](create-first-function-vs-code-node.md) の手順を完了する。 
::: zone-end   
::: zone pivot="programming-language-java"  
* [Visual Studio Code のクイックスタートのパート 1](create-first-function-vs-code-java.md) の手順を完了する。 
::: zone-end   
::: zone pivot="programming-language-typescript"  
* [Visual Studio Code のクイックスタートのパート 1](create-first-function-vs-code-typescript.md) の手順を完了する。 
::: zone-end   
::: zone pivot="programming-language-python"  
* [Visual Studio Code のクイックスタートのパート 1](create-first-function-vs-code-python.md) の手順を完了する。 
::: zone-end   
::: zone pivot="programming-language-powershell"  
* [Visual Studio Code のクイックスタートのパート 1](create-first-function-vs-code-powershell.md) の手順を完了する。 
::: zone-end   

この記事では、Visual Studio Code から Azure サブスクリプションに既にサインインしていることを前提としています。 コマンド パレットから `Azure: Sign In` を実行するとサインインできます。 

## <a name="download-the-function-app-settings"></a>関数アプリの設定をダウンロードする

[前のクイックスタートの記事](./create-first-function-vs-code-csharp.md)では、必要なストレージ アカウントと共に Azure で関数アプリを作成しました。 このアカウントの接続文字列は、Azure のアプリ設定に安全に格納されています。 この記事では、同じアカウントのストレージ キューにメッセージを書き込みます。 関数をローカルで実行しているときにストレージ アカウントに接続するには、アプリ設定を local.settings.json ファイルにダウンロードする必要があります。 

1. F1 キーを押してコマンド パレットを開き、コマンド `Azure Functions: Download Remote Settings....` を検索して実行します。 

1. 前の記事で作成した関数アプリを選択します。 **[すべてはい]** を選択して既存のローカル設定を上書きします。 

    > [!IMPORTANT]  
    > local.settings.json ファイルは、機密情報が含まれているため、公開されることはなく、ソース管理から除外されます。

1. ストレージ アカウントの接続文字列値のキーである `AzureWebJobsStorage` をコピーします。 この接続を使用して、出力バインドが期待どおりに動作することを確認します。

## <a name="register-binding-extensions"></a>バインディング拡張機能を登録する

Queue storage の出力バインドを使用しているため、このプロジェクトを実行する前に Storage のバインド拡張機能をインストールしておく必要があります。 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

プロジェクトは、[拡張機能バンドル](functions-bindings-register.md#extension-bundles)を使用するように構成されています。これにより、事前定義された一連の拡張機能パッケージが自動的にインストールされます。 

拡張機能バンドルの使用は、プロジェクトのルートにある host.json ファイルで次のように有効になっています。

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

HTTP トリガーとタイマー トリガーを除き、バインドは拡張機能パッケージとして実装されます。 ターミナル ウィンドウで次の [dotnet add package](/dotnet/core/tools/dotnet-add-package) コマンドを実行して、Storage 拡張機能パッケージをプロジェクトに追加します。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage
```

::: zone-end

これで、Storage の出力バインドをプロジェクトに追加できるようになります。

## <a name="add-an-output-binding"></a>出力バインディングを追加する

Functions では、各種のバインドで、`direction`、`type`、および固有の `name` が function.json ファイル内で定義される必要があります。 これらの属性を定義する方法は、関数アプリの言語によって異なります。

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [functions-add-output-binding-java](../../includes/functions-add-output-binding-java.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>出力バインディングを使用するコードを追加する

バインドが定義されたら、そのバインドの `name` を使用して、関数シグネチャの属性としてアクセスできます。 出力バインドを使用すると、認証、キュー参照の取得、またはデータの書き込みに、Azure Storage SDK のコードを使用する必要がなくなります。 Functions ランタイムおよびキューの出力バインドが、ユーザーに代わってこれらのタスクを処理します。

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end  

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-storage-binding-java-code](../../includes/functions-add-storage-binding-java-code.md)]

## <a name="update-the-tests"></a>テストを更新する

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

## <a name="run-the-function-locally"></a>関数をローカルで実行する

1. 前の記事と同様、<kbd>F5</kbd> キーを押して関数アプリ プロジェクトと Core Tools を起動します。 

1. Core Tools を実行したまま、**Azure: Functions** 領域に移動します。 **[Functions]** の **[ローカル プロジェクト]**  >  **[Functions]** を展開します。 `HttpExample` 関数を右クリック (Mac では Ctrl キーを押しながらクリック) し、 **[Execute Function Now]\(今すぐ関数を実行\)** を選択します。

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Visual Studio Code から今すぐ関数を実行する":::

1. **[Enter request body]\(要求本文を入力してください\)** に、要求メッセージ本文の値として `{ "name": "Azure" }` が表示されます。 Enter キーを押して、この要求メッセージを関数に送信します。  
 
1. 応答が返されたら、<kbd>Ctrl + C</kbd> キーを押して Core Tools を停止します。

ストレージの接続文字列を使用しているため、ローカルで実行されている関数は Azure ストレージ アカウントに接続します。 出力バインディングを最初に使用するときに、**outqueue** という名前の新しいキューが、Functions ランタイムによってストレージ アカウントに作成されます。 このキューが新しいメッセージと共に作成されたことを確認するために、Storage Explorer を使用します。

### <a name="connect-storage-explorer-to-your-account"></a>ストレージ エクスプローラーをアカウントに接続する

既に Azure Storage Explorer をインストールして Azure アカウントに接続している場合は、このセクションをスキップしてください。

1. [Azure Storage Explorer] ツールを実行し、左側の接続アイコンを選択して、 **[アカウントの追加]** を選択します。

    ![Microsoft Azure Storage Explorer に Azure アカウントを追加する](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. **[接続]** ダイアログで、 **[Add an Azure account]\(Azure アカウントを追加する\)** を選択し、お使いの **Azure 環境** を選択して、 **[サインイン]** を選択します。 

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

1. デプロイの完了後、もう一度 **[Execute Function Now]\(今すぐ関数を実行\)** 機能を使用して Azure で関数をトリガーできます。

1. もう一度[ストレージ キューのメッセージを表示](#examine-the-output-queue)して、出力バインドによってキューに新しいメッセージが再生成されていることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure では、"*リソース*" とは、関数アプリ、関数、ストレージ アカウントなどのことを指します。 これらは "*リソース グループ*" に分類されており、グループを削除することでグループ内のすべてのものを削除できます。

これらのクイックスタートを完了するためにリソースを作成しました。 これらのリソースには、[アカウントの状態](https://azure.microsoft.com/account/)と[サービスの価格](https://azure.microsoft.com/pricing/)に応じて課金される場合があります。 リソースの必要がなくなった場合にそれらを削除する方法を、次に示します。

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>次のステップ

HTTP によってトリガーされる関数を、ストレージ キューにデータを書き込むように更新しました。 この後は、Visual Studio Code を使用した Functions の開発について理解を深めましょう。

+ [Visual Studio Code を使用する Azure Functions の開発](functions-develop-vs-code.md)

+ [Azure Functions のトリガーとバインディング](functions-triggers-bindings.md)。
::: zone pivot="programming-language-csharp"  
+ [C# での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=csharp)。

+ [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [JavaScript での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=javascript)。

+ [Azure Functions の JavaScript 開発者向けガイド](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Java での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=java)。

+ [Azure Functions の Java 開発者向けガイド](functions-reference-java.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [TypeScript での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=typescript)。

+ [Azure Functions の TypeScript 開発者向けガイド](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Python での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=python)。

+ [Azure Functions の Python 開発者向けガイド](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [PowerShell での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=azurepowershell)。

+ [Azure Functions の PowerShell 開発者向けガイド](functions-reference-powershell.md) 
::: zone-end