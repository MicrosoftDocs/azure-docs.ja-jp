---
title: Azure Functions ウォームアップ トリガー
description: Azure Functions でウォームアップ トリガーを使用する方法について説明します。
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: Azure Functions, 関数, イベント処理, ウォームアップ, コールド スタート, Premium, 動的コンピューティング, サーバーレス アーキテクチャ
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: a947ff11fbbe418af84ff49033cba3857bb8447f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925181"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure Functions のウォームアップ トリガー

この記事では、Azure Functions でウォームアップ トリガーを使用する方法について説明します。 ウォームアップ トリガーは、[Premium プラン](functions-premium-plan.md)で実行されている関数アプリでのみサポートされています。 実行中の関数アプリをスケーリングするためにインスタンスが追加されると、ウォームアップ トリガーが呼び出されます。 ウォームアップ トリガーを使用して[事前ウォームアップ プロセス](./functions-premium-plan.md#pre-warmed-instances)の間にカスタムの依存関係を事前に読み込むと、関数がすぐに要求の処理を開始できるようになります。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>パッケージ - Functions 2.x 以降

[Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet パッケージ、バージョン **3.0.5 以降**が必要です。 パッケージのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub リポジトリにあります。 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>トリガー

ウォームアップ トリガーを使用すると、実行中のアプリに追加されたときにインスタンス上で実行される関数を定義できます。 ウォームアップ関数を使用すると、アプリでトラフィックの受信を開始される前に、接続を開く、依存関係を読み込むなどのカスタム ロジックを実行できます。 

ウォームアップ トリガーは、アプリ内の他の関数で使用される共有依存関係を作成することを目的としています。 [共有依存関係の例についてはこちらを参照してください](./manage-connections.md#client-code-examples)。

ウォームアップ トリガーはスケールアップ操作中にのみ呼び出され、再起動やその他の非スケール スタートアップ中には呼び出されない点に注意してください。 ロジックで、ウォームアップ トリガーを使用せずに、すべての必要な依存関係を読み込めることを確認する必要があります。 これを実現するには、遅延読み込みがお勧めのパターンです。

## <a name="trigger---example"></a>トリガー - 例

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

新しいインスタンスがアプリに追加されるたびに実行される [C# 関数](functions-dotnet-class-library.md)の例を次に示します。 戻り値の属性は必須ではありません。


* 関数には ```warmup``` (大文字と小文字を区別しない) という名前を付ける必要があります。アプリごとに 1 つのウォームアップ関数しか存在しない場合があります。
* ウォームアップを .NET クラス ライブラリ関数として使用するには、**Microsoft.Azure.WebJobs.Extensions >= 3.0.5** へのパッケージ参照があることを確認します
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


プレースホルダーのコメントは、共有依存関係を宣言して初期化するアプリケーション内の場所を示します。 
[共有依存関係の詳細についてはこちらを参照してください](./manage-connections.md#client-code-examples)。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)


*function.json* ファイルのウォームアップ トリガーと、新しいインスタンスがアプリに追加されるたびに実行される [C# スクリプト関数](functions-reference-csharp.md)の例を次に示します。

関数には ```warmup``` (大文字と小文字を区別しない) という名前を付ける必要があります。アプリごとに 1 つのウォームアップ関数しか存在しない場合があります。

*function.json* ファイルを次に示します。

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

`HttpRequest` にバインドする C# スクリプト コードを次に示します。

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

*function.json* ファイルのウォームアップ トリガーと、新しいインスタンスがアプリに追加されるたびに実行される [JavaScript 関数](functions-reference-node.md)の例を次に示します。

関数には ```warmup``` (大文字と小文字を区別しない) という名前を付ける必要があります。アプリごとに 1 つのウォームアップ関数しか存在しない場合があります。

*function.json* ファイルを次に示します。

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

*function.json* ファイルのウォームアップ トリガーと、新しいインスタンスがアプリに追加されるたびに実行される [Python 関数](functions-reference-python.md)の例を次に示します。

関数には ```warmup``` (大文字と小文字を区別しない) という名前を付ける必要があります。アプリごとに 1 つのウォームアップ関数しか存在しない場合があります。

*function.json* ファイルを次に示します。

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="javatabjava"></a>[Java](#tab/java)

*function.json* ファイルのウォームアップ トリガーと、新しいインスタンスがアプリに追加されるたびに実行される [Java 関数](functions-reference-java.md)の例を次に示します。

関数には ```warmup``` (大文字と小文字を区別しない) という名前を付ける必要があります。アプリごとに 1 つのウォームアップ関数しか存在しない場合があります。

*function.json* ファイルを次に示します。

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

Java コードを次に示します。

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>トリガー - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、`WarmupTrigger` 属性を使用して関数を構成できます。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

この例では、[warmup](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) 属性の使用方法を示します。

関数の名前は ```Warmup``` にする必要があること、アプリごとに使用できるウォームアップ関数は 1 つのみであることに注意してください。

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

完全な例については、[トリガーの例](#trigger---example)を参照してください。

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="pythontabpython"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="javatabjava"></a>[Java](#tab/java)

ウォームアップ トリガーは、Java では属性としてサポートされていません。

---

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルと `WarmupTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
| **type** | 該当なし| 必須 - `warmupTrigger` に設定する必要があります。 |
| **direction** | 該当なし| 必須 - `in` に設定する必要があります。 |
| **name** | 該当なし| 必須 - 関数コードで使用される変数名。|

## <a name="trigger---usage"></a>トリガー - 使用方法

呼び出されるときに、ウォームアップ トリガー関数に追加情報は提供されません。

## <a name="trigger---limits"></a>トリガー - 制限

* ウォームアップ トリガーは、[Premium プラン](./functions-premium-plan.md)で実行されているアプリでのみ使用できます。
* ウォームアップ トリガーはスケールアップ操作中にのみ呼び出され、再起動やその他の非スケール スタートアップ中には呼び出されません。 ロジックで、ウォームアップ トリガーを使用せずに、すべての必要な依存関係を読み込めることを確認する必要があります。 これを実現するには、遅延読み込みがお勧めのパターンです。
* インスタンスが既に実行中の場合、ウォームアップ トリガーを呼び出すことはできません。
* 関数アプリごとに使用できるウォームアップ トリガー関数は 1 つのみです。

## <a name="next-steps"></a>次の手順

[Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
