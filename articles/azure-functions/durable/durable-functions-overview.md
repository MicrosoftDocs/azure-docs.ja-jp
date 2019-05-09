---
title: Durable Functions の概要 - Azure
description: Azure Functions の Durable Functions 拡張機能の概要です。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 12/22/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: 4c7b4733d05f18d3c30e45fd08c3cf9c50354ebc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816096"
---
# <a name="what-are-durable-functions"></a>Durable Functions とは

*Durable Functions* は、サーバーレス環境でステートフル関数を記述できる [Azure Functions](../functions-overview.md) の拡張機能です。 この拡張機能は状態、チェックポイント、再起動を管理します。

## <a name="benefits"></a>メリット

この拡張機能を使用すると、"[*オーケストレーター関数*](durable-functions-types-features-overview.md#orchestrator-functions)" を使用してステートフル ワークフローを定義できます。これには、次のメリットがあります。

* コードでワークフローを定義できます。 JSON スキーマまたはデザイナーが不要です。
* 他の関数を同期と非同期のどちらでも呼び出すことができます。 呼び出された関数からの出力は、ローカル変数に保存できます。
* 関数が待機するたびに、自動で進行状況にチェックポイントが設定されます。 プロセスがリサイクルされるときや VM が再起動されるときに、ローカルの状態が失われることがありません。

## <a name="application-patterns"></a>アプリケーション パターン

Durable Functions の主なユース ケースは、サーバーレス アプリケーションにおける複雑でステートフルな調整の要件をシンプルにすることです。 次に示すのは、Durable Functions を使用することでメリットがある、いくつかの典型的なアプリケーションのパターンです。

* [チェーン](durable-functions-concepts.md#chaining)
* [ファンアウト/ファンイン](durable-functions-concepts.md#fan-in-out)
* [非同期 HTTP API シリーズ](durable-functions-concepts.md#async-http)
* [監視](durable-functions-concepts.md#monitoring)
* [人による操作](durable-functions-concepts.md#human)

## <a name="language-support"></a>サポートされている言語

Durable Functions では、現在次の言語をサポートしています。

* **C#**: [プリコンパイル済みクラス ライブラリ](../functions-dotnet-class-library.md)と [C# スクリプト](../functions-reference-csharp.md)の両方。
* **F#**: プリコンパイル済みクラス ライブラリと F# スクリプト。 F# スクリプトは、Azure Functions ランタイムのバージョン 1.x でのみサポートされています。
* **JavaScript**: Azure Functions ランタイムのバージョン 2.x でのみサポートされています。 Durable Functions 拡張機能のバージョン 1.7.0 以降が必要です。 

Durable Functions では、すべての [Azure Functions 言語](../supported-languages.md)をサポートすることを目標としています。 追加言語をサポートするための最新の作業状況については、[Durable Functions の問題の一覧](https://github.com/Azure/azure-functions-durable-extension/issues)を参照してください。

Azure Functions と同様に、[Visual Studio 2017](durable-functions-create-first-csharp.md)、[Visual Studio Code](quickstart-js-vscode.md)、および [Azure portal](durable-functions-create-portal.md) を使用して永続関数を開発するのに役立つテンプレートがあります。

## <a name="billing"></a>課金

Durable Functions は Azure Functions と同じように課金されます。 詳細については、[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)に関するページを参照してください。

## <a name="jump-right-in"></a>すぐに始める

次の言語固有のクイック スタート チュートリアルのいずれかを完了することで、10 分足らずで Durable Functions を使い始めることができます。

* [C# と Visual Studio 2017 を使用する場合](durable-functions-create-first-csharp.md)
* [Visual Studio Code と JavaScript を使用する場合](quickstart-js-vscode.md)

どちらのクイック スタートでも、"hello world" という永続関数をローカルで作成してテストします。 その後、関数コードを Azure に発行します。 作成した関数は、他の関数の呼び出しを調整し、連結します。

## <a name="learn-more"></a>詳細情報

次のビデオでは、Durable Functions の利点を取り上げています。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Durable Functions は [Azure Functions](../functions-overview.md)の高度な拡張機能であるため、すべてのアプリケーションに適しているわけではありません。 Durable Functions の詳細については、「[Durable Functions patterns and technical concepts (Durable Functions のパターンおよび技術的概念)](durable-functions-concepts.md)」を参照してください。 他の Azure オーケストレーション テクノロジとの比較については、「[Azure Functions と Azure Logic Apps の比較](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps)」を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Durable Functions のパターンおよび技術的概念](durable-functions-concepts.md)
