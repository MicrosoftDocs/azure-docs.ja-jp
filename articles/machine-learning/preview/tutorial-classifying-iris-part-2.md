---
title: "Azure Machine Learning サービス (プレビュー) のモデルの構築 | Microsoft Docs"
description: "このチュートリアルでは、Azure Machine Learning サービス (プレビュー) の使い方をエンド ツー エンドで詳しく説明します。 こちらは実験の第 2 部となります。"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f1f270b95a60e847ce7d29c2d7d60c57891924cc
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-2-build-a-model"></a>あやめの分類 (パート 2): モデルを構築する
Azure Machine Learning サービス (プレビュー) は、データ サイエンスと高度な分析をエンド ツー エンドで支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

このチュートリアルは、3 部構成のシリーズのパート 2 になります。 チュートリアルのこのパートでは、Azure Machine Learning サービス (プレビュー) を使って次の作業を行う方法について説明します。

> [!div class="checklist"]
> * Azure Machine Learning Workbench を使って作業する
> * スクリプトを開いてコードを確認する
> * ローカル環境でスクリプトを実行する
> * 実行履歴を確認する
> * ローカル Docker 環境でスクリプトを実行する
> * ローカル Azure CLI ウィンドウでスクリプトを実行する
> * リモート Docker 環境でスクリプトを実行する
> * クラウド HDInsight 環境でスクリプトを実行する

このチュートリアルでは、単純化するために、経時的な変化を伴わない[あやめデータセット](https://en.wikipedia.org/wiki/Iris_flower_data_set)を使用します。 スクリーンショットは Windows のものですが、macOS を使用した場合も大きな違いはありません。

## <a name="prerequisites"></a>前提条件
このチュートリアル シリーズのパート 1 を完了している必要があります。 このチュートリアルの手順を始める前に、[データの準備に関するチュートリアル](tutorial-classifying-iris-part-1.md)に従って Azure Machine Learning リソースを作成し、Azure Machine Learning Workbench アプリケーションをインストールしてください。

## <a name="review-irissklearnpy-and-configuration-files"></a>iris_sklearn.py と構成ファイルを確認する
1. **Azure Machine Learning Workbench** アプリケーションを起動し、チュートリアル シリーズの前のパートで作成した **myIris** プロジェクトを開きます。

2. プロジェクトを開いたら、Azure Machine Learning Workbench の左側のツール バーにある **[ファイル]** ボタン (フォルダー アイコン) をクリックして、プロジェクト フォルダーのファイルを一覧表示します。

3. **iris_sklearn.py** ファイルを選択すると、Workbench の新しいテキスト エディター タブに Python コードが表示されます。

   ![ファイルを開く](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >このサンプル プロジェクトは頻繁に更新されるため、表示されるコードが前出のコードとは厳密に一致しない場合があります。

4. Python スクリプト コードを確認して、コーディングのスタイルを把握します。 このスクリプトで次のタスクが実行されることに注目してください。

   - データ準備パッケージ **iris.dprep** を読み込んで [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) を作成します。 

        >[!NOTE]
        >サンプル プロジェクトに付属する `iris.dprep` データ準備パッケージを使用していますが、おそらく、このチュートリアルのパート 1 で作成した `iris-1.dprep` ファイルと同じです。

   - 問題を解きづらくするためにランダム機能を追加します  (あやめデータセットはほぼ 100% の精度で簡単に分類できるほどに小さいため、ランダム性が必要となります)。

   - [scikit-learn](http://scikit-learn.org/stable/index.html) 機械学習ライブラリを使って単純なロジスティック回帰モデルを構築します。 

   - [pickle](https://docs.python.org/2/library/pickle.html) ライブラリを使ってモデルを `outputs` フォルダー内のファイルにシリアル化した後、それをメモリに読み込んで逆シリアル化します。

   - 逆シリアル化されたモデルを使用して、新しいレコードに対する予測を行います。 

   - [matplotlib](https://matplotlib.org/) ライブラリを使って 2 つのグラフ (混同行列と多クラス ROC 曲線) をプロットし、`outputs` フォルダーに保存します。

   - `run_logger` オブジェクトは、正則化項とモデル精度をログに記録する目的で終始使用されます。また、ログは実行履歴に自動的にプロットされます。


## <a name="execute-irissklearnpy-script-in-local-environment"></a>ローカル環境で iris_sklearn.py スクリプトを実行する

**iris_sklearn.py** スクリプトを初めて実行する準備をしましょう。 このスクリプトには、scikit-learn パッケージと matplotlib パッケージが必要です。 **scikit-learn** は、Azure ML Workbench によってあらかじめインストールされます。 ただし、**matplotlib** については自分でインストールする必要があります。 

1. Azure Machine Learning Workbench の **[ファイル]** メニューをクリックし、**[コマンド プロンプトを開く]** を選択してコマンド プロンプトを起動します。 このコマンド ライン インターフェイス ウィンドウは、"Azure Machine Learning Workbench CLI ウィンドウ" または略して "CLI ウィンドウ" と呼ばれています。

2. CLI ウィンドウで次のコマンドを入力して、Python パッケージ **matplotlib** をインストールします。 通常は 1 分かからずに完了します。

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >上記の `pip install` コマンドを省略しても `iris_sklearn.py` のコードは正常に実行されますが、履歴で視覚化されるような混同行列の出力や多クラス ROC 曲線のプロットは生成されません。

3. Workbench アプリのウィンドウに戻ります。 

4. **[iris_sklearn.py]** タブの左上に表示されている保存アイコンの横のドロップダウンをクリックして、**[実行構成]** の選択肢を選びます。  実行環境として **[ローカル]** を選択し、実行するスクリプトとして `iris_sklearn.py` を選択します。

5. 次に、同じタブの右へ移動し、**[引数]** フィールドに「`0.01`」という値を入力します。 

   ![画像](media/tutorial-classifying-iris/run_control.png)

6. **[実行]** ボタンをクリックします。 ジョブが直ちにスケジュールされます。 Workbench ウィンドウの右側の **[ジョブ]** パネルにジョブが一覧表示されます。 

7. しばらくすると、ジョブの状態が **[送信しています]** から **[実行中]** になり、その後すぐに **[完了]** になります。

   ![sklearn の実行](media/tutorial-classifying-iris/run_sklearn.png)

8. [ジョブ] パネルで、ジョブの状態テキストの **[完了]** という語をクリックします。 ポップアップ ウィンドウが開いて、実行中のスクリプトの標準出力 (stdout) テキストが表示されます。 stdout テキストを閉じるには、ポップアップの右上の **[X]** ボタンをクリックします。

9. [ジョブ] パネルの同じジョブの状態で、**[完了]** 状態と開始時刻のすぐ上に表示されている青色のテキスト **iris_sklearn.py [1]** をクリックします。 **[Run Properties]\(実行プロパティ\)** ページが開き、特定の実行のプロパティ情報、**出力**ファイル、**視覚化**、**ログ**が表示されます。 

   実行が完了すると、ポップアップ ウィンドウに次の結果が表示されます。

   >[!NOTE]
   >トレーニング セットにはあらかじめランダム化を導入しているため、実際の結果は多少異なります。

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details page.
   ```

10. **[Run Properties]\(実行プロパティ\)** タブを閉じて **[iris_sklearn.py]** タブに戻ります。 

11. 実行を繰り返します。 

    **[引数]** フィールドには、`0.001` ～ `10` の範囲で一連の異なる数値を入力してください。 **[実行]** をクリックして、さらに何度かコードを実行します。 コードのロジスティック回帰アルゴリズムに渡す引数の値を毎回変えることで、その都度異なる結果が出力されます。

## <a name="review-run-history-in-detail"></a>実行履歴を詳しく確認する
Azure Machine Learning Workbench では、スクリプトの実行がすべて実行履歴レコードとしてキャプチャされます。 **[実行]** ビューを開くと、特定のスクリプトの実行履歴を表示することができます。

1. 左側のツール バーにある **[実行]** ボタン (時計のアイコン) をクリックして**実行**の一覧を開きます。 次に、**[iris_sklearn.py]** をクリックして `iris_sklearn.py` の **[Run Dashboard]\(実行ダッシュボード\)** を表示します。

   ![画像](media/tutorial-classifying-iris/run_view.png)

2. **[Run Dashboard]\(実行ダッシュボード\)** タブが表示されます。 複数回の実行を通じてキャプチャされた統計情報を確認します。 タブの上部にはグラフが表示され、ページ下部のテーブルには、実行番号ごとに詳細が一覧表示されます。

   ![画像](media/tutorial-classifying-iris/run_dashboard.png)

3. テーブルをフィルタリングし、グラフをインタラクティブにクリックすると、各実行の状態、期間、精度、正則化項が表示されます。 

4. **実行**テーブルで 2 ～ 3 個の実行を選択し、**[比較]** ボタンをクリックすると、詳細な比較ページが表示されます。 横並びに表示される比較結果を確認してください。 比較ページの左上に**実行の一覧**に戻るボタンがあるので、それをクリックし、**[Run Dashboard]\(実行ダッシュボード\)** に戻ります。

5. 個々の実行をクリックして、実行の詳細ビューを表示します。 _[Run Properties]\(実行プロパティ\)_ セクションに、選択した実行の統計情報が一覧表示されることに注目してください。 出力フォルダーに書き込まれたファイルは **[出力]** セクションに一覧表示され、ダウンロードすることができます。

   ![画像](media/tutorial-classifying-iris/run_details.png)

   **[Visualizations]\(視覚化\)** セクションには、混同行列と多クラス ROC 曲線の 2 つのプロットが表示されます。 ログ ファイルはすべて **[ログ]** セクションでも確認できます。

## <a name="execute-scripts-in-the-local-docker-environment"></a>ローカル Docker 環境でスクリプトを実行する

Azure ML では、Docker などの実行環境を簡単に追加構成し、それらの環境でスクリプトを実行することができます。 

>[!IMPORTANT]
>この手順を実施するためには、あらかじめ Docker エンジンをローカルにインストールして起動しておく必要があります。 詳細については、インストール ガイドを参照してください。

1. 左側のツール バーでフォルダー アイコンを選択して、目的のプロジェクトの**ファイル**を一覧表示します。 `aml_config` フォルダーを展開します。 

2. あらかじめ構成されているいくつかの環境が存在することに注目してください (**docker-python**、**docker-spark**、**local** など)。 

   それぞれの環境には、2 つのファイルがあります (`docker-python.compute` と `docker-python.runconfig` など)。 各タイプのファイルをテキスト エディターで開くと、特定のオプションを構成することができます。  

   開いているテキスト エディターは、邪魔にならないよう [X] ボタンをクリックしてタブを閉じてください。

3. **docker-python** 環境を使用して **iris_sklearn.py** スクリプトを実行します。 

   - 左側のツール バーで時計アイコンをクリックして **[実行]** パネルを開きます。 **[すべての実行]** をクリックします。 
   - **[すべての実行]** タブの上部で、既定の **[ローカル]** ではなく、**[docker-python]** を対象の環境として選択します。 
   - 次に右へ移動して、実行するスクリプトとして **[iris_sklearn.py]** を選択します。 
   - **[引数]** フィールドは空のままにしてください。これは、スクリプトによって既定値が指定されるためです。 
   - **[実行]** ボタンをクリックします。

4. Workbench ウィンドウの右側の **[ジョブ]** パネルで、新しいジョブが開始されるようすを観察します。

   Docker に対して初めて実行するときは、完了までに数分程度、多く時間がかかります。 

   `docker.compute` ファイルに指定された基本 Docker イメージと `conda_dependencies.yml` ファイルに指定された Python の依存関係パッケージとを参照する新しい Docker ファイルが、Azure Machine Learning Workbench によってバックグラウンドで構築されます。 その後、Docker エンジンによって基本イメージが Azure からダウンロードされ、`conda_dependencies.yml` ファイルに指定された Python パッケージがインストールされた後、Docker コンテナーが起動されます。 さらに、プロジェクト フォルダーのローカル コピーが複製 (実行構成によっては参照) されて、`iris_sklearn.py` スクリプトが実行されます。 最終的には、対象の環境に **[ローカル]** を選んだときとまったく同じ結果になります。

5. 今度は Spark を試してみましょう。 Docker の基本イメージには、プレインストールされて構成済みの Spark インスタンスが含まれています。 したがってそこで PySpark スクリプトを実行することができます。 Spark のインストールと構成に時間を費やすことなく、Spark プログラムの開発とテストを簡単に行うことができます。 

   `iris_pyspark.py` ファイルを開きます。 このスクリプトは、`iris.csv` データ ファイルを読み込み、Spark ML ライブラリのロジスティック回帰アルゴリズムを使って、あやめデータセットを分類します。 実行環境を **[docker-spark]** に変更し、スクリプトを **[iris_pyspark.py]** に変更して、再度実行します。 今回は、先ほどよりも少し時間がかかります。これは、Docker コンテナー内で Spark セッションを作成して起動する必要があるためです。 また、stdout も `iris_pyspark.py` の場合とは異なります。

6. 引数を変えながら、さらに数回実行します。 

7. `iris_pyspark.py` ファイルを開いて、Spark ML ライブラリを使って構築された単純なロジスティック回帰モデルを確認します。 

8. **[ジョブ]** パネル、実行履歴リスト ビュー、実行詳細ビューを操作して、異なる実行環境で行われた実行を確認します。

## <a name="execute-scripts-in-the-azure-ml-cli-window"></a>Azure ML CLI ウィンドウでスクリプトを実行する

1. Azure Machine Learning Workbench の **[ファイル]** メニューをクリックし、**[コマンド プロンプトを開く]** をクリックしてコマンド ライン ウィンドウを起動します。 プロジェクト フォルダーでコマンド プロンプトが起動し、`C:\Temp\myIris\>` というプロンプトが表示されます

   >[!Important]
   >以降の手順を実行するには、(Workbench から起動した) コマンド ライン ウィンドウを使う必要があります。

2. コマンド プロンプト (CLI) を使って Azure にログインします。 

   Workbench アプリと CLI では、Azure リソースに対して認証を行う際に、独立した資格情報キャッシュが使用されます。 キャッシュされたトークンの有効期限が切れない限り、ログインは 1 回だけでかまいません。 **az account list** コマンドを実行すると、ご使用のログインで利用できるサブスクリプションの一覧が返されます。 複数のサブスクリプションがある場合は、目的のサブスクリプションの ID 値を使います。また、**az set account -s** コマンドにサブスクリプション ID 値を指定して、既定のアカウントとして設定してください。 次に、account show コマンドを使って設定を確認します。

   ```azurecli
   REM login using aka.ms/devicelogin site.
   az login
   
   REM list all Azure subscriptions you have access to. 
   az account list -o table
   
   REM set the current Azure subscription to the one you want to use.
   az set account -s <subscriptionId>
   
   REM verify your current subscription is set correctly
   az account show
   ```

3. 認証が行われ、現在の Azure サブスクリプション コンテキストが設定されたら、CLI ウィンドウに次のコマンドを入力して matplotlib をインストールし、実行する実験として Python スクリプトを送信します。

   ```azurecli
   REM You don't need to do this if you have installed matplotlib locally from the previous steps.
   pip install matplotlib
   
   REM Kick off an execution of the iris_sklearn.py file against local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. 出力結果を確認します。 このチュートリアルで前に実行 (Workbench を使ってスクリプトを実行) したときと同じ出力内容と結果であることがわかります。 

5. ご使用のマシンに Docker がインストールされている場合は、Docker 実行環境を使って同じスクリプトを実行します。

   ```azurecli
   REM Execute iris_sklearn.py in local Docker container Python environment.
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM Execute iris_pyspark.py in local Docker container Spark environment.
   az ml experiment submit -c docker-spark .\iris_pyspark.py 0.1
   ```
6. Azure Machine Learning Workbench で、左側のツール バーにあるフォルダー アイコンをクリックしてプロジェクト ファイルを一覧表示し、**run.py** という名前の Python スクリプトを開きます。 

   このスクリプトは、正則化項を変化させながら実験を繰り返し実行するのに便利です。 このスクリプトは、`10.0` という極端に大きな正則化項で `iris_sklearn.py` ジョブを開始します。その後、ループで実行を繰り返しながら正則化項を半分にしていき、`0.005` 以下になったところでループを抜けます。 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   コマンド ラインで **run.py** スクリプトを起動するには、次のコマンドを実行します。

   ```cmd
   REM Submit iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   `run.py` が完了すると、Azure Machine Learning Workbench の実行履歴リスト ビューにグラフが表示されます。

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>リモート マシンで Docker コンテナーを実行する
リモート Linux マシンの Docker コンテナーでスクリプトを実行するには、そのリモート マシンへの SSH アクセス (ユーザー名とパスワード) が必要です。 また、このリモート マシンには、Docker エンジンがインストールされ実行されている必要があります。 このような Linux マシンを入手する方法としては、Azure 上に [Ubuntu ベースのデータ サイエンス仮想マシン (DSVM)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) を作成するのが最も簡単です  (CentOS ベースの DSVM はサポートされないので注意してください)。 

1. VM を作成したら、次のコマンドを使って `.runconfig` と `.compute` という一対のファイルを生成することによって、その VM を実行環境としてアタッチすることができます。 この新しい環境に `myvm` という名前を付けましょう。
 
   ```azurecli
   REM create myvm compute target
   az ml computetarget attach --name myvm --address <IP address> --username <username> --password <password> --type remotedocker
   ```
   
   >[!NOTE]
   >IP アドレス部分は、パブリックにアドレス指定可能な FQDN (完全修飾ドメイン名) にすることもできます (例: `vm-name.southcentralus.cloudapp.azure.com`)。 DSVM に FQDN を追加し、ここで、それを IP アドレスの代わりに使うことをお勧めします。なぜなら VM は、いずれコストを抑えるために停止されることも考えられるからです。 さらに、次回 VM を起動したときには、IP アドレスが変わっている可能性があります。

   次に、次のコマンドを実行して VM に Docker イメージを作成し、スクリプトを実行できる状態にします。
   
   ```azurecli
   REM prepare the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >`myvm.runconfig` の `PrepareEnvironment` の値は、既定値の `false` から `true` に変更することもできます。 これで初回実行時に Docker コンテナーが自動的に準備されます。

2. `aml_config` に生成された `myvm.runconfig` ファイルを編集し、Framework を既定値の `PySpark` から `Python` に変更します。

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >フレームワークの設定を PySpark のままにしても動作上の問題はありません。 しかし Python スクリプトを実行するうえで Spark セッションを必要としないならば、やや非効率といえます。

3. 先ほど CLI ウィンドウで実行したものと同じコマンドを実行します。ただし、対象の環境は _myvm_ とします。
   ```azurecli
   REM execute iris_sklearn.py in remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   実行場所がリモート Linux VM であることを除けば、`docker-python` 環境を使った場合と同じようにコマンドが実行されます。 CLI ウィンドウには同じ出力情報が表示されます。

4. コンテナー内の Spark を試してみましょう。 エクスプローラーを開きます (基本的なファイル操作コマンドに慣れている場合は CLI ウィンドウでこの作業を行うこともできます)。 `myvm.runconfig` ファイルをコピーして `myvm-spark.runconfig` という名前を付けます。 新しいファイルを編集し、`Framework` の設定を `Python` から `PySpark` に変更します。
   ```yaml
   "Framework": "PySpark"
   ```
   `myvm.compute` ファイルには一切変更を加えないでください。 同じ VM 上の同じ Docker イメージが Spark の実行に使用されます。 新しい `myvy-spark.runconfig` の `target` フィールドから、同じ `myvm.compute` ファイルがその名前 (`myvm`) で参照されます。

5. 次のコマンドを入力し、リモート Docker コンテナーの Spark インスタンスで実行します。
   ```azureli
   REM execute iris_pyspark.py in Spark instance on remote Docker container
   az ml experiment submit -c myvm-spark .\iris_pyspark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>HDInsight クラスターでスクリプトを実行する
このスクリプトは、実際の Spark クラスターで実行することもできます。 

1. Spark for Azure HDInsight クラスターにアクセスできる場合は、次に示した HDI 実行構成コマンドを生成します。 パラメーターとして HDInsight クラスター名、HDInsight ユーザー名、パスワードを指定してください。 次のコマンドを使用します。

   ```azurecli
   REM create a compute target that points to a HDI cluster
   az ml computetarget attach --name myhdi --address <cluster head node FQDN> --username <username> --password <password> --type cluster

   REM prepare the HDI cluster
   az ml experiment prepare -c myhdi
   ```

   通常、クラスターのヘッド ノードの完全修飾ドメイン名 (FQDN) は `<cluster_name>-ssh.azurehdinsight.net` になります。

   >[!NOTE]
   >`username` はクラスターの SSH ユーザー名です。 HDI のプロビジョニング時に変更しなかった場合、既定値は `sshuser` となります。 `admin` (クラスターの管理者 Web サイトへのアクセス用としてプロビジョニング時に作成される別のユーザー) ではありません。 

2. 次のコマンドを実行すると、スクリプトが HDInsight クラスターで実行されます。

   ```azurecli
   REM execute iris_pyspark on the HDI cluster
   az ml experiment submit -c myhdi .\iris_pyspark.py
   ```

   >[!NOTE]
   >リモート HDI クラスターで実行するときは、`admin` ユーザー アカウントを使って YARN ジョブの実行情報を `https://<cluster_name>.azurehdinsight.net/yarnui` で確認することもできます。


## <a name="next-steps"></a>次のステップ
3 部構成のチュートリアル シリーズのパート 2 では、Azure Machine Learning サービスを使って次の作業を行う方法について説明しました。
> [!div class="checklist"]
> * Azure Machine Learning Workbench を使って作業する
> * スクリプトを開いてコードを確認する
> * ローカル環境でスクリプトを実行する
> * 実行履歴を確認する
> * ローカル Docker 環境でスクリプトを実行する
> * ローカル Azure CLI ウィンドウでスクリプトを実行する
> * リモート Docker 環境でスクリプトを実行する
> * クラウド HDInsight 環境でスクリプトを実行する

シリーズのパート 3 に進む準備が整いました。 次は作成したロジスティック回帰モデルを、リアルタイムの Web サービスとしてデプロイしましょう。

> [!div class="nextstepaction"]
> [モデルのデプロイ](tutorial-classifying-iris-part-3.md)

