---
title: "Azure の Machine Learning 用 Visual Studio Code ツールに関するクイックスタート記事 | Microsoft Docs"
description: "この記事では、Machine Learning 用 Visual Studio Code ツールの使用を開始するために、実験の作成、モデルのトレーニング、Web サービスの運用を開始する方法について説明します。"
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: get-started-article
ms.date: 09/12/2017
ms.openlocfilehash: 680c1afab1af31cfef51b1c82d2db49f452ba6ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools for AI
Visual Studio Code Tools for AI は、ディープ ラーニングおよび AI ソリューションを構築、テスト、デプロイするための開発拡張機能です。 その重要な特徴として、Azure Machine Learning とのシームレスな統合、特に過去のトレーニングのパフォーマンスとカスタム メトリックの詳細が表示される実行履歴ビューがあります。 サンプル エクスプローラー ビューも用意されています。これを使用すると、[Microsoft Cognitive Toolkit (従来は CNTK と呼ばれていました)](http://www.microsoft.com/en-us/cognitive-toolkit)、[Google TensorFlow](https://www.tensorflow.org)、その他のディープ ラーニング フレームワークで新しいプロジェクトを参照およびブートストラップすることができます。 最後に、コンピューティング ターゲット用のエクスプローラーも用意されています。これにより、Azure Virtual Machines や GPU 搭載の Linux サーバーなどのリモート環境でモデルをトレーニングするためのジョブを送信できます。 
 
## <a name="getting-started"></a>使用の開始 
操作を開始するには、最初に [Visual Studio Code](https://code.visualstudio.com/Download) をダウンロードしてインストールする必要があります。 Visual Studio Code を開いたら、次の手順を実行します。
1. アクティビティ バーの拡張機能アイコンをクリックします。 
2. "Visual Studio Code Tools for AI" を検索します。 
3. **[インストール]** ボタンをクリックします。 
4. インストール後、**[再読み込み]** ボタンをクリックします。 

Visual Studio Code の再読み込みが完了すると、拡張機能がアクティブになります。 [拡張機能のインストールの詳細についてはこちらを参照してください](https://code.visualstudio.com/docs/editor/extension-gallery)。

## <a name="exploring-project-samples"></a>プロジェクト サンプルの探索
Visual Studio Code Tools for AI には、サンプル エクスプローラーが付属しています。 サンプル エクスプローラーを使用すると、数回のクリックでサンプルを簡単に見つけて試すことができます。 エクスプローラーを開くには、次の操作を実行します。   
1. コマンド パレットを開きます ([表示] > **[コマンド パレット]** または **Ctrl + Shift + P** キー)。
2. 「AI Sample」と入力します。 
3. [AI: Open Azure ML Sample Explorer]\(AI: Azure ML サンプル エクスプローラーを開く\) が推奨されるのでこれを選択し、Enter キーを押します。 

または、サンプル エクスプローラーのアイコンをクリックすることもできます。

## <a name="creating-a-new-project-from-the-sample-explorer"></a>サンプル エクスプローラーからの新しいプロジェクトの作成 
さまざまなサンプルを参照し、その詳細情報を取得できます。 サンプルを参照して "あやめの分類" サンプルを見つけます。 このサンプルに基づいて新しいプロジェクトを作成するには、次の手順を実行します。
1. プロジェクト サンプルのインストール ボタンをクリックします。表示されるコマンドに従って、新しいプロジェクトの作成手順を実行します。 
2. プロジェクトの名前を選択します (例: "Iris")。
3. プロジェクトを作成するフォルダー パスを選択し、Enter キーを押します。 
4. 既存のワークスペースを選択し、Enter キーを押します。

プロジェクトが作成されます。

> [!TIP]
> Azure リソースにアクセスするには、ログインする必要があります。 埋め込みターミナルで「az login」と入力し、指示に従います。 

## <a name="submitting-experiment-with-the-new-project"></a>新しいプロジェクトでの実験の送信
新しいプロジェクトが Visual Studio Code で開かれたら、別のコンピューティング ターゲット (ローカルおよび Docker が動作している VM) にジョブを送信します。
Visual Studio Code Tools for AI では、複数の方法で実験を送信できます。 
1. コンテキスト メニュー (右クリック) - **[AI: Submit Job]\(AI: ジョブの送信\)**。
2. コマンド パレットから: [AI: Submit Job]\(AI: ジョブの送信\)。
3. または、Azure CLI、Machine Learning コマンド、埋め込みターミナルを使用して、コマンドを直接実行することもできます。

iris_sklearn.py を開き、右クリックして **[AI: Submit Job]\(AI: ジョブの送信\)** を選択します。
1. プラットフォームとして、[Azure Machine Learning] を選択します。
2. 実行構成として、[Docker-Python] を選択します。

> [!NOTE]
> 初めてジョブを送信する場合は、"No Machine Learning configuration found, creating... (Machine Learning 構成が見つかりません。作成しています...)" というメッセージが表示されます。 JSON ファイルが開いたら保存します (**Ctrl + S**)。

ジョブが送信されると、埋め込みターミナルに実行の進行状況が表示されます。 

## <a name="view-list-of-jobs"></a>ジョブの一覧の表示
ジョブが送信されると、実行履歴からジョブの一覧を表示できます。
1. コマンド パレットを開きます ([表示] > **[コマンド パレット]** または **Ctrl + Shift + P** キー)。
2. 「AI List」と入力します。
3. [AI: List Jobs]\(AI: ジョブの一覧表示\) が推奨されるのでこれを選択し、Enter キーを押します。
4. プラットフォームとして、[Azure Machine Learning] を選択します。

ジョブ リスト ビューが開き、すべての実行といくつかの関連情報が表示されます。

## <a name="view-job-details"></a>ジョブの詳細を表示する
開かれたジョブ リスト ビューのリストの最初の実行をクリックします。
ジョブの結果を詳しく調べるために、先頭の**ジョブ ID** をクリックして詳細情報を表示します。 

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [IDE を使用するための Azure Machine Learning の構成方法](./how-to-configure-your-IDE.md)
