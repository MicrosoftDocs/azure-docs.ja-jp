---
title: IntelliJ を使用して Azure Functions に Kotlin 関数を作成する
description: IntelliJ を使用して、HTTP によってトリガーされる単純な Kotlin 関数を作成する方法について説明します。この関数は後に、Azure のサーバーレス環境で実行できるように発行します。
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: f02643ee28d76d4f90206a1aa2879b4672da2a38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179458"
---
# <a name="create-your-first-kotlin-function-in-azure-using-intellij"></a>IntelliJ を使用して Azure で初めての Kotlin 関数を作成する

この記事では、IntelliJ IDEA プロジェクトで HTTP によってトリガーされる Java 関数を作成して、統合開発環境 (IDE) でこのプロジェクトを実行およびデバッグし、最後にこの関数プロジェクトを Azure の関数アプリにデプロイする方法について説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>開発環境を設定する

IntelliJ を使用して Kotlin 関数を作成し、Azure に発行するには、次のソフトウェアをインストールします。

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK) バージョン 8
- [Apache Maven](https://maven.apache.org) バージョン 3.0 以降
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download)、コミュニティまたは Maven が付属した Ultimate バージョン
- [Azure CLI](/cli/azure)
- Azure Functions Core Tools の[バージョン 2.x](functions-run-local.md#v2)。 ここには、Azure Functions を記述、実行、デバッグするためのローカル開発環境が提供されます。

> [!IMPORTANT]
> この記事の手順を行うには、JAVA_HOME 環境変数を JDK のインストール場所に設定する必要があります。

## <a name="create-a-function-project"></a>関数プロジェクトを作成する

1. IntelliJ IDEA で、 **[新しいプロジェクトの作成]** を選択します。  
1. **[新しいプロジェクト]** ウィンドウの左ウィンドウで **[Maven]** を選択します。
1. **[Create from archetype]\(アーキタイプからの作成\)** チェック ボックスをオンにして、 **[Add Archetype]\(アーキタイプの追加\)** で [[azure-functions-kotlin-archetype]](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype) を選択します。
1. **[Add Archetype]** (アーキタイプの追加) ウィンドウで、次のように各フィールドに入力します。
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-kotlin-archetype
    - _バージョン_:[中央リポジトリの最新バージョンを使用します](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![IntelliJ IDEAでアーキタイプから Maven プロジェクトを作成します](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. **[OK]** を選択し、 **[次へ]** を選択します。
1. 現在のプロジェクトの詳細を入力し、 **[完了]** を選択します。

Maven は、_ArtifactId_ 値と同じ名前で新しいフォルダーにプロジェクト ファイルを作成します。 生成されるプロジェクトのコードは、トリガーする HTTP 要求の本文をエコーする、[HTTP によってトリガーされる](./functions-bindings-http-webhook.md)単純な関数です。

## <a name="run-project-locally-in-the-ide"></a>IDE でプロジェクトをローカルに実行する

> [!NOTE]
> プロジェクトをローカルで実行およびデバッグするには、[Azure Functions Core Tools、バージョン 2](functions-run-local.md#v2) がインストールされていることを確認してください。

1. 変更を手動でインポートするか、[自動インポート](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)を有効にします。
1. **[Maven Projects]** (Maven プロジェクト) ツールバーを開きます。
1. **[Lifecycle]** を展開し、 **[package]** を開きます。 ソリューションが、新しく作成されたターゲット ディレクトリに構築されパッケージ化されます。
1. **[Plugins]**  >  **[azure-functions]** を展開し、 **[azure-functions:run]** を開いて Azure Functions ローカル ランタイムを開始します。  
  ![Azure Functions の Maven ツールバー](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. 関数のテストが終了したら、実行ダイアログ ボックスを閉じます。 アクティブにして同時にローカルで実行できる関数ホストは 1 つだけです。

## <a name="debug-the-project-in-intellij"></a>IntelliJ でプロジェクトをデバッグする

1. デバッグ モードで関数ホストを開始するには、関数の実行時に **-DenableDebug** を引数として追加します。 [maven goals](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) で構成を変更することも、ターミナル ウィンドウで次のコマンドを実行することもできます。  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   このコマンドにより、関数ホストがデバッグ ポートを 5005 で開きます。

1. **[実行]** メニューで **[構成の編集]** を選択します。
1. **(+)** を選択して、 **[リモート]** を追加します。
1. _[名前]_ および _[設定]_ フィールドに入力し、 **[OK]** を選択して構成を保存します。
1. セットアップ後、 **[Debug < Remote Configuration Name >]** (<リモート構成名> のデバッグ) を選択するか、キーボードで Shift + F9 キーを押してデバッグを開始します。

   ![IntelliJ でプロジェクトをデバッグする](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. 完了したら、デバッガーと実行中のプロセスを停止します。 アクティブにして同時にローカルで実行できる関数ホストは 1 つだけです。

## <a name="deploy-the-project-to-azure"></a>Azure にプロジェクトをデプロイする

1. プロジェクトを Azure の関数アプリにデプロイする前に、[Azure CLI を使用してログイン](/cli/azure/authenticate-azure-cli)する必要があります。

   ``` azurecli
   az login
   ```

1. `azure-functions:deploy` Maven ターゲットを使用して、新しい関数アプリにコードをデプロイします。 [Maven Projects]\(Maven プロジェクト) ウィンドウで **azure-functions:deploy** オプションを選択することもできます。

   ```
   mvn azure-functions:deploy
   ```

1. 関数アプリが正常にデプロイされると、Azure CLI 出力に HTTP トリガー関数の URL が表示されます。

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>次のステップ

これで、最初の Kotlin 関数アプリを Azure にデプロイできました。Java と Kotlin の関数の開発については、「[Azure Functions の Java 開発者向けガイド](functions-reference-java.md)」をご確認ください。
- `azure-functions:add` Maven ターゲットを使って、異なるトリガーの新しい関数アプリをプロジェクトに追加します。
