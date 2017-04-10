---
title: "Azure Functions のベスト プラクティス | Microsoft Docs"
description: "Azure Functions のベスト プラクティスとパターンについて説明します。"
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, パターン, ベスト プラクティス, 関数, イベント処理, webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2fd12dd32ed3c8479c7460cbc0a1cac3330ff4f4
ms.openlocfilehash: 53dcaea155471d47eb61317c52d38524c05e4600
ms.lasthandoff: 03/01/2017


---

# <a name="tips-for-improving-the-performance-and-reliability-of-azure-functions"></a>Azure Functions のパフォーマンスと信頼性を向上させるためのヒント

##<a name="overview"></a>概要

この記事では、Function App を実装する際に考慮する必要がある一連のベスト プラクティスについて説明します。 Function App は Azure App Service のアプリの&1; つであることに注意してください。 したがって、App Service のベスト プラクティスも適用されます。


## <a name="avoid-large-long-running-functions"></a>サイズが大きく実行時間の長い関数を使用しない

サイズが大きく実行時間の長い関数では、予期しないタイムアウトの問題が発生する可能性があります。 関数は、Node.js 依存関係の多さからそのサイズが大きくなることがあります。 これらの依存関係をインポートすると、読み込み時間が長くなり、予期しないタイムアウトが発生する可能性があります。 Node.js 依存関係は、コード内の複数の `require()` ステートメントによって明示的に読み込むことができます。 依存関係は、独自の内部依存関係を持つコードによって読み込まれる単一のモジュールに基づいて暗黙的に指定することもできます。  

可能な限り、大きな関数は、連携して高速な応答を返す、より小さな関数セットにリファクタリングしてください。 たとえば、webhook または HTTP トリガー関数では、一定の時間内に確認応答が必要になる場合があります。 この HTTP トリガー ペイロードは、キュー トリガー関数によって処理されるキューに渡すことができます。 このアプローチを使用すると、実際の作業を遅らせて、即座に応答を返すことができます。 webhook は、通常、即座に応答を必要とします。


## <a name="cross-function-communication"></a>関数間の通信

複数の関数を統合する場合の一般的なベスト プラクティスは、関数間通信にストレージ キューを使用することです。  その主な理由は、ストレージ キューの方が安価であり、プロビジョニングがはるかに容易なためです。 

ストレージ キュー内では、個々のメッセージのサイズが 64 KB に制限されます。 関数間でこれよりも大きなメッセージを渡す必要がある場合は、Azure Service Bus キューを使用して最大 256 KB のメッセージ サイズをサポートできます。

メッセージの処理の前にフィルター処理を必要とする場合は、Service Bus のトピックが役立ちます。

イベント ハブは、大量の通信をサポートするのに便利です。



## <a name="write-functions-to-be-stateless"></a>ステートレスな関数を記述する 

可能であれば、関数はステートレスかつべき等である必要があります。 すべての必要な状態情報をデータに関連付けます。 たとえば、処理する注文には、`state` メンバーが関連付けられている可能性があります。 関数は、それ自体がステートレスなまま、その状態に基づいて注文を処理できます。 

べき等性を持つ関数は、特にタイマー トリガーと共に使用することをお勧めします。 たとえば、1 日に&1; 回必ず実行する必要のある操作がある場合は、1 日の中の任意の時間にその操作を実行して同じ結果が得られるように記述します。 特定の日に操作を実行しない場合、この関数は終了することができます。 また、前回の実行が完了しなかった場合は、次の実行では中断した場所から操作を実行する必要があります。


## <a name="write-defensive-functions"></a>防御的な関数を記述する

関数は時を選ばす例外に遭遇する可能性があることを想定する必要があります。 関数を設計する場合は、前回失敗した処理を、次回そのポイントから続行する機能を組み込みます。 次のアクションを必要とするシナリオを考えてみましょう。

1. データベース内の 10,000 行を照会する。
2. これらの行のそれぞれに対してキュー メッセージを作成して、最後まで処理を実行する。
 
システムの複雑さによっては、ダウンストリーム サービスが正しく動作しない、ネットワーキングが停止する、クォータ制限に達するなどの問題が発生する可能性があります。いずれも、時を選ばず関数に影響する可能性があります。 関数を設計する際は、このような状況を想定する必要があります。

これらのアイテムのうちの 5,000 個を処理のためにキューに入れた後で障害が発生する状況を想定した場合、どのように対応すればよいでしょうか。 完了したセット内のアイテムを追跡するようにしましょう。 そうでないと、次回同じアイテムを再び挿入することになります。 その場合、ワークフローに大きな影響が及ぶ可能性があります。 

キュー アイテムが既に処理されている場合は、関数による操作をなしにします。

Azure Functions プラットフォームで使用するコンポーネントに対して既に提供されている防御策を活用してください。 一例として、「[Azure Storage キュー トリガー](functions-bindings-storage-queue.md#trigger)」の「**有害キュー メッセージの処理**」を参照してください。
 

## <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>同じ Function App にテスト コードと運用環境のコードを混在させない

Function App 内の関数はリソースを共有します。 たとえば、メモリは共有されます。 運用環境で Function App を使用している場合は、テストに関連する関数およびリソースを追加しないでください。 これが原因で、運用環境のコードの実行中に予期しないオーバーヘッドが発生する可能性があります。

運用環境の Function App で読み込むものに注意してください。 メモリは、アプリ内の各関数間で平均化されます。

複数の .NET 関数で参照される共有アセンブリがある場合は、それを共通の共有フォルダーに配置します。 次の例のようなステートメントを使用して、アセンブリを参照します。 

    #r "..\Shared\MyAssembly.dll". 

そうしないと、関数間で動作が異なる同じバイナリの複数のテスト バージョンを誤ってデプロイする可能性があります。

運用環境のコードでは、詳細ログ記録を使用しないでください。 パフォーマンスに悪影響が及びます。



## <a name="use-async-code-but-avoid-taskresult"></a>非同期コードを使用する。ただし Task.Result は避ける

非同期プログラミングは、推奨されるベスト プラクティスです。 ただし、`Task.Result` プロパティを参照することは必ず避けてください。 この方法は、基本的に、別のスレッドのロックで "busy-wait" を実行します。 ロックを保持すると、デッドロックが発生する可能性があります。




## <a name="next-steps"></a>次のステップ
詳細については、次のリソースを参照してください。

* [Azure Functions developer reference (Azure Functions 開発者向けリファレンス)](functions-reference.md)
* [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-reference-csharp.md)
* [Azure Functions F# 開発者向けリファレンス](functions-reference-fsharp.md)
* [Azure Functions NodeJS 開発者向けリファレンス](functions-reference-node.md)
* [パターンとプラクティスによる HTTP パフォーマンスの最適化](https://github.com/mspnp/performance-optimization/blob/master/ImproperInstantiation/docs/ImproperInstantiation.md)


