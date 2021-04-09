---
title: Azure Stream Analytics のユーザー定義関数をデバッグする
description: この記事では、Azure Stream Analytics でユーザー定義関数をデバッグする方法について説明します。
author: jenssuessmeyer
ms.author: jenss
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7f5a24e4d351d7c133251900a80c70094b84cc4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019433"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>Azure Stream Analytics のユーザー定義関数をデバッグする 

ユーザー定義関数 (UDF) が期待どおりに動作しない場合、デバッグして問題を見つける必要があります。 Stream Analytics ジョブの UDF をデバッグすることは、[Visual Studio Code](visual-studio-code-local-run-live-input.md) や [Visual Studio](stream-analytics-vs-tools-local-run.md) を使用してジョブをローカルで実行する場合に可能です。

ライブ入力ストリームに対してジョブをローカルで実行すると、1 つのノード上のクラウド Azure Stream Analytics エンジンの実行のみが模倣されます。 ライブ データのローカル テストは、クラウドで実行するパフォーマンスとスケーラビリティのテストに取って代わるものではありませんが、テストするたびにクラウドにジョブを送信する必要がないため、機能テストの時間を節約できます。 また、ローカルまたはサンプル データを使用したローカル実行では時間ポリシーが無効ですが、ライブ データ テストでは時間ポリシーがサポートされています。

## <a name="pick-your-language"></a>言語の選択

Azure Stream Analytics の UDF は、.NET (C#) か JavaScript を使用して作成できます。 

### <a name="functions-in-c"></a>C# の関数 

[Visual Studio を使用して .NET UDF を作成する](stream-analytics-edge-csharp-udf-methods.md)と、.NET クラス プロジェクトの場合と同じレベルのサポートが得られます。 次のものがサポートされます。

* 構文チェックやコンパイラ サポートなどのコンパイルのサポート。

* Stream Analytics ソリューション内で C# プロジェクトおよび成果物を追加、ビルド、参照する機能。 

* 共有可能なプロジェクトにカプセル化されたコードの簡単な再利用。 

* Visual Studio での直接のデバッグ サポート。 Stream Analytics プロジェクトをスタート プロジェクトとして設定し、C# コードにブレークポイントを設定します。 その後、他の C# プロジェクトの場合と同様に、**F5** キーを押して C# コードをデバッグします。 

### <a name="functions-in-javascript"></a>JavaScript の関数

Stream Analytics で関数を作成するためのもう 1 つの選択肢は、JavaScript です。 JavaScript コードは、Stream Analytics プロジェクトの関数領域に直接配置されるため、プロジェクト間で共有することはより困難になります。

コンパイルは、Stream Analytics プロジェクトがコンパイルまたは実行されるときに行われます。 実行時になって初めて問題が発見される可能性が高くなります。 Stream Analytics の JavaScript 関数には、直接のデバッグ サポートは用意されていません。

## <a name="debug-options-for-javascript"></a>JavaScript のデバッグ オプション

Stream Analytics の JavaScript 関数には直接のデバッグ サポートが用意されていないため、HTML サイトでその関数をカプセル化し、そこから出力を取得してデバッグできます。

[Visual Studio Code](quick-create-visual-studio-code.md) の統合ランタイム環境で、いくらかの制限があるものの、JavaScript UDF をデバッグする方法を次の例で示します。

### <a name="prerequisites"></a>前提条件

開始する前に、Azure Stream Analytics プロジェクトに次の項目が含まれていることを確認します。

* 入力 
* 出力 
* Stream Analytics クエリ (.asaql) 
* Stream Analytics ジョブ構成 (JobConfig.json)
* JavaScript UDF

### <a name="prepare-files"></a>ファイルの準備

次の図では、 *.asaql* クエリ ファイルに、UDF *fxcharCount* への呼び出しのみが含まれています。 変更を加えた後にもプロジェクトをコンパイルできるようにするため、この変更を加えます。

> [!div class="mx-imgBorder"]
> ![Stream Analytics のテスト クエリ ファイル](./media/debug-user-defined-functions/asaql-file.png)

テスト ファイルをホストするため、 **[テスト]** に追加のフォルダーを作成します。このテスト ファイルは、JavaScript 関数でテストを実行するために呼び出されます。 この例では、フォルダーの名前は *fxcharCount* で、テストの名前は *Test_UDF.js* です。 

次の図に、テスト ファイルのコードを示します。このコードは、関数ファイルを読み込み、その関数を実行します。 この例は単純ですが、追加のテスト データ ファイルを読み込み、追加のテストをループ処理して出力を取得することもできます。 関数呼び出しの表記は、一般的な呼び出しといくらか異なります。これは、デバッグできるようにするため、ファイルは参照され、ランタイムに読み込まれないからです。 

> [!div class="mx-imgBorder"]
> ![Stream Analytics テスト ファイル](./media/debug-user-defined-functions/test-file.png)

関数で、次のコード行をファイルに追加して、メソッドを公開します。 これが Visual Studio Code でコードをコンパイルする機能に影響することはありません。

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Stream Analytics JavaScript UDF](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>デバッグ サポートのインストール

デバッグするには、**Node.js** を [ダウンロード](https://nodejs.org/en/download/)してインストールする必要があります。 使用しているプラットフォームに応じて、正しいバージョンをインストールしてください。 Node.js ランタイムをインストールしたら、Visual Studio Code を再起動して変更を適用します。 

デバッグを開始するには、 **[Run and Debug]\(実行とデバッグ\)** を選択するか、**Ctrl + Shift + D** キーを同時に押します。 表示されるコンボ ボックスで、ランタイムとして **Node.js** を選択します。 Node.js のみがインストールされている場合は、これが既定で使用されます。 コードをステップ実行でき、必要であれば F11 でサテライト ファイルにステップインできます。 

> [!div class="mx-imgBorder"]
> ![Stream Analytics UDF の実行とデバッグ](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>ユーザー定義集計のデバッグ 

JavaScript UDF のデバッグ メソッドを使用して、ユーザー定義集計 (UDA) をデバッグできます。 次の例では、UDA が *.asaql* クエリ ファイルとテスト ファイルに追加されています。

UDF と同様に、変更が行われた後にプロジェクトがコンパイルされるように、UDA への呼び出しを含めます。 

> [!div class="mx-imgBorder"]
> ![UDA を asaql に追加する](./media/debug-user-defined-functions/asaql-uda.png)

UDF で行ったのと同様に、*Test_UDA.js* ファイルで UDA ファイルを参照します。 また、`main()`、`init()`、`accumulate()` を呼び出します。 `accumulate()` メソッドは、状態スタックに値を置くためにループで呼び出されます。 `computeresult()` メソッドは、最終的なクエリを作成するために呼び出されます。 

> [!div class="mx-imgBorder"]
> ![UDA テスト ファイル](./media/debug-user-defined-functions/uda-test.png)

UDF の例と同様に、関連するメソッドを公開するには、UDA 自体にコードを追加する必要があります。

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![UDA に追加されたコード](./media/debug-user-defined-functions/uda-expose-methods.png)

デバッグを開始するには、 **[Run and Debug]\(実行とデバッグ\)** を選択するか、**Ctrl + Shift + D** キーを同時に押します。 表示されるコンボ ボックスで、ランタイムとして **Node.js** を選択します。 Node.js のみがインストールされている場合は、これが既定で使用されます。 コードをステップ実行でき、必要であれば F11 でサテライト ファイルにステップインできます。

> [!div class="mx-imgBorder"]
> ![Stream Analytics UDA の実行とデバッグ](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics ジョブをローカル環境で開発してデバッグする](develop-locally.md)
* [Visual Studio のジョブ ダイアグラムを使用して Azure Stream Analytics のクエリをローカルでデバッグする](debug-locally-using-job-diagram.md)
* [Azure Stream Analytics でのユーザー定義関数](functions-overview.md)
 
