---
title: Visual Studio Code を使用して Java 関数を作成する - Azure Functions
description: Visual Studio Code の Azure Functions 拡張機能を使用して Java 関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法について説明します。
ms.topic: quickstart
ms.date: 11/03/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6c84bf4ebc73857fa0280ffbcbf46b68c2da630f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031720"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>クイックスタート: Visual Studio Code を使用して Azure に Java 関数を作成する

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Visual Studio Code を使用して、HTTP 要求に応答する Java 関数を作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。

このクイックスタートを完了すると、わずかな (数セント未満の) コストが発生します。 <abbr title="Azure の使用に関する課金情報を保持するプロファイル。">Azure アカウント</abbr>.

Visual Studio Code が好みの開発ツールでない場合は、[Maven](create-first-function-cli-java.md)、[Gradle](./functions-create-first-java-gradle.md)、および [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) を使用する Java 開発者向けの同様のチュートリアルを確認してください。

## <a name="1-prepare-your-environment"></a>1.環境を準備する

作業を開始する前に、次の要件が満たされていることを確認します。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 <abbr title="Azure 内のリソース管理に使用される基本的な組織構造。通常、組織内の部門または個人に関連付けられます。">subscription</abbr>. [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)、バージョン 8 または 11。

+ [Apache Maven](https://maven.apache.org) バージョン 3.0 以降。

+ [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。

+ [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。

<br/>
<hr/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a>ローカル Functions プロジェクトを作成する

1. **アクティビティ バー** で Azure アイコンを選択し、 **[Azure: Functions]** 領域の **[新しいプロジェクトの作成]** アイコンを選択します。

    ![[新しいプロジェクトの作成] を選択する](./media/functions-create-first-function-vs-code/create-new-project.png)

1. プロジェクト ワークスペースの **ディレクトリの場所を選択** し、 **[選択]** を選択します。

1. プロンプトで、次の情報を入力します。

    + **Select a language for your function project (関数プロジェクトの言語を選択してください)** : [`Java`] を選択します。

    + **Select a version of Java (Java のバージョンを選択してください)** : Azure における関数の実行環境 (Java バージョン) として `Java 8` または `Java 11` を選択します。 ローカルで確認済みの Java バージョンを選択してください。

    + **Provide a group ID (グループ ID を指定してください)** : [`com.function`] を選択します。

    + **Provide an artifact ID (成果物 ID を指定してください)** : [`myFunction`] を選択します。

    + **Provide a version (バージョンを指定してください)** : [`1.0-SNAPSHOT`] を選択します。

    + **Provide a package name (パッケージ名を指定してください)** : [`com.function`] を選択します。

    + **Provide an app name (アプリ名を指定してください)** : [`myFunction-12345`] を選択します。

    + **承認レベル**: `Anonymous` を選択します。この場合、すべてのユーザーが関数のエンドポイントを呼び出すことができます。

    + **Select how you would like to open your project (プロジェクトを開く方法を選択してください)** : [`Add to workspace`] を選択します。

<br/>

<details>
<summary><strong>関数プロジェクトを作成できない場合</strong></summary>

ローカル Functions プロジェクトの作成時に解決すべき最も一般的な問題は次のとおりです。
* Azure Functions 拡張機能がインストールされていません。 
</details>

<br/>
<hr/>

## <a name="3-run-the-function-locally"></a>3. 関数をローカルで実行する

1. <kbd>F5</kbd> キーを押して関数アプリ プロジェクトを起動します。

1. ローカルで実行されている関数の URL エンドポイントを **ターミナル** で確認します。

    ![ローカル関数の VS Code の出力](media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Core Tools を実行したまま、**Azure: Functions** 領域に移動します。 **[Functions]** の **[ローカル プロジェクト]**  >  **[Functions]** を展開します。 `HttpExample` 関数を右クリック (Windows) または <kbd>Ctrl</kbd> キーを押しながらクリック (macOS) して、 **[Execute Function Now]\(今すぐ関数を実行\)** を選択します。

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Visual Studio Code から今すぐ関数を実行する":::

1. **[Enter request body]\(要求本文を入力してください\)** に、要求メッセージ本文の値として `{ "name": "Azure" }` が表示されます。 <kbd>Enter</kbd> キーを押して、この要求メッセージを関数に送信します。  

1. ローカルで関数を実行し、応答が返されると、Visual Studio Code で通知が発生します。 関数の実行に関する情報は、 **[ターミナル]** パネルに表示されます。

1. <kbd>Ctrl + C</kbd> キーを押して Core Tools を停止し、デバッガーの接続を解除します。

<br/>

<details>
<summary><strong>関数をローカルで実行できない場合</strong></summary>

ローカル Functions プロジェクトの実行時に解決すべき最も一般的な問題は次のとおりです。
* Core Tools がインストールされていません。 
*  Windows での実行に問題がある場合、Visual Studio Code の既定のターミナル シェルが WSL Bash に設定されていないことをご確認ください。 
</details>

<br/>
<hr/>

## <a name="4-sign-in-to-azure"></a>4.Azure へのサインイン

アプリを発行するには、Azure にサインインします。 既にサインインしている場合は、次のセクションに進んでください。

1. アクティビティ バーで Azure アイコンを選択し、 **[Azure: Functions]** 領域の **[Azure にサインイン]** を選択します。

    ![VS Code 内で Azure にサインインする](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. ブラウザーでプロンプトが表示されたら、**ご利用の Azure アカウントを選択** し、その Azure アカウントの資格情報を使用して **サインイン** します。

1. 正常にサインインしたら、新しいブラウザー ウィンドウを閉じて Visual Studio Code に戻ります。

<br/>
<hr/>

## <a name="5-publish-the-project-to-azure"></a>5.Azure にプロジェクトを発行する

初めてコードをデプロイする際は、Azure サブスクリプションに関数リソースを作成します。

1. アクティビティ バーの Azure アイコンを選択し、 **[Azure: Functions]** 領域で、 **[Deploy to function app]\(関数アプリにデプロイ\)** ボタンを選択します。

    ![プロジェクトを Azure に発行する](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. プロンプトで、次の情報を入力します。

    + **フォルダーの選択**: 関数アプリが格納されているフォルダーを選択します。 

    + **Select subscription (サブスクリプションを選択してください)** : 使用するサブスクリプションを選択します。 サブスクリプションが 1 つのみの場合、このプロンプトは表示されません。

    + **Select function app in Azure (Azure で関数アプリを選択してください)** : [`Create new Function App`] を選択します。

    + **Enter a globally unique name for the function app (関数アプリのグローバルに一意の名前を入力します)** : Azure 全体で一意の名前を URL パスに入力します。 入力した名前は、グローバルな一意性を確保するために検証されます。

    - **Select a location for new resources (新しいリソースの場所を選択してください)** : パフォーマンスを向上させるために、お近くの [リージョン](https://azure.microsoft.com/regions/)を選択してください。

1. 関数アプリが作成され、展開パッケージが適用されると、通知が表示されます。 **[View Output]\(出力の表示\)** を選択して、作成とデプロイの結果を表示します。

    ![作成完了通知](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

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

<br/>
<hr/>

## <a name="6-run-the-function-in-azure"></a>6.Azure で関数を実行する

1. **[Azure: Functions]** 領域 (サイド バー内) に戻り、サブスクリプション、新しい関数アプリ、 **[関数]** の順に展開します。 `HttpExample` 関数を右クリック (Windows) または <kbd>Ctrl</kbd> キーを押しながらクリック (macOS) して、 **[Execute Function Now]\(今すぐ関数を実行\)** を選択します。

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Visual Studio Code から今すぐ Azure で関数を実行する":::

1. **[Enter request body]\(要求本文を入力してください\)** に、要求メッセージ本文の値として `{ "name": "Azure" }` が表示されます。 Enter キーを押して、この要求メッセージを関数に送信します。  

1. Azure で関数を実行し、応答が返されると、Visual Studio Code で通知が発生します。

<br/>
<hr/>

## <a name="7-clean-up-resources"></a>7.リソースをクリーンアップする

[次のステップ](#next-steps)に進む予定がなければ、追加コストの発生を避けるために関数アプリとそのリソースを削除してください。

1. Visual Studio Code のアクティビティ バーにある Azure アイコンを選択し、サイド バーの [Functions]\(関数\) 領域を選択します。
1. 関数アプリを選択し、右クリックして **[Delete Function app]\(関数アプリの削除\)** を選択します。

<br/>
<hr/>

## <a name="next-steps"></a>次のステップ

を追加して関数を展開します。 <abbr title="Azure Storage で、関数をストレージ キューに関連付けて、そのキューにメッセージを作成できるようにするための手段。">出力バインディング</abbr>. このバインディングでは、HTTP 要求の文字列が Azure Queue Storage キュー内のメッセージに書き込まれます。

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)
