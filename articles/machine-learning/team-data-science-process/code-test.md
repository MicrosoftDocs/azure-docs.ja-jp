---
title: Azure DevOps Services でデータ サイエンス コードをテストする - Team Data Science Process
description: Azure 上で Team Data Science Process と Azure DevOps Services を使用して UCI 成人収入予測データセットでデータ サイエンス コードをテストします
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: 66853b580ba1b619bf4cc995e05bd3cfaff07cfb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565871"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Team Data Science Process と Azure DevOps Services を使用した Azure でのデータ サイエンス コードのテスト
この記事では、データ サイエンス ワークフローでコードをテストするうえでの予備的ガイドラインについて取り上げます。 このようなテストにより、データ サイエンティストは、秩序立った効率的な方法で、自分のコードの品質と期待される成果をチェックすることができます。 ここでは、[UCI 成人収入データセットを使用した Team Data Science Process (TDSP) プロジェクト](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)を用います。以前、コード テストの実行方法を紹介する際に発行したものです。 

## <a name="introduction-on-code-testing"></a>コード テストの概要
"単体テスト" は、伝統的なソフトウェア開発手法です。 しかし、データ サイエンスの場合、"単体テスト" が何を意味し、次のようなデータ サイエンス ライフサイクルのさまざまなステージに対してどのようにコードをテストすべきかが明確でないケースが多くあります。

* データの準備
* データ品質試験
* モデリング
* モデル デプロイ 

この記事の "単体テスト" という用語は、"コード テスト" に読み替えてください。 データ サイエンス ライフサイクルにおける特定段階のコードから "期待どおり" の結果が生成されるかどうかの評価を支援する関数としてテストすることを指します。 何をもって "期待どおり" とするかは、テストを作成する担当者が、関数の成果 (データ品質チェック、モデリングなど) に応じて定義します。

この記事では、実用的なリソースとしてのリファレンスを提供しています。

## <a name="azure-devops-for-the-testing-framework"></a>テスト フレームワーク用の Azure DevOps
この記事では、Azure DevOps を使用してテストを実行および自動化する方法について説明しています。 その他のツールで代替してもかまいません。 ここでは、Azure DevOps とビルド エージェントを使用して自動ビルドをセットアップする方法についても取り上げています。 ビルド エージェントについては、Azure データ サイエンス仮想マシン (DSVM) を使用します。

## <a name="flow-of-code-testing"></a>コード テストのフロー
データ サイエンス プロジェクトにおけるコード テストの全体的なワークフローは、次のようになります。 

![コード テストのフロー チャート](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>詳細な手順

ビルド エージェントと Azure DevOps を使用してコード テストと自動ビルドをセットアップし、実行するには、次の手順に従います。

1. Visual Studio デスクトップ アプリケーションでプロジェクトを作成します。

    ![Visual Studio の [新しいプロジェクトの作成] 画面](./media/code-test/create_project.PNG)

   作成したプロジェクトは、右側のウィンドウのソリューション エクスプローラーに表示されます。
    
    ![プロジェクトの作成手順](./media/code-test/create_python_project_in_vs.PNG)

    ![ソリューション エクスプローラー](./media/code-test/solution_explorer_in_vs.PNG)

1. プロジェクト コードを Azure DevOps プロジェクト コード リポジトリにフィードします。 

    ![プロジェクト コード リポジトリ](./media/code-test/create_repo.PNG)

1. いくつかのデータ準備作業 (データ インジェスト、特徴エンジニアリング、ラベル列の作成など) が完了しているとします。 期待した結果がコードから生成されることを確認する必要があります。 データ処理コードが適切に動作しているかどうかは、以下のようなコードを使ってテストできます。

    * 列名が正しいことを確認する。
    
      ![列名を突き合わせるためのコード](./media/code-test/check_column_names.PNG)

    * 回答レベルが正しいことを確認する。

      ![レベルを突き合わせるためのコード](./media/code-test/check_response_levels.PNG)

    * 回答率が妥当であることを確認する。

      ![回答率のコード](./media/code-test/check_response_percentage.PNG)

    * データの各列の欠損率を確認する。
    
      ![欠損率のコード](./media/code-test/check_missing_rate.PNG)


1. データ処理と特徴エンジニアリングの作業が済み、適切なモデルをトレーニングしたら、そのモデルで新しいデータセットを正しくスコア付けできることを確認します。 次の 2 つのテストを使用して、予測レベルとラベル値の分布を確認します。

    * 予測レベルを確認する。
    
      ![予測レベルを確認するためのコード](./media/code-test/check_prediction_levels.PNG)

    * 予測値の分布を確認する。

      ![予測値を確認するためのコード](./media/code-test/check_prediction_values.PNG)

1. すべてのテスト関数を、**test_funcs.py** という Python スクリプトにまとめます。

    ![テスト関数の Python スクリプト](./media/code-test/create_file_test_func.PNG)


1. テスト コードの準備が整ったら、Visual Studio でテスト環境をセットアップすることができます。

   **test1.py** という Python ファイルを作成します。 このファイルに、実行するテストをすべて含んだクラスを作成します。 次の例を見ると、6 つのテストが用意されていることがわかります。
    
    ![クラスとして一連のテストを含んだ Python ファイル](./media/code-test/create_file_test1_class.PNG)

1. クラス名の後に **codetest.testCase** を置くと、これらのテストが自動的に検出されます。 右側のウィンドウでテスト エクスプローラーを開き、 **[Run All]\(すべて実行\)** を選択します。 すべてのテストが順次実行され、その成否が通知されます。

    ![テストの実行](./media/code-test/run_tests.PNG)

1. Git コマンドを使用してプロジェクト リポジトリにコードをチェックインします。 直近に行った作業がすぐ Azure DevOps に反映されます。

    ![コードをチェックインするための Git コマンド](./media/code-test/git_check_in.PNG)

    ![Azure DevOps に反映された直近の作業](./media/code-test/git_check_in_most_recent_work.PNG)

1. Azure DevOps で自動ビルドとテストをセットアップします。

    a. プロジェクト リポジトリで **[ビルドとリリース]** を選択し、 **[+新規]** を選択して新しいビルド プロセスを作成します。

    ![新しいビルド プロセスを開始するための選択](./media/code-test/create_new_build.PNG)

    b. 画面の指示に従って、ソース コードの場所、プロジェクト名、リポジトリ、ブランチ情報を選択します。
    
    ![ソース、名前、リポジトリ、ブランチ情報](./media/code-test/fill_in_build_info.PNG)

    c. テンプレートを選択します。 Python プロジェクト テンプレートは存在しないので、 **[空のプロセス]** を選択して開始します。 

    ![テンプレートのリストおよび [空のプロセス] ボタン](./media/code-test/start_empty_process_template.PNG)

    d. ビルドに名前を付けて、エージェントを選択します。 DSVM を使用してビルド プロセスを完了する場合、ここでは既定値を選択できます。 エージェントの設定の詳細については、「[Build and release agents (ビルド エージェントとリリース エージェント)](/azure/devops/pipelines/agents/agents)」を参照してください。
    
    ![ビルドとエージェントの選択](./media/code-test/select_agent.PNG)

    e. 左側のウィンドウで [ **+** ] を選択して、このビルド フェーズのタスクを追加します。 Python スクリプト **test1.py** を実行してすべてのチェックを完了するので、このタスクでは、PowerShell コマンドを使用して Python コードを実行します。
    
    ![[タスクの追加] ウィンドウで PowerShell を選択したところ](./media/code-test/add_task_powershell.PNG)

    f. PowerShell の詳細で、必要な情報を入力します (PowerShell の名前やバージョンなど)。 種類には **[インライン スクリプト]** を選択してください。 
    
    **[インライン スクリプト]** の下のボックスに、「**python test1.py**」と入力することができます。 Python の環境変数が正しく設定されていることを確認します。 別のバージョンまたは別のカーネルの Python が必要な場合は、そのパスを明示的に指定できます (下図参照)。 
    
    ![PowerShell の詳細](./media/code-test/powershell_scripts.PNG)

    g. **[保存してキューに登録]** を選択して、ビルド パイプライン プロセスを完了します。

    ![[保存してキューに登録] ボタン](./media/code-test/save_and_queue_build_definition.PNG)

今後、新たにコミットをコード リポジトリにプッシュするたびに、ビルド プロセスが自動的に開始されます。 任意のブランチを定義できます。 エージェント マシンで **test1.py** ファイルが実行され、コードのあらゆる定義が正しく実行されることが確認されます。 

アラートが正しく設定されていれば、ビルドが完了したときにメールで通知されます。 ビルドの状態を Azure DevOps で確認することもできます。 ビルドに失敗した場合は、その詳細をチェックして問題箇所を突き止めることができます。

![ビルド成功の通知 (メール)](./media/code-test/email_build_succeed.PNG)

![ビルド成功の Azure DevOps 通知](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>次のステップ
* データ サイエンスのシナリオを想定した単体テストの具体例については、[UCI 収入予測リポジトリ](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)を参照してください。
* ここで説明した概要と UCI 収入予測シナリオの例を、独自のデータ サイエンス プロジェクトで実際に行ってみます。

## <a name="references"></a>References
* [Team Data Science Process とは](./index.yml)
* [Visual Studio のテスト ツール](https://www.visualstudio.com/vs/features/testing-tools/)
* [Azure DevOps のテスト リソース](https://www.visualstudio.com/team-services/)
* [データ サイエンス仮想マシン](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)