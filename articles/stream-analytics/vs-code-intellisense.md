---
title: Visual Studio Code 用の Azure Stream Analytics ツールでの IntelliSense
description: この記事では、Visual Studio Code 用の Azure Stream Analytics ツールで IntelliSense 機能を使用する方法について説明します。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: how-to
ms.openlocfilehash: 988b9d364f09a4c88bcaab1fe81ae80c8de5d4d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491572"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>Visual Studio Code 用の Azure Stream Analytics ツールでの IntelliSense

IntelliSense は、[VS Code 用 Azure Stream Analytics ツール](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview)の [Stream Analytics クエリ言語](/stream-analytics-query/stream-analytics-query-language-reference?bc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fbread%2ftoc.json&toc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fstream-analytics%2ftoc.json)で使用できます。 IntelliSense は、次のような多くの機能を備えたコード補完支援機能です: メンバーの一覧表示、パラメーター ヒント、クイック ヒント、入力候補。 IntelliSense 機能は、"コード補完"、"コンテンツ アシスト"、"コード ヒント" といった他の名前で呼ばれることがあります。

![IntelliSense のデモ](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense の機能

VS Code 用 Stream Analytics ツールでの IntelliSense 機能では、言語サービスが利用されています。 言語サービスでは、ソース コードが分析され、言語のセマンティクスに基づいてインテリジェントなコード補完機能が提供されます。 言語サービスで入力候補が認識された場合、入力時に IntelliSense の候補がポップアップ表示されます。 入力を続けると、メンバー (変数、メソッドなど) の一覧がフィルター処理されて、入力した文字を含むメンバーのみが含まれるようになります。 `Tab` キーまたは `Enter` キーを押すと、選択したメンバーが IntelliSense によって挿入されます。

任意のエディター ウィンドウでトリガー文字 (ドット文字 `.` など) を入力することによって、IntelliSense をトリガーできます。

![IntelliSense のオートコンプリート](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> 候補ウィジェットでは、キャメルケースのフィルター処理がサポートされています。 メソッド名で大文字になっている文字を入力することで、候補を制限できます。 たとえば、「cra」と入力すると、"createApplication" がすぐに表示されます。

### <a name="types-of-completions"></a>入力候補の種類

VS Code 用 Stream Analytics ツールの IntelliSense では、言語サーバーの提案、スニペット、単純な単語ベースのテキスト入力候補など、さまざまな種類の入力候補が提供されます。

|Completion     |  Type       |
| ----- | ------- |
| Keywords | `keyword`
| 関数 | `build-in function`, `user defined function`  |
| データ セット名| `input`, `output`, `intermediate result set`|
| データ セットの列名|`input`, `intermediate result set`|

#### <a name="name-completion"></a>名前の補完

キーワードのオートコンプリートだけでなく、VS Code 用 Stream Analytics ツールでは、ジョブの入力名と出力名の一覧と、構成されている場合はデータ ソース内の列の名前が、読み取られます。 拡張機能では、この情報が記憶されて、わずかなキー ストロークを使用してステートメントを入力する際に役立つ名前補完機能が提供されます。

コーディング中に、エディターを離れて、ジョブの入力名、出力名、列名の検索を実行する必要はありません。 コンテキストを保持したまま、必要な情報を探し、要素を直接コードに挿入できます。IntelliSense で入力を補完することもできます。

名前の補完を使用できるようにするには、ローカル入力またはライブ入力のいずれかを構成し、構成ファイルを保存する必要があることに注意してください。

![名前の補完](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>パラメーター ヒント

IntelliSense の **パラメーター ヒント** オプションを使用すると、パラメーター リストが表示され、関数で必要とされるパラメーターの数、名前、およびデータ型について確認できます。 太字で表示されるパラメーターは、入力中の関数で次に必要なパラメーターを示しています。

パラメーター リストは入れ子にされた関数についても表示されます。 他の関数のパラメーターとして関数を入力する場合、パラメーター リストには内部関数のパラメーターが表示されます。 内部関数のパラメーター リストが完了すると、パラメーター リストには再び外部関数のパラメーターが表示されます。

![パラメーター ヒント](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>クイック ヒント

言語サービスで提供されているように、コード内の各識別子の **クイック ヒント** を見ることができます。 識別子の例としては、入力、出力、中間結果セット、関数などがあります。 識別子の上にマウスのポインターを置くと、ポップアップ ウィンドウにその宣言が表示されます。 入力のプロパティとデータ スキーマ (構成されている場合) および中間データ セットが表示されます。

![クイック ヒント](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>IntelliSense のトラブルシューティング

この問題は、データを提供する入力構成がないために発生します。 [ローカル入力](visual-studio-code-local-run.md#define-a-local-input)または[ライブ入力](visual-studio-code-local-run-live-input.md#define-a-live-stream-input)が正しく構成されているかどうかを確認します。

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Visual Studio Code で Azure Stream Analytics ジョブを作成する](quick-create-visual-studio-code.md)
* [Visual Studio Code を使用してサンプル データで Stream Analytics クエリをローカルでテストする](visual-studio-code-local-run.md)
* [Visual Studio Code を使用してライブ ストリーム入力に対して Stream Analytics クエリをローカルでテストする](visual-studio-code-local-run-live-input.md)