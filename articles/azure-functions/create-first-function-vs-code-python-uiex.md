---
title: Visual Studio Code を使用して Python 関数を作成する - Azure Functions
description: Visual Studio Code の Azure Functions 拡張機能を使用して Python 関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法について説明します。
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 42f07b76cefed38aad53caba9ba35c74238540fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031737"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>クイックスタート: Visual Studio Code と Python を使用して Azure に関数を作成する

> [!div class="op_single_selector" title1="関数の言語を選択します。 "]
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [その他 (Go/Rust)](create-first-function-vs-code-other.md)

この記事では、HTTP 要求に応答する Python 関数を、Visual Studio Code を使用して作成します。 コードをローカルでテストした後、デプロイします。 <abbr title="コードをデプロイして管理するプロセスは、ランタイムのコンピューティング環境によって省力化されています。アプリケーション開発者がサーバーの細部を意識する必要は一切ありません。">サーバーレス</abbr> 環境 <abbr title="アプリケーションのための低コストのサーバーレス コンピューティング環境を提供する Azure のサービス。">Azure Functions</abbr>.

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

また、この記事の [CLI ベースのバージョン](create-first-function-cli-python.md)もあります。

## <a name="1-prepare-your-environment"></a>1.環境を準備する

作業を開始する前に、次の要件が満たされていることを確認します。

+ Azure <abbr title="Azure の使用に関する課金情報を保持するプロファイル。">account</abbr> アクティブ <abbr title="Azure 内のリソース管理に使用される基本的な組織構造。通常、組織内の部門または個人に関連付けられます。">subscription</abbr>. [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) バージョン 3.x。

+ [Python 3.8](https://www.python.org/downloads/release/python-381/)、[Python 3.7](https://www.python.org/downloads/release/python-375/)、[Python 3.6](https://www.python.org/downloads/release/python-368/) が Azure Functions (x64) でサポートされます。

+ [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。

+ Visual Studio Code 用の [Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。  

+ Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。

<hr/>
<br/>

## <a name="2-create-your-local-project"></a>2. <a name="create-an-azure-functions-project"></a>ローカル プロジェクトを作成する

1. Azure アイコンを <abbr title="Visual Studio Code ウィンドウの左側で縦に並んだ一連のアイコン。">アクティビティ バー</abbr>で選択し、 **[Azure: Functions]** 領域の **[新しいプロジェクトの作成]** アイコンを選択します。

    ![[新しいプロジェクトの作成] を選択する](./media/functions-create-first-function-vs-code/create-new-project.png)

1. プロジェクト ワークスペースのディレクトリの場所を選択し、 **[選択]** をクリックします。

    > [!NOTE]
    > これらの手順は、ワークスペースの外部で実行するように設計されています。 ここでは、ワークスペースに含まれるプロジェクト フォルダーは選択しないでください。

1. プロンプトで、次の情報を入力します。

    + **Select a language for your function project (関数プロジェクトの言語を選択してください)** : [`Python`] を選択します。

    + **Select a Python alias to create a virtual environment (仮想環境を作成する Python エイリアスを選択してください)** : Python インタープリターの場所を選択します。 場所が表示されない場合は、Python バイナリの完全パスを入力してください。  

    + **Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください)** : [`HTTP trigger`] を選択します。

    + **Provide a function name (関数名を指定してください):** 「`HttpExample`.

    + **承認レベル**: `Anonymous` を選択します。この場合、すべてのユーザーが関数のエンドポイントを呼び出すことができます。 承認レベルの詳細については、[承認キー](functions-bindings-http-webhook-trigger.md#authorization-keys)に関するページを参照してください。

    + **Select how you would like to open your project (プロジェクトを開く方法を選択してください)** : [`Add to workspace`] を選択します。

<br/>
<details>
<summary><strong>関数プロジェクトを作成できない場合</strong></summary>

ローカル Functions プロジェクトの作成時に解決すべき最も一般的な問題は次のとおりです。
* Azure Functions 拡張機能がインストールされていません。 
</details>

<hr/>
<br/>

## <a name="run-the-function-locally"></a>関数をローカルで実行する

1. <kbd>F5</kbd> キーを押して関数アプリ プロジェクトを起動します。

1. ローカルで実行されている関数の URL エンドポイントを **ターミナル** パネルで確認します。

    ![ローカル関数の VS Code の出力](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)


1. Core Tools を実行したまま、**Azure: Functions** 領域に移動します。 **[Functions]** の **[ローカル プロジェクト]**  >  **[Functions]** を展開します。 `HttpExample` 関数を右クリック (Windows) または <kbd>Ctrl</kbd> キーを押しながらクリック (macOS) して、 **[Execute Function Now]\(今すぐ関数を実行\)** を選択します。

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Visual Studio Code から今すぐ関数を実行する":::

1. **[Enter request body]\(要求本文を入力してください\)** に、要求メッセージ本文の値として `{ "name": "Azure" }` が表示されます。 Enter キーを押して、この要求メッセージを関数に送信します。  

1. ローカルで関数を実行し、応答が返されると、Visual Studio Code で通知が発生します。 関数の実行に関する情報は、 **[ターミナル]** パネルに表示されます。

1. <kbd>Ctrl + C</kbd> キーを押して Core Tools を停止し、デバッガーの接続を解除します。

<br/>
<details>
<summary><strong>関数をローカルで実行できない場合</strong></summary>

ローカル Functions プロジェクトの実行時に解決すべき最も一般的な問題は次のとおりです。
* Core Tools がインストールされていません。 
*  Windows での実行に問題がある場合、Visual Studio Code の既定のターミナル シェルが **WSL Bash** に設定されていないことをご確認ください。 
</details>

<hr/>
<br/>

## <a name="4-sign-in-to-azure"></a>4.Azure へのサインイン

アプリを発行するには、Azure にサインインします。 既にサインインしている場合は、次のセクションに進んでください。

1. アクティビティ バーで Azure アイコンを選択し、 **[Azure: Functions]** 領域の **[Azure にサインイン]** を選択します。

    ![VS Code 内で Azure にサインインする](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. ブラウザーでプロンプトが表示されたら、**ご利用の Azure アカウントを選択** し、その Azure アカウントの資格情報を使用して **サインイン** します。

1. 正常にサインインしたら、新しいブラウザー ウィンドウを閉じて Visual Studio Code に戻ります。 

<hr/>
<br/>

## <a name="5-publish-the-project-to-azure"></a>5.Azure にプロジェクトを発行する

初めてコードをデプロイする際は、Azure サブスクリプションに関数リソースを作成します。

1. アクティビティ バーの Azure アイコンを選択し、 **[Azure: Functions]** 領域で、 **[Deploy to function app]\(関数アプリにデプロイ\)** ボタンを選択します。

    ![プロジェクトを Azure に発行する](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. プロンプトで、次の情報を入力します。

    + **フォルダーの選択**: 関数アプリが格納されているフォルダーを選択します。

    + **Select subscription (サブスクリプションを選択してください)** : 使用するサブスクリプションを選択します。 サブスクリプションが 1 つのみの場合、このプロンプトは表示されません。

    + **Select function app in Azure (Azure で関数アプリを選択してください)** : [`+ Create new Function App`] を選択します。

    + **Enter a globally unique name for the function app (関数アプリのグローバルに一意の名前を入力します)** : URL パスに有効な名前を入力します。 入力した名前は、 <abbr title="Azure ユーザー全体でグローバルに一意であるべき名前。 たとえば、contoso-bizapp-func-20 のように、個人名または組織名、アプリケーション名、数値 ID の組み合わせを使用できます。">Azure 全体での一意性を確保するために検証されます。</abbr>. 

    + **Select a runtime (ランタイムを選択してください)** : ローカルで実行している Python のバージョンを選択します。 `python --version` コマンドを使用してバージョンを確認してください。

    + **Select a location for new resources (新しいリソースの場所を選択してください)** : パフォーマンスを向上させるために、お近くの [リージョン](https://azure.microsoft.com/regions/)を選択してください。

    この拡張機能は、Azure に作成されている個々のリソースの状態を通知領域に表示します。

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure リソース作成の通知":::

1. 関数アプリが作成され、展開パッケージが適用されると、通知が表示されます。 **[View Output]\(出力の表示\)** を選択して、作成とデプロイの結果を表示します。 

    ![作成完了通知](./media/functions-create-first-function-vs-code/function-create-notifications.png)

<br/>
<details>
<summary><strong>関数を発行できない場合</strong></summary>

このセクションでは Azure リソースを作成し、ローカル コードを関数アプリにデプロイしました。 成功しなかった場合は、次のことを確認してください。

* 出力でエラー情報を確認します。 出力は、右下隅にあるベル アイコンでも確認できます。 
* 既存の関数アプリに発行していないか確認します。 この操作を実行すると、Azure にあるそのアプリの内容が上書きされます。
</details>


<br/>
<details>
<summary><strong>作成されたリソース</strong></summary>

完了すると、関数アプリ名に基づく名前を使用して、次の Azure リソースがサブスクリプションに作成されます。 
* **リソース グループ**: 同じリージョンにある関連するリソースの論理上のコンテナーです。
* **Azure ストレージ アカウント**: プロジェクトの状態など、プロジェクトに関する情報はストレージ リソースに保管されます。
* **従量課金プラン**: サーバーレス関数アプリの実行環境となるホストを定義します。
* **関数アプリ**: 関数コードを実行するための環境を提供し、論理ユニットとして関数をグループ化します。
* **Application Insights**: サーバーレス関数の使用状況を追跡します。

</details>

<hr/>
<br/>

## <a name="6-run-the-function-in-azure"></a>6.Azure で関数を実行する

1. **[Azure: Functions]** サイド バーに戻り、新しい関数アプリを展開します。
1. **[Functions]** を展開し、`HttpExample` 関数を右クリック (Windows) または <kbd>Ctrl</kbd> キーを押しながらクリック (macOS) して、 **[Execute Function Now]\(今すぐ関数を実行\)** を選択します。

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Visual Studio Code から今すぐ Azure で関数を実行する":::

1. **[Enter request body]\(要求本文を入力してください\)** に、要求メッセージ本文の値として `{ "name": "Azure" }` が表示されます。

    Enter キーを押して、この要求メッセージを関数に送信します。  

1. Azure で関数を実行し、応答が返されると、Visual Studio Code で通知が発生します。

## <a name="7-clean-up-resources"></a>7.リソースをクリーンアップする

この後、[次の手順](#next-steps)に進んで <abbr title="関数をストレージ キューに関連付けて、そのキューにメッセージを作成できるようにするための手段。"> Azure Storage キュー出力バインド</abbr> を関数に追加する場合、既存の作業をベースにするので、リソースはすべてそのままにしておく必要があります。

それ以外の場合は、追加コストの発生を避けるために、次の手順に従って関数アプリとその関連リソースを削除してください。

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Functions のコストについて詳しくは、「[従量課金プランのコストの見積もり](functions-consumption-costs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

出力バインディングを追加してその関数を拡張します。 <abbr title="関数と他のリソースとの間の宣言型の接続。 入力バインドは関数にデータを提供し、出力バインドは関数から他のリソースにデータを提供します。">binding</abbr>. このバインディングでは、HTTP 要求の文字列が Azure Queue Storage キュー内のメッセージに書き込まれます。 

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[問題がある場合は、お知らせください。](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
