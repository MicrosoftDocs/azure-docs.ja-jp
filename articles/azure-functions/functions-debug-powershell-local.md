---
title: PowerShell Azure Functions をローカル環境でデバッグする
description: ローカルで実行するときに PowerShell 関数をデバッグする方法について説明します。
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a668024db126c82f96756555aba513b77f7d7366
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422962"
---
# <a name="debug-powershell-azure-functions-locally"></a>PowerShell Azure Functions をローカル環境でデバッグする

Azure Functions では、関数を PowerShell スクリプトとして開発することができます。

次の標準的な開発ツールを使用する PowerShell スクリプトと同様、PowerShell 関数をローカル環境でデバッグできます。

* [Visual Studio Code](https://code.visualstudio.com/):Microsoft の無料で軽量のオープンソース テキスト エディターであり、PowerShell の完全な開発エクスペリエンスを提供する PowerShell 拡張機能を備えています。
* PowerShell コンソール: 他の PowerShell プロセスのデバッグに使うものと同じコマンドを使ってデバッグします。

[Azure Functions Core Tools](functions-run-local.md) では、PowerShell 関数を含む Azure Functions のローカル デバッグがサポートされています。

## <a name="example-function-app"></a>関数アプリの例

この記事で使う関数アプリには、1 つの HTTP によってトリガーされる関数と、次のファイルが含まれます。

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

この関数アプリは、[PowerShell のクイックスタート](./create-first-function-vs-code-powershell.md)を完了したときに作成されるものと似ています。

`run.ps1` の関数のコードは、次のようなスクリプトです。

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>アタッチ ポイントを設定する

どのような PowerShell 関数でも、デバッグするには、デバッガーをアタッチするために停止する必要があります。 `Wait-Debugger` コマンドレットは実行を停止して、デバッガーを待機します。

>[!NOTE]
>PowerShell 7 を使用するとき、コードに `Wait-Debugger` 呼び出しを追加する必要はありません。

開発者が行う必要があるのは、次のように、`if` ステートメントのすぐ上に `Wait-Debugger` コマンドレットの呼び出しを追加することだけです。

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

`if` ステートメントでデバッグが開始されます。 

`Wait-Debugger` を配置した後は、Visual Studio Code または PowerShell コンソールを使って関数をデバッグできます。

## <a name="debug-in-visual-studio-code"></a>Visual Studio Code でのデバッグ

Visual Studio Code で PowerShell 関数をデバッグするには、以下がインストール済みである必要があります。

* [Visual Studio Code 用 PowerShell 拡張機能](/powershell/scripting/components/vscode/using-vscode)
* [Visual Studio Code 用 Azure Functions 拡張機能](./create-first-function-cli-powershell.md)
* [PowerShell Core 6.2 以降](/powershell/scripting/install/installing-powershell-core-on-windows)

これらの依存関係をインストールした後、既存の PowerShell 関数プロジェクトを読み込むか、[Azure で初めての PowerShell 関数を作成します](./create-first-function-vs-code-powershell.md)。

>[!NOTE]
> プロジェクトに必要な構成ファイルがない場合は、追加を求めるメッセージが表示されます。

### <a name="set-the-powershell-version"></a>PowerShell のバージョンを設定する

PowerShell Core は、Windows PowerShell とサイドバイサイドでインストールされます。 Visual Studio Code 用 PowerShell 拡張機能を使用するには、PowerShell のバージョンとして Visual Studio Code を 設定します。

1. F1 キーを押してコマンド パレットを表示し、`Session` を検索します。

1. **[PowerShell:Show Session Menu]\(PowerShell: セッション メニューを表示\)** をクリックします。

1. **[現在のセッション]** が **[PowerShell Core 6]** になっていない場合は、 **[切り替え:PowerShell Core 6]** を選択します。

PowerShell ファイルが開いている場合は、ウィンドウの右下に緑色で表示されているバージョンを確認します。 このテキストの選択でも、セッション メニューが表示されます。 詳細については、「[拡張機能で使用する PowerShell のバージョンの選択](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)」を参照してください。

### <a name="start-the-function-app"></a>関数アプリを開始する

デバッガーをアタッチしたい関数に、`Wait-Debugger` を設定したことを確認します。  `Wait-Debugger` を追加したら、Visual Studio Code を使って関数アプリをデバッグできます。

**[デバッグ]** ウィンドウを選択し、 **[Attach to PowerShell function]\(PowerShell 関数にアタッチする\)** を選択します。

![デバッガー](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

F5 キーを押してデバッグを開始することもできます。

デバッグ開始操作では、次のタスクが行われます。

* ターミナルで `func extensions install` が実行され、関数アプリで必要なすべての Azure Functions 拡張機能がインストールされます。
* ターミナルで `func host start` が実行され、Functions ホストで関数アプリが起動されます。
* Functions ランタイム内の PowerShell 実行空間に、PowerShell デバッガーがアタッチされます。

>[!NOTE]
> Visual Studio Code で正しいデバッグのエクスペリエンスを実現するには、PSWorkerInProcConcurrencyUpperBound が 1 に設定されていることを確認する必要があります。 これは既定値です。

関数アプリが実行されたら、別の PowerShell コンソールを開いて、HTTP によってトリガーされる関数を呼び出す必要があります。

この場合は、PowerShell コンソールがクライアントです。 関数をトリガーするには `Invoke-RestMethod` を使います。

PowerShell コンソールで次のコマンドを実行します。

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

応答がすぐに返されないことがわかります。 これは、`Wait-Debugger` でデバッガーがアタッチされていて、PowerShell の実行は可能になるとすぐに中断モードに移行したためです。 これは、後で説明する [BreakAll の概念](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place)によるものです。 `continue` ボタンをクリックした後、デバッガーは `Wait-Debugger` の直後の行で中断しています。

この時点で、デバッガーがアタッチされ、デバッガーでの通常の操作をすべて行うことができます。 Visual Studio Code でのデバッガーの使用に関して詳しくは、[公式ドキュメント](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)をご覧ください。

続行してスクリプトを完全に呼び出すと、次のことがわかります。

* `Invoke-RestMethod` を行った PowerShell コンソールで、結果が返されています
* Visual Studio Code の PowerShell 統合コンソールで、スクリプトの実行が待機されています

その後、同じ関数を呼び出すと、PowerShell 拡張機能のデバッガーは `Wait-Debugger` の直後で中断します。

## <a name="debugging-in-a-powershell-console"></a>PowerShell コンソールでのデバッグ

>[!NOTE]
> このセクションを読む前に、[Azure Functions Core Tools のドキュメント](functions-run-local.md)を読み、`func host start` コマンドを使って関数アプリを起動する方法を理解しておく必要があります。

コンソールを開き、関数アプリのディレクトリに `cd` で移動して、次のコマンドを実行します。

```sh
func host start
```

関数アプリが実行され、`Wait-Debugger` が配置されていると、プロセスにアタッチできます。 さらに 2 つの PowerShell コンソールが必要です。

コンソールの 1 つは、クライアントとして機能します。 ここから、`Invoke-RestMethod` を呼び出して、関数をトリガーします。 たとえば、次のコマンドを実行できます。

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

応答が返されないことがわかります。これは `Wait-Debugger` の結果です。 PowerShell の実行空間では、デバッガーがアタッチされることが待機されています。 それではアタッチしてみましょう。

他の PowerShell コンソールで次のコマンドを実行します。

```powershell
Get-PSHostProcessInfo
```

このコマンドレットでは、次の出力のようなテーブルが返されます。

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

テーブルで `ProcessName` が `dotnet` である項目の `ProcessId` を書き留めておきます。 このプロセスが関数アプリです。

次に、以下のスニペットを実行します。

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

開始されたデバッガーは中断し、次のような出力が表示されます。

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

この時点では、[PowerShell デバッガー](/powershell/module/microsoft.powershell.core/about/about_debuggers)のブレークポイントで停止しています。 ここからは、ステップ オーバー、ステップ イン、続行、終了など、通常のすべてのデバッグ操作を行うことができます。 コンソールで使用できるデバッグ コマンドの完全なセットを表示するには、`h` または `?` コマンドを実行します。

`Set-PSBreakpoint` コマンドレットを使って、このレベルでブレークポイントを設定することもできます。

続行してスクリプトを完全に呼び出すと、次のことがわかります。

* `Invoke-RestMethod` を実行した PowerShell コンソールに、結果が返されています。
* `Debug-Runspace` を実行した PowerShell コンソールでは、スクリプトの実行が待機されています。

同じ関数をもう一度呼び出すことができ (たとえば、`Invoke-RestMethod` を使って)、デバッガーは `Wait-Debugger` コマンドの直後で中断されます。

## <a name="considerations-for-debugging"></a>デバッグに関する考慮事項

Functions のコードをデバッグするときは、次の問題に注意してください。

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` を使うと、デバッガーが予期しない場所で中断される可能性があります

PowerShell 拡張機能では `Debug-Runspace` が使われており、それはさらに PowerShell の `BreakAll` 機能に依存しています。 この機能では、PowerShell は実行された最初のコマンドで停止するよう指示されます。 この動作により、デバッグ対象の実行空間内にブレークポイントを設定する機会ができます。

Azure Functions ランタイムでは、`run.ps1` スクリプトが実際に呼び出される前にいくつかのコマンドが実行されるため、デバッガーが `Microsoft.Azure.Functions.PowerShellWorker.psm1` または `Microsoft.Azure.Functions.PowerShellWorker.psd1` の中で中断される可能性があります。

この中断が発生した場合は、`continue` または `c` コマンドを実行して、このブレークポイントをスキップします。 その後は、予期されるブレークポイントで停止します。

## <a name="troubleshooting"></a>トラブルシューティング

デバッグ中に問題が発生した場合は、次の点を確認する必要があります。

| ○ | アクション |
|------|------|
| ターミナルから `func --version` を実行します。 `func` が見つからないというエラーが表示される場合は、ローカルの `path` 変数に Core Tools (func.exe) がない可能性があります。| [Core Tools を再インストールします](functions-run-local.md#v2)。|  
| Visual Studio Code では、既定のターミナルが func.exe にアクセスできる必要があります。 Linux 用 Windows サブシステム (WSL) などの Core Tools がインストールされていない既定のターミナルを使用していないことを確認します。  | Visual Studio Code の既定のシェルを PowerShell 7 (推奨) または Windows PowerShell 5.1 に設定します。|
  

## <a name="next-steps"></a>次のステップ

PowerShell を使用した Functions の開発の詳細については、「[Azure Functions PowerShell developer guide (Azure Functions PowerShell 開発者ガイド)](functions-reference-powershell.md)」をご覧ください。
