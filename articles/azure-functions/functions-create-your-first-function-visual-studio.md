---
title: Visual Studio を使用して Azure で初めての関数を作成する
description: Visual Studio を使用し、HTTP によってトリガーされる Azure 関数を作成して発行します。
services: functions
documentationcenter: na
author: ggailey777
manager: gwallace
keywords: Azure Functions, 関数, イベント処理, コンピューティング, サーバーなしのアーキテクチャ
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: azure-functions
ms.topic: quickstart
ms.date: 07/19/2019
ms.author: glenga
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 9a2978bc181fd4c50665b6178d22d19eeb9e2568
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096567"
---
# <a name="create-your-first-function-using-visual-studio"></a>Visual Studio を使用して初めての関数を作成する

Azure Functions を使用すると、最初に VM を作成したり Web アプリケーションを発行したりしなくても、[サーバーレス](https://azure.microsoft.com/solutions/serverless/)環境でコードを実行できます。

この記事では、Visual Studio 2019 を使用して、"hello world" 関数をローカルで作成およびテストしてから、それを Azure に発行する方法を学習します。 このクイックスタートは、Visual Studio 2019 用に設計されています。 Visual Studio 2017 を使用して Functions プロジェクトを作成している場合は、まず、[最新の Azure Functions ツール](functions-develop-vs.md#check-your-tools-version)をインストールする必要があります。

![ブラウザーでの関数 localhost の応答](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、まず、[Visual Studio 2019](https://azure.microsoft.com/downloads/) をインストールする必要があります。 **Azure 開発**ワークロードもインストールされていることを確認します。

![Azure 開発ワークロードと共に Visual Studio をインストールする](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>関数アプリ プロジェクトを作成する

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio によってプロジェクトとクラスが作成されます。クラスの中には、HTTP トリガー関数型のスケルトン コードが含まれています。 メソッドの `FunctionName` 属性は、関数の名前を設定します。その名前は、既定では `HttpTrigger` です。 `HttpTrigger`属性は、関数が HTTP 要求によってトリガーされることを指定します。 スケルトン コードは、要求本文またはクエリ文字列の値を含む HTTP 応答を送信します。

関数の機能は、メソッドに適切な属性を適用することによって、入力および出力バインディングを使用して拡張することができます。 詳細については、[Azure Functions C# 開発者向けリファレンス](functions-dotnet-class-library.md)の「[トリガーとバインド](functions-dotnet-class-library.md#triggers-and-bindings)」のセクションを参照してください。

関数プロジェクトと、HTTP によってトリガーされる関数を作成できたので、この関数をローカル コンピューターでテストすることができます。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

Visual Studio は Azure Functions Core Tools と統合されるため、完全な Functions ランタイムを使用してローカルに関数をテストできます。  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

関数がローカル コンピューター上で正常に動作することを確認した後、プロジェクトを Azure に発行します。

## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する

プロジェクトを発行するには、Azure サブスクリプションに関数アプリがあることが必要です。 初めてプロジェクトを発行するときに、Visual Studio の発行機能によって、関数アプリが自動的に作成されます。

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Azure で関数をテストする

1. [発行プロファイル] ページから関数アプリのベース URL をコピーします。 関数をローカルでテストしたときに使用した URL の `localhost:port` 部分を新しいベース URL に置き換えます。 前と同様に、この URL にクエリ文字列 `?name=<YOUR_NAME>` を追加してから、要求を実行します。

    HTTP によってトリガーされる関数を呼び出す URL は、次の形式である必要があります。

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. HTTP 要求のこの新しい URL をブラウザーのアドレス バーに貼り付けます。 関数によって返されたリモート GET 要求に対するブラウザーでの応答を次に示します。

    ![ブラウザーでの関数の応答](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="next-steps"></a>次の手順

Visual Studio を使用して、HTTP によってトリガーされる単純な関数を含む C# 関数アプリを作成して Azure に発行しました。 .NET クラス ライブラリとしての関数の開発の詳細については、「[Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-dotnet-class-library.md)」を参照してください。

> [!div class="nextstepaction"]
> [関数に Azure Storage キュー バインドを追加する](functions-add-output-binding-storage-queue-vs.md)
