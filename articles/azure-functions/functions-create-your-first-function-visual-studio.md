---
title: "Visual Studio を使用して Azure で初めての関数を作成する | Microsoft Docs"
description: "Azure Functions Tools for Visual Studio を使用して、HTTP によってトリガーされる単純な関数を作成し、Azure に発行します。"
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/06/2017
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 3d9e7a4eb6d94067af6b5c64e71910694d5a6077
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-function-using-visual-studio"></a>Visual Studio を使用して初めての関数を作成する

Azure Functions を使用すると、最初に VM を作成したり Web アプリケーションを発行したりしなくても、サーバーレス環境でコードを実行できます。

このトピックでは、Visual Studio 2017 Tools for Azure Functions を使用して、"hello world" 関数を作成してローカルでテストする方法を学習します。 その後、関数コードを Azure に発行します。 これらのツールは、Visual Studio 2017 バージョン 15.3 以降の Azure 開発ワークロードの一部として利用できます。

![Visual Studio プロジェクトの Azure Functions コード](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下をインストールしてください。

* [Visual Studio 2017 バージョン 15.3](https://www.visualstudio.com/vs/preview/) (**Azure 開発**ワークロードを含む)。

    ![Visual Studio 2017 と Azure 開発ワークロードのインストール](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)
    
    >[!NOTE]  
    >Visual Studio 2017 バージョン 15.3 をインストールするか、このバージョンにアップグレードした後、Visual Studio 2017 Tools for Azure Functions を手動で更新する必要があります。 ツールを更新するには、**[ツール]** メニューで **[拡張機能と更新プログラム]** > **[更新プログラム]** > **[Visual Studio Marketplace]** > **[Azure Functions and Web Jobs Tools]\(Azure Functions と Web ジョブのツール\)** > **[更新]** の順に選択します。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

## <a name="create-an-azure-functions-project-in-visual-studio"></a>Visual Studio で Azure Functions プロジェクトを作成する

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

プロジェクトが作成されたので、初めての関数を作成できます。

## <a name="create-the-function"></a>関数を作成する

1. **ソリューション エクスプローラー**で、プロジェクト ノードを右クリックし、**[追加]** > **[新しい項目]** の順に選択します。 **[Azure 関数]** を選択し、**[追加]** をクリックします。

2. **[HttpTrigger]** を選択して、**関数名**を入力し、**[AccessRights]** では **[匿名]** を選択して、**[作成]** をクリックします。 作成された関数は、任意のクライアントからの HTTP 要求によってアクセスされます。 

    ![新しい Azure 関数の作成](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

    コード ファイルが、関数コードを実装するクラスを含むプロジェクトに追加されます。 このコードは、値を受け取ってエコー バックするテンプレートに基づいています。 **FunctionName** 属性は関数の名前を設定し、 **HttpTrigger** 属性は、関数をトリガーするメッセージを示します。 

    ![関数コード ファイル](./media/functions-create-your-first-function-visual-studio/functions-code-page.png)

HTTP によってトリガーされる関数を作成できたので、この関数をローカル コンピューターでテストすることができます。

## <a name="test-the-function-locally"></a>関数をローカルでテストする

Azure Functions Core Tools を使用すると、ローカルの開発用コンピューター上で Azure Functions プロジェクトを実行できます。 Visual Studio から初めて関数を開始すると、これらのツールをインストールするよう求めるメッセージが表示されます。  

1. 関数をテストするには、F5 キーを押します。 メッセージが表示されたら、Visual Studio からの要求に同意し、Azure Functions Core (CLI) ツールをダウンロードしてインストールします。  また、ツールで HTTP 要求を処理できるようにファイアウォールの例外を有効にすることが必要になる場合もあります。

2. Azure Functions のランタイムの出力から、関数の URL をコピーします。  

    ![Azure ローカル ランタイム](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

3. HTTP 要求の URL をブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `?name=<yourname>` を追加して、要求を実行します。 関数によって返されたローカルの GET 要求に対するブラウザーでの応答を次に示します。 

    ![ブラウザーでの関数 localhost の応答](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

4. デバッグを停止するには、Visual Studio ツール バーの **[停止]** ボタンをクリックします。

関数がローカル コンピューター上で正常に動作することを確認した後、プロジェクトを Azure に発行します。

## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する

プロジェクトを発行するには、Azure サブスクリプションに関数アプリがあることが必要です。 関数アプリは、Visual Studio から直接作成できます。

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Azure で関数をテストする

1. [発行プロファイル] ページから関数アプリのベース URL をコピーします。 関数をローカルでテストしたときに使用した URL の `localhost:port` 部分を新しいベース URL に置き換えます。 前と同様に、この URL にクエリ文字列 `?name=<yourname>` を追加してから、要求を実行します。

    HTTP によってトリガーされる関数を呼び出す URL は、次のようになります。

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

2. HTTP 要求のこの新しい URL をブラウザーのアドレス バーに貼り付けます。 関数によって返されたリモート GET 要求に対するブラウザーでの応答を次に示します。 

    ![ブラウザーでの関数の応答](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)
 
## <a name="next-steps"></a>次のステップ

Visual Studio を使用して、HTTP によってトリガーされる単純な関数を含む C# 関数アプリを作成しました。 

+ 他の種類のトリガーとバインディングをサポートするようにプロジェクトを構成する方法については、「[Azure Functions Tools for Visual Studio](functions-develop-vs.md)」の「[ローカル開発用のプロジェクトを構成する](functions-develop-vs.md#configure-the-project-for-local-development)」セクションを参照してください。
+ Azure Functions Core Tools を使用したローカル テストとデバッグの詳細については、「[Azure 関数をローカルでコーディングしてテストする方法](functions-run-local.md)」を参照してください。 
+ .NET クラス ライブラリとしての関数の開発の詳細については、「[Azure Functions での .NET クラス ライブラリの使用](functions-dotnet-class-library.md)」を参照してください。 

