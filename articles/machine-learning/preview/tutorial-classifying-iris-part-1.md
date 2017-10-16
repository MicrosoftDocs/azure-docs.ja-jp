---
title: "Machine Learning サービス (プレビュー) であやめの分類のチュートリアル用のデータを準備する | Microsoft Docs"
description: "Azure Machine Learning サービス (プレビュー) の使い方をエンド ツー エンドで詳しく紹介したチュートリアルです。 これは、データの準備のパート 1 です。"
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
ms.openlocfilehash: e88b6bf74b7492353e5d5d004bde12fa4787e405
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-1-prepare-data"></a>あやめの分類のパート 1: データを準備する
Azure Machine Learning サービス (プレビュー) は、データ サイエンスと高度な分析をエンド ツー エンドで支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

このチュートリアルは、3 部構成のシリーズのパート 1 になります。 このチュートリアルでは、Azure Machine Learning のサービス (プレビュー) の基礎を確認します。 このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Azure Machine Learning Workbench でプロジェクトを作成する
> * データ準備パッケージを作成する
> * データ準備パッケージを呼び出す Python/PySpark コードを生成する

このチュートリアルでは、単純化するために、経時的な変化を伴わない[あやめデータセット](https://en.wikipedia.org/wiki/Iris_flower_data_set)を使用します。 スクリーンショットは Windows のものですが、macOS を使用した場合も大きな違いはありません。

## <a name="launch-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench を起動する
[インストールと作成のクイック スタート](quickstart-installation.md)に関する記事に従い、コマンドライン インターフェイス (CLI) を含むアプリケーションである Azure Machine Learning Workbench をインストールします。 Azure Machine Learning Workbench アプリを起動し、必要に応じてログインします。

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する
1. **[PROJECTS (プロジェクト)]** ペインで **+** アイコンをクリックし、**新しいプロジェクト**を作成します。

   ![新しいワークスペース](media/tutorial-classifying-iris/new_ws.png)

2. **[新しいプロジェクトの作成]** に詳細を入力します。 

   ![[新しいプロジェクト]](media/tutorial-classifying-iris/new_project.png)

   - **[Project name (プロジェクト名)]** フィールドにプロジェクトの名前を入力します。 たとえば、「**myIris**」という値を使用します。
   - プロジェクトを作成する **[Project directory (プロジェクト ディレクトリ)]** を選択します。 たとえば、「**C:\Temp**」という値を使用します。 
   - **[プロジェクトの説明]** を入力します。 
   - **[Git repository (Git リポジトリ)]** フィールドは省略可能であり、空白にしておくことができます。 VSTS 上にある既存の空の Git リポジトリ (マスター分岐のないリポジトリ) を指定することもできます。 これにより、後でローミングと共有のシナリオを実行できるようになります。 詳細については、[Git リポジトリの使用](using-git-ml-project.md)に関する記事を参照してください。 
   - **[ワークスペース]** を選択します (たとえば、このチュートリアルでは **[IrisGarden]** を使用しています)。 
   - プロジェクト テンプレートの一覧から **[Classifying Iris (あやめの分類)]** テンプレートを選択します。 

3. **[作成]** ボタンをクリックしてプロジェクトを作成します。 プロジェクトが作成され、自動的に開きます。

## <a name="create-a-data-preparation-package"></a>データ準備パッケージを作成する
1. **ファイル ビュー**から `iris.csv` ファイルを開きます。 このファイルは、5 つの列と 150 の行を含む単純なテーブルです。 4 つの数値特徴列と 1 つの文字列ターゲット列があります。 列ヘッダーはありません。

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   >注: プロジェクト フォルダーにデータ ファイルを含めることは推奨されません。ファイル サイズが大きい場合は特にそうです。 `iris.csv` は小さいため、デモの目的でこのテンプレートに含められています。 詳細については、[大きなデータ ファイルの読み取りと書き込みの方法](how-to-read-write-files.md)に関する記事を参照してください。

2. **データ ビュー**で **+** アイコンをクリックして、新しいデータ ソースを追加します。 **[データ ソースの追加]** ウィザードが起動します。 

   ![データ ビュー](media/tutorial-classifying-iris/data_view.png)

3. **[ファイル/ディレクトリ]** オプションを選択し、ローカル ファイル `iris.csv` を選択します。 各画面で既定の設定を受け入れ、最後に **[完了]** をクリックします。 

   ![iris を選択する](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >必ず、この演習の現在のプロジェクト ディレクトリ内で `iris.csv` ファイルを選択してください。そうでないと、以降の手順は失敗します。 

4. 新しいファイル `iris-1.dsource` が作成されます。 サンプル プロジェクトに番号なしの `iris.dsource` ファイルが既にあるため、このファイルは -1 が付いた一意の名前になります。  ファイルが開き、データが表示されます。 一連の列ヘッダー (`Column1` から `Column5`) がこのデータセットに自動的に追加されます。 下部までスクロールし、データセットの最後の行が空であることに注意してください。 これは、csv ファイルに余分な改行があるためです。

   ![iris データ ビュー](media/tutorial-classifying-iris/iris_data_view.png)

5. **[メトリック]** ボタンをクリックします。 ヒストグラムと、各列について計算された統計情報の完全なセットを観察します。 **[データ]** ボタンをクリックしてデータを再度表示することもできます。 

   ![iris データ ビュー](media/tutorial-classifying-iris/iris_metrics_view.png)

6. **[メトリック]** ボタン (メトリック ビュー上では **[データ]** ボタン) の横にある **[準備]** ボタンをクリックします。 **[準備]** ダイアログが表示されます。 サンプル プロジェクトには既に `iris.dprep` ファイルが付属しているため、既定により既存の **iris.dprep** データ準備パッケージで新しいデータ フローを作成するように求められます。 ドロップダウンの値を **[+New Data Preparation Package (+新しいデータ準備パッケージ)]** に変更し、新しい値「iris-1」を入力して **[OK]** をクリックします。

   ![iris データ ビュー](media/tutorial-classifying-iris/new_dprep.png)

`iris-1.dprep` という名前の新しいデータ準備パッケージが作成され、データ準備エディター内で開きます。

いくつかの単純なデータ準備作業を実行してみましょう。 列の名前を変更するため、それぞれの列ヘッダーをクリックし、ヘッダーのテキストを編集可能にします。 5 つの列のそれぞれについて、「`Sepal Length`」、「`Sepal Width`」、「`Petal Length`」、「`Petal Width`」、および「`Species`」と入力します。

![列名の変更](media/tutorial-classifying-iris/rename_column.png)

`Species` 列を選択し、右クリックします。 **[Value Counts (値のカウント)]** を選択します。 

![値のカウント](media/tutorial-classifying-iris/value_count.png)

この操作により、4 つのバーのあるヒストグラムが作成されます。 ターゲット列に 3 つの個別の値 (`Iris_virginica`、`Iris_versicolor`、`Iris-setosa`) があることに注意してください。 `(null)` の値のある 1 つの行もあります。 この行を取り除いてみましょう。null 値を表すバーを選択して、**-** フィルター ボタンをクリックすると除外されます。 

![値のカウント](media/tutorial-classifying-iris/filter_out.png)

列の名前を変更し、null 値の行をフィルターで取り除いている間、各操作は **[STEPS (ステップ)]** ペインでデータ準備ステップとして記録されています。 これらのステップは、編集 (設定を調整する) や並べ替えのほか、削除することもできます。

![steps](media/tutorial-classifying-iris/steps.png)

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>データ準備パッケージを呼び出す Python/PySpark コードを生成する

データ準備エディターを終了します  (自動的に保存されます)。**iris-1.dprep** ファイルを右クリックしてコンテキスト メニューを表示し、**[Generate Data Access Code File (データ アクセス コード ファイルの生成)]** を選択します。 

![コードを生成する](media/tutorial-classifying-iris/generate_code.png)

次の 2 つのコード行が (コメントと共に) あらかじめ入力された **iris-1.py** ファイルが生成されます。

```python
# This code snippet will load the referenced package and return a DataFrame.
# If the code is run in a PySpark environment, the code will return a
# Spark DataFrame. If not, the code will return a Pandas DataFrame.

from azureml.dataprep.package import run
df = run('iris.dprep', dataflow_idx=0)
```
このコード スニペットにより、データ準備パッケージとして作成したロジックが呼び出されます。 このコードが実行されるコンテキストに応じて、`df` は [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) (Python ランタイムで実行される場合) または [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) (Spark コンテキストで実行される場合) になります。 Azure Machine Learning Workbench でデータを準備する方法の詳細については、「[Getting Started with Data Preparation (Data Preparation の概要)](data-prep-getting-started.md)」ガイドを参照してください。

## <a name="next-steps"></a>次のステップ
3 部構成のチュートリアル シリーズのこのパート 1 では、Azure Machine Learning Workbench を使って次のことを行いました。
> [!div class="checklist"]
> * 新しいプロジェクトを作成する 
> * データ準備パッケージを作成する
> * データ準備パッケージを呼び出す Python/PySpark コードを生成する

シリーズの次のパートに移り、機械学習モデルをビルドすることができます。
> [!div class="nextstepaction"]
> [モデルのビルド](tutorial-classifying-iris-part-2.md)

