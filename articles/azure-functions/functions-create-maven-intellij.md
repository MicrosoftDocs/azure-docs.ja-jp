---
title: Java と IntelliJ を使用して Azure 関数を作成する | Microsoft Docs
description: Java と IntelliJ を使用して、単純な HTTP によってトリガーしたサーバーレス アプリを Azure で作成し公開する方法について説明します。
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: Azure Functions, 関数, イベント処理, コンピューティング, サーバーレス アーキテクチャ, Java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: da93c60b52edf509900adf89fb688a0596d9763b
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877808"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Java と IntelliJ を使用して初めての Azure 関数を作成する

この記事では、次の内容について説明します。
- IntelliJ IDEA と Apache Maven を使用して[サーバーレス](https://azure.microsoft.com/overview/serverless-computing/)関数プロジェクトを作成する方法
- 自身のコンピューターで統合開発環境 (IDE) 内の関数をテストおよびデバッグする手順
- 関数プロジェクトを Azure Functions にデプロイする手順

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>開発環境を設定する

Java および IntelliJ で関数を開発するには、次のソフトウェアをインストールします。

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) (JDK) バージョン 8
- [Apache Maven](https://maven.apache.org) バージョン 3.0 以降
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download)、コミュニティまたは Maven が付属した Ultimate バージョン
- [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> この記事の手順を行うには、JAVA_HOME 環境変数を JDK のインストール場所に設定する必要があります。

 [Azure Functions Core Tools、バージョン 2](functions-run-local.md#v2) をインストールすることをお勧めします。 ここには、Azure Functions を記述、実行、デバッグするためのローカル開発環境が提供されます。

## <a name="create-a-functions-project"></a>Functions プロジェクトを作成する

1. IntelliJ IDEA で、**[新しいプロジェクトの作成]** を選択します。  
1. **[新しいプロジェクト]** ウィンドウの左ウィンドウで **[Maven]** を選択します。
1. **[Create from archetype]**(アーキタイプからの作成) チェック ボックスをオンにして、**[Add Archetype]**(アーキタイプの追加) に [[azure-functions-archetype]](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype) を選択します。
1. **[Add Archetype]**(アーキタイプの追加) ウィンドウで、次のように各フィールドに入力します。
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-archetype
    - _バージョン_:[中央リポジトリの最新バージョンを使用します](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![IntelliJ IDEAでアーキタイプから Maven プロジェクトを作成します](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. **[OK]** を選択し、**[次へ]** を選択します。
1. 現在のプロジェクトの詳細を入力し、**[完了]** を選択します。

Maven は、_ArtifactId_ 値と同じ名前で新しいフォルダーにプロジェクト ファイルを作成します。 生成されるプロジェクトのコードは、トリガーする HTTP 要求の本文をエコーする、[HTTP によってトリガーされる](/azure/azure-functions/functions-bindings-http-webhook)単純な関数です。

## <a name="run-functions-locally-in-the-ide"></a>IDE で関数をローカルで実行する

> [!NOTE]
> 関数をローカルで実行およびデバッグするには、[Azure Functions Core Tools、バージョン 2](functions-run-local.md#v2) をインストールしていることを確認してください。

1. 変更を手動でインポートするか、[自動インポート](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)を有効にします。
1. **[Maven Projects]**(Maven プロジェクト) ツールバーを開きます。
1. **[Lifecycle]** を展開し、**[package]** を開きます。 ソリューションが、新しく作成されたターゲット ディレクトリに構築されパッケージ化されます。
1. **[Plugins]** > **[azure-functions]** を展開し、**[azure-functions:run]** を開いて Azure Functions ローカル ランタイムを開始します。  
  ![Azure Functions の Maven ツールバー](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. 関数のテストが終了したら、実行ダイアログ ボックスを閉じます。 アクティブにして同時にローカルで実行できる関数ホストは 1 つだけです。

## <a name="debug-the-function-in-intellij"></a>IntelliJ で関数をデバッグする

1. デバッグ モードで関数ホストを開始するには、関数の実行時に **-DenableDebug** を引数として追加します。 [maven goals](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) で構成を変更することも、ターミナル ウィンドウで次のコマンドを実行することもできます。  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   このコマンドにより、関数ホストがデバッグ ポートを 5005 で開きます。

1. **[実行]** メニューで **[構成の編集]** を選択します。
1. **(+)** を選択して、**[リモート]** を追加します。
1. _[名前]_ および _[設定]_ フィールドに入力し、**[OK]** を選択して構成を保存します。
1. セットアップ後、**[Debug < Remote Configuration Name >]**(<リモート構成名> のデバッグ) を選択するか、キーボードで Shift + F9 キーを押してデバッグを開始します。

   ![IntelliJ での関数のデバッグ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

1. 完了したら、デバッガーと実行中のプロセスを停止します。 アクティブにして同時にローカルで実行できる関数ホストは 1 つだけです。

## <a name="deploy-the-function-to-azure"></a>関数を Azure にデプロイする

1. 関数を Azure にデプロイする前に、[Azure CLI を使用してログイン](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)する必要があります。

   ``` azurecli
   az login
   ```

1. `azure-functions:deploy` Maven ターゲットを使用して、新しい関数にコードをデプロイします。 [Maven Projects]\(Maven プロジェクト) ウィンドウで **azure-functions:deploy** オプションを選択することもできます。

   ```
   mvn azure-functions:deploy
   ```

1. 関数が正常にデプロイされると、Azure CLI 出力に関数の URL が表示されます。

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>次の手順

- 「[Azure Functions Java developer guide](functions-reference-java.md)」(Azure Functions Java 開発者ガイド) で、Java 関数の開発の詳細について確認します。
- `azure-functions:add` Maven ターゲットを使って、異なるトリガーの新しい関数をプロジェクトに追加します。
