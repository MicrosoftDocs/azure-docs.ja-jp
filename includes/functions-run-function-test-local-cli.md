---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 55c64048e0604987c5a4c26961e5617106358e76
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2020
ms.locfileid: "84436106"
---
## <a name="run-the-function-locally"></a>関数をローカルで実行する

*LocalFunctionProj* フォルダーから、ローカルの Azure Functions ランタイム ホストを起動して関数を実行します。

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
```
::: zone-end

出力の最後の方に、次の行があります。 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> HttpExample が以下のように表示されない場合、プロジェクトのルート フォルダー以外からホストを起動したと考えられます。 その場合は **Ctrl** + **C** キーを使用してホストを停止し、プロジェクトのルート フォルダーに移動して、前出のコマンドを再度実行してください。

この出力から `HttpExample` 関数の URL をブラウザーにコピーし、クエリ文字列 `?name=<your-name>` を追加して、URL 全体を `http://localhost:7071/api/HttpExample?name=Functions` のようにします。 `Hello Functions` のようなメッセージがブラウザーに表示されます。

![ブラウザーでローカルに関数を実行した結果](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

要求を行うと、プロジェクトを起動したターミナルにもログ出力が表示されます。

準備が完了したら、**Ctrl** + **C** キーを押し、`y` を選択して関数ホストを停止してください。
