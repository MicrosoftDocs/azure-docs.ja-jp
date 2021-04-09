---
title: Visual Studio Code を使用して C# 関数を作成する - Azure Functions
description: Visual Studio Code の Azure Functions 拡張機能を使用して C# 関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法について説明します。
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2848ce6214d59ba2732dcfc148ccaf9936497f17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121033"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>クイックスタート: Visual Studio Code を使用して Azure に C# 関数を作成する

> [!div class="op_single_selector" title1="関数の言語を選択します。 "]
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [その他 (Go/Rust)](create-first-function-vs-code-other.md)

この記事では、Visual Studio Code を使用して、HTTP 要求に応答する C# クラス ライブラリベースの関数を作成します。 コードをローカルでテストした後、デプロイします。 <abbr title="コードをデプロイして管理するプロセスは、ランタイムのコンピューティング環境によって省力化されています。アプリケーション開発者がサーバーの細部を意識する必要は一切ありません。">サーバーレス</abbr> 環境 <abbr title="アプリケーションのための低コストのサーバーレス コンピューティング環境を提供する Azure のサービス。">Azure Functions</abbr>.

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

また、この記事の [CLI ベースのバージョン](create-first-function-cli-csharp.md)もあります。
    
## <a name="1-configure-your-environment"></a>1. 環境を構成する

作業を開始する前に、次の要件が満たされていることを確認します。

+ Azure <abbr title="Azure の使用に関する課金情報を保持するプロファイル。">account</abbr> アクティブ <abbr title="Azure 内のリソース管理に使用される基本的な組織構造。通常、組織内の部門または個人に関連付けられます。">subscription</abbr>. [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) バージョン 3.x。

+ [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。

+ Visual Studio Code 用の [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。  

+ Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。

## <a name="2-create-your-local-project"></a><a name="create-an-azure-functions-project"></a>2.ローカル プロジェクトを作成する

このセクションでは、Visual Studio Code を使用して、ローカル <abbr title="個別に存在する 1 つまたは複数の関数に使用される論理上のコンテナー。これらをまとめてデプロイして管理することができます。">Azure Functions プロジェクト</abbr> を C# で作成します。 後からこの記事の中で、関数コードを Azure に発行します。

1. Azure アイコンを <abbr title="Visual Studio Code ウィンドウの左側で縦に並んだ一連のアイコン。">アクティビティ バー</abbr>で選択し、 **[Azure: Functions]** 領域の **[新しいプロジェクトの作成]** アイコンを選択します。

    ![[新しいプロジェクトの作成] を選択する](./media/functions-create-first-function-vs-code/create-new-project.png)

1. プロジェクト ワークスペースのディレクトリの場所を選択し、 **[選択]** をクリックします。

    > [!NOTE]
    > これらの手順は、ワークスペースの外部で実行するように設計されています。 ここでは、ワークスペースに含まれるプロジェクト フォルダーは選択しないでください。

1. プロンプトで、次の情報を入力します。

    + **Select a language for your function project (関数プロジェクトの言語を選択してください)** : [`C#`] を選択します。

    + **Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください)** : [`HTTP trigger`] を選択します。

    + **Provide a function name (関数名を指定してください):** 「`HttpExample`.

    + **Provide a namespace (名前空間を指定してください)** : 「`My.Functions`.

    + **承認レベル**: `Anonymous` を選択します。この場合、すべてのユーザーが関数のエンドポイントを呼び出すことができます。 承認レベルの詳細については、[承認キー](functions-bindings-http-webhook-trigger.md#authorization-keys)に関するページを参照してください。

    + **Select how you would like to open your project (プロジェクトを開く方法を選択してください)** : [`Add to workspace`] を選択します。

1. Visual Studio Code は、この情報を使用して、HTTP トリガーによる Azure Functions プロジェクトを生成します。 <abbr title="HTTP 要求、キュー メッセージ、特定の時刻など、関数のコードを呼び出すイベントの種類。">トリガー (trigger)</abbr>. ローカル プロジェクト ファイルは、エクスプローラーで表示できます。 作成されるファイルの詳細については、「[生成されるプロジェクト ファイル](functions-develop-vs-code.md#generated-project-files)」を参照してください。

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

関数がローカル コンピューター上で正常に動作することを確認したら、Visual Studio Code を使用してプロジェクトを直接 Azure に発行します。

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="5-publish-the-project-to-azure"></a>5.Azure にプロジェクトを発行する

このセクションでは、ご利用の Azure サブスクリプションに関数アプリと関連リソースを作成し、コードをデプロイします。 

> [!IMPORTANT]
> 既存の関数アプリに発行すると、Azure のそのアプリのコンテンツが上書きされます。 

1. アクティビティ バーの Azure アイコンを選択し、 **[Azure: Functions]** 領域で、 **[Deploy to function app]\(関数アプリにデプロイ\)** ボタンを選択します。

    

1. プロンプトで、次の情報を入力します。

    + **Select folder (フォルダーを選択してください)** : ワークスペースのフォルダーを選択するか、関数アプリが格納されているフォルダーを参照します。 既に有効な関数アプリが開いている場合には、このプロンプトは表示されません。

    + **Select subscription (サブスクリプションを選択してください)** : 使用するサブスクリプションを選択します。 サブスクリプションが 1 つのみの場合、このプロンプトは表示されません。

    + **Select function app in Azure (Azure で関数アプリを選択してください)** : [`+ Create new Function App`] を選択します。 (`Advanced` オプションはこの記事では取り上げません。選択しないでください。)

    + **Enter a globally unique name for the function app (関数アプリのグローバルに一意の名前を入力します)** : URL パスに有効な名前を入力します。 入力した名前は、 <abbr title="名前は、全 Azure ユーザーが使用するすべての Functions プロジェクト全体でグローバルに一意である必要があります。 通常、contoso-bizapp-func-20 のように、個人名または会社名、アプリケーション名、数値 ID の組み合わせが使用されます">Azure Functions 内での一意性を確保するために検証されます。</abbr>. 

    + **Select a location for new resources (新しいリソースの場所を選択してください)** : パフォーマンスを向上させるために、お近くの [リージョン](https://azure.microsoft.com/regions/)を選択してください。

    この拡張機能は、Azure に作成されている個々のリソースの状態を通知領域に表示します。

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure リソース作成の通知":::

1. 完了すると、関数アプリ名に基づく名前を使用して、次の Azure リソースがサブスクリプションに作成されます。

    + **リソース グループ**。関連リソースの論理コンテナーです。
    + Standard **Azure ストレージ アカウント**。プロジェクトについての状態とその他の情報を保持します。
    + **従量課金プラン**。サーバーレス関数アプリの実行環境となるホストを定義します。 
    + **関数アプリ**。関数コードを実行するための環境となります。 関数アプリを使用すると、同じホスティング プランに含まれるリソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。
    + 関数アプリに接続された **Application Insights インスタンス**。サーバーレス関数の使用を追跡します。

    関数アプリが作成され、展開パッケージが適用されると、通知が表示されます。 

    > [!TIP]
    > 既定では、関数アプリに必要な Azure リソースが、指定した関数アプリ名に基づいて作成されます。 また、既定では、関数アプリを含んだ同じ新しいリソース グループがその作成先となります。 それらのリソースの名前をカスタマイズしたり、既存のリソースを再利用したりする場合は、[高度な作成オプションを使用してプロジェクトを発行](functions-develop-vs-code.md#enable-publishing-with-advanced-create-options)する必要があります。


1. この通知の **[View Output]\(出力の表示\)** を選択すると、作成済みの Azure リソースなど、作成とデプロイの結果が表示されます。 通知を見逃した場合は、右下隅にあるベル アイコンを選択して、再度確認します。

    ![作成完了通知](./media/functions-create-first-function-vs-code/function-create-notifications.png)

## <a name="6-run-the-function-in-azure"></a>6.Azure で関数を実行する

1. **[Azure: Functions]** 領域 (サイド バー内) に戻り、サブスクリプション、新しい関数アプリ、 **[関数]** の順に展開します。 `HttpExample` 関数を右クリック (Windows) または <kbd>Ctrl</kbd> キーを押しながらクリック (macOS) して、 **[Execute Function Now]\(今すぐ関数を実行\)** を選択します。

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Visual Studio Code から今すぐ Azure で関数を実行する":::

1. **[Enter request body]\(要求本文を入力してください\)** に、要求メッセージ本文の値として `{ "name": "Azure" }` が表示されます。

    Enter キーを押して、この要求メッセージを関数に送信します。  

1. Azure で関数を実行し、応答が返されると、Visual Studio Code で通知が発生します。

## <a name="5-clean-up-resources"></a>5.リソースをクリーンアップする

この後、[次の手順](#next-steps)に進んで <abbr title="関数をストレージ キューに関連付けて、そのキューにメッセージを作成できるようにするための手段。"> Azure Storage キュー出力バインド</abbr> を関数に追加する場合、既存の作業をベースにするので、リソースはすべてそのままにしておく必要があります。

それ以外の場合は、追加コストの発生を避けるために、次の手順に従って関数アプリとその関連リソースを削除してください。

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Functions のコストについて詳しくは、「[従量課金プランのコストの見積もり](functions-consumption-costs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Visual Studio Code を使用して、HTTP によってトリガーされる単純な関数を含む関数アプリを作成しました。 次の記事では、出力バインディングを追加してその関数を拡張します。 <abbr title="関数と他のリソースとの間の宣言型の接続。 入力バインドは関数にデータを提供し、出力バインドは関数から他のリソースにデータを提供します。">binding</abbr>. このバインディングでは、HTTP 要求の文字列が Azure Queue Storage キュー内のメッセージに書き込まれます。 

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
