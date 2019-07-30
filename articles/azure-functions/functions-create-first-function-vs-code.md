---
title: Visual Studio Code を使用して Azure で初めての関数を作成する
description: Visual Studio Code で Azure Functions 拡張機能を使用して、HTTP によってトリガーされる単純な関数を作成し、Azure に発行します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, 関数, イベント処理, コンピューティング, サーバーなしのアーキテクチャ
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 06/25/2019
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: fcf9f1d6420dbbde359d386bc3b67a0866aca30d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444613"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Visual Studio Code を使用した初めての関数の作成

Azure Functions を使用すると、最初に VM を作成したり Web アプリケーションを発行したりしなくても、[サーバーレス](https://azure.microsoft.com/solutions/serverless/)環境でコードを実行できます。

この記事では、Microsoft Visual Studio Code で [Visual Studio Code 用 Azure Functions 拡張機能]を使用して、ローカル コンピューター上で "hello world" 関数を作成し、テストする方法について説明します。 その後、Visual Studio Code から関数コードを Azure に発行します。

![Visual Studio プロジェクトの Azure Functions コード](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

現在、この拡張機能では、C#、JavaScript、Java 関数がサポートされており、Python のサポートはプレビュー段階です。 この記事と次の記事の手順では、JavaScript と C# の関数のみをサポートしています。 Visual Studio Code を使用して Python 関数を作成および発行する方法については、「[Deploy Python to Azure Functions (Azure Functions に Python をデプロイする)](https://code.visualstudio.com/docs/python/tutorial-azure-functions)」を参照してください。 Visual Studio Code を使用して PowerShell 関数を作成および発行する方法については、「[Azure で初めての PowerShell 関数を作成する](functions-create-first-function-powershell.md)」を参照してください。 

この拡張機能は現在プレビュー段階にあります。 詳細については、[Visual Studio Code 用 Azure Functions 拡張機能]のページをご覧ください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかに [Visual Studio Code](https://code.visualstudio.com/) をインストールします。

* [Azure Functions Core Tools](functions-run-local.md#v2) のバージョン 2.x をインストールします。

* 選択した言語に固有の要件をインストールします。

    | 言語 | 要件 |
    | -------- | --------- |
    | **C#** | [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

関数がローカル コンピューター上で正常に動作することを確認したら、プロジェクトを Azure に発行します。

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Azure で関数を実行する

1. **出力**パネルから HTTP トリガーの URL をコピーします。 前と同様に、この URL の末尾にクエリ文字列 `?name=<yourname>` を追加してから、要求を実行します。

    HTTP によってトリガーされる関数を呼び出す URL は、次の形式である必要があります。

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

1. HTTP 要求のこの新しい URL をブラウザーのアドレス バーに貼り付けます。 関数によって返されたリモート GET 要求に対するブラウザーでの応答を次に示します。 

    ![ブラウザーでの関数の応答](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>次の手順

Visual Studio Code を使用して、HTTP によってトリガーされる単純な関数を含む関数アプリを作成しました。 次の記事では、出力バインディングを追加してその関数を拡張します。 このバインディングでは、HTTP 要求の文字列が Azure Queue Storage キュー内のメッセージに書き込まれます。 次の記事では、作成したリソース グループを削除してこれらの新しい Azure リソースをクリーンアップする方法も説明します。

> [!div class="nextstepaction"]
> [関数に Azure Storage キュー バインドを追加する](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Visual Studio Code 用 Azure Functions 拡張機能]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
