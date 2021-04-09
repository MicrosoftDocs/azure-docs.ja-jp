---
title: Visual Studio Code を使用して Java 関数を作成する - Azure Functions
description: Visual Studio Code の Azure Functions 拡張機能を使用して Java 関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法について説明します。
ms.topic: quickstart
ms.date: 11/03/2020
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-java-uiex
ms.openlocfilehash: 10ac677fb65428e8b8855c43b319bbebe7bbbb71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701434"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>クイックスタート: Visual Studio Code を使用して Azure に Java 関数を作成する

> [!div class="op_single_selector" title1="関数の言語を選択します。 "]
> - [Java](create-first-function-vs-code-java.md)
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [その他 (Go/Rust)](create-first-function-vs-code-other.md)

この記事では、HTTP 要求に応答する Java 関数を、Visual Studio Code を使用して作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

> [!NOTE]
> Visual Studio Code が好みの開発ツールでない場合は、[Maven](create-first-function-cli-java.md)、[Gradle](./functions-create-first-java-gradle.md)、および [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) を使用する Java 開発者向けの同様のチュートリアルを確認してください。

## <a name="configure-your-environment"></a>環境を構成する

作業を開始する前に、次の要件が満たされていることを確認します。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)、バージョン 8 または 11。

+ [Apache Maven](https://maven.apache.org) バージョン 3.0 以降。

+ [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。

+ [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>ローカル プロジェクトを作成する

このセクションでは、Visual Studio Code を使用して、ローカル Azure Functions プロジェクトを Java で作成します。 後からこの記事の中で、関数コードを Azure に発行します。 

1. アクティビティ バーの Azure アイコンを選択し、 **[Azure: Functions]** 領域で **[新しいプロジェクトの作成]** アイコンを選択します。

    ![[新しいプロジェクトの作成] を選択する](./media/functions-create-first-function-vs-code/create-new-project.png)

1. プロジェクト ワークスペースのディレクトリの場所を選択し、 **[選択]** をクリックします。

    > [!NOTE]
    > これらの手順は、ワークスペースの外部で実行するように設計されています。 ここでは、ワークスペースに含まれるプロジェクト フォルダーは選択しないでください。

1. プロンプトで、次の情報を入力します。

    + **Select a language for your function project (関数プロジェクトの言語を選択してください)** : [`Java`] を選択します。

    + **Select a version of Java (Java のバージョンを選択してください)** : Azure における関数の実行環境 (Java バージョン) として `Java 8` または `Java 11` を選択します。 ローカルで確認済みの Java バージョンを選択してください。

    + **Provide a group ID (グループ ID を指定してください)** : [`com.function`] を選択します。

    + **Provide an artifact ID (成果物 ID を指定してください)** : [`myFunction`] を選択します。

    + **Provide a version (バージョンを指定してください)** : [`1.0-SNAPSHOT`] を選択します。

    + **Provide a package name (パッケージ名を指定してください)** : [`com.function`] を選択します。

    + **Provide an app name (アプリ名を指定してください)** : [`myFunction-12345`] を選択します。

    + **承認レベル**: `Anonymous` を選択します。この場合、すべてのユーザーが関数のエンドポイントを呼び出すことができます。 承認レベルについては、「[承認キー](functions-bindings-http-webhook-trigger.md#authorization-keys)」を参照してください。

    + **Select how you would like to open your project (プロジェクトを開く方法を選択してください)** : [`Add to workspace`] を選択します。

1. Visual Studio Code は、この情報を使用して、HTTP トリガーによる Azure Functions プロジェクトを生成します。 ローカル プロジェクト ファイルは、エクスプローラーで表示できます。 作成されるファイルの詳細については、「[生成されるプロジェクト ファイル](functions-develop-vs-code.md#generated-project-files)」を参照してください。 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

関数がローカル コンピューター上で正常に動作することを確認したら、Visual Studio Code を使用してプロジェクトを直接 Azure に発行します。

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>次のステップ

[Visual Studio Code](functions-develop-vs-code.md?tabs=java) を使用して、HTTP によってトリガーされる単純な関数を含む関数アプリを作成しました。 次の記事では、Azure Storage に接続することによってその関数を拡張します。 他の Azure サービスへの接続について詳しくは、「[Azure Functions の既存の関数にバインドを追加する](add-bindings-existing-function.md?tabs=java)」を参照してください。 

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
