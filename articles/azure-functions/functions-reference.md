---
title: Azure Functions の開発に関するガイダンス | Microsoft Docs
description: プログラミング言語とバインドを問わず、Azure での関数開発に必要な Azure Functions の概念とテクニックについて説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: 開発者向けガイド, Azure Functions, 関数, イベント処理, webhook, 動的コンピューティング, サーバーレス アーキテクチャ
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 737da27fd8da292b6c1f8997d4bd300bbec8740e
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565526"
---
# <a name="azure-functions-developers-guide"></a>Azure Functions の開発者向けガイド
Azure Functions の特定の関数は、使用する言語またはバインドに関係なく、いくつかの中核となる技術的な概念とコンポーネントを共有します。 特定の言語またはバインドに固有の詳細を学習する前に、それらすべてに適用されるこの概要をお読みください。

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

`bindings` プロパティで、トリガーとバインドの両方を構成します。 各バインドは、いくつかの一般的な設定と、バインドの特定の種類に固有の設定を共有します。 すべてのバインドには次の設定が必要です。

| プロパティ | 値/型 | 説明 |
| --- | --- | --- |
| `type` |string |バインドの種類。 たとえば、「 `queueTrigger`」のように入力します。 |
| `direction` |"in"、"'out" |バインドが関数への受信データか、関数からの送信データかを示します。 |
| `name` |string |関数のバインドされたデータに使用される名前。 C# の場合は引数の名前です。JavaScript の場合はキー/値リストのキーです。 |

## <a name="function-app"></a>関数アプリ
関数アプリからは、関数が実行される、Azure における実行コンテキストが提供されます。 関数アプリは、まとめて管理、デプロイ、およびスケールされる 1 つまたは複数の個々の関数で構成されます。 関数アプリ内のすべての関数は、同じ料金プラン、継続的なデプロイ、およびランタイムのバージョンを共有します。 関数を整理し、まとめて管理する方法として関数アプリを考えてください。 

> [!NOTE]
> 関数アプリ内のすべての関数は、同じ言語で作成する必要があります。 Azure Functions ランタイムの[以前のバージョン](functions-versions.md)では、これは必須ではありませんでした。

## <a name="folder-structure"></a>フォルダー構造
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

上記は、関数アプリの既定の (そして推奨される) フォルダー構造です。 関数のコードのファイルの場所を変更するには、_function.json_ ファイルの `scriptFile` セクションを変更します。 また、[パッケージ デプロイ](deployment-zip-push.md)を使用して Azure の関数アプリにプロジェクトをデプロイすることもお勧めします。 [継続的インテグレーションとデプロイ](functions-continuous-deployment.md)や Azure DevOps など、既存のツールを使用することもできます。

> [!NOTE]
> パッケージを手動でデプロイする場合は、必ず _host.json_ ファイルと関数フォルダーを直接 `wwwroot` フォルダーにデプロイします。 デプロイに `wwwroot` フォルダーを含めないでください。 そうしないと、`wwwroot\wwwroot` フォルダーができてしまいます。

#### <a name="use-local-tools-and-publishing"></a>ローカル ツールの使用と公開
関数アプリは、[Visual Studio](./functions-develop-vs.md)、[Visual Studio Code](functions-create-first-function-vs-code.md)、[IntelliJ](./functions-create-maven-intellij.md)、[Eclipse](./functions-create-maven-eclipse.md)、[Azure Functions Core Tools](./functions-develop-local.md) など、さまざまなツールを利用して作成し、公開できます。 詳細については、「[Azure Functions をローカルでコーディングしてテストする](./functions-develop-local.md)」を参照してください。

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a> Azure portal で関数を編集する方法
Azure portal に組み込まれている関数エディターを使用すると、コードと *function.json* ファイルを直接インラインで更新できます。 これは、軽微な変更や概念実証の場合にのみお勧めします。ベスト プラクティスは、VS Code などのローカル開発ツールを使うことです。

## <a name="parallel-execution"></a>並列実行
シングル スレッドの関数ランタイムが処理できるより速く複数のトリガー イベントが発生する場合、ランタイムは関数を並列で複数回呼び出す場合があります。  関数アプリが[従量課金ホスティング プラン](functions-scale.md#how-the-consumption-and-premium-plans-work)を使用している場合、関数アプリは自動的にスケールアウトできます。  アプリが従量課金ホスティング プランと標準の [App Service ホスティング プラン](../app-service/overview-hosting-plans.md)のどちらで実行されていても、関数アプリの各インスタンスは、複数の同時関数呼び出しを、複数のスレッドを使用して並列に処理します。  各関数アプリ インスタンスでの同時関数呼び出しの最大数は、使用されるトリガーの種類と、関数アプリ内の他の関数によって使用されるリソースに応じて異なります。

## <a name="functions-runtime-versioning"></a>Functions ランタイムのバージョン管理

`FUNCTIONS_EXTENSION_VERSION` アプリ設定を使用して、Functions ランタイムのバージョンを構成できます。 たとえば、"~2" の値は、Function App がそのメジャー バージョンとして 2.x を使用することを示します。 Function App は、リリースされたときにそれぞれの新しいマイナー バージョンにアップグレードされます。 お使いの関数アプリの正確なバージョンを表示する方法など、詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](set-runtime-version.md)」をご覧ください。

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

## <a name="reporting-issues"></a>問題の報告
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>次の手順
詳細については、次のリソースを参照してください。

* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)
* [Azure Functions をローカルでコーディングしてテストする](./functions-develop-local.md)
* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-dotnet-class-library.md)
* [Azure Functions NodeJS 開発者向けリファレンス](functions-reference-node.md)
