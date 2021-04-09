---
title: Azure Functions の開発に関するガイダンス
description: プログラミング言語とバインドを問わず、Azure での関数開発に必要な Azure Functions の概念とテクニックについて説明します。
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 7030ca1c1950f7c06580ce7417a4429fbe330c4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614821"
---
# <a name="azure-functions-developer-guide"></a>Azure Functions 開発者ガイド
Azure Functions の特定の関数は、使用する言語またはバインドに関係なく、いくつかの中核となる技術的な概念とコンポーネントを共有します。 特定の言語またはバインド固有の詳細を学習する前に、それらすべてに当てはまるこの概要をお読みください。

この記事では、「[Azure Functions の概要](functions-overview.md)」を既に読んでいることを前提としています。

## <a name="function-code"></a>関数のコード
*関数* は Azure Functions の主要な概念です。 関数には 2 つの重要な要素が含まれています。さまざまな言語で記述できるコードと、いくつかの構成、つまり function.json ファイルです。 コンパイル式の言語の場合、この構成ファイルはコード内の注釈から自動的に生成されます。 スクリプト言語の場合は、構成ファイルを自分で用意する必要があります。

function.json ファイルには、関数のトリガー、バインド、その他の構成設定を定義します。 すべての関数には、1 つだけトリガーがあります。 ランタイムはこの構成ファイルを使用して、監視対象のイベントを特定し、関数の実行との間でデータを渡したりデータを受け取ったりする方法を判断します。 以下に function.json ファイルの例を示します。

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

詳しくは、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」をご覧ください。

`bindings` プロパティで、トリガーとバインドの両方を構成します。 各バインドは、いくつかの一般的な設定と、バインドの特定の種類に固有の設定を共有します。 すべてのバインドには次の設定が必要です。

| プロパティ    | 値 | Type | 説明|
|---|---|---|---|
| type  | バインドの名前。<br><br>たとえば、「 `queueTrigger` 」のように入力します。 | string | |
| 方向 | `in`, `out`  | string | バインドが関数への受信データか、関数からの送信データかを示します。 |
| name | 関数識別子。<br><br>たとえば、「 `myQueue` 」のように入力します。 | string | 関数のバインドされたデータに使用される名前。 C# の場合は引数の名前です。JavaScript の場合はキー/値リストのキーです。 |

## <a name="function-app"></a>関数アプリ
関数アプリからは、関数が実行される、Azure における実行コンテキストが提供されます。 そのため、これが関数のデプロイと管理の単位となります。 関数アプリは、まとめて管理、デプロイ、およびスケールされる 1 つまたは複数の個々の関数で構成されます。 関数アプリ内のすべての関数は、同じ料金プラン、デプロイ方法、およびランタイム バージョンを共有します。 関数を整理し、まとめて管理する方法として関数アプリを考えてください。 詳しくは、[関数アプリの管理方法](functions-how-to-use-azure-function-app-settings.md)に関する記事をご覧ください。 

> [!NOTE]
> 関数アプリ内のすべての関数は、同じ言語で作成する必要があります。 Azure Functions ランタイムの[以前のバージョン](functions-versions.md)では、これは必須ではありませんでした。

## <a name="folder-structure"></a>フォルダー構造
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

上記は、関数アプリの既定の (そして推奨される) フォルダー構造です。 関数のコードのファイルの場所を変更するには、_function.json_ ファイルの `scriptFile` セクションを変更します。 また、[パッケージ デプロイ](deployment-zip-push.md)を使用して Azure の関数アプリにプロジェクトをデプロイすることもお勧めします。 [継続的インテグレーションとデプロイ](functions-continuous-deployment.md)や Azure DevOps など、既存のツールを使用することもできます。

> [!NOTE]
> パッケージを手動でデプロイする場合は、必ず _host.json_ ファイルと関数フォルダーを直接 `wwwroot` フォルダーにデプロイします。 デプロイに `wwwroot` フォルダーを含めないでください。 そうしないと、`wwwroot\wwwroot` フォルダーができてしまいます。

#### <a name="use-local-tools-and-publishing"></a>ローカル ツールの使用と公開
関数アプリは、[Visual Studio](./functions-develop-vs.md)、[Visual Studio Code](./create-first-function-vs-code-csharp.md)、[IntelliJ](./functions-create-maven-intellij.md)、[Eclipse](./functions-create-maven-eclipse.md)、[Azure Functions Core Tools](./functions-develop-local.md) など、さまざまなツールを利用して作成し、公開できます。 詳細については、「[Azure Functions をローカルでコーディングしてテストする](./functions-develop-local.md)」を参照してください。

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Azure portal で関数を編集する方法
Azure portal に組み込まれている関数エディターを使用すると、コードと *function.json* ファイルを直接インラインで更新できます。 これは、軽微な変更や概念実証の場合にのみお勧めします。ベスト プラクティスは、VS Code などのローカル開発ツールを使うことです。

## <a name="parallel-execution"></a>並列実行
シングル スレッドの関数ランタイムが処理できるより速く複数のトリガー イベントが発生する場合、ランタイムは関数を並列で複数回呼び出す場合があります。  関数アプリが[従量課金ホスティング プラン](event-driven-scaling.md)を使用している場合、関数アプリは自動的にスケールアウトできます。  アプリが従量課金ホスティング プランと標準の [App Service ホスティング プラン](../app-service/overview-hosting-plans.md)のどちらで実行されていても、関数アプリの各インスタンスは、複数の同時関数呼び出しを、複数のスレッドを使用して並列に処理します。  各関数アプリ インスタンスでの同時関数呼び出しの最大数は、使用されるトリガーの種類と、関数アプリ内の他の関数によって使用されるリソースに応じて異なります。

## <a name="functions-runtime-versioning"></a>Functions ランタイムのバージョン管理

`FUNCTIONS_EXTENSION_VERSION` アプリ設定を使用して、Functions ランタイムのバージョンを構成できます。 たとえば、"~3" の値は、関数アプリがそのメジャー バージョンとして 3.x を使用することを示します。 関数アプリは、リリースされたときにそれぞれの新しいマイナー バージョンにアップグレードされます。 お使いの関数アプリの正確なバージョンを表示する方法など、詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](set-runtime-version.md)」をご覧ください。

## <a name="repositories"></a>リポジトリ
Azure Functions のコードはオープン ソースであり、GitHub リポジトリに保存されています。

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions のホスト](https://github.com/Azure/azure-functions-host/)
* [Azure Functions ポータル](https://github.com/azure/azure-functions-ux)
* [Azure Functions テンプレート](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK 拡張機能](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>バインド
サポートされるすべてのバインドを次の表に示します。

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

バインドが原因のエラーが発生している場合は、 [Azure Functions のバインド エラー コード](functions-bindings-error-pages.md)に関するドキュメントを参照してください。


## <a name="connections"></a>接続

関数プロジェクトでは、接続情報を構成プロバイダーからの名前で参照しています。 接続の詳細を直接受け入れないため、環境間で変更できます。 たとえば、トリガー定義に `connection` プロパティが含まれるとします。 これは接続文字列が参照されている場合がありますが、接続文字列を `function.json` に直接設定することはできません。 代わりに、`connection` を、接続文字列を含む環境変数の名前に設定します。

既定の構成プロバイダーでは環境変数を使用します。 これらは、Azure Functions サービスで実行している場合は [[アプリケーションの設定]](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings)、ローカルでの開発時には[ローカル設定ファイル](functions-run-local.md#local-settings-file)で設定できます。

### <a name="connection-values"></a>接続値

接続名が 1 つの正確な値に解決されると、ランタイムでは、値を _接続文字列_ として識別します。これには通常、シークレットが含まれます。 接続文字列の詳細は、接続先のサービスによって定義されます。

ただし、接続名では複数の構成アイテムのコレクションを参照することもできます。 2 つのアンダースコア `__` で終わる共有プレフィックスを使用して、環境変数をコレクションとして扱うことができます。 このプレフィックスに接続名を設定することによって、グループを参照できます。

たとえば、Azure Blob トリガー定義の `connection` プロパティが `Storage1` であるとします。 名前を `Storage1` として構成された 1 つの文字列値がない限り、`Storage1__serviceUri` は接続の `serviceUri` プロパティに使用されます。 接続のプロパティはサービスによって異なります。 接続を使用する拡張機能のドキュメントを参照してください。

### <a name="configure-an-identity-based-connection"></a>ID ベースの接続を構成する

Azure Functions の一部の接続は、シークレットの代わりに ID を使用するように構成されています。 サポートは、接続を使用する拡張機能によって異なります。 場合によっては、接続先のサービスで ID ベースの接続がサポートされている場合でも、Functions で接続文字列が必要になることがあります。

> [!IMPORTANT]
> バインド拡張機能が ID ベースの接続をサポートしている場合でも、その構成は従量課金プランではまだサポートされていない可能性があります。 以下のサポート表を参照してください。

ID ベースの接続は、次のトリガーおよびバインド拡張機能でサポートされています。

| 拡張機能の名前 | 拡張機能のバージョン                                                                                     | 従量課金プランでサポート |
|----------------|-------------------------------------------------------------------------------------------------------|---------------------------------------|
| Azure BLOB     | [バージョン 5.0.0-beta1 以降](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)  | いいえ                                    |
| Azure Queue    | [バージョン 5.0.0-beta1 以降](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) | いいえ                                    |
| Azure Event Hubs    | [バージョン 5.0.0-beta1 以降](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher) | いいえ                                    |

> [!NOTE]
> 主な動作に対して Functions ランタイムによって使用されるストレージ接続では、ID ベースの接続のサポートはまだ利用できません。 これは、`AzureWebJobsStorage` 設定が接続文字列である必要があることを意味します。

#### <a name="connection-properties"></a>接続のプロパティ

Azure サービスに対する ID ベースの接続では、次のプロパティを使用できます。

| プロパティ    | 拡張機能に必要 | 環境変数 | 説明 |
|---|---|---|---|
| サービス URI | Azure Blob、Azure キュー | `<CONNECTION_NAME_PREFIX>__serviceUri` |  接続先サービスのデータ プレーン URI。 |
| 完全修飾名前空間 | Event Hubs | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | 完全修飾イベント ハブの名前空間。 |

特定の接続の種類に対して、追加のオプションがサポートされている場合があります。 接続を確立するコンポーネントのドキュメントを参照してください。

Azure Functions サービスでホストされている場合、ID ベースの接続では、[マネージド ID](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json) が使用されます。 既定では、システム割り当て ID が使用されます。 ローカル開発などの他のコンテキストで実行する場合は、代わりに開発者 ID が使用されます。ただし、代替接続パラメーターを使用してカスタマイズすることもできます。

##### <a name="local-development"></a>ローカル開発

ローカルで実行している場合、上記の構成によって、ローカルの開発者 ID を使用するようにランタイムに指示します。 接続では次の場所から順番にトークンを取得しようとします。

- Microsoft アプリケーション間で共有されるローカル キャッシュ
- Visual Studio の現在のユーザー コンテキスト
- Visual Studio Code の現在のユーザー コンテキスト
- Azure CLI の現在のユーザー コンテキスト

これらのオプションのいずれも成功しなかった場合は、エラーが発生します。

場合によっては、別の ID の使用を指定したい場合があります。 代替 ID を指す接続の構成プロパティを追加できます。

> [!NOTE]
> Azure Functions サービスでホストされている場合、次の構成オプションはサポートされません。

クライアント ID とシークレットを指定して Azure Active Directory サービス プリンシパルを使用して接続するには、上記の[接続プロパティ](#connection-properties)に加えて、次の必須プロパティを使用して接続を定義します。

| プロパティ    | 環境変数 | 説明 |
|---|---|---|
| テナント ID | `<CONNECTION_NAME_PREFIX>__tenantId` | Azure Active Directory のテナント (ディレクトリ) ID。 |
| クライアント ID | `<CONNECTION_NAME_PREFIX>__clientId` |  テナント内のアプリの登録のクライアント (アプリケーション) ID。 |
| クライアント シークレット | `<CONNECTION_NAME_PREFIX>__clientSecret` | アプリの登録で生成されたクライアント シークレット。 |

Azure Blob との ID ベースの接続に必要な `local.settings.json` プロパティの例を次に示します。 
```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__serviceUri": "<serviceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="grant-permission-to-the-identity"></a>ID にアクセス許可を付与する

使用されている ID が何であれ、目的のアクションを実行するためのアクセス許可が必要です。 これは通常、接続先のサービスに応じて、Azure RBAC でロールを割り当てるか、アクセスポリシーで ID を指定することによって行われます。 必要なアクセス許可とその設定方法については、各サービスのドキュメントを参照してください。

> [!IMPORTANT]
> すべてのコンテキストに必要ではない一部のアクセス許可がサービスによって公開される場合があります。 可能であれば、**最小限の特権の原則** に従い、必要な特権だけを ID に付与します。 たとえば、アプリが BLOB からの読み取りのみを必要とする場合、[ストレージ BLOB データ所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)では読み取り操作に対して過剰なアクセス許可が含まれるため、[ストレージ BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)ロールを使用します。


## <a name="reporting-issues"></a>問題の報告
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>次のステップ
詳細については、次のリソースを参照してください。

* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)
* [Azure Functions をローカルでコーディングしてテストする](./functions-develop-local.md)
* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-dotnet-class-library.md)
* [Azure Functions Node.js 開発者向けリファレンス](functions-reference-node.md)
