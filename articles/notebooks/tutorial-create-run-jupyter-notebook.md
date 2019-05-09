---
title: チュートリアル - Azure で Jupyter ノートブックを作成して実行する
description: データ サイエンスの線形回帰のプロセスを示す Jupyter ノートブックを Azure Notebook で作成して実行する方法です。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 65bbb5fe-9939-4e8e-8f5b-c197d4be142a
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: d5ccf3e9f35a8d35387962278577333ff92ff02b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59258536"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>チュートリアル: Python で Jupyter ノートブックを作成して実行する

このチュートリアルでは、Azure Notebooks を使用して単純な線形回帰を示す完全な Jupyter ノートブックを作成するプロセスについて説明します。 このチュートリアルでは、異なるセルの作成、セルの実行、スライド ショーとしてのノートブックの提示など、Jupyter Notebook の UI について理解します。

完成したノートブックは、[GitHub の Azure Notebook サンプル](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)にありあます。 ただし、このチュートリアルでは、新しいプロジェクトと空のノートブックで始めるので、作成する手順を経験できます。

## <a name="create-the-project"></a>プロジェクトを作成する

1. [Azure Notebooks](https://notebooks.azure.com) に移動してサインインします  (詳しくは、「[Quickstart - Sign in to Azure Notebooks](quickstart-sign-in-azure-notebooks.md)」(クイック スタート - Azure Notebook にサインインする) をご覧ください)。

1. パブリック プロファイル ページから、ページの上部にある **[マイ プロジェクト]** を選択します。

    ![ブラウザー ウィンドウの上部にある [マイ プロジェクト] リンク](media/quickstarts/my-projects-link.png)

1. **[マイ プロジェクト]** ページで、**[+ 新しいプロジェクト]** (キーボード ショートカット: N) を選択します。ブラウザー ウィンドウが狭い場合、ボタンに **[+]** だけが表示されることがあります。

    ![[マイ プロジェクト] ページの [新しいプロジェクト] コマンド](media/quickstarts/new-project-command.png)

1. 表示される **[新しいプロジェクトの作成]** ポップアップで、次の詳細を入力または設定した後、**[作成]** を選択します。

    - **[プロジェクト名]**:Linear Regression Example - Cricket Chirps
    - **[プロジェクト ID]**: linear-regression-example
    - **[Public project]\(パブリック プロジェクト\)**: (オフ)
    - **[Create a README.md]\(README.md を作成する\)**: (オフ)

1. しばらくすると、Azure Notebooks は新しいプロジェクトに移動します。

## <a name="create-the-data-file"></a>データ ファイルを作成する

ノートブックで作成する線形回帰モデルでは、*cricket_chirps.csv* という名前のプロジェクトのファイルからデータを取得します。 このファイルは、[GitHub の Azure Notebooks サンプル](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)からコピーするか、データを直接入力することで作成できます。 以下のセクションでは、両方の方法について説明します。

### <a name="upload-the-data-file"></a>データ ファイルをアップロードする

1. Azure Notebooks のプロジェクト ダッシュボードで、**[Upload]\(アップロード\)** > **[From URL]\(URL から\)** を選択します
1. ポップアップで、次の URL を **[File URL]\(ファイルの URL\)** に入力し、**[File Name]\(ファイル名\)** に「*cricket_chirps.csv*」と入力して、**[Done]\(完了\)** を選択します。

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. *cricket_chirps.csv* ファイルが、プロジェクトのファイルの一覧に表示されます。

    ![プロジェクト ファイルの一覧に表示された新しく作成した CSV ファイル](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>ファイルを最初から作成する

1. Azure Notebooks のプロジェクト ダッシュボードで、**[+ New]\(+ 新規作成\)** > **[Blank File]\(空のファイル\)** を選択します
1. プロジェクトのファイル一覧にフィールドが表示されます。 「*cricket_chirps.csv*」と入力して Enter キーを押します。
1. *cricket_chirps.csv* を右クリックして、**[Edit File]\(ファイルの編集\)** を選択します。
1. 表示されるエディターで、次のデータを入力します。

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. **[Save File]\(ファイルの保存\)** を選択してファイルを保存し、プロジェクト ダッシュボードに戻ります。

## <a name="install-project-level-packages"></a>プロジェクト レベルのパッケージをインストールする

ノートブックでは常に、コード セルで `!pip install` などのコマンドを使用して必要なパッケージをインストールできます。 ただし、そのようなコマンドはノートブックのコード セルを実行するたびに実行され、長い時間がかかる場合があります。 このため、代わりに `requirements.txt` ファイルを使用してプロジェクト レベルでパッケージをインストールすることができます。

1. 「[ファイルを最初から作成する](#create-a-file-from-scratch)」で説明されているプロセスを使用して、`requirements.txt` という名前で次の内容のファイルを作成します。

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    その方がよければ、「[データ ファイルをアップロードする](#upload-the-data-file)」で説明されているように、ローカル コンピューターから `requirements.txt` ファイルをアップロードすることもできます。

1. プロジェクト ダッシュボードで、**[Project Settings]\(プロジェクトの設定\)** を選択します。
1. 表示されるポップアップで、**[Environment]\(環境\)** タブを選択し、**[+Add]\(+ 追加\)** を選択します。
1. **[Environment Setup Steps]\(環境のセットアップ手順\)** の最初のドロップダウン コントロール (操作) で、**Requirements.txt** を選択します。
1. 2 番目のドロップダウン コントロール (ファイル名) で、*requirements.txt* (作成したファイル) を選択します。
1. 3 番目のドロップダウン コントロール (Python のバージョン) で、**[Python Version 3.6]\(Python バージョン 3.6\)** を選択します。
1. **[保存]** を選択します。

![[Project Settings]\(プロジェクトの設定\) の [Environment]\(環境\) タブでの requirements.txt ファイルの指定](media/tutorial/tutorial-requirements-txt.png)

このセットアップ手順を行うと、プロジェクトで実行するすべてのノートブックは、それらのパッケージがインストールされている環境で実行されるようになります。

## <a name="create-and-run-a-notebook"></a>ノートブックの作成と実行

データ ファイルを準備してプロジェクトの環境を設定したら、ノートブックを作成して開くことができます。

1. プロジェクト ダッシュボードで、**[+ New]\(+ 新規作成\)** > **[Notebook]\(ノートブック\)** を選択します。
1. ポップアップで、**[Item Name]\(アイテム名\)** に「*Linear Regression Example - Cricket Chirps.ipynb*」と入力し、言語で **[Python 3.6]** を選択して、**[New]\(新規作成\)** を選択します。
1. ファイルの一覧に新しいノートブックが表示されたら、それを選択してノートブックを開始します。 ブラウザーに新しいタブが自動的に開きます。
1. 環境設定に *requirements.txt* ファイルがあるので、"Waiting for your container to finish being prepared" (コンテナーの準備が完了するのを待っています) というメッセージが表示されます。 **[OK]** を選択してメッセージを閉じ、ノートブックでの作業を継続できます。ただし、環境が完全に設定されるまで、コード セルを実行することはできません。
1. 既定で空のコード セルが 1 つ含まれるノートブックが、Jupyter のインターフェイスで開きます。

    [![Azure Notebooks での新しいノートブックの初期表示](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Notebook のインターフェイスをツアーする

ノートブックが実行されていると、コード セルや Markdown セルを追加し、それらのセルを実行し、ノートブックの動作を管理することができます。 ただし、最初に少し時間を取ってインターフェイスに慣れておくと役に立ちます。 完全なドキュメントで、**[Help]\(ヘルプ\)** > **[Notebook Help]\(Notebook ヘルプ\)** メニュー コマンドを選択します。

ウィンドウの上部には次の項目が表示されています。

(A) ノートブックの名前。クリックして編集できます。
(B) それを含んでいるプロジェクトとプロジェクト ダッシュボードに移動するボタン。ブラウザーで新しいタブが開かれます。
(C) ノートブックを操作するためのコマンドが含まれるメニュー。
(D) 一般的な操作のショートカットのツールバー。
(E) セルが含まれる編集キャンバス。
(F) ノートブックが信頼されているかどうかを示すインジケーター (既定では**信頼されていません**)。
(G) ノートブックが実行されているカーネルと、アクティビティ インジケーター。

[![Jupyter インターフェイスの主な UI 領域](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter では、主な UI 要素の組み込みツアーが提供されています。 ツアーを始めるには、**[Help]\(ヘルプ\)** > **[User Interface Tour]\(ユーザー インターフェイス ツアー\)** コマンドを選択し、ポップアップをクリックします。

メニュー コマンドのグループは次のとおりです。

| メニュー | 説明 |
| --- | --- |
| ファイル | ノートブック ファイルを管理するコマンドです。ノートブックの作成とコピー、印刷プレビューの表示、さまざまな形式でのノートブックのダウンロードなどのコマンドが含まれます。 |
| Edit | セルの切り取り、コピー、貼り付け、値の検索と置換、セルの添付の管理、イメージの挿入など、一般的なコマンドです。  |
| 表示 | Jupyter UI のさまざまな部分の表示を制御するコマンドです。 |
| 挿入 | 現在のセルの上または下に新しいセルを挿入するコマンドです。 ノートブックを作成するときは、これらのコマンドを頻繁に使用します。 |
| Cell (セル) | さまざまな **[Run]\(実行\)** コマンドでは、1 つまたは複数のセルをさまざまな組み合わせで実行します。 **[Cell Type]\(セルの種類\)** コマンドでは、セルの種類を **[Code]\(コード\)**、**[Markdown]**、**[Raw NBConvert]\(生 NBConvert\)** (プレーン テキスト) の間で変更します。 **[Current Outputs]\(現在の出力\)** と **[All Outputs]\(すべて出力\)** コマンドでは実行コードからの出力の表示方法を制御し、すべての出力をクリアするコマンドが含まれます。 |
| Kernel (カーネル) | カーネルでのコードの実行方法を管理するコマンドです。**[Change kernel]\(カーネルの変更\)** では、ノートブックの実行に使用される言語または Python バージョンを変更します。 |
| データ | プロジェクトまたはセッションからファイルをアップロードおよびダウンロードするコマンドです。 [プロジェクト データ ファイルの操作](work-with-project-data-files.md)に関する記事をご覧ください |
| Widgets (ウィジェット) | [Jupyter のウィジェット](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html)を管理するコマンドです。視覚化、マッピング、プロットに関する追加機能が提供されます。|
| [Help] | Jupyter インターフェイスのヘルプとドキュメントを提供するコマンドです。 |

ツール バーのほとんどのコマンドには、対応するメニュー コマンドがあります。 1 つの例外は **[Enter/Edit RISE Slideshow]\(RISE スライドショーの入力/編集\)** で、[ノートブックの共有と提供](present-jupyter-notebooks-slideshow.md)に関するページで説明されています。

後のセクションでノートブックを作成するときに、これらのコマンドのいくつかを使用します。

## <a name="create-a-markdown-cell"></a>Markdown セルを作成する

1. ノートブックのキャンバスに表示されている最初の空のセルをクリックします。 既定ではセルの種類は**コード**であり、選択したカーネル (Python、R、F#) で実行可能なコードを含むように設計されていることを意味します。 現在の種類は、ツール バーの種類ドロップダウンに表示されます。

    ![ツール バーのセルの種類ドロップダウン](media/tutorial/tutorial-cell-type-drop-down.png)

1. ツール バーのドロップダウンを使用して、セルの種類を **[Markdown]** に変更します。または、**[Cell]\(セル\)** > **[Cell Type]\(セルの種類\)** > **[Markdown]** メニュー コマンドを使用します。

    ![セルの種類のメニュー コマンド](media/tutorial/tutorial-cell-type-menu.png)

1. セルをクリックして編集を開始し、次の Markdown を入力します。

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Markdown をブラウザーの HTML にレンダリングするには、ツール バーの **[Run]\(実行\)** コマンドを選択するか、または **[Cell]\(セル\)** > **[Run Cells]\(セルの実行\)** コマンドを使用します。 書式設定とリンクのための Markdown コードが、ブラウザーに期待どおりに表示されます。

1. ノートブックで最後のセルを実行すると、実行したセルの下に新しいセルが自動的に作成されます。 このセクションの手順を繰り返し、次の Markdown を使用して、このセルにさらに Markdown を配置します。

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Markdown を再び編集するには、レンダリングされたセルをダブルクリックします。 変更した後でもう一度 HTML をレンダリングするには、セルを実行します。

## <a name="create-a-code-cell-with-commands"></a>コマンドを使用してコード セルを作成する

前の Markdown のセルで説明したように、ノートブックにコマンドを直接含めることができます。 コマンドを使用して、パッケージのインストール、データを取得するための curl または wget の実行、その他を行うことができます。 Jupyter Notebook は Linux 仮想マシン内で実質的に実行されるので、完全な Linux コマンド セットを使用できます。

1. 前の Markdown セルで **[Run]\(実行\)** を使用した後に表示されるコード セルに、次のコマンドを入力します。 新しいセルが表示されない場合は、**[Insert]\(挿入\)** > **[Insert Cell Below]\(下にセルを挿入\)** またはツール バーの **[+]** ボタンを使用して、セルを作成します。

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. セルを実行する前に、ツール バーの **[+]** ボタンで新しいセルを作成し、Markdown に設定して、次の説明を入力します。

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. **[Cell]\(セル\)** > **[Run All]\(すべて実行\)** コマンドを選択して、ノートブックのすべてのセルを実行します。 Markdown のセルが HTML としてレンダリングされ、コマンドがカーネルで実行され、Markdown 自体で記述されているようにカーネル インジケーターが表示されることに注意してください。

    ![ノートブック カーネルのビジー状態インジケーター](media/tutorial/tutorial-kernel-busy.png)

1. すべての `pip install` コマンドの実行には少し時間がかかります。プロジェクト環境にはこれらのパッケージが既にインストールされているため (そして、それらは Azure Notebooks にも既定で含まれるため)、"Requirement already satisfied" (要件は既に満たされています) というメッセージが何回も表示されます。 その出力はすべて視覚的な混乱を招く可能性があるので、そのセルを選択し (シングル クリックを使用)、**[Cell]\(セル\)** > **[Cell Outputs]\(セルの出力\)** > **[Toggle]\(切り替え\)** を使用して、出力を非表示にします。 その同じサブメニューの **[Clear]\(クリア\)** コマンドを使用して、出力を完全に削除することもできます。

    **[Toggle]\(切り替え\)** コマンドでは、セルから最新の出力だけが非表示になります。セルをもう一度実行すると、出力が再び表示されます。

1. パッケージはプロジェクト環境にインストールされるため、`#` を使用して `! pip install` コマンドをコメントにします。こうすると、これらのコマンドを説明資料としてノートブックに残しておくことができ、実行に時間を取られたり、不要な出力が生成されたりすることはありません。 このケースでは、コメント化したコマンドをノートブックに残しておくと、ノートブックの依存関係も示されます。

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>残りのセルを作成する

ノートブックの残りの部分を設定するには、次に一連の Markdown セルとコード セルを作成します。 以下に示す各セルについて、最初に新しいセルを作成した後、種類を設定し、内容を貼り付けます。

各セルを作成した後まで待ってからノートブックを実行することもできますが、各セルを作成しながら実行するという興味深い方法もあります。 すべてのセルで出力が表示されるわけではありません。エラーが何も表示されなければ、セルは正常に実行されたものと見なされます。

各コード セルは、それより前のセルで実行されたコードに依存し、1 つのセルの実行を忘れると、以降のセルでエラーが発生する可能性があります。 セルの実行を忘れたことがわかった場合は、現在のセルを実行する前に **[Cell]\(セル\)** > **[Run All Above]\(上のすべてを実行\)** を使用してみます。

予期しない結果が表示される場合は (きっとそうなります)、各セルが必要に応じて "コード" または "Markdown" に設定されていることを確認します。 たとえば、"Invalid syntax" (構文が無効です) エラーは、通常、コード セルに Markdown を入力したときに発生します。

1. Markdown セル:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. コード セル: 実行すると、テーブルの内容が出力として表示されます。 `print` ステートメントをコメントにすることで、出力を抑制できます。

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > このコードからは、"numpy.dtype size changed" (numpy.dtype のサイズが変わりました) という警告が表示される可能性があります。この警告は無視しても安全です。

1. Markdown セル:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. コード セル: 実行しても、このセルには出力がありません。

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markdown セル:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept`).
    ```

1. コード セル: このセルを実行すると、`LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)` という出力が表示されます。

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdown セル:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_` and `coef_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. コード セル: このセルを実行すると、`[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]` のような結果が表示されます。

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markdown セル:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markdown セル:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. コード セル: このセルを実行すると、グラフィカルなプロットが生成されます。 初めてのときにプロットが表示されない場合は (代わりに、"Figure size 640x480 with 1 Axes" (図のサイズは 640 x 480 で 1 軸) と表示されます)、セルを再度実行します。

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![matplotlib コードからのプロット出力](media/tutorial/tutorial-plot-output.png)

1. Markdown セル:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>出力をクリアしてすべてのセルを再実行する

前のセクションの手順を実行してノートブック全体の設定が済むと、線形回帰の完全なチュートリアルのコンテキストで実行するコードの両方の部分が作成されています。 このようなコードとテキストの直接的な組み合わせは、Notebooks の多くの利点の 1 つです。

ここでノートブック全体をもう一度実行してみます。

1. **[Kernel]\(カーネル\)** > **[Restart & Clear Output]\(再起動して出力をクリア\)** を選択して、カーネルのすべてのセッション データとすべてのセル出力をクリアします。 このコマンドは常に、ノートブックが完成して、コード セル間におかしな依存関係が作成されていないことを確認するだけのときに、実行するのに適しています。

1. **[Cell]\(セル\)** > **[Run All]\(すべて実行\)** を使用して、ノートブックを再実行します。 コードの実行中、カーネル インジケーターが設定されていることに注意してください。

    コードの実行が長すぎる場合、またはそれ以外でコードがスタックしている場合は、**[Kernel]\(カーネル\)** > **[Interrupt]\(中断\)** コマンドを使用してカーネルを停止できます。

1. ノートブックをスクロールして、結果を確認します。 (やはりプロットが表示されない場合は、そのセルを再実行します。)

## <a name="save-halt-and-close-the-notebook"></a>ノートブックを保存、停止、および閉じる

ノートブックを編集しているときは、**[File]\(ファイル\)** > **[Save and Checkpoint]\(保存およびチェックポイント\)** コマンドを使用するか、ツール バーの保存ボタンを使用して、現在の状態を保存できます。 "チェックポイント" では、セッション中にいつでも戻すことができるスナップショットが作成されます。 チェックポイントを使用すると、一連の実験的な変更を行い、その変更が動作しない場合でも、**[File]\(ファイル\)** > **[Revert to Checkpoint]\(チェックポイントに戻す\)** コマンドを使用するだけでチェックポイントに戻すことができます。 もう 1 つの方法は、余分なセルを作成し、実行したくないコードをコメントにすることです。どちらの方法でも機能します。

いつでも **[File]\(ファイル\)** > **[Make a Copy]\(コピーの作成\)** コマンドを使用して、ノートブックの現在の状態のコピーをプロジェクト内の新しいファイルに作成することもできます。 そのコピーは、新しいブラウザー タブで自動的に開きます。

ノートブックの作業が終わったら、**[File]\(ファイル\)** > **[Close and halt]\(閉じて停止\)** コマンドを使用します。ノートブックが閉じられて、ノートブックを実行していたカーネルがシャットダウンされます。 その後、Azure Notebooks によってブラウザーのタブが自動的に閉じられます。

## <a name="debug-notebooks-using-visual-studio-code"></a>Visual Studio Code を使用してノートブックをデバッグする

ノートブックのコード セルが期待どおりに動作しない場合は、コードのバグまたはその他の欠陥が存在する可能性があります。 ただし、`print` ステートメントを使用して変数の値を表示する以外に、一般的な Jupyter 環境ではデバッグ機能は提供されていません。

幸いにも、ノートブックの *.ipynb* ファイルをダウンロードし、Python 拡張機能を使用して Visual Studio Code で開くことができます。 この拡張機能は、コメント内に Markdown セルを保持する 1 つのコード ファイルとして、ノートブックを直接インポートします。 ノートブックをインポートした後、Visual Studio Code のデバッガーを使用して、コードのステップ実行、ブレークポイントの設定、状態の確認などを行うことができます。 次のコードを修正したら、Visual Studio Code から *.ipynb* ファイルをエクスポートして、Azure Notebooks に再度アップロードします。

詳しくは、Visual Studio Code のドキュメントの [Jupyter ノートブックのデバッグ](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook)に関するページをご覧ください。

また、[Visual Studio Code の Jupyter サポート](https://code.visualstudio.com/docs/python/jupyter-support)に関するページで、Jupyter ノートブック用の Visual Studio Code の追加機能についてもご覧ください。

## <a name="next-steps"></a>次の手順

- [サンプル ノートブックの調査](azure-notebooks-samples.md)

ハウツー記事:

- [プロジェクトの作成と複製](create-clone-jupyter-notebooks.md)
- [デバイスの構成と管理](configure-manage-azure-notebooks-projects.md)
- [ノートブック内からのパッケージのインストール](install-packages-jupyter-notebook.md)
- [スライド ショーの表示](present-jupyter-notebooks-slideshow.md)
- [データ ファイルの操作](work-with-project-data-files.md)
- [データ リソースへのアクセス](access-data-resources-jupyter-notebooks.md)
- [Azure Machine Learning Services の使用](use-machine-learning-services-jupyter-notebooks.md)
