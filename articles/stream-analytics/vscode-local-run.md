---
title: Visual Studio Code で Azure Stream Analytics クエリをローカルでテストする (プレビュー)
description: この記事では、Visual Studio Code の Azure Stream Analytics ツールを使ってクエリをローカルでテストする方法について説明します。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 751cdf50fccc654dfab06b4d18428531312d08e6
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673039"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Visual Studio Code で Stream Analytics クエリをローカルでテストする

Visual Studio Code の Azure Stream Analytics ツールを使用して、Stream Analytics ジョブをサンプル データを使ってローカルでテストすることができます。

この[クイックスタート](quick-create-vs-code.md)を使用して、Visual Studio Code を使用して Stream Analytics ジョブを作成する方法を学習します。

## <a name="prerequisites"></a>前提条件
* [.NET Core SDK](https://dotnet.microsoft.com/download) のインストール。
* Visual Studio Code を再起動します。
 
## <a name="run-queries-locally"></a>ローカルでのクエリの実行

Visual Studio Code の Azure Stream Analytics 拡張機能を使用して、Stream Analytics ジョブをサンプル データを使ってローカルでテストすることができます。

1. Stream Analytics ジョブを作成したら、**Ctrl + Shift + P** キーを使ってコマンド パレットを開きます。 次に「**ASA: Add Input**」と入力して選択します。

    ![Visual Studio Code での ASA 入力の追加](./media/vscode-local-run/add-input.png)

2. **[ローカル入力]** を選択します。

    ![Visual Studio Code での ASA ローカル入力の追加](./media/vscode-local-run/add-local-input.png)

3. **[+ New Local Input]\(新規ローカル入力\)** を選択します。

    ![Visual Studio Code での新しい ASA ローカル入力の追加](./media/vscode-local-run/add-new-local-input.png)

4. クエリで使用したのと同じ入力エイリアスを入力します。

    ![新しい ASA ローカル入力エイリアスの追加](./media/vscode-local-run/new-local-input-alias.png)

5. **LocalInput_DefaultLocalStream.json** ファイル内に、ローカル データ ファイルが配置されているファイル パスを入力します。

    ![Visual Studio でのローカル ファイル パスの入力](./media/vscode-local-run/local-file-path.png)

6. クエリ エディターに戻り、 **[ローカルで実行]** を選択します。

    ![クエリ エディターで [ローカルで実行] を選択](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>次の手順

* [Visual Studio Code で Azure Stream Analytics クラウド ジョブを作成する (プレビュー)](quick-create-vs-code.md)

* [Visual Studio Code で使用する Azure Stream Analytics ジョブの詳細 (プレビュー)](vscode-explore-jobs.md)
