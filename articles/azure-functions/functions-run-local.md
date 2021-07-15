---
title: Azure Functions Core Tools の操作
description: Azure 関数を Azure Functions で実行する前に、ローカル コンピューターのコマンド プロンプトまたはターミナルでコーディングしてテストする方法について説明します。
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: 3ddd84f2f73546b42a3925802b3357df16485488
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100521443"
---
# <a name="work-with-azure-functions-core-tools"></a>Azure Functions Core Tools の操作

Azure Functions Core Tools を使用すると、ローカル コンピューター上のコマンド プロンプトまたはターミナルから関数を開発およびテストできます。 ローカル関数はライブ Azure サービスに接続できるため、完全な Functions ランタイムを使用してローカル コンピューター上で関数をデバッグすることができます。 Azure サブスクリプションに関数アプリをデプロイすることもできます。

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Core Tools を使用して、ローカル コンピューターで関数を開発し、Azure に発行する基本的な手順は次の通りです。

> [!div class="checklist"]
> * [Core Tools と依存関係をインストールします。](#v2)
> * [言語固有のテンプレートから関数アプリ プロジェクトを作成します。](#create-a-local-functions-project)
> * [トリガーとバインド拡張を登録します。](#register-extensions)
> * [ストレージとその他の接続を定義します。](#local-settings-file)
> * [トリガーおよび言語固有のテンプレートから関数を作成します。](#create-func)
> * [関数をローカルで実行します。](#start)
> * [Azure にプロジェクトを発行します。](#publish)

## <a name="core-tools-versions"></a>Core Tools のバージョン

Azure Functions Core Tools には、3 つのバージョンがあります。 使用するバージョンは、ローカル開発環境、[選択した言語](supported-languages.md)、および必要なサポートのレベルによって異なります。

+ [**バージョン 3.x および 2.x**](#v2):[Azure Functions ランタイムのバージョン 3.x または 2.x](functions-versions.md) をサポートします。 これらのバージョンでは [Windows](?tabs=windows#v2)、[macOS](?tabs=macos#v2)、および [Linux](?tabs=linux#v2) がサポートされ、インストールにはプラットフォーム固有のパッケージ マネージャーまたは npm を使用します。

+ **バージョン 1.x**: Azure Functions ランタイムのバージョン 1.x をサポートします。 ツールのこのバージョンは Windows コンピューター上でのみサポートされ、[npm パッケージ](https://www.npmjs.com/package/azure-functions-core-tools)からインストールされます。

特定のコンピューターには、1 つのバージョンの Core Tools のみをインストールできます。 特に記載がない限り、この記事の例ではバージョン 3.x を対象にしています。

## <a name="prerequisites"></a>前提条件

現在、Azure Functions Core Tools では、[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/install-az-ps) に依存して Azure アカウントでの認証を行っています。 つまり、Azure Functions Core Tools から [Azure に発行](#publish)できるようにするには、このいずれかのツールをインストールする必要があります。 

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions Core Tools のインストール

[Azure Functions Core Tools] には、Azure Functions ランタイムを実行するのと同じランタイムのバージョンが含まれており、ローカルの開発コンピューターで実行できます。 また、関数の作成、Azure への接続、関数プロジェクトのデプロイを行うコマンドも用意されています。

### <a name="version-3x-and-2x"></a><a name="v2"></a>バージョン 3.x および 2.x

バージョン 3.x および 2.x のツールは、.NET Core 上に構築されている Azure Functions ランタイムを使用します。 このバージョンは、[Windows](?tabs=windows#v2)、[macOS](?tabs=macos#v2)、[Linux](?tabs=linux#v2) など、.NET Core が対応しているすべてのプラットフォームでサポートされています。 

> [!IMPORTANT]
> [拡張バンドル]を使用すると、.NET Core SDK をインストールするための要件をバイパスできます。

# <a name="windows"></a>[Windows](#tab/windows)

次の手順では、Windows インストーラー (MSI) を使用して Core Tools v3.x をインストールします。 Core Tools v2.x のインストールに必要な、その他のパッケージベースのインストーラーの詳細については、[Core Tools の Readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows) をご覧ください。

1. Windows のバージョンに応じて、 以下の Core Tools インストーラーをダウンロードして実行します。

    - [v3.x - Windows 64 ビット](https://go.microsoft.com/fwlink/?linkid=2135274) (推奨。 [Visual Studio Code のデバッグ](functions-develop-vs-code.md#debugging-functions-locally) には 64 ビットが必要です)
    - [v3.x - Windows 32 ビット](https://go.microsoft.com/fwlink/?linkid=2135275)

1. [拡張バンドル](functions-bindings-register.md#extension-bundles)を使用しない場合、[.NET Core 3.x SDK for Windows](https://dotnet.microsoft.com/download) をインストールします。

# <a name="macos"></a>[macOS](#tab/macos)

次の手順では、Homebrew を使用して macOS 上に Core Tools をインストールします。

1. まだインストールしていない場合は、[Homebrew](https://brew.sh/) をインストールします。

1. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ##### <a name="v3x-recommended"></a>v3.x (推奨)

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```
    
    ##### <a name="v2x"></a>v2.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```
    
1. [拡張バンドル](functions-bindings-register.md#extension-bundles)を使用しない場合、[.NET Core 3.x SDK for macOS](https://dotnet.microsoft.com/download) をインストールします。

# <a name="linux"></a>[Linux](#tab/linux)

次の手順では [APT ](https://wiki.debian.org/Apt)を使用して、Ubuntu/Debian Linux ディストリビューションに Core Tools をインストールします。 他の Linux ディストリビューションについては、[Core Tools の readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux) に関するページを参照してください。

1. パッケージの整合性を検証するには、Microsoft パッケージ リポジトリの GPG キーをインストールします。

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. APT 更新を行う前に、APT ソース リストを設定します。

    ##### <a name="ubuntu"></a>Ubuntu

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

    ##### <a name="debian"></a>Debian

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. `/etc/apt/sources.list.d/dotnetdev.list` ファイルを調べて、次に示す適切な Linux バージョン文字列のいずれかがあることを確認します。

    | Linux ディストリビューション | Version |
    | --------------- | ----------- |
    | Debian 10 | `buster`  |
    | Debian 9  | `stretch` |
    | Ubuntu 20.04    | `focal`     |
    | Ubuntu 19.04    | `disco`     |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. APT ソースの更新を開始します。

    ```bash
    sudo apt-get update
    ```

1. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ##### <a name="v3x-recommended"></a>v3.x (推奨)
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```
    
    ##### <a name="v2x"></a>v2.x
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

1. [拡張バンドル](functions-bindings-register.md#extension-bundles)を使用しない場合、[.NET Core 3.x SDK for Linux](https://dotnet.microsoft.com/download) をインストールします。

---

## <a name="create-a-local-functions-project"></a>ローカル関数プロジェクトを作成する

Functions プロジェクト ディレクトリには、[host.json](functions-host-json.md) および [local.settings.json](#local-settings-file) ファイル、および個々の関数のコードを含むサブフォルダーが含まれています。 このディレクトリは、Azure の関数アプリに相当します。 Functions のフォルダー構造の詳細については、[Azure Functions の開発者向けガイド](functions-reference.md#folder-structure)を参照してください。

バージョン 3.x/2.x では、プロジェクトの初期化時に既定の言語を選択する必要があります。 バージョン 3.x/2.x では、追加されたすべての関数に既定の言語テンプレートが使用されます。 バージョン 1.x では、関数を作成するたびに言語を指定します。

ターミナル ウィンドウまたはコマンド プロンプトで、次のコマンドを実行してプロジェクトおよびローカルの Git リポジトリを作成します。

```
func init MyFunctionProj
```

>[!IMPORTANT]
> Java では、HTTP でトリガーされる最初の関数と共に、Maven アーキタイプを使用してローカル関数プロジェクトを作成します。 次のコマンドを使用して、Java プロジェクト `mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype` を作成します。 Maven アーキタイプの使用例については、[コマンド ラインのクイックスタート](./create-first-function-cli-java.md)に関する記事を参照してください。  

プロジェクト名を指定すると、その名前の新しいフォルダーの作成と初期化が実行されます。 それ以外の場合は、現在のフォルダーが初期化されます。  
バージョン 3.x/2.x では、コマンドを実行するときにプロジェクトのランタイムを選択する必要があります。 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

上/下方向キーを使用して言語を選択し、Enter キーを押します。 JavaScript または TypeScript 関数の開発を計画している場合は、**node** を選択し、言語を選択します。 TypeScript には[いくつかの追加要件](functions-reference-node.md#typescript)があります。 

出力は、次の JavaScript プロジェクトの例のようになります。

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init` では、次のオプションがサポートされています。特に注意書きがない限り、バージョン 3.x/2.x だけが対象です。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--csx`** | .NET 関数を C# スクリプトとして作成します。これはバージョン 1.x の動作です。 `--worker-runtime dotnet` でのみ有効です。 |
| **`--docker`** | 選択した `--worker-runtime` に基づく基本イメージを使用して、コンテナーの Dockerfile を作成します。 カスタム Linux コンテナーに発行する場合は、このオプションを使用します。 |
| **`--docker-only`** |  既存のプロジェクトに Dockerfile を追加します。 local.settings.json で設定されていないか指定されていない場合、worker ランタイムのプロンプトを表示します。 既存のプロジェクトをカスタム Linux コンテナーに発行する場合は、このオプションを使用します。 |
| **`--force`** | プロジェクトに既存のファイルがある場合でも、プロジェクトを初期化します。 この設定は、同じ名前の既存のファイルを上書きします。 プロジェクト フォルダー内の他のファイルには影響ありません。 |
| **`--language`** | 言語固有のプロジェクトを初期化します。 現時点では、`--worker-runtime` が `node` に設定されている場合にサポートされます。 オプションは `typescript` と `javascript` です。 `--worker-runtime javascript` または `--worker-runtime typescript` を使用することもできます。  |
| **`--managed-dependencies`**  | マネージドの依存関係をインストールします。 現時点では、この機能は PowerShell worker ランタイムのみでサポートされています。 |
| **`--source-control`** | Git リポジトリを作成するかどうかを制御します。 既定では、リポジトリは作成されません。 `true` を指定すると、リポジトリが作成されます。 |
| **`--worker-runtime`** | プロジェクトの言語ランタイムを設定します。 サポートされる値は、`csharp`、`dotnet`、`javascript`、`node` (JavaScript)、`powershell`、`python`、`typescript` です。 Java の場合は、[Maven](functions-reference-java.md#create-java-functions) を使用します。設定しない場合は、初期化中にランタイムの選択を求められます。 |
|
> [!IMPORTANT]
> 既定では、Core Tools のバージョン 2.x 以降のバージョンでは、.NET ランタイムの関数アプリ プロジェクトは [C# クラス プロジェクト](functions-dotnet-class-library.md) (.csproj) として作成されます。 Visual Studio または Visual Studio Code で使用できるこれらの C# プロジェクトは、テスト中および Azure への発行時にコンパイルされます。 バージョン 1.x およびポータルで作成される同じ C# スクリプト (.csx) ファイルを代わりに作成および使用したい場合は、関数を作成して展開するときに、`--csx` パラメーターを指定する必要があります。

## <a name="register-extensions"></a>拡張機能を登録する

HTTP およびタイマートリガーを除き、ランタイム バージョン 2.x およびそれ以降の関数バインディングは、拡張パッケージとして実装されます。 HTTP バインドとタイマー トリガーには、拡張機能は必要ありません。 

さまざまな拡張機能パッケージ間の非互換性を減らすために、Functions では、host.json プロジェクト ファイル内で拡張機能バンドルを参照することが許可されます。 拡張機能バンドルを使用しないことを選択する場合でも、.NET Core 2.x SDK をローカルにインストールし、Functions プロジェクトと共に extensions.csproj を維持する必要があります。  

Azure Functions ランタイム バージョン 2.x では、関数で使用するバインディングの型に対応した拡張機能を明示的に登録する必要があります。 バインド拡張機能を個別にインストールするか、host.json プロジェクト ファイルに拡張機能のバンドルの参照を追加することができます。 拡張機能のバンドルは、複数のバインディングの種類を使用するときに、パッケージの互換性の問題を発生する可能性をなくします。 これはバインド拡張機能を登録するための推奨される方法です。 また、拡張機能のバンドルにより、.NET Core 2.x SDK をインストールする必要もなくなります。 

### <a name="use-extension-bundles"></a>拡張機能バンドルを使用する

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

詳細については、「[Azure Functions バインド拡張機能を登録する](functions-bindings-register.md#extension-bundles)」を参照してください。 function.json ファイルへのバインドを追加する前に、host.json に拡張機能のバンドルを追加する必要があります。

### <a name="explicitly-install-extensions"></a>拡張機能を明示的にインストールする

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

既定では、プロジェクトが Azure に発行されても、これらの設定は自動的に移行されません。 [発行する際](#publish)に `--publish-local-settings` スイッチを使用して、これらの設定が Azure 内の関数アプリに追加されていることを確認してください。 **ConnectionStrings** 内の値は発行されないことに注意してください。

関数アプリの設定値は、コードの中で環境変数として読み込むこともできます。 詳細については、以下の言語固有のリファレンス トピックの「環境変数」のセクションを参照してください。

* [C# プリコンパイル済み](functions-dotnet-class-library.md#environment-variables)
* [C# スクリプト (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

有効なストレージ接続文字列が [`AzureWebJobsStorage`] に設定されていなく、エミュレーターが使用されていない場合は、次のエラー メッセージが表示されます。

> local.settings.json に AzureWebJobsStorage の値がありません。 これは HTTP 以外のすべてのトリガーに必要です。 "func azure functionapp fetch-app-settings \<functionAppName\>" を実行するか、local.settings.json で接続文字列を指定することができます。

### <a name="get-your-storage-connection-strings"></a>ストレージ接続文字列の取得

開発のために Microsoft Azure ストレージ エミュレーターを使用している場合であっても、実際のストレージに接続してテストすることができます。 [ストレージ アカウントを作成済み](../storage/common/storage-account-create.md)である場合は、次の方法のいずれかで、有効なストレージ接続文字列を取得できます。

- [Azure portal] から、 **[ストレージ アカウント]** を検索して選択します。 
  ![Azure portal で [ストレージ アカウント] を選択する](./media/functions-run-local/select-storage-accounts.png)
  
  使用するストレージ アカウントを選択し、 **[設定]** の **[アクセス キー]** を選択してから、 **[接続文字列]** の値のいずれかをコピーします。
  ![Azure portal から接続文字列をコピーする](./media/functions-run-local/copy-storage-connection-portal.png)

- [Azure Storage Explorer](https://storageexplorer.com/) を使用して、Azure アカウントに接続します。 **Explorer** でサブスクリプションを展開し、 **[ストレージ アカウント]** を展開し、ストレージ アカウントを選択して、プライマリまたはセカンダリの接続文字列をコピーします。

  ![Storage Explorer から接続文字列をコピーする](./media/functions-run-local/storage-explorer.png)

+ プロジェクトのルートから Core Tools を使用して、次のいずれかのコマンドで Azure から接続文字列をダウンロードします。

  + 既存の関数アプリからすべての設定をダウンロードします。

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```

  + 特定のストレージ アカウントの接続文字列を取得します。

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Azure にまだサインインしていない場合は、それを求めるメッセージが表示されます。 これらのコマンドは、local.settings.json ファイル内のすべての既存の設定を上書きします。 

## <a name="create-a-function"></a><a name="create-func"></a>関数を作成する

関数を作成するには、次のコマンドを実行します。

```
func new
```

バージョン 3.x/2.x では、`func new` を実行したときに関数アプリの既定の言語のテンプレートを選択するように求められ、次に関数の名前を選択するように求められます。 バージョン 1.x では、さらに言語を選択するように求められます。

<pre>
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
</pre>

次のキュー トリガーの出力に示すように、指定した関数名のサブフォルダーに関数のコードが生成されます。

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

次の引数を使用して、コマンドでこれらのオプションを指定することもできます。

| 引数     | 説明                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (バージョン 2.x 以降のバージョン。)バージョン 1.x およびポータルで使用されるものと同じ C# スクリプト (.csx) テンプレートを生成します。 |
| **`--language`**, **`-l`**| テンプレート プログラミング言語。C#、F#、JavaScript など。 このオプションは、バージョン 1.x で必須です。 バージョン 2.x 以降のバージョンでは、このオプションを使用しないか、または worker ランタイムと一致する言語を選択してください。 |
| **`--name`**, **`-n`** | 関数名。 |
| **`--template`**, **`-t`** | サポートされている各言語で使用可能なテンプレートの完全な一覧を表示するには、`func templates list` コマンドを使います。   |


たとえば、JavaScript HTTP トリガーを 1 つのコマンドで作成するには、次を実行します。

```
func new --template "Http Trigger" --name MyHttpTrigger
```

キューによってトリガーされる関数を 1 つのコマンドで作成するには、次を実行します。

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>関数をローカルで実行する

Functions プロジェクトを実行するには、Functions ホストを実行します。 ホストによって、プロジェクトのすべての関数に対するトリガーが有効になります。 起動コマンドは、使用するプロジェクトの言語によって異なります。

# <a name="c"></a>[C\#](#tab/csharp)

```
func start --build
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
このコマンドは[仮想環境で実行する](./create-first-function-cli-python.md)必要があります。

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Functions ランタイムのバージョン 1.x では、次の例のように `host` コマンドが必要です。
>
> ```
> func host start
> ```

`func start` では、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | 実行前に現在のプロジェクトをビルドしません。 dotnet プロジェクトの場合のみ。 既定値は false に設定されます。 バージョン 1.x はサポートされません。 |
| **`--cors-credentials`** | クロスオリジン認証済み要求 (つまり、Cookie と Authentication ヘッダー) バージョン 1.x はサポートされません。 |
| **`--cors`** | CORS オリジンのコンマ区切りのリスト (スペースなし)。 |
| **`--language-worker`** | 言語ワーカーを構成するための引数。 たとえば、[デバッグ ポートとその他の必要な引数](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers)を指定して、言語ワーカーのデバッグを有効にすることができます。 バージョン 1.x はサポートされません。 |
| **`--cert`** | 秘密キーが含まれる .pfx ファイルへのパス。 `--useHttps` でのみ使用されます。 バージョン 1.x はサポートされません。 |
| **`--password`** | .pfx ファイルのパスワードまたはパスワードが格納されているファイルのいずれか。 `--cert` でのみ使用されます。 バージョン 1.x はサポートされません。 |
| **`--port`**, **`-p`** | ローカル ポート。このポートでリッスンします。 既定値:7071。 |
| **`--pause-on-error`** | プロセスを終了する前に、追加入力を一時停止します。 統合開発環境 (IDE) から Core Tools を起動した場合にのみ使用されます。|
| **`--script-root`**, **`--prefix`** | 実行または展開される関数アプリのルートへのパスを指定するために使用されます。 これは、サブフォルダーにプロジェクト ファイルを生成するコンパイル済みプロジェクトに使用されます。 たとえば、C# クラス ライブラリ プロジェクトをビルドすると、host.json、local.settings.json、および function.json ファイルが、`MyProject/bin/Debug/netstandard2.0` のようなパスの "*ルート*" サブフォルダーに生成されます。 この場合は、プレフィックスを `--script-root MyProject/bin/Debug/netstandard2.0` と設定します。 これは、Azure で実行する場合の関数アプリのルートです。 |
| **`--timeout`**, **`-t`** | Functions ホスト開始のタイムアウト (秒単位)。 既定値は20 秒。|
| **`--useHttps`** | `http://localhost:{port}` ではなく `https://localhost:{port}` にバインドします。 既定では、このオプションにより、信頼された証明書がコンピューターに作成されます。|

Functions ホストの起動時、HTTP によってトリガーされる関数の URL が出力されます。

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>ローカルで実行される場合、HTTP エンドポイントに対して承認は適用されません。 つまり、ローカルの HTTP 要求はすべて、`authLevel = "anonymous"` として処理されます。 詳細については、[HTTP バインディング](functions-bindings-http-webhook-trigger.md#authorization-keys)に関する記事を参照してください。

### <a name="passing-test-data-to-a-function"></a>関数へのテスト データの受け渡し

関数をローカルでテストするには、[Functions ホストを起動](#start)し、HTTP 要求を使用してローカル サーバーでエンドポイントを呼び出します。 呼び出すエンドポイントは、関数の種類によって異なります。

>[!NOTE]
> このトピックの例では、cURL ツールを使用してターミナルまたはコマンド プロンプトから HTTP 要求を送信します。 お好みのツールを使用して HTTP 要求をローカル サーバーに送信できます。 Linux ベースのシステムと Windows 10 ビルド 17063 以降では既定で cURL ツールを使用できます。 以前の Windows では、最初に [cURL ツール](https://curl.haxx.se/)をダウンロードし、インストールする必要があります。

関数のテストの全般的な情報については、「[Azure Functions のコードをテストするための戦略](functions-test-a-function.md)」を参照してください。

#### <a name="http-and-webhook-triggered-functions"></a>HTTP と webhook でトリガーされる関数

次のエンドポイントを呼び出して、HTTP と webhook でトリガーされる関数をローカルで実行できます。

```
http://localhost:{port}/api/{function_name}
```

Functions ホストがリッスンしているのと同じサーバー名とポートを使用していることを確認してください。 これは、Functions ホストの起動時に生成される出力で確認できます。 トリガーでサポートされている任意の HTTP メソッドを使用して、この URL を呼び出すことができます。

次の cURL コマンドは、`MyHttpTrigger` クイックスタート関数を、クエリ文字列で渡された _name_ パラメーターを使用して、GET 要求からトリガーします。

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

次の例は、要求本文で _name_ を渡す POST 要求から呼び出される同じ関数です。

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

ブラウザーから GET 要求を行ってクエリ文字列でデータを渡すことが可能です。 その他すべての HTTP メソッドについては、cURL、Fiddler、Postman、または類似の HTTP テスト ツールを使用する必要があります。

#### <a name="non-http-triggered-functions"></a>HTTP でトリガーされない関数

HTTP トリガー、Webhook トリガー、Event Grid トリガーを除く、あらゆる種類の関数の場合、管理エンドポイントを呼び出すことによって、関数をローカルでテストできます。 HTTP POST 要求を使ってローカル サーバーでこのエンドポイントを呼び出すと、関数がトリガーされます。 

Event Grid でトリガーされた関数をローカルでテストする方法については、「[ビューアー Web アプリでのローカル テスト](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app)」を参照してください。

必要に応じて、POST 要求の本体でテスト データを実行に渡すことができます。 この機能は、Azure Portal の **[テスト]** タブに似ています。

次の管理者エンドポイントを呼び出して、非 HTTP 関数をトリガーします。

```
http://localhost:{port}/admin/functions/{function_name}
```

テスト データを関数の管理者エンドポイントに渡すには、そのデータを POST 要求メッセージの本文で提供する必要があります。 メッセージ本文は、次の JSON 形式にする必要があります。

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>` 値には、関数が必要とする形式でデータが含まれています。 次の cURL の例は、`QueueTriggerJS` 関数に対する POST です。 この場合、入力は、キューにあることが期待されるメッセージに相当する文字列です。

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>`func run` コマンドの使用 (バージョン 1.x のみ)

>[!IMPORTANT]
> `func run` コマンドは、ツールのバージョン 1.x でのみサポートされます。 詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](set-runtime-version.md)」を参照してください。

バージョン 1.x では、`func run <FunctionName>` を使用して関数を直接呼び出し、関数の入力データを渡すこともできます。 このコマンドは、Azure Portal の **[テスト]** タブを使用して関数を実行するのと似ています。

`func run` では、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | インライン コンテンツ。 |
| **`--debug`**, **`-d`** | 関数を実行する前に、デバッガーを、ホスト プロセスにアタッチします。|
| **`--timeout`**, **`-t`** | Functions ホストの準備が完了するまでの待機時間 (秒単位)。|
| **`--file`**, **`-f`** | コンテンツとして使用するファイル名。|
| **`--no-interactive`** | 入力を促しません。 自動シナリオで便利です。|

たとえば、HTTP によってトリガーされる関数を呼び出して、コンテンツ本文を渡すには、次のコマンドを実行します。

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Azure に発行する

Azure Functions Core Tools でサポートされている 2 種類のデプロイは、[Zip デプロイ](functions-deployment-technologies.md#zip-deploy)による関数アプリへの関数プロジェクト ファイルの直接的なデプロイと、[カスタム Docker コンテナーのデプロイ](functions-deployment-technologies.md#docker-container)です。 コードをデプロイする [Azure サブスクリプションで関数アプリを作成しておく](functions-cli-samples.md#create)必要があります。 コンパイルを必要とするプロジェクトは、バイナリをデプロイできるように、ビルドする必要があります。

>[!IMPORTANT]
>Core Tools から Azure に発行できるようにするには、[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/install-az-ps) がローカルにインストールされている必要があります。  

プロジェクト フォルダーには、発行するべきではない言語固有のファイルやディレクトリが含まれている場合があります。 除外された項目は、ルート プロジェクト フォルダーの .funcignore ファイルにリストされます。     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>プロジェクト ファイルのデプロイ

Azure で ローカル コードを関数アプリに発行するには、`publish` コマンドを使用します。

```
func azure functionapp publish <FunctionAppName>
```

>[!IMPORTANT]
> Java では、Maven を使用して、ローカル プロジェクトが Azure に発行されます。 コマンド `mvn azure-functions:deploy` を実行して、Azure に発行します。 Azure リソースは、初期デプロイ中に作成されます。

このコマンドにより、Azure の既存の関数アプリに公開されます。 サブスクリプションに存在しない `<FunctionAppName>` に発行しようとすると、エラーが表示されます。 Azure CLI または Azure PowerShell を使用してコマンド プロンプトまたはターミナル ウィンドウから関数アプリを作成する方法については、「[サーバーレス実行用の Function App を作成する](./scripts/functions-cli-create-serverless.md)」を参照してください。 既定では、このコマンドでは[リモート ビルド](functions-deployment-technologies.md#remote-build)が使用され、アプリがデプロイされて、[デプロイ パッケージから実行](run-functions-from-deployment-package.md)されます。 この推奨されるデプロイ モードを無効にするには、`--nozip` オプションを使用します。

>[!IMPORTANT]
> Azure portal で関数アプリを作成すると、既定でバージョン 3.x の Function ランタイムが使用されます。 関数アプリにバージョン 1.x のランタイムを使用させるには、[バージョン 1.x での実行](functions-versions.md#creating-1x-apps)に関するページの説明に従ってください。
> 既存の関数がある関数アプリのランタイム バージョンを変更することはできません。

次の発行オプションは、次のすべてのバージョンに適用されます。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  local.settings.json の設定を Azure に発行し、設定が既に存在する場合は上書きを促します。 Microsoft Azure ストレージ エミュレーターを使用している場合は、まずアプリ設定を[実際のストレージ接続](#get-your-storage-connection-strings)に変更します。 |
| **`--overwrite-settings -y`** | `--publish-local-settings -i` を使用するときに、アプリの設定を上書きするプロンプトを抑制します。|

次の発行オプションは、バージョン 2.x 以降のバージョンでのみサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  設定のみを発行し、コンテンツをスキップします。 既定値は prompt です。 |
|**`--list-ignored-files`** | 発行時に無視されるファイルの一覧を表示します。これは、.funcignore ファイルに基づきます。 |
| **`--list-included-files`** | 発行されるファイルの一覧を表示します。これは、.funcignore ファイルに基づきます。 |
| **`--nozip`** | 既定の `Run-From-Package` モードをオフにします。 |
| **`--build-native-deps`** | Python 関数アプリを発行するときに、.wheels フォルダーの生成をスキップします。 |
| **`--build`**, **`-b`** | Linux 関数アプリにデプロイするときにビルド アクションを実行します。 `remote` および `local` を受け入れます。 |
| **`--additional-packages`** | ネイティブの依存関係を構築するときにインストールするパッケージの一覧 (例: `python3-dev libevent-dev`)。 |
| **`--force`** | 特定のシナリオで発行前の検証を無視します。 |
| **`--csx`** | C# スクリプト (.csx) プロジェクトを発行します。 |
| **`--no-build`** | プロジェクトは発行中にはビルドされません。 Python の場合、`pip install` は実行されません。 |
| **`--dotnet-cli-params`** | コンパイル済み C# (.csproj) 関数を発行するとき、Core Tools は "dotnet build --output bin/publish" を呼び出します。 これに渡されるすべてのパラメーターは、コマンド ラインに追加されます。 |

### <a name="deploy-custom-container"></a>カスタム コンテナーのデプロイ

Azure Functions を使用すると、[カスタム Docker コンテナー](functions-deployment-technologies.md#docker-container)に関数プロジェクトをデプロイできます。 詳しくは、「[カスタム イメージを使用して Linux で関数を作成する](functions-create-function-linux-custom-image.md)」をご覧ください。 カスタム コンテナーには、Dockerfile が必要です。 Dockerfile でアプリを作成するには、`func init` で --dockerfile オプションを使用します。

```
func deploy
```

次のカスタム コンテナー デプロイ オプションを使用できます。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--registry`** | 現在のユーザーがサインインしている Docker レジストリの名前。 |
| **`--platform`** | 関数アプリのホスティング プラットフォーム。 有効なオプションは `kubernetes` です。 |
| **`--name`** | 関数アプリの名前。 |
| **`--max`**  | 必要に応じて、デプロイする関数アプリ インスタンスの最大数を設定します。 |
| **`--min`**  | 必要に応じて、デプロイする関数アプリ インスタンスの最小数を設定します。 |
| **`--config`** | オプションのデプロイ構成ファイルを設定します。 |

## <a name="monitoring-functions"></a>関数の監視

関数の実行を監視するための推奨される方法は、Azure Application Insights との統合です。 また、ローカル コンピューターに実行ログをストリーミングすることもできます。 詳細については、「[Azure Functions を監視する](functions-monitoring.md)」を参照してください。

### <a name="application-insights-integration"></a>Application Insights の統合

Azure で関数アプリを作成するときに Application Insights の統合を有効にする必要があります。 何らかの理由で関数アプリが Application Insights インスタンスに接続されていない場合は、Azure portal でこの統合を簡単に行うことができます。 詳細については、「[Application Insights との統合を有効にする](configure-monitoring.md#enable-application-insights-integration)」を参照してください。

### <a name="enable-streaming-logs"></a>ストリーミング ログを有効にする

関数によって生成されているログ ファイルのストリームは、ローカル コンピューター上のコマンド ライン セッションで表示できます。 

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

この種類のストリーミング ログを使用するには、関数アプリの Application Insights との統合を有効にする必要があります。   


## <a name="next-steps"></a>次のステップ

Azure Functions Core Tools [Microsoft 学習モジュール](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) を使用して Azure Functions を開発、テスト、および発行する方法について説明します。Azure Functions Core Tools は[オープンソースであり、GitHub ](https://github.com/azure/azure-functions-cli)でホストされています。  
バグまたは機能要求を提出するには、[GitHub の問題をオープン](https://github.com/azure/azure-functions-cli/issues)してください。

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
[拡張バンドル]: functions-bindings-register.md#extension-bundles
