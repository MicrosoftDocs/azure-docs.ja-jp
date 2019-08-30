---
title: Azure Functions のテスト
description: Visual Studio の C# 関数と VS Code の JavaScript 関数の自動化テストを作成する
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, 関数, イベント処理, webhook, 動的コンピューティング, サーバーレス アーキテクチャ, テスト
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: 0bd6222a6f2a2582fb715dbaf364fe23e41630d5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085117"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Azure Functions のコードをテストするための戦略

この記事では、Azure Functions の自動化テストを作成する方法を示します。 

すべてのコードをテストすることをお勧めします。ただし、Functions のロジックをまとめて Function の外部にテストを作成することで、最適な結果が得られる場合があります。 ロジックを切り離して抽象化することで、Function のコード行が制限され、Function 単独で他のクラスやモジュールを呼び出すことができます。 ただし、この記事では、HTTP およびタイマーによってトリガーされる関数に対して自動化テストを作成する方法を示します。

次のコンテンツは、異なる言語と環境を対象とするため、2 つの異なるセクションに分かれています。 次の言語でのテストの作成を学習できます。

- [xUnit を使用した Visual Studio での C#](#c-in-visual-studio)
- [Jest を使用した VS Code での JavaScript](#javascript-in-vs-code)

サンプル リポジトリは [GitHub](https://github.com/Azure-Samples/azure-functions-tests) で入手できます。

## <a name="c-in-visual-studio"></a>Visual Studio での C#
次の例では、Visual Studio で C# 関数アプリを作成し、[xUnit](https://xunit.github.io) を使用して実行およびテストする方法を説明します。

![Visual Studio で C# を使って Azure Functions をテストする](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>セットアップ

環境を設定するには、関数を作成し、アプリをテストします。 次の手順では、テストをサポートするために必要なアプリと関数を作成できます。

1. [新しい関数アプリを作成](./functions-create-first-azure-function.md)して *Functions* という名前を付けます
2. [テンプレートから HTTP 関数を作成](./functions-create-first-azure-function.md)して *HttpTrigger* という名前を付けます。
3. [テンプレートからタイマー関数を作成](./functions-create-scheduled-function.md)して *TimerTrigger* という名前を付けます。
4. Visual Studio で **[ファイル] > [新規作成] > [プロジェクト] > [Visual C#] > [.NET Core] > [xUnit テスト プロジェクト]** の順にクリックして [xUnit テスト アプリを作成](https://xunit.github.io/docs/getting-started-dotnet-core)し、*Functions.Test* という名前を付けます。 
5. Nuget を使用して、テスト アプリ [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/) からの参照を追加します
6. [*Functions.Test* アプリから *Functions* アプリを参照](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017)します。

### <a name="create-test-classes"></a>テスト クラスの作成

これでアプリケーションが作成されたため、自動テストの実行に使用するクラスを作成できます。

各関数は、[ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) のインスタンスを取得して、メッセージ ログを処理します。 テストによっては、メッセージをログに記録しないことも、ログの実装方法に対する懸念がないこともあります。 その他のテストは、テストを渡すかどうかを判断するために、ログに記録されたメッセージを評価する必要があります。

`ListLogger` クラスは、`ILogger` インターフェイスを実装するためのもので、テスト中に評価するメッセージの内部リストに保持されます。

*Functions.Test* アプリケーションを**右クリック**し、 **[追加] > [クラス]** の順に選択し、**NullScope.cs** という名前を付けて、次のコードを入力します。

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

次に、*Functions.Test* アプリケーションを**右クリック**し、 **[追加] > [クラス]** の順に選択し、**ListLogger.cs** という名前を付けて、次のコードを入力します。

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel, 
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

`ListLogger` クラスは、`ILogger` インターフェイスによって縮小される次のメンバーを実装します。

- **BeginScope**:スコープがログにコンテキストを追加します。 この場合、テストで `NullScope` クラスの静的インスタンスをポイントするだけで、テストを機能させることができます。

- **IsEnabled**:既定の値 `false` が指定されます。

- **Log**:このメソッドは、指定された `formatter` 関数を使用してメッセージの書式を設定してから、結果のテキストを `Logs` コレクションに追加します。

`Logs` コレクションは `List<string>` のインスタンスであり、コンストラクターで初期化されます。

次に、*Functions.Test* アプリケーションを**右クリック**し、 **[追加] > [クラス]** の順に選択し、**LoggerTypes.cs** という名前を付けて、次のコードを入力します。

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```
この列挙型は、テストで使用されるロガーの種類を指定します。 

次に、*Functions.Test* アプリケーションを**右クリック**し、 **[追加] > [クラス]** の順に選択し、**TestFactory.cs** という名前を付けて、次のコードを入力します。

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static DefaultHttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var request = new DefaultHttpRequest(new DefaultHttpContext())
            {
                Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue))
            };
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```
`TestFactory` クラスは、次のメンバーを実装します。

- **Data**:このプロパティは、サンプル データの [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) コレクションを返します。 キー値のペアは、クエリ文字列に渡される値を表します。

- **CreateDictionary**:このメソッドは、キー/値ペアを引数として受け取り、クエリ文字列値を表す `QueryCollection` を作成するために使用する新しい `Dictionary` を返します。

- **CreateHttpRequest**:このメソッドは、指定されたクエリ文字列パラメーターで初期化される HTTP 要求を作成します。

- **CreateLogger**:このメソッドは、ロガーの種類に応じて、テストに使用されるロガー クラスを返します。 `ListLogger` は、テストで評価に使用できるログ記録されたメッセージを追跡します。

次に、*Functions.Test* アプリケーションを**右クリック**し、 **[追加] > [クラス]** の順に選択し、**FunctionsTests.cs** という名前を付けて、次のコードを入力します。

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            TimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
このクラスで実装されるメンバーは次のとおりです。

- **Http_trigger_should_return_known_string**:このテストは、HTTP 関数へのクエリ文字列値 `name=Bill` を含む要求を作成し、予期された応答が返されることを確認します。

- **Http_trigger_should_return_string_from_member_data**:このテストは、xUnit 属性を使用して、HTTP 関数にサンプル データを提供します。

- **Timer_should_log_message**:このテストは、`ListLogger` のインスタンスを作成してタイマー関数に渡します。 関数が実行されると、予期されたメッセージが存在することを確認するためにログがチェックされます。

テストでアプリケーション設定にアクセスする場合は、[System.Environment.GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables) を使用できます。

### <a name="run-tests"></a>テストの実行

テストを実行するには、**テスト エクスプローラー**に移動し、 **[すべて実行]** の順にクリックします。

![Visual Studio で C# を使って Azure Functions をテストする](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>テストのデバッグ

テストをデバッグするには、テストにブレークポイントを設定し、**テスト エクスプローラー**に移動して、 **[実行] > [前回の実行をデバッグする]** の順にクリックします。

## <a name="javascript-in-vs-code"></a>VS Code での JavaScript

次の例では、VS Code で JavaScript 関数アプリを作成し、[Jest](https://jestjs.io) を使用して実行およびテストする方法を説明します。 この手順では、[VS Code の Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)を使用して Azure Functions を作成します。

![VS Code で JavaScript を使って Azure Functions をテストする](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>セットアップ

環境を設定するには、`npm init` を実行して空のフォルダーで新しい Node.js アプリを初期化します。

```bash
npm init -y
```
次に、次のコマンドを実行して Jest をインストールします。

```bash
npm i jest
```
この時点で _package.json_ を更新して、既存のテスト コマンドを次のコマンドに置き換えます。

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>テスト モジュールを作成する
プロジェクトが初期化されたら、自動テストの実行に使用するモジュールを作成できます。 最初に、*testing* という名前の、サポート モジュールを保持する新しいフォルダーを作成します。

*testing* フォルダーに新しいファイルを追加して **defaultContext.js** という名前を付け、次のコードを追加します。

```javascript
module.exports = {
    log: jest.fn()
};
```
このモジュールは、*log* 関数をモックして既定の実行コンテキストを表します。

次に、新しいファイルを追加して **defaultTimer.js** という名前を付け、次のコードを追加します。

```javascript
module.exports = {
    IsPastDue: false
};
```
このモジュールは、偽のタイマー インスタンスである `IsPastDue` プロパティを実装します。

次に、VS Code の Functions 拡張機能を使用して、[JavaScript HTTP 関数を新規作成](https://code.visualstudio.com/tutorials/functions-extension/getting-started)して *HttpTrigger* という名前を付けます。 関数を作成した後、**index.test.js** という名前の同じフォルダーに新しいファイルを追加し、次のコードを追加します。

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```
テンプレートからの HTTP 関数は、クエリ文字列に指定された名前と連結された文字列 "Hello" を返します。 このテストは、要求の偽のインスタンスを作成して HTTP 関数に渡します。 このテストは、*log* メソッドが 1 回呼び出され、返されるテキストが "Hello Bill" に等しいことを確認します。

次に、VS Code の Functions 拡張機能を使用して、JavaScript タイマー関数を新規作成して *TimerTrigger*という名前を付けます。 関数を作成した後、**index.test.js** という名前の同じフォルダーに新しいファイルを追加し、次のコードを追加します。

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
テンプレートからのタイマー関数は、関数本文の最後のメッセージをログに記録します。 このテストは、*log* 関数が 1 回呼び出されることを確認します。

### <a name="run-tests"></a>テストの実行
キーを実行するには、**CTRL + ~** を押してコマンド ウィンドウを開き、`npm test` を実行します。

```bash
npm test
```

![VS Code で JavaScript を使って Azure Functions をテストする](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>テストのデバッグ

テストをデバッグするには、*launch.json* ファイルに次の構成を追加します。

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "program": "${workspaceRoot}\\node_modules\\jest\\bin\\jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

次に、テストにブレークポイントを設定し、**F5** を押します。

## <a name="next-steps"></a>次の手順

関数に対する自動テストを記述する方法を習得したので、以下のリソースに進みます。
- [HTTP によってトリガーされない関数を手動で実行する](./functions-manually-run-non-http.md)
- [Azure Functions のエラー処理](./functions-bindings-error-pages.md)
- [Azure Functions の Event Grid トリガーのローカル デバッグ](./functions-debug-event-grid-trigger-local.md)
