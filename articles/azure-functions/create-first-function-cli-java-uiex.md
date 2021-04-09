---
title: コマンド ラインから Java 関数を作成する - Azure Functions
description: コマンド ラインから Java 関数を作成し、ローカル プロジェクトを Azure Functions のサーバーレス ホスティングに発行する方法を学習します。
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 32d91f01fcebec3e1ae0d19b1bff29a71f41c0d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050428"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>クイックスタート: コマンド ラインから Azure に Java 関数を作成する

> [!div class="op_single_selector" title1="関数の言語を選択します。 "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

コマンドライン ツールを使用して、HTTP 要求に応答する Java 関数を作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。

このクイックスタートを完了すると、わずかな (数セント未満の) コストが発生します。 <abbr title="Azure の使用に関する課金情報を保持するプロファイル。">Azure アカウント</abbr>.

Maven が好みの開発ツールでない場合は、[Gradle](./functions-create-first-java-gradle.md)、[IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions)、および [Visual Studio Code](create-first-function-vs-code-java.md) を使用する Java 開発者向けの同様のチュートリアルを確認してください。

## <a name="1-prepare-your-environment"></a>1.環境を準備する

開始する前に、次の項目を用意する必要があります。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 <abbr title="Azure 内のリソース管理に使用される基本的な組織構造。通常、組織内の部門または個人に関連付けられます。">subscription</abbr>. [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.x。

+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)、バージョン 8 または 11。 `JAVA_HOME` 環境変数を、適切なバージョンの JDK のインストール場所に設定する必要があります。

+ [Apache Maven](https://maven.apache.org) バージョン 3.0 以降。

### <a name="prerequisite-check"></a>前提条件のチェック

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、 <abbr title="ローカル コンピューターで Azure Functions を操作するための一連のコマンド ライン ツール。">Azure Functions Core Tools</abbr> のバージョンが 3.x であることを確認します。

+ `az --version` を実行して、Azure CLI バージョンが 2.4 以降であることを確認します。

+ `az login` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. ローカル関数プロジェクトを作成する

Azure Functions における関数プロジェクトとは、それぞれが特定のトリガーに応答する個別の関数を 1 つまたは複数含んだコンテナーです。 <abbr title="HTTP 要求、キュー メッセージ、特定の時刻など、関数のコードを呼び出すイベントの種類。">トリガー (trigger)</abbr>. プロジェクト内のすべての関数は、同じローカル構成とホスティング構成を共有します。 このセクションでは、関数を 1 つだけ含んだ関数プロジェクトを作成します。

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

    <br/>
    <details>
    <summary><strong>Java 11 で関数を実行するには</strong></summary>

    Java 11 で関数を実行する場合は、`-DjavaVersion=11` を使用します。 詳細については、「[Java のバージョン](functions-reference-java.md#java-versions)」を参照してください。
    </details>

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

<br/>
<details>
<summary><strong>LocalFunctionProj フォルダーには何が作成されますか?</strong></summary>

このフォルダーには、*Function.java*、*FunctionTest.java*、*pom.xml* など、プロジェクトに関するさまざまなファイルが格納されています。 [local.settings.json](functions-run-local.md#local-settings-file) や [host.json](functions-host-json.md) という名前の構成ファイルも存在します。 *local.settings.json* には Azure からダウンロードしたシークレットを含めることができるため、このファイルは既定で *.gitignore* ファイルによってソース管理から除外されます。
</details>

<br/>
<details>
<summary><strong>Function.java のコード</strong></summary>

*Function.java* には、`run` メソッドが含まれています。これは、トリガー動作を定義する [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) 注釈で装飾された [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) 内の `request` 変数で要求データを受け取ります。 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

応答メッセージは、[HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API によって生成されます。

このアーキタイプでは、関数の単体テストも生成されます。 関数を変更してバインドを追加したり、新しい関数をプロジェクトに追加したりする場合は、*FunctionTest.java* ファイル内のテストも変更する必要があります。
</details>

<br/>
<details>
<summary><strong>pom.xml のコード</strong></summary>

アプリをホストするために作成された Azure リソースの設定は、生成された *pom.xml* ファイル内の `com.microsoft.azure` という **groupId** のプラグインの **configuration** 要素で定義されます。 たとえば、次の構成要素では、Maven ベースのデプロイに対して、`westus` リージョンの `java-functions-group` リソース グループに関数アプリを作成することを指示しています。 <abbr title="リソースが割り当てられている特定の Azure データ センターの地理的な呼称。">region</abbr>. 関数アプリ自体は、`java-functions-app-service-plan` プラン (既定では、サーバーレスの従量課金プラン) でホストされている Windows 上で実行されます。

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

これらの設定を変更して、Azure でのリソースの作成方法を制御できます。たとえば、初期デプロイの前に `runtime.os` を `windows` から `linux` に変更することができます。 Maven プラグインでサポートされている設定の完全な一覧については、[構成の詳細](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)に関するページを参照してください。
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. 関数をローカルで実行する

1. *LocalFunctionProj* フォルダーから、ローカルの Azure Functions ランタイム ホストを起動して **関数を実行** します。

    ```console
    mvn clean package
    mvn azure-functions:run
    ```

    出力の最後の方に、次の行があります。

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    </pre>

    HttpExample が上記のように表示されない場合、プロジェクトのルート フォルダー以外からホストを起動したと考えられます。 その場合は <kbd>Ctrl+C</kbd> キーを使用してホストを停止し、プロジェクトのルート フォルダーに移動して、前出のコマンドを再度実行してください。

1. この出力から `HttpExample` 関数の **URL をブラウザーにコピー** し、クエリ文字列 `?name=<YOUR_NAME>` を追加して、URL 全体を `http://localhost:7071/api/HttpExample?name=Functions` のようにします。 `Hello Functions` のようなメッセージがブラウザーに表示されます。

    ![ブラウザーでローカルに関数を実行した結果](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    要求を行うと、プロジェクトを起動したターミナルにもログ出力が表示されます。

1. 完了したら、<kbd>Ctrl + C</kbd> キーを押し、<kbd>y</kbd> を選択して関数ホストを停止してください。

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4.Azure に関数プロジェクトをデプロイする

最初に関数プロジェクトをデプロイすると、関数アプリと関連リソースが Azure に作成されます。 アプリをホストするために作成された Azure リソースの設定は、*pom.xml* ファイルに定義されています。 この記事では、既定値をそのまま使用します。

<br/>
<details>
<summary><strong>Linux で実行される関数アプリを作成するには</strong></summary>

Windows ではなく Linux で実行される関数アプリを作成するには、*pom.xml* ファイルの `runtime.os` 要素を `windows` から `linux` に変更します。 従量課金プランでの Linux の実行は、[これらのリージョン](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)でサポートされています。 Linux 上で実行されるアプリと Windows 上で実行されるアプリを同じリソース グループに含めることはできません。
</details>

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

    デプロイにより、プロジェクト ファイルがパッケージ化され、[zip デプロイ](functions-deployment-technologies.md#zip-deploy)を使用して新しい関数アプリにデプロイされます。 このコードは、Azure の展開パッケージから実行されます。

<br/>
<details>
<summary><strong>Azure には何が作成されますか?</strong></summary>

+ リソース グループ。 名前は _java-functions-group_ です。
+ ストレージ アカウント。 Functions に必要です。 名前は、ストレージ アカウント名の要件に基づいてランダムに生成されます。
+ ホスティング プラン。 _westus_ リージョンでの関数アプリのサーバーレス ホスティング。 名前は _java-functions-app-service-plan_ です。
+ 関数アプリ。 関数アプリは、関数のデプロイと実行の単位です。 名前は _artifactId_ に基づいてランダムに生成され、ランダムに生成された番号が付加されます。
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5.Azure 上の関数を呼び出す

この関数は HTTP トリガーを使用しているため、呼び出しは、**その URL にブラウザーから HTTP 要求を送信** するか、または curl などのツールを使用して行います。 <abbr title="URL に対する HTTP 要求を生成するためのコマンド ライン ツール。 https://curl.se/">curl</abbr>.

# <a name="browser"></a>[ブラウザー](#tab/browser)

`publish` コマンドの出力に表示されている完全な **呼び出し URL** にクエリ パラメーター `&name=Functions` を追加して、ブラウザーのアドレス バーにコピーします。 関数をローカルで実行したときと同様の出力がブラウザーに表示されるはずです。

![Azure 上で実行された関数の出力をブラウザーで表示したところ](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

**呼び出し URL** にパラメーター `&name=Functions` を追加して [`curl`](https://curl.haxx.se/) を実行します。 "Hello Functions" というテキストがコマンドの出力として表示されます。

![Azure 上で実行された関数の出力を curl を使用して表示したところ](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6.リソースをクリーンアップする

[次のステップ](#next-steps)に進んで Azure Storage <abbr title="Azure Storage で、関数をストレージ キューに関連付けて、そのキューにメッセージを作成できるようにするための手段。">キュー出力バインド</abbr>を追加する場合、既存の作業をベースにするので、リソースはすべてそのままにしておいてください。

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

<br>
<hr/>

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Storage キューに接続する](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
