---
title: コマンド ラインから Java 関数を作成する - Azure Functions
description: コマンド ラインから Java 関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法を学習します。
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-java-uiex
ms.openlocfilehash: 0ef52da8400cfadfaaa26664d984d8b898a3b9a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729302"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>クイックスタート: コマンド ラインから Azure に Java 関数を作成する

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

この記事では、コマンドライン ツールを使用して、HTTP 要求に応答する Java 関数を作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

> [!NOTE]
> Maven が好みの開発ツールでない場合は、[Gradle](./functions-create-first-java-gradle.md)、[IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions)、および [Visual Studio Code](create-first-function-vs-code-java.md) を使用する Java 開発者向けの同様のチュートリアルを確認してください。

## <a name="configure-your-local-environment"></a>ローカル環境を構成する

開始する前に、次の項目を用意する必要があります。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.x。

+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)、バージョン 8 または 11。 `JAVA_HOME` 環境変数を、適切なバージョンの JDK のインストール場所に設定する必要があります。     

+ [Apache Maven](https://maven.apache.org) バージョン 3.0 以降。

### <a name="prerequisite-check"></a>前提条件のチェック

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 3.x であることを確認します。

+ `az --version` を実行して、Azure CLI バージョンが 2.4 以降であることを確認します。

+ `az login` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

## <a name="create-a-local-function-project"></a>ローカル関数プロジェクトを作成する

Azure Functions における関数プロジェクトとは、それぞれが特定のトリガーに応答する個別の関数を 1 つまたは複数含んだコンテナーです。 プロジェクト内のすべての関数は、同じローカル構成とホスティング構成を共有します。 このセクションでは、関数を 1 つだけ含んだ関数プロジェクトを作成します。

1. 空のフォルダーで次のコマンドを実行して、[Maven アーキタイプ](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)から Functions プロジェクトを生成します。 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```
    
    ---

    > [!IMPORTANT]
    > + Java 11 で関数を実行する場合は、`-DjavaVersion=11` を使用します。 詳細については、「[Java のバージョン](functions-reference-java.md#java-versions)」を参照してください。 
    > + この記事の作業を行うには、`JAVA_HOME` 環境変数を、適切なバージョンの JDK のインストール場所に設定する必要があります。

1. Maven により、デプロイ時にプロジェクトの生成を終了するための値の入力が求められます。   
    入力を求められたら、次の値を入力します。

    | Prompt | 値 | 説明 |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Java の[パッケージ命名規則](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)に従って、すべてのプロジェクトにわたって対象のプロジェクトを一意に識別する値。 |
    | **artifactId** | `fabrikam-functions` | バージョン番号のない、jar の名前である値。 |
    | **version** | `1.0-SNAPSHOT` | 既定値を選択します。 |
    | **package** | `com.fabrikam` | 生成された関数コードの Java パッケージである値。 既定値を使用します。 |

1. 「`Y`」と入力するか、Enter キーを押して確認します。

    Maven により、_artifactId_ という名前の新しいフォルダーにプロジェクト ファイルが作成されます (この例では `fabrikam-functions`)。 

1. プロジェクト フォルダーに移動します。

    ```console
    cd fabrikam-functions
    ```

    このフォルダーにはプロジェクト用の各種ファイルが格納されています。たとえば、[local.settings.json](functions-run-local.md#local-settings-file) や [host.json](functions-host-json.md) といった名前の構成ファイルです。 *local.settings.json* には Azure からダウンロードしたシークレットを含めることができるため、このファイルは既定で *.gitignore* ファイルによってソース管理から除外されます。

### <a name="optional-examine-the-file-contents"></a>(省略可) ファイルの内容を確認する

先に「[関数をローカルで実行する](#run-the-function-locally)」に進み、ファイルの内容は後から確認してもかまいません。

#### <a name="functionjava"></a>Function.java
*Function.java* には、`run` メソッドが含まれています。これは、トリガー動作を定義する [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) 注釈で装飾された [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) 内の `request` 変数で要求データを受け取ります。 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

応答メッセージは、[HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API によって生成されます。

#### <a name="pomxml"></a>pom.xml

アプリをホストするために作成された Azure リソースの設定は、生成された pom.xml ファイル内の `com.microsoft.azure` という **groupId** を持つプラグインの **configuration** 要素で定義されます。 たとえば、次の構成要素では、Maven ベースのデプロイに対して、`westus` リージョンの `java-functions-group` リソース グループに関数アプリを作成することを指示しています。 関数アプリ自体は、`java-functions-app-service-plan` プラン (既定では、サーバーレスの従量課金プラン) でホストされている Windows 上で実行されます。

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

これらの設定を変更して、Azure でのリソースの作成方法を制御できます。たとえば、初期デプロイの前に `runtime.os` を `windows` から `linux` に変更することができます。 Maven プラグインでサポートされている設定の完全な一覧については、[構成の詳細](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)に関するページを参照してください。

#### <a name="functiontestjava"></a>FunctionTest.java

このアーキタイプでは、関数の単体テストも生成されます。 関数を変更してバインドを追加したり、新しい関数をプロジェクトに追加したりする場合は、*FunctionTest.java* ファイル内のテストも変更する必要があります。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

1. *LocalFunctionProj* フォルダーから、ローカルの Azure Functions ランタイム ホストを起動して関数を実行します。

    ```console
    mvn clean package
    mvn azure-functions:run
    ```
    
    出力の最後の方に、次の行があります。
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    > [!NOTE]  
    > HttpExample が上記のように表示されない場合、プロジェクトのルート フォルダー以外からホストを起動したと考えられます。 その場合は **Ctrl** + **C** キーを使用してホストを停止し、プロジェクトのルート フォルダーに移動して、前出のコマンドを再度実行してください。

1. この出力から `HttpExample` 関数の URL をブラウザーにコピーし、クエリ文字列 `?name=<YOUR_NAME>` を追加して、URL 全体を `http://localhost:7071/api/HttpExample?name=Functions` のようにします。 `Hello Functions` のようなメッセージがブラウザーに表示されます。

    ![ブラウザーでローカルに関数を実行した結果](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    要求を行うと、プロジェクトを起動したターミナルにもログ出力が表示されます。

1. 完了したら、**Ctrl** + **C** キーを押し、`y` を選択して関数ホストを停止してください。

## <a name="deploy-the-function-project-to-azure"></a>Azure に関数プロジェクトをデプロイする

最初に関数プロジェクトをデプロイすると、関数アプリと関連リソースが Azure に作成されます。 アプリをホストするために作成された Azure リソースの設定は、[ファイル](#pomxml)で定義されています。 この記事では、既定値をそのまま使用します。

> [!TIP]
> Windows ではなく Linux で実行される関数アプリを作成するには、pom.xml ファイルの `runtime.os` 要素を `windows` から `linux` に変更します。 従量課金プランでの Linux の実行は、[これらのリージョン](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)でサポートされています。 Linux 上で実行されるアプリと Windows 上で実行されるアプリを同じリソース グループに含めることはできません。

1. デプロイする前に、Azure CLI または Azure PowerShell を使用して、Azure サブスクリプションにサインインします。 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    [az login](/cli/azure/reference-index#az-login) コマンドで Azure アカウントにサインインします。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットで Azure アカウントにサインインします。

    ---

1. 次のコマンドを使用して、対象のプロジェクトを新しい関数アプリにデプロイします。

    ```console
    mvn azure-functions:deploy
    ```
    
    これにより、Azure に次のリソースが作成されます。
    
    + リソース グループ。 名前は _java-functions-group_ です。
    + ストレージ アカウント。 Functions に必要です。 名前は、ストレージ アカウント名の要件に基づいてランダムに生成されます。
    + ホスティング プラン。 _westus_ リージョンでの関数アプリのサーバーレス ホスティング。 名前は _java-functions-app-service-plan_ です。
    + 関数アプリ。 関数アプリは、関数のデプロイと実行の単位です。 名前は _artifactId_ に基づいてランダムに生成され、ランダムに生成された番号が付加されます。
    
    デプロイにより、プロジェクト ファイルがパッケージ化され、[zip デプロイ](functions-deployment-technologies.md#zip-deploy)を使用して新しい関数アプリにデプロイされます。 このコードは、Azure の展開パッケージから実行されます。

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[次の手順](#next-steps)に進んで Azure Storage キュー出力バインディングを追加する場合、既存の作業をベースにするので、リソースはすべてそのままにしておいてください。

それ以外の場合は、追加コストの発生を避けるために、次のコマンドを使用して、リソース グループとそこに含まれるすべてのリソースを削除してください。

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
