---
title: Visual Studio Code を使用してサンプル データで Azure Stream Analytics ジョブをローカルでテストする
description: この記事では、Visual Studio Code の Azure Stream Analytics ツールを使ってサンプル データでクエリをローカルでテストする方法について説明します。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: how-to
ms.openlocfilehash: bbd83fb3ef3225fc19c48bb4c5962d6559cf32f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97708451"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Visual Studio Code を使用してサンプル データで Stream Analytics クエリをローカルでテストする

Visual Studio Code の Azure Stream Analytics ツールを使用して、Stream Analytics ジョブをサンプル データを使ってローカルでテストすることができます。 クエリの結果は、プロジェクトの **LocalRunOutputs** フォルダー内の JSON ファイルにあります。

## <a name="prerequisites"></a>前提条件

* [.NET Core SDK](https://dotnet.microsoft.com/download) をインストールし、Visual Studio Code を再起動します。

* この[クイックスタート](quick-create-visual-studio-code.md)を使用して、Visual Studio Code を使用して Stream Analytics ジョブを作成する方法を学習します。

## <a name="prepare-sample-data"></a>サンプル データの準備

最初に、サンプルの入力データ ファイルを準備する必要があります。 コンピューターにサンプル データ ファイルが既にある場合は、この手順をスキップして次に進むことができます。

1. 入力構成ファイルの一番上の行で **[データのプレビュー]** をクリックします。 一部の入力データが IoT Hub からフェッチされ、プレビュー ウィンドウに表示されます。 これには時間がかかる場合があります。

2. データが表示されたら、 **[名前を付けて保存]** をクリックして、ローカル ファイルにデータを保存します。

 ![ライブ入力をプレビューする](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-a-local-input"></a>ローカル入力の定義

1. Stream Analytics プロジェクトの Inputs フォルダーの **input.json** をクリックします。 次に、一番上の行で **[ローカル入力の追加]** を選択します。

    ![プロジェクトからローカル入力を追加](./media/quick-create-visual-studio-code/add-input-from-project.png)

    または、**Ctrl + Shift + P** キーを押してコマンド パレットを開き、 **[ASA: 入力の追加]** を入力します。

   ![VS Code に Stream Analytics の入力を追加する](./media/quick-create-visual-studio-code/add-input.png)

2. **[ローカル入力]** を選択します。

    ![Visual Studio Code での ASA ローカル入力の追加](./media/vscode-local-run/add-local-input.png)

3. **[+ New Local Input]\(新規ローカル入力\)** を選択します。

    ![Visual Studio Code での新しい ASA ローカル入力の追加](./media/vscode-local-run/add-new-local-input.png)

4. クエリで使用したのと同じ入力エイリアスを入力します。

    ![新しい ASA ローカル入力エイリアスの追加](./media/vscode-local-run/new-local-input-alias.png)

5. 新しく生成された **LocalInput_Input.json** ファイル内に、ローカル データ ファイルが配置されているファイル パスを入力します。

    ![Visual Studio でのローカル ファイル パスの入力](./media/vscode-local-run/local-file-path.png)

6. **[データのプレビュー]** を選択して入力データをプレビューします。 ご利用のデータに対するシリアル化の種類では、JSON と CSV のいずれであるかが自動的に検出されます。 セレクターを使用して、ご利用のデータを **表** または **未加工** の形式で表示します。 次の表は、**表形式** でのデータの例です。

     ![表形式でのローカル データのプレビュー](./media/vscode-local-run/local-file-preview-table.png)

    次の表は、**未加工の形式** でのデータの例です。

    ![未加工の形式でのローカル データのプレビュー](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>ローカルでのクエリの実行

クエリ エディターに戻り、 **[ローカルで実行]** を選択します。 次に、ドロップダウン リストから **[ローカル入力の使用]** を選択します。

![クエリ エディターで [ローカルで実行] を選択](./media/vscode-local-run/run-locally.png)

![ローカル入力の使用](./media/vscode-local-run/run-locally-use-local-input.png)

結果が右側のウィンドウに表示されます。 **[実行]** をクリックして、もう一度テストすることができます。 **[フォルダーで開く]** を選択し、ファイル エクスプローラーで結果ファイルを表示して、他のツールでそのファイルを開くこともできます。 結果ファイルは JSON 形式でのみ使用できることに注意してください。

![ローカル実行の結果を表示](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>次のステップ

* [Visual Studio Code を使用してライブ入力で Azure Stream Analytics ジョブをローカルでテストする](visual-studio-code-local-run-live-input.md)

* [Visual Studio Code で使用する Azure Stream Analytics ジョブの詳細 (プレビュー)](visual-studio-code-explore-jobs.md)
