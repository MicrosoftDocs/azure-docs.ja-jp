---
title: Java と IntelliJ を使用して Azure 関数を作成する
description: Java と IntelliJ を使用して、単純な HTTP によってトリガーしたサーバーレス アプリを Azure で作成し公開する方法について説明します。
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 38ccc99de72af8f989cca0fefae2f962fb178f0e
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373370"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Java と IntelliJ を使用して初めての Azure 関数を作成する

この記事では、次の内容について説明します。
- IntelliJ IDEA を使用して[サーバーレス](https://azure.microsoft.com/overview/serverless-computing/)関数プロジェクトを作成する方法
- 自身のコンピューターで統合開発環境 (IDE) 内の関数をテストおよびデバッグする手順
- 関数プロジェクトを Azure Functions にデプロイする手順

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>開発環境を設定する

Java および IntelliJ で関数を開発するには、次のソフトウェアをインストールします。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
+ [Azure でサポートされている Java 8 用の Java Development Kit (JDK)](https://aka.ms/azure-jdks)
+ [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition または Community Edition がインストールされていること
+ [Maven 3.5.0 以降](https://maven.apache.org/download.cgi)
+ 最新の [Function Core Tools](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>インストールとサインイン

1. IntelliJ IDEA の [設定/環境設定] ダイアログ (Ctrl+Alt+S) で、 **[プラグイン]** を選択します。 次に、**Marketplace** で **Azure Toolkit for IntelliJ** を見つけ、 **[インストール]** をクリックします。 インストール後、 **[再起動]** をクリックしてプラグインをアクティブにします。 

    ![Marketplace の Azure Toolkit for IntelliJ プラグイン][marketplace]

2. Azure アカウントにサインインするには、サイドバーの **Azure Explorer** を開き、上部のバーにある **[Azure サインイン]** アイコンをクリックします (または [IDEA] メニューから **[ツール]/[Azure]/ [Azure サインイン]** をクリックします)。
    ![IntelliJ Azure サインイン コマンド][intellij-azure-login]

3. **[Azure サインイン]** ウィンドウで、 **[Device Login]\(デバイスのログイン\)** を選択し、次に **[サインイン]** をクリックします ([他のサインイン オプション](/azure/developer/java/toolkit-for-intellij/sign-in-instructions))。

   ![[デバイスのログイン] が選択されている [Azure サインイン] ウィンドウ][intellij-azure-popup]

4. **[Azure Device Login]\(Azure デバイスのログイ\)** ダイアログで **[Copy&Open]\(コピーして開く\)** をクリックします。

   ![[Azure ログイン] ダイアログ ウィンドウ][intellij-azure-copycode]

5. ブラウザーで、該当のデバイス コード (前の手順で **[Copy&Open]\(コピーして開く\)** をクリックしたときにコピーされたもの) を貼り付け、 **[次へ]** をクリックします。

   ![デバイスのログイン ブラウザー][intellij-azure-link-ms-account]

6. **[サブスクリプションの選択]** ダイアログ ボックスで、使用するサブスクリプションを選択し、 **[OK]** をクリックします。

   ![[サブスクリプションの選択] ダイアログ ボックス][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>ローカル プロジェクトを作成する

このセクションでは、Azure Toolkit for IntelliJ を使用して、ローカル Azure Functions プロジェクトを作成します。 後からこの記事の中で、関数コードを Azure に発行します。 

1. IntelliJ のウェルカム ダイアログを開き、 *[Create New Project]\(新規プロジェクトの作成\)* を選択して新規プロジェクト ウィザードを開き、 *[Azure Functions]* を選択します。

    ![関数プロジェクトを作成する](media/functions-create-first-java-intellij/create-functions-project.png)

1. *[Http Trigger]\(HTTP トリガー\)* を選択し、 *[Next]\(次へ\)* をクリックして、以降のページですべての構成を行います。プロジェクトの場所を確認して *[Finish]\(終了\)* をクリックすると、Intellj IDEA で新しいプロジェクトが開きます。

    ![関数プロジェクトの作成の終了](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>関数アプリをローカルで実行する

1. `src/main/java/org/example/functions/HttpTriggerFunction.java`に移動して生成されたコードを確認します。 行 *17*の横に、緑色の *[Run]\(実行\)* ボタンが表示されています。それをクリックして *[Run 'azure-function-exam...']\('azure-function-exam...' の実行\)* を選択すると、関数アプリが実行されていくつかのログが出力されます。

    ![関数プロジェクトのローカル実行](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![関数プロジェクトのローカル実行の出力](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. 出力されたエンドポイントにブラウザーからアクセスし、関数を試すことができます (例: `http://localhost:7071/api/HttpTrigger-Java?name=Azure`)。

    ![関数のローカル実行のテスト結果](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. ログは IDEA にも出力されます。ここで、"*停止*" ボタンをクリックして関数を停止します。

    ![関数のローカル実行のテスト ログ](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>関数アプリをローカルでデバッグする

1. 関数アプリをローカルでデバッグしてみましょう。ツールバーの *[Debug]\(デバッグ\)* ボタンをクリックします (表示されていない場合は、 *[View]\(表示\) -> [Appearance]\(外観\) -> [Toolbar]\(ツールバー\)* をクリックしてツールバーを有効にします)。

    ![関数をローカルでデバッグするボタン](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. ファイル `src/main/java/org/example/functions/HttpTriggerFunction.java` の行 *20* をクリックしてブレークポイントを追加し、エンドポイント `http://localhost:7071/api/HttpTrigger-Java?name=Azure` に再度アクセスすると、ブレークポイントにヒットします。これにより、"*ステップ実行*"、"*ウォッチ*"、"*評価*"など、より多くのデバッグ機能を試すことができます。 停止ボタンをクリックしてデバッグ セッションを停止します。

    ![関数のローカル デバッグの中断](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>Azure に関数アプリをデプロイする

1. IntelliJ の [Project]\(プロジェクト\) エクスプローラーで、 *[Azure] -> [Deploy to Azure Functions]\(Azure Functionsにデプロイ\)* を選択します。

    ![Azure に関数をデプロイする](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. まだ関数アプリがない場合は、 *[No available function, click to create a new one]\(使用可能な関数がありません。クリックして新規作成します\)* をクリックします。

    ![Azure への関数のデプロイでのアプリの作成](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. 関数アプリの名前を入力して適切なサブスクリプション、プラットフォーム、リソース グループ、App Service プランを選択します。ここで、リソース グループや App Service プランを作成することもできます。 次に、アプリの設定を変更せずに *[OK]* をクリックして新しい関数が作成されるまでしばらく待ちます。 *[Creating New Function App]\(新しい関数アプリを作成しています\)* 進行状況バーが消えます。

    ![Azure への関数のデプロイでのアプリの作成ウィザード](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. デプロイする関数アプリを選択します (作成した新しい関数アプリが自動的に選択されます)。 *[Run]\(実行\)* をクリックして関数をデプロイします。

    ![Azure への関数のデプロイの実行](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![Azure への関数のデプロイのログ](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>IDEA から Azure Functions を管理する

1. IDEA の "*Azure 用エクスプローラー*" を使用して関数を管理できます。 *[Function App]\(関数アプリ\)* をクリックすると、ここにすべての関数が表示されます。

    ![エクスプローラーでの関数の表示](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. いずれかの関数をクリックして選択し、右クリックして *[Show Properties]\(プロパティの表示\)* を選択して詳細ページを開きます。 

    ![関数プロパティの表示](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. 関数 *HttpTrigger-Java* を右クリックし、 *[Trigger Function]\(関数のトリガー\)* を選択します。トリガー URL でブラウザーが開きます。

    ![Azure への関数のデプロイの実行](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>プロジェクトに関数を追加する

1. パッケージ *org.example.functions* を右クリックして *[New]\(新規\) -> [Azure Function Class]\(Azure 関数クラス\)* を選択します。 

    ![プロジェクト エントリへの関数の追加](media/functions-create-first-java-intellij/add-functions-entry.png)

1. 関数クラスの作成ウィザードで、クラス名に「*HttpTest*」を入力し、 *[HttpTrigger]* を選択し、 *[OK]* をクリックして作成します。この方法で、新しい関数を必要なだけ作成できます。

    ![プロジェクトへの関数の追加でのトリガーの選択](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![プロジェクトへの関数の追加での出力](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>関数のクリーンアップ

1. Azure Explorer での Azure Functions の削除
      
      ![プロジェクトへの関数の追加でのトリガーの選択](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>次のステップ

HTTP でトリガーされる関数を含む Java 関数プロジェクトを作成し、ローカル コンピューターでそれを実行し、Azure にデプロイしました。 次は以下の方法で関数を拡張します。

> [!div class="nextstepaction"]
> [Azure Storage キュー出力バインドを追加する](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
