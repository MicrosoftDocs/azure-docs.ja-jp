---
title: "ローカルでの Azure Functions の開発と実行 | Microsoft Docs"
description: "Azure 関数を Azure Functions で実行する前に、ローカル コンピューターでコーディングしてテストする方法について説明します。"
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 07/12/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 07ad15c61bd4b3912dfa2f629218deebdebd6dc8
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="code-and-test-azure-functions-locally"></a>Azure Functions をローカルでコーディングしてテストする

[Azure Portal] では Azure Functions の開発用およびテスト用ツールの完全なセットが提供されていますが、多くの開発者はローカルでの開発を選択します。 Azure Functions では、お気に入りのコード エディターとローカル開発ツールを使用して、ローカル コンピューターで簡単に関数を開発し、テストできます。 独自の関数を使用して Azure でイベントをトリガーし、ローカル コンピューターで C# 関数や JavaScript 関数をデバッグできます。 

Visual Studio C# の開発者は、Azure Functions を [Visual Studio 2017 に統合](functions-develop-vs.md)することもできます。

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions Core Tools のインストール

Azure Functions Core Tools は、ローカル バージョンの Azure Functions ランタイムで、ローカルの Windows コンピューターで実行できます。 エミュレーターまたはシミュレーターではありません。 Azure で Functions を実行するランタイムと同じです。

[Azure Functions Core Tools] は npm パッケージとして提供されています。 まず [NodeJS をインストール](https://docs.npmjs.com/getting-started/installing-node) する必要があります。これには npm が含まれています。  

>[!NOTE]
>現時点では、Azure Functions Core Tools パッケージをインストールできるのは Windows コンピューターのみです。 この制限は、Functions ホストの一時的な制限によるものです。

[Azure Functions Core Tools] で追加されるコマンドのエイリアスを次に示します。
* **func**
* **azfun**
* **azurefunctions**

これらの別名はすべて、このトピックの例に示す `func` の代わりに使用できます。

## <a name="create-a-local-functions-project"></a>ローカル関数プロジェクトを作成する

ローカルで実行中は、Functions プロジェクトが、host.json ファイルと local.settings.json ファイルが含まれるディレクトリです。 このディレクトリは、Azure の関数アプリに相当します。 Azure Functions のフォルダー構造の詳細については、[Azure Functions の開発者向けガイド](functions-reference.md#folder-structure)を参照してください。

コマンド プロンプトで、次のコマンドを実行します。

```
func init MyFunctionProj
```

出力は次のテキストのようになります。

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Git リポジトリの作成を除外するには、`--no-source-control [-n]` オプションを使用します。

<a name="local-settings"></a>

## <a name="local-settings-file"></a>ローカル設定ファイル

local.settings.json ファイルには、アプリの設定、接続文字列、および Azure Functions Core Tools の設定が格納されます。 その構造を次に示します。

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
  },
  "Host": {
    "LocalHttpPort": 7071, 
    "CORS": "*" 
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```
| 設定      | Description                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | **true** に設定すると、すべての値がローカル コンピューターのキーを使用して暗号化されます。 `func settings` コマンドと共に使用されます。 既定値は **false** です。 |
| **値** | ローカルで実行するときに使用されるアプリケーション設定のコレクションです。 このオブジェクトに、アプリケーション設定を追加します。  |
| **AzureWebJobsStorage** | Azure Functions ランタイムによって内部的に使用される Azure Storage アカウントへの接続文字列を設定します。 このストレージ アカウントは、関数のトリガーをサポーします。 このストレージ アカウントの接続設定は、HTTP によってトリガーされる関数を除くすべての関数で必要です。  |
| **AzureWebJobsDashboard** | 関数のログを保存するのに使用される Azure Storage アカウントへの接続文字列を設定します。 この省略可能な値により、ポータルでログにアクセスできるようになります。|
| **Host** | このセクションの設定により、ローカルで実行時の Functions ホスト プロセスをカスタマイズできます。 | 
| **LocalHttpPort** | ローカルの Functions ホストの実行時に使用される既定のポートを設定します (`func host start`と`func run`)。 `--port` コマンド ライン オプションは、この値に優先します。 |
| **CORS** | [クロス オリジン リソース共有 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) で許可されるオリジンを定義します。 スペースなしのコンマ区切りのリストでオリジンを指定します。 ワイルドカード値 (**\***) がサポートされており、これによって任意のオリジンからの要求を許可できます。 |
| **ConnectionStrings** | 関数のデータベース接続文字列が含まれています。 このオブジェクト内の接続文字列は、**System.Data.SqlClient** のプロバイダーの種類と共に、環境に追加されます。  | 

トリガーとバインディングのほとんどには、環境変数またはアプリ設定の名前にマップされた **Connection** プロパティが含まれています。 各接続プロパティについては、local.settings.json ファイルに定義されたアプリ設定である必要があります。 

この設定は、コードの中で環境変数として読み込むこともできます。 C# の場合は、[System.Environment.GetEnvironmentVariable](https://msdn.microsoft.com/library/system.environment.getenvironmentvariable(v=vs.110).aspx) または[ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx) を使用します。 JavaScript では、`process.env` を使用します。 システム環境変数として指定されている設定は、local.settings.json ファイル内の値よりも優先されます。 

local.settings.json ファイル内の設定は、ローカルで実行されている Functions ツールでのみ使用されます。 既定では、プロジェクトが Azure に発行されても、これらの設定は自動的に移行されません。 [発行する際](#publish)に `--publish-local-settings` スイッチを使用して、これらの設定が Azure 内の関数アプリに追加されていることを確認してください。

有効なストレージ接続文字列が **AzureWebJobsStorage** に設定されていない場合は、次のエラー メッセージが表示されます。  

>local.settings.json に AzureWebJobsStorage の値がありません。 これは HTTP 以外のすべてのトリガーに必要です。 'func azure functionapp fetch-app-settings' <functionAppName> を実行するか、local.settings.json で接続文字列を指定することができます。
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>アプリケーションの設定の構成

接続文字列の値を設定するには、次のいずれかの操作を行います。
* [Azure Storage Explorer](http://storageexplorer.com/) で、接続文字列を入力します。
* 次のコマンドのいずれかを使用します。

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure functionapp storage fetch-connection-string <StorageAccountName>
    ```
    どちらのコマンドも、最初に Azure にサインインする必要があります。

## <a name="create-a-function"></a>関数を作成する

関数を作成するには、次のコマンドを実行します。

```
func new
``` 
`func new` では、次の省略可能な引数がサポートされています。

| 引数     | Description                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | テンプレート プログラミング言語。C#、F#、JavaScript など。 |
| **`--template -t`** | テンプレート名。 |
| **`--name -n`** | 関数名。 |

たとえば、JavaScript HTTP トリガーを作成するには、次を実行します。

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

キューによってトリガーされる関数を作成するには、次を実行します。

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```

## <a name="run-functions-locally"></a>関数をローカルで実行する

Functions プロジェクトを実行するには、Functions ホストを実行します。 ホストによって、プロジェクトのすべての関数に対するトリガーが有効になります。

```
func host start
```

`func host start` では、次のオプションがサポートされています。

| オプション     | Description                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | ローカル ポート。このポートでリッスンします。 既定値: 7071。 |
| **`--debug <type>`** | オプションは `VSCode` と `VS` です。 |
| **`--cors`** | CORS オリジンのコンマ区切りのリスト (スペースなし)。 |
| **`--nodeDebugPort -n`** | 使用するノード デバッガーのポート。 既定値: launch.json または 5858 の値。 |
| **`--debugLevel -d`** | コンソール トレース レベル (オフ、詳細、情報、警告、またはエラー)。 既定値: 情報。|
| **`--timeout -t`** | Functions ホスト開始のタイムアウト (秒単位)。 既定値: 20 秒。|
| **`--useHttps`** | https://localhost:{port} rather than to http://localhost:{port} にバインドします。 既定では、このオプションにより、信頼された証明書がコンピューターに作成されます。|
| **`--pause-on-error`** | プロセスを終了する前に、追加入力を一時停止します。 統合開発環境 (IDE) から Azure Functions Core Tools を起動する場合に役立ちます。|

Functions ホストの起動時、HTTP によってトリガーされる関数の URL が出力されます。

```
Found the following functions:
Host.Functions.MyHttpTrigger

ob host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>VS Code または Visual Studio でのデバッグ

デバッガーをアタッチするには、`--debug` 引数を渡します。 JavaScript 関数をデバッグするには、Visual Studio Code を使用します。 C# 関数については、Visual Studio を使用します。

C# 関数をデバッグするには、`--debug vs` を使用します。 または、[Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) を使用することもできます。 

ホストを起動して、JavaScript のデバッグを設定するには、次を実行します。

```
func host start --debug vscode
```

その後、Visual Studio Code の **[デバッグ]** ビューで、**[Attach to Azure Functions]\(Azure Functions にアタッチ\)** を選択します。 ブレークポイントをアタッチし、変数の調査をして、コードをステップ実行できます。

![Visual Studio Code での JavaScript デバッグ](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>関数へのテスト データの受け渡し

`func run <FunctionName>` を使用して関数を直接呼び出し、関数の入力データを渡すこともできます。 このコマンドは、Azure Portal の **[テスト]** タブを使用して関数を実行するのと似ています。 このコマンドにより、Functions ホスト全体が起動します。

`func run` では、次のオプションがサポートされています。

| オプション     | Description                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | インライン コンテンツ。 |
| **`--debug -d`** | 関数を実行する前に、デバッガーを、ホスト プロセスにアタッチします。|
| **`--timeout -t`** | Functions ホストの準備が完了するまでの待機時間 (秒単位)。|
| **`--file -f`** | コンテンツとして使用するファイル名。|
| **`--no-interactive`** | 入力を促しません。 自動シナリオで便利です。|

たとえば、HTTP によってトリガーされる関数を呼び出して、コンテンツ本文を渡すには、次のコマンドを実行します。

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Azure に発行する

Azure で Functions プロジェクトを関数アプリに発行するには、`publish` コマンドを使用します。

```
func azure functionapp publish <FunctionAppName>
```

以下のオプションを使用できます。

| オプション     | Description                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  local.settings.json の設定を Azure に発行し、設定が既に存在する場合は上書きを促します。|
| **`--overwrite-settings -y`** | `-i` で使用する必要があります。 値が異なる場合は、Azure の AppSettings をローカル値で上書きします。 既定値は prompt です。|

`publish` コマンドは、Functions プロジェクト ディレクトリのコンテンツをアップロードします。 ローカルでファイルを削除する場合、`publish` コマンドではファイルは Azure から削除されません。 Azure 内のファイルは、[Azure Portal] の [Kudu ツール](functions-how-to-use-azure-function-app-settings.md#kudu) を使用することで削除できます。

## <a name="next-steps"></a>次のステップ

Azure Functions Core Tools は[オープン ソースであり、GitHub でホストされています](https://github.com/azure/azure-functions-cli)。  
バグまたは機能要求を提出するには、[GitHub の問題をオープン](https://github.com/azure/azure-functions-cli/issues)してください。 

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 

