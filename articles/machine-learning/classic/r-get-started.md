---
title: Machine Learning スタジオで R を使用する - Azure
description: この R プログラミング チュートリアルは、R での Azure Machine Learning Studio (classic) を使用した予測ソリューションの作成に必要な基本的な事柄を習得できるように作成されています。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: c619b51f9323477bda4f1ec99aeeb1bfa01028fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517741"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>R での Azure Machine Learning Studio (classic) の使用を開始する

**適用対象:** ![これはチェック マークです。つまり、この記事は Machine Learning Studio (クラシック) を対象としています。](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (クラシック)   ![これは X 印です。つまり、この記事は Azure Machine Learning を対象としています。](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

<!-- Stephen F Elston, Ph.D. -->
このチュートリアルでは、Azure Machine Learning スタジオを使用して R コードを作成、テスト、実行する方法について説明します。 最終的には、予測ソリューションが完成します。

> [!div class="checklist"]
> * データのクリーニングと変換のためのコードを作成します。
> * データセット内のいくつかの変数間の相関関係を分析します。
> * 牛乳生産用の季節性時系列予測モデルを作成します。


Machine Learning スタジオには、多くの強力な機械学習モジュールとデータ操作モジュールが含まれています。 R プログラミング言語を組み合わせて使用すると、R の柔軟性と深い分析を活用して、Machine Learning スタジオのデプロイがスケーラブルかつ容易になります。

予測は、広く採用されている役に立つ分析方法です。 その用途は、一般的に季節商品の販売予測や最適な在庫レベルの決定から、マクロ経済変数の予測にまでわたります。 予測は通常、時系列モデルで行われます。 時系列データの値には、時間インデックスがあります。 時間インデックスは、1 か月ごとや 1 分ごとなど規則的な場合があります。 時間インデックスは不規則になる場合もあります。 時系列モデルは、時系列データに基づいています。 R プログラミング言語には、時系列データ向けの柔軟なフレームワークと広範な分析機能が備わっています。

## <a name="get-the-data"></a>データを取得する

このチュートリアルでは、カリフォルニアの酪農生産と価格のデータを使用します。これには、複数の乳製品の生産と、ベンチマーク商品である乳脂肪の価格に関する月単位の情報が含まれます。

この記事で使用するデータと R スクリプトは、[MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) からダウンロードできます。 ファイル `cadairydata.csv` のデータは、ウィスコンシン大学の[乳製品市場サイト](https://dairymarkets.com)から入手可能な情報から独自に合成されました。

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Machine Learning Studio (クラシック) での R 言語の操作

このセクションでは、Machine Learning Studio (クラシック) 環境における R プログラミング言語操作の基本を見ていきます。 R 言語は、Machine Learning スタジオ環境内でカスタマイズされた分析とデータ操作のモジュールを作成するための強力なツールを提供します。

R コードの小規模な開発、テスト、デバッグを行うには、RStudio を使用します。 このコードを Machine Learning スタジオで実行するには、コードを切り取り、[Execute R Script][execute-r-script] モジュールに貼り付けます。

### <a name="the-execute-r-script-module"></a>R スクリプトの実行モジュール

Machine Learning Studio (クラシック) では、R スクリプトは [R スクリプトの実行][execute-r-script]モジュール内で実行します。 Machine Learning スタジオにおける [Execute R Script][execute-r-script] モジュールの例を、ここに示します。

 ![R プログラミング言語を示すスクリーンショット:Machine Learning スタジオで Execute R Script モジュールが選択されています。](./media/r-quickstart/fig1.png)

前の画像には、[Execute R Script][execute-r-script] モジュールを操作するための Machine Learning スタジオ環境の主要部分が示されています。

* 実験するモジュールが、中央のウィンドウに表示されています。
* 左側のペインの上部には、R スクリプトを表示、編集するために使用できるウィンドウがあります。
* 右側のペインの下部には、[Execute R Script][execute-r-script] モジュールのプロパティがいくつか表示されています。 このペインの該当する領域を選択すると、エラー ログと出力ログを表示できます。

これ以降この記事では、[Execute R Script][execute-r-script] についてより詳細に説明します。

複雑な R 関数を操作するときには、RStudio 内で編集、テスト、デバッグすることをお勧めします。 他のソフトウェア開発と同様に、コードを段階的に拡張し、小規模で単純なテスト ケースでテストします。 次に、関数を切り取り、[R スクリプトの実行][execute-r-script]モジュールの R スクリプト ウィンドウに貼り付けます。 このアプローチでは、RStudio 統合開発環境 (IDE) と Machine Learning スタジオの両方の機能を活用できます。

#### <a name="execute-r-code"></a>R コードの実行

[R スクリプトの実行][execute-r-script]モジュール内の R コードは、 **[実行]** ボタンを選択して実験を行うと、実行されます。 実行が完了すると、[[Execute R Script]][execute-r-script] アイコンにチェックマークが表示されます。

#### <a name="defensive-r-coding-for-machine-learning-studio-classic"></a>Machine Learning スタジオ向けの防御的な R コーディング

たとえば、Machine Learning スタジオを使用して Web サービス用の R コードを開発している場合、当然、コードが予期しないデータ入力や例外に対処するように計画する必要があります。 ほとんどのコードの例で示しているように、明確性を維持するために、チェックや例外処理をあまり多く含めないようにしています。 話を進める中で、R の例外処理機能を使用した関数の例をいくつか示していきます。

R 例外処理のより包括的な取り扱いが必要な場合は、「[参考資料](#appendixb)」に示した Wickham による著書の該当するセクションをご覧ください。

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Machine Learning Studio (クラシック) での R のデバッグとテスト

RStudio では小規模な R コードのテストとデバッグを行います。 [Execute R Script][execute-r-script] 自体で、R コードの問題を追跡することが必要になる場合もあります。 また、Machine Learning スタジオで結果をチェックすることもお勧めします。

Machine Learning スタジオ プラットフォームでの R コード実行の出力は、主に output.log にあります。 いくつかの追加情報は error.log にあります。

R コードを実行したときに Machine Learning スタジオでエラーが発生した場合、最初にすべきことは error.log を調べることです。 このファイルには、エラーを把握して修正するために役立つ有用なエラー メッセージが含まれています。 error.log を表示するには、エラーを含む [Execute R Script][execute-r-script] のプロパティ ペインで、 **[エラー ログの表示]** を選択します。

たとえば、未定義変数 y を持つ次の R コードを [Execute R Script][execute-r-script] モジュールで実行します。

```r
x <- 1.0
z <- x + y
```

このコードは実行に失敗し、エラー状態になります。 プロパティ ペインで、 **[エラー ログの表示]** を選択すると、次の内容が表示されます。

  ![エラー メッセージのポップアップを示すスクリーンショット。](./media/r-quickstart/fig2.png)

この場合、output.log で R のエラー メッセージを見る必要がありそうです。 [[Execute R Script]][execute-r-script] モジュールを選択して、右側のプロパティ ペインにある **[出力ログの表示]** 項目を選択します。 新しいブラウザー ウィンドウが開き、次のエラー メッセージが表示されます。

```output
[Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
---------- Start of error message from R ----------
object 'y' not found


object 'y' not found
----------- End of error message from R -----------
```

このエラー メッセージに意外性はなく、明確に問題を特定しています。

R のオブジェクトの値を調べるために、これらの値を output.log ファイルに出力できます。 オブジェクトの値を調べるためのルールは、本質的に対話型の R セッションの場合と同じです。 たとえば、変数名を行に入力すると、オブジェクトの値が output.log ファイルに出力されます。

#### <a name="packages-in-machine-learning-studio-classic"></a>Machine Learning Studio (クラシック) のパッケージ

Machine Learning スタジオには、350 を超える R 言語パッケージがプレインストールされています。 [R スクリプトの実行][execute-r-script]モジュールで次のコードを使用し、プレインストールされているパッケージの一覧を取得できます。

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

今のところ、このコードの最終行が理解できないかもしれませんが、読んでみてください。 これ以降この記事では、Machine Learning スタジオ環境での R の使用について詳しく説明します。

### <a name="introduction-to-rstudio"></a>RStudio の概要

RStudio は、広く使用されている R 向けの IDE です。このガイドで使用される R コードの一部は、RStudio を使用して編集、テスト、デバッグします。 R コードをテストして準備ができたら、RStudio エディターで切り取って、Machine Learning スタジオの [Execute R Script][execute-r-script] モジュールに貼り付けることができます。

デスクトップ コンピューターに R プログラミング言語がインストールされていない場合は、今すぐ行います。 オープンソースの R 言語の無料ダウンロードが、[包括的な R アーカイブ ネットワーク (CRAN)](https://www.r-project.org/) で入手できます。 Windows、macOS、Linux/UNIX で利用できるダウンロードが用意されています。 お近くのミラー サイトを選択し、ダウンロードの指示に従ってください。 さらに、CRAN には、多くの便利な分析とデータ操作のパッケージが用意されています。

RStudio を初めて使用する場合は、デスクトップ バージョンをダウンロードしてインストールします。 Windows、macOS、Linux/UNIX 向けの RStudio ダウンロードが [RStudio](http://www.rstudio.com/products/RStudio/) にあります。 指示に従って、RStudio をデスクトップ コンピューターにインストールします。

RStudio のチュートリアル入門書が「[Using the RStudio IDE (RStudio IDE の使用)](https://support.rstudio.com/hc/sections/200107586-Using-RStudio)」で入手できます。

RStudio の使用方法の詳細については、「[RStudio ドキュメントのガイド](#appendixa)」をご覧ください。

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>R スクリプトの実行モジュールでのデータ入出力

このセクションでは、[Execute R Script][execute-r-script] モジュールでデータを入出力する方法を説明します。 また、[Execute R Script][execute-r-script]モジュールでさまざまなデータ型の入出力を操作する方法を確認します。

このセクションの完全なコードは、[MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) にあります。

### <a name="load-and-check-data"></a>データの読み込みとチェック

#### <a name="load-the-dataset"></a><a id="loading"></a>データセットの読み込み

最初に、**csdairydata.csv** ファイルを Machine Learning スタジオに読み込みます。

1. Machine Learning スタジオ環境を起動します。
1. 画面の左下の **[+ 新規]** を選択し、 **[データセット]** を選択します。
1. **[ローカル ファイルから]** 、 **[参照]** の順に選択し、ファイルを選択します。
1. データセットの種類として **[一般的なヘッダー付き CSV ファイル (.csv)]** が選択されていることを確認します。
1. チェック マークを選択します。
1. データセットがアップロードされた後、 **[データセット]** タブを選択すると、新しいデータセットを確認できます。

#### <a name="create-an-experiment"></a>実験の作成

いくつかのデータを Machine Learning スタジオに読み込んだので、分析をするために実験を作成する必要があります。  

1. 画面の左下の **[+ 新規]** を選択し、 **[Experiments]\(実験\)**  >  **[Blank Experiment]\(空の実験\)** を選択します。
1. 実験を選択し、ページ上部のタイトル **[... に関して作成された実験]** を変更することで、実験に名前を付けます。 たとえば、「**カリフォルニア乳製品分析**」に変更します。
1. 実験ページの左側で、 **[保存済みのデータセット]**  >  **[マイ データセット]** の順に選択します。 前にアップロードした **cadairydata.csv** ファイルが表示されます。
1. **csdairydata.csv データセット** を実験にドラッグします。
1. 左側のペインの上部にある **[実験項目の検索]** ボックスで、「[Execute R Script][execute-r-script]」と入力します。 検索一覧に、モジュールが表示されます。
1. [Execute R Script][execute-r-script] モジュールを、パレットにドラッグします。
1. **csdairydata.csv データセット** の出力を、[Execute R Script][execute-r-script] の左端の入力 (**データセット 1**) に接続します。
1. **[保存]** を選択します。

この時点で、実験はこの例のように表示されます。

![データセットと Execute R Script モジュールを含むカリフォルニア酪農分析実験を示す図。](./media/r-quickstart/fig3.png)


#### <a name="check-on-the-data"></a>データのチェック

実験に読み込んだデータを調べましょう。 実験で、**cadairydata.csv データセット** の出力を選択し、 **[視覚化]** を選択します。 こちらのような結果が表示されます。

![cadairydata.csv データセットのサマリーを示すスクリーンショット。](./media/r-quickstart/fig4.png)

このビューには、有用な情報が多数表示されます。 データセットの最初の数列も表示されます。 列を選択すると、 **[統計]** セクションには列に関する詳細情報が表示されます。 たとえば、 **[機能の種類]** 行には、Machine Learning スタジオが列に割り当てたデータ型が表示されます。 重要な作業を開始する前に、このビューを確認してください。

### <a name="first-r-script"></a>最初の R スクリプト

単純な最初の R スクリプトを作成し、Machine Learning スタジオで実験を行いましょう。 次のスクリプトを、RStudio で作成し、テストしました。

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

このスクリプトを Machine Learning スタジオに転送する必要があります。 切り取って貼り付けることができますが、この場合は zip ファイルを使用して R スクリプトを転送します。

### <a name="data-input-to-the-execute-r-script-module"></a>R スクリプトの実行モジュールへのデータ入力

[R スクリプトの実行][execute-r-script]モジュールへの入力を調べましょう。 この例では、カリフォルニア酪農データを [Execute R Script][execute-r-script] モジュールに読み込みます。

[R スクリプトの実行][execute-r-script]モジュールに入力できるデータは 3 つあります。 アプリケーションに応じて、いずれか 1 つ、またはすべての入力を使用できます。 入力をまったく受け取らない R スクリプトを使用することもできます。

これらの各入力を左から右に見ていきましょう。 カーソルを入力の上に移動してツール ヒントを読むと、各入力の名前がわかります。

#### <a name="script-bundle"></a>スクリプト バンドル

スクリプト バンドル入力により、zip ファイルの内容を [Execute R Script][execute-r-script] モジュールに渡すことができます。 次のコマンドのいずれかを使用すると、zip ファイルの内容を R コードに読み込むことができます。

```r
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Machine Learning スタジオは zip ファイル内のファイルを、src/ ディレクトリに存在するものとして扱うため、ファイル名の前にこのディレクトリ名を付ける必要があります。 たとえば、zip ファイルのルートに `yourfile.R` と `yourData.rdata` というファイルが含まれている場合、`source` と `load` を使用するときに `src/yourfile.R` と `src/yourData.rdata` のようにこれらのファイルのアドレスを指定します。

データセットの読み込みについては、既に「[データセットの読み込み](#loading)」で説明しました。 前のセクションで示した R スクリプトを作成してテストした後は、次の手順を行います。

1. R スクリプトを .R ファイルに保存します。 このスクリプト ファイルの名前を **simpleplot.R** にします。 ファイルの内容は次のとおりです。

   ```r
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. zip ファイルを作成し、スクリプトをこの zip ファイルにコピーします。 Windows の場合、ファイルを右クリックし、 **[送る]**  >  **[圧縮フォルダー]** の順に選択します。 この操作により、**simpleplot.R** ファイルを含む新しい zip ファイルが作成されます。

1. ファイルを、Machine Learning スタジオの **データセット** に追加し、種類を **zip** に指定します。 データセットに zip ファイルが表示されるようになります。

1. この zip ファイルを **データセット** から **ML スタジオのキャンバス** にドラッグします。

1. **zip データ アイコン** の出力を、[R スクリプトの実行][execute-r-script]モジュールの **スクリプト バンドル** 入力に接続します。

1. [Execute R Script][execute-r-script] モジュールのコード ウィンドウに、`source()` 関数を zip ファイル名を指定して入力します。 この例では、「`source("src/simpleplot.R")`」と入力しました。

1. **[保存]** を選択します。

これらの手順の完了後に実験を実行すると、[Execute R Script][execute-r-script] モジュールが zip ファイル内の R スクリプトを実行します。 この時点で、実験はこの例のように表示されます。

![zip 形式の R スクリプトを使用する実験を示す図。](./media/r-quickstart/fig6.png)

#### <a name="dataset1"></a>データセット 1

データセット 1 入力を使用して、データの方形テーブルを R コードに渡すことができます。 このシンプルなスクリプトでは、`maml.mapInputPort(1)` 関数がデータをポート 1 から読み込みます。 これらのデータは、コード内のデータ フレーム変数名に割り当てられます。 このシンプルなスクリプトでは、最初のコード行が割り当てを実行します。

```r
cadairydata <- maml.mapInputPort(1)
```

**[実行]** ボタンを選択して、実験を実行します。 実行が完了したら、[[Execute R Script]][execute-r-script] モジュールを選択し、プロパティ ペインの **[出力ログの表示]** を選択します。 ブラウザーで新しいページが開き、output.log ファイルの内容が表示されます。 下へスクロールすると、次のような出力が表示されます。

```output
[ModuleOutput] InputDataStructure
[ModuleOutput]
[ModuleOutput] {
[ModuleOutput]  "InputName":Dataset1
[ModuleOutput]  "Rows":228
[ModuleOutput]  "Cols":9
[ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
[ModuleOutput] }
```

さらに下にスクロールすると、次のような出力で列に関する詳細情報が表示されます。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput]
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput]
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
[ModuleOutput]
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput]
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput]
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput]
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
```

これらの結果はほとんど予想どおりであり、228 個の記録と 9 個の列が含まれています。 列の名前、R データの型、各列のサンプルが表示されています。

> [!NOTE]
> [R スクリプトの実行][execute-r-script]モジュールの R デバイス出力からは、出力された同じ内容を簡単に入手できます。 次のセクションでは、[Execute R Script][execute-r-script] モジュールの出力について説明します。  

#### <a name="dataset2"></a>データセット 2

データセット 2 入力の動作は、データセット 1 と同じです。 この入力を使用すると、別の方形のデータ テーブルを R コードに渡すことができます。 このデータを渡すには、引数 2 を指定した `maml.mapInputPort(2)`関数を使用します。  

### <a name="execute-r-script-outputs"></a>R スクリプトの実行の出力

#### <a name="output-a-dataframe"></a>データフレームの出力

`maml.mapOutputPort()` 関数を使用し、結果データセット 1 ポートを通じて、R データフレームの内容を方形テーブルとして出力できます。 このシンプルな R スクリプトでは、この操作は次の行で実行されます。

```r
maml.mapOutputPort('cadairydata')
```

この実験の実行後、結果データセット 1 の出力ポートを選択し、 **[視覚化]** を選択します。 こちらのような結果が表示されます。

![カリフォルニア酪農データの出力の視覚化を示すスクリーンショット。](./media/r-quickstart/fig7.png)

この出力は、まったく予想どおりに、入力と同一になります。

### <a name="r-device-output"></a>R デバイス出力

[R スクリプトの実行][execute-r-script]モジュールのデバイス出力には、メッセージとグラフィックスの出力が含まれています。 R からの標準出力と標準エラー メッセージは両方とも、R デバイス出力ポートに送信されます。

R デバイス出力を表示するには、ポートを選択して、 **[視覚化]** を選択します。 ここでは、R スクリプトからの標準出力と標準エラーが表示されます。

![R デバイス ポートからの標準出力と標準エラーを示すスクリーンショット。](./media/r-quickstart/fig8.png)

下へスクロールすると、R スクリプトからのグラフィックス出力が表示されます。

![R デバイス ポートからのグラフィックス出力を示すスクリーンショット。](./media/r-quickstart/fig9.png)

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>データのフィルター処理と変換

このセクションでは、カリフォルニア酪農データに対して、基本的なフィルター処理と変換操作を行います。 このセクションの終了までに、データを分析モデルの構築に適した形式にします。

具体的には、このセクションで一般的なデータ クリーニングと変換タスク (型の変換、データフレームのフィルター処理、新しい計算列の追加、値の変換) を行います。 このような背景情報は、現実の問題で直面するさまざまなバリエーションに対処するのに役立ちます。

このセクションの完全な R コードは、[MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) で入手できます。

### <a name="type-transformations"></a>型の変換

カリフォルニア酪農データを [R スクリプトの実行][execute-r-script]モジュールの R コードに読み込めるようになったため、列のデータが目的の型と形式になっていることを確認する必要があります。

R は動的に型を変更できる言語なので、必要に応じてデータ型を別の型に変換できます。 R の atomic データ型には、数値、論理値、文字があります。 因子型は、カテゴリ化されたデータをコンパクトに格納するために使用されます。 データ型の詳細については、「[参考資料](#appendixb)」にある文献をご覧ください。

外部ソースから表形式データを R に読み込む際には、最終的な列の型をチェックすることお勧めします。 文字型の列を求めているのに列が因子型として表示される場合やその逆がよくあります。 別のケースでは、数値を想定していた列が文字データとして表示されることがあります (例: 浮動小数点の 1.23 ではなく "1.23" と表示される)。

幸いにも、マッピングが可能であれば、ある型から別の型に変換するのは簡単です。 たとえば、"Nevada" を数値に変換することはできませんが、因子 (カテゴリ別変数) に変換することは可能です。 別の例として、数字の 1 を文字の "1"、または因子に変換することができます。

これらすべての変換の構文 `as.datatype()`はシンプルです。 これらの型変換関数には、次の関数があります。

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

前のセクションで入力した列のデータ型を調べると、すべての列は数値型になります。 例外は、"Month" というラベルが付いた列で、これは文字型です。 この型を因子に変換し、結果をテストしましょう。

散布図マトリックスを作成した行を削除し、Month 列を因子に変換する行を追加しました。 この実験では、R コードを切り取り、[Execute R Script][execute-r-script] モジュールのコード ウィンドウに貼り付けます。 zip ファイルを更新し、それを Machine Learning スタジオにアップロードすることもできますが、このオプションにはいくつかの手順が必要になります。

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

このコードを実行して、R スクリプトの出力ログを確認してください。 ログの関連するデータを次に示します。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Month の型は、**Factor w/ 14 levels** になりました。 1 年には 12 か月しかないため、この型は問題です。 また、結果データセット ポートの **[視覚化]** における型が **Categorical** であることもチェックできます。

問題は、Month 列が体系的にコード化されなかったことです。 ある月が April と呼ばれる場合もあれば、Apr と省略される場合もあります。この問題は、文字列を 3 文字にトリミングすることで解決できます。 コード行は、次の例のようになります。

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

実験を再実行し、出力ログを確認します。 予想される結果を次に示します。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


因子変数が、希望する 12 レベルになりました。

### <a name="basic-dataframe-filtering"></a>基本データフレームのフィルター処理

R データフレームは、強力なフィルター処理機能をサポートします。 データセットは、行または列のいずれかに対して、論理フィルターを使用してサブセット化できます。 多くの場合、複雑なフィルター条件が必要になります。 データフレームのフィルター処理に関する幅広い例については、「[参考資料](#appendixb)」にある文献をご覧ください。

このデータセットに適用する必要があるフィルター処理はわずかです。 cadairydata データフレームの列を調べると、2 つの不要な列があるのがわかります。 最初の列には行番号が含まれ、あまり役に立ちません。 二番目の Year.Month 列には、重複する情報が含まれています。 次の R コードを使用して、これらの列を簡単に除外できます。

> [!NOTE]
> これからこのセクションで、[Execute R Script][execute-r-script] モジュールに追加するコードを示します。 `str()` 関数の "*前*" に、新しく各行を追加します。 この関数を使用し、Machine Learning スタジオで結果を検証します。

次の行を、[Execute R Script][execute-r-script]モジュールの R コードに追加します。

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

実験でこのコードを実行し、出力ログの結果を確認します。 これらの結果を次に示します。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  7 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

期待した結果が得られました。

### <a name="add-a-new-column"></a>新しい列の追加

時系列モデルを作成するには、時系列の開始時点からの月を含む列を用意するのが便利です。 新しい列 Month.Count を作成します。

コードを整理するために、最初の単純な関数 `num.month()` を作成します。 次に、この関数を適用し、データフレームに新しい列を作成します。 新しいコードは、次のとおりです。

```r
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

更新した実験を実行し、出力ログで結果を確認します。 これらの結果をこちらに示します。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


すべてがうまく機能しているようです。 新しい列には、データフレームの期待する値が含まれています。

### <a name="value-transformations"></a>値の変換

このセクションでは、データフレームのいくつかの列に含まれる値に対して、いくつかの単純な変換を実行します。 R 言語は、ほとんどの値の変換をサポートします。 他の例については、「[参考資料](#appendixb)」にある文献をご覧ください。

データフレームのサマリーの値を調べると、何かおかしいことに気づきます。 カリフォルニアでは、牛乳よりもアイスクリームを多く生産しているのでしょうか。 いいえ、もちろんそうではありません。 問題は、単位が異なることです。 価格は米国ポンド単位であり、牛乳は 100 万米国ポンド単位ですが、アイスクリームは 1,000 米国ガロン単位です。また、カッテージ チーズは、1,000 米国ポンド単位です。 アイスクリームの重量を 1 ガロンあたり 6.5 ポンドと想定すると、簡単に掛け算を行い、すべてが 1,000 ポンド単位になるように、これらの値を変換できます。

予測モデルでは、これらのデータの傾向分析と季節的調整のために乗法モデルを使用します。 対数変換により、線形モデルを使用して、このプロセスを簡略化できます。 乗数が適用される同じ関数で、対数変換を適用できます。

次のコードで、新しい関数 `log.transform()`を定義し、それを数値を含む行に適用します。 R の `Map()` 関数は、データフレームの選択した列に `log.transform()` 関数を適用するために使用されます。 `Map()` 関数は `apply()` と類似していますが、この関数には複数の引数リストを使用できます。 乗数のリストは、 `log.transform()` 関数に第二の引数を提供することに注意してください。 `na.omit()` 関数は、データフレームに不足している値や、未定義の値がないように少しクリーンアップするために使用されます。

```r
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warning message to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

`log.transform()` 関数ではさまざまなことが起こります。 このコードのほとんどは、引数に関して発生し得る問題をチェックするか、計算処理時に発生し得る例外に対処します。 このコードの少数の行のみが、計算処理を行います。

防御的なプログラミングの目的は、処理の継続を妨げる単一関数のエラーを防ぐことです。 長時間実行している分析が突然エラーになると、ユーザーはかなりの苛立ちを感じます。 この状況を防ぐには、下流処理に対するダメージを限定する既定の戻り値を選択する必要があります。 また、何か問題があることをユーザーに警告するメッセージが生成されます。

防御的なプログラミングを R で使用したことがない場合、このコードのすべてが過剰に思えるかもしれません。 主な手順を確認してみましょう。

1. 4 つのメッセージのベクトルが定義されます。 これらは、このコードで発生し得るエラーと例外に関する情報を通知するために使用されます。
1. 各ケースで値 NA を返します。 副作用がより少ないその他の選択肢も多数存在します。 たとえば、ゼロ ベクトル、または元の入力ベクトルを返すこともできます。
1. 関数の引数に関するチェックが実行されます。 各ケースでエラーが検出された場合、`warning()` 関数により既定値が戻され、メッセージが生成されます。 現在、`warning()` を、`stop()` の代わりに使用しているのは、後者は実行を終了することになるためです。これは、まさに避けようとしていることです。 このコードは手続き型のスタイルで記述されていますが、これはこの場合、関数型アプローチでは複雑で曖昧に思えたからです。
1. 例外によって処理が突然中断しないように、対数計算は `tryCatch()` の中に含まれています。 `tryCatch()` を使用しない場合、R 関数により発生したほとんどのエラーは、停止信号になっていしまい、処理が中断します。

実験内でこの R コードを実行し、output.log ファイルに出力された内容を調べます。 こちらに示すように、ログで変換後の 4 列の値を確認できます。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

値が変換されたことが確認できます。 現在、牛乳の生産が他のすべての乳製品の生産を大きく超えていますが、対数スケールで見ていることに注意してください。

この時点でデータはクリーンアップされ、モデリングの準備が整いました。 [Execute R Script][execute-r-script] モジュールの結果データセット出力に関する視覚化サマリーを調べると、意図していたように、Month 列は Categorical であり、12 個の一意値があることがわかります。

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>時系列オブジェクトと相関関係分析

このセクションでは、いくつかの基本的な R 時系列オブジェクトについて調べ、変数間の相関関係を分析します。 目標は、いくつかの時間差におけるペアワイズ相関情報を含むデータフレームを出力することです。

このセクションの完全な R コードは、[MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) にあります。

### <a name="time-series-objects-in-r"></a>R の時系列オブジェクト

既に述べたように、時系列データは、時間インデックスを持つ一連のデータ値です。 R 時系列オブジェクトは、時間インデックスを作成し管理するために使用されます。 時系列オブジェクトの使用には、いくつかの利点があります。 時系列オブジェクトにより、オブジェクトにカプセル化されている時系列インデックス値のさまざまな細かな管理から解放されます。 さらに、時系列オブジェクトにより、さまざまな時系列メソッドを使用して、プロット、印刷、モデリングなどを行えます。

一般的には、比較的シンプルな POSIXct 時系列クラスが使用されます。 この時系列クラスは、エポックの開始 (1970 年 1 月 1 日) からの時間を測定します。 この例では、POSIXct 時系列オブジェクトを使用します。 広範に使用されるその他の R 時系列オブジェクト クラスには、zoo や xts など (広範な時系列) があります。

### <a name="time-series-object-example"></a>時系列オブジェクトの例

それでは、例の操作を開始しましょう。 新しい [Execute R Script][execute-r-script] モジュールを実験にドラッグします。 既存の [R スクリプトの実行][execute-r-script]モジュールの結果データセット 1 出力ポートを、新しい [R スクリプトの実行][execute-r-script]モジュールのデータセット 1 入力ポートに接続します。

最初の例で行ったように、例を通じて見ていきます。 いくつかの時点で、各ステップでの R コードの増分行のみを表示します。

#### <a name="read-the-dataframe"></a>データフレームを読み取る

最初の手順として、データフレームを読み込み、期待する結果が得られることを確認しましょう。 次のコードで、ジョブを実行します。

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

では、実験を実行します。 新しい Execute R Script の形式のログは、こちらの例のようになります。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
```

このデータは、期待した型と形式です。 ここで、Month 列は因子型で、期待したレベル数になっています。

#### <a name="create-a-time-series-object"></a>時系列オブジェクトを作成する

時系列オブジェクトをデータフレームに追加する必要があります。 現在のコードを、POSIXct クラスの新しい列を追加する次のコードに置き換えます。

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

ここでログを確認します。 これは、こちらの例のようになります。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

このサマリーから、新しい列が実際に POSIXct クラスであることがわかります。

### <a name="explore-and-transform-the-data"></a>データを調べて変換する

データセットのいくつかの変数を調べてみましょう。 散布図マトリックスは、簡単な外観を作成するのに適した方法です。 以前の R コードの `str()` 関数を、次の行で置き換えます。

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

このコードを実行し、何が起こるかを確認します。 R デバイス ポートで生成されたプロットは、こちらの例のように表示されます。

![選択された変数の散布図マトリックスを示すスクリーンショット。](./media/r-quickstart/fig17.png)

これらの変数間の関係には、外見上おかしな構造体がいくつかあります。 この構造体はおそらく、データの傾向や、変数を標準化していないことから生じています。

### <a name="correlation-analysis"></a>相関関係分析

相関関係分析を実行するには、変数の傾向除去と標準化の両方を行う必要があります。 単純に、変数の中心化とスケールの両方を行う `scale()` 関数を使用します。 この関数は、非常に高速に実行できます。 とはいえ、R での防御的プログラミングの例を見てみましょう。

次の `ts.detrend()` 関数は、これらの操作を両方行います。 次の 2 つのコード行は、データの傾向除去を行い、値を標準化します。

```r
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

`ts.detrend()` 関数ではさまざまなことが起こります。 このコードのほとんどは、引数に関して発生し得る問題をチェックするか、計算処理時に発生し得る例外に対処します。 このコードの少数の行のみが、実際に計算処理を行います。

防御的プログラミングの例については、既に「値の変換」で説明しました。 両方の計算ブロックは、 `tryCatch()`に含まれています。 一部のエラーでは、元の入力ベクトルを返すことが理にかなっています。 それ以外の場合は、0 のベクトルを返します。

傾向除去で使用する線形回帰は、時系列回帰であることに注意してください。 予測変数は、時系列オブジェクトです。

`ts.detrend()` を定義した後、それをデータフレームの対象の変数に適用します。 `lapply()` の結果として作成されたリストを、`as.data.frame()` を使用してデータフレーム内のデータに強制的に変換する必要があります。 `ts.detrend()` の防御的な側面により、変数の 1 つの処理でエラーが発生しても、その他の変数を正しく処理できます。

最後のコード行は、ペアワイズ散布図を作成します。 R コードの実行結果の散布図をこちらに示します。

![傾向除去され標準化された時系列のペアワイズ散布図を示すスクリーンショット。](./media/r-quickstart/fig18.png)

これらの結果を、前の例で示した結果と比較できます。 変数が傾向除去され標準化されると、変数間の関係の構造体が非常に少なくなります。

R ccf オブジェクトとしての相関関係を計算するコードは、次のとおりです。

```r
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

このコードを実行すると、こちらに示すログが生成されます。

```output
[ModuleOutput] Loading objects:
[ModuleOutput]   port1
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] [[1]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.148 0.358 0.317 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[2]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.395 -0.186 -0.238 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[3]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.059 -0.089 -0.127 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[4]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.140 0.294 0.293 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[5]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.002 -0.074 -0.124 
```

各時間差には、相関関係値があります。 これらの相関関係値には、十分に有意な大きなものはありません。 各変数を個別にモデル化できると結論付けることができます。

### <a name="output-a-dataframe"></a>データフレームの出力
ペアワイズ相関を R ccf オブジェクトのリストとして計算しました。 この場合、結果データセット出力ポートが実際にデータフレームを要求するという小さな問題が発生します。 さらに、ccf オブジェクト自体がリストであり、このリストの最初の要素内の値、つまりさまざまな時間差における相関関係のみを必要としています。

次のコードは、それ自体がリストである ccf オブジェクトのリストから時間差値を抽出します。

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation dataframe and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

最初のコード行は少しわかりにくく、説明があれば理解するのに役立つかもしれません。 次に、操作を詳しく説明します。

1. 引数が **1** の演算子 **[[** を指定すると、ccf オブジェクト リストの最初の要素からの時間差における相関関係のベクトルが選択されます。
1. `do.call()` 関数は、`lapply()` によるリスト応答の要素に、`rbind()` 関数を適用します。
1. `data.frame()` 関数は、`do.call()` により生成された結果を、データフレームに変換します。

行名は、データフレームの列内にあることに注意してください。 そうすることで、行名が [Execute R Script][execute-r-script] からの出力の場合に、保存されます。

このコードを実行すると、結果データセット ポートの出力を表示する **[視覚化]** を選択したときに、こちらに示す出力が生成されます。 行名は、意図したとおり、最初の列になっています。

![相関関係分析による結果出力を示すスクリーンショット。](./media/r-quickstart/fig20.png)

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>時系列の例:季節的予測

現在データは、分析に適したフォームになっており、変数間に有意な相関関係はないと判断しています。 先に進み、時系列予測モデルを作成しましょう。 このモデルを使用し、2013 年の 12 か月のカリフォルニアにおける牛乳生産を予測します。

この予測モデルには、傾向コンポーネントと季節コンポーネントの 2 つのコンポーネントがあります。 これら 2 つのコンポーネントの成果により、完全な予測が可能になります。 このタイプのモデルは、乗法モデルと呼ばれます。 それに代わるものが、加法モデルです。 この分析を容易にするために、対象の変数に既に対数変換を適用しています。

このセクションの完全な R コードは、[MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) にあります。

### <a name="create-the-dataframe-for-analysis"></a>分析用のデータフレームを作成する

最初に、新しい [Execute R Script][execute-r-script] モジュールを実験に追加します。 既存の R スクリプトの実行モジュールの [結果データセット][execute-r-script]出力を、新しいモジュールの **データセット 1** 入力に接続します。 結果はこちらの例のようになります。

![新しい Execute R Script モジュールが追加された実験を示す図。](./media/r-quickstart/fig21.png)

完了した相関関係分析と同様に、POSIXct 時系列オブジェクトを含む列を追加する必要があります。 次のコードでは、列が追加されます。

```r
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

このコードを実行し、ログを確認します。 結果は、こちらの例のようになります。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

この結果で、分析を開始する準備が整いました。

### <a name="create-a-training-dataset"></a>トレーニング データセットの作成

構築されたデータフレームを使用して、トレーニング データセットを作成する必要があります。 このデータはテスト データセットであり、2013 年の 12 か月を除くすべての記録が含まれます。 次のコードはデータフレームをサブセット化し、酪農生産のプロットと価格変数を作成します。 次に、4 つの生産のプロットと価格変数を作成します。 匿名関数を使用してプロット用の引数を定義し、 `Map()`を使用して他の 2 つの引数のリストを順に処理します。 ここで、for ループで適切に機能しただろうと考えるのは正しいことです。 ただし、R は関数型言語なので、ここでは関数型のアプローチを示します。

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

このコードを実行すると、こちらに示す R デバイス出力から一連の時系列プロットが生成されます。 時間軸は日単位であり、これは時系列プロット手法の利点の 1 つです。

![カリフォルニア酪農生産の最初の時系列プロットと価格データ](./media/r-quickstart/unnamed-chunk-161.png)

![カリフォルニア酪農生産の 2 番目の時系列プロットと価格データ](./media/r-quickstart/unnamed-chunk-162.png)

![カリフォルニア酪農生産の 3 番目の時系列プロットと価格データ](./media/r-quickstart/unnamed-chunk-163.png).

![カリフォルニア酪農生産の 4 番目の時系列プロットと価格データ](./media/r-quickstart/unnamed-chunk-164.png)

### <a name="a-trend-model"></a>傾向モデル

時系列オブジェクトを作成し、データを調べたので、カリフォルニア牛乳生産データの傾向モデルを構築しましょう。 時系列回帰を使用できます。 そのプロットでは、トレーニング データで観測した傾向を正確にモデル化するために、複数の傾きと切片が必要であることは明らかです。

小規模なデータの場合は、RStudio で傾向モデルを構築し、結果として得られたモデルを切り取って Machine Learning スタジオに貼り付けます。 RStudio は、このタイプの対話型分析向けの対話型環境を提供します。

最初の試みとして、最大指数 3 で多項式回帰を試します。 実際には、この種類のモデルには過剰適合の危険があります。 高次項を使用しないことをお勧めします。 `I()` 関数では、コンテンツの変換が禁止されているため (つまり、コンテンツがそのまま解釈されているため)、回帰方程式内で文字どおりに解釈された関数を記述できます。

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

この関数は、次の出力を生成します。

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12667 -0.02730  0.00236  0.02943  0.10586
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0418 on 212 degrees of freedom
## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16
```

この出力の P 値 (`Pr(>|t|)`) から、2 次の項が有意でない場合があることがわかります。 `update()` 関数を使用し、2 次の項を除去してモデルを変更します。

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

この関数は、次の出力を生成します。

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12597 -0.02659  0.00185  0.02963  0.10696
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0417 on 213 degrees of freedom
## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16
```

この出力は改善されているようです。 すべての項が有意です。 値 2e-16 は既定値であり、あまり深刻に考える必要はありません。  

サニティ テストとして、傾向曲線を示すカリフォルニア酪農生産データの時系列プロットを作成しましょう。 モデルとプロットを作成するために、Machine Learning スタジオの [Execute R Script][execute-r-script] モデル (RStudio ではない) に、次のコードを追加しました。 結果を次の例に示します。

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![傾向モデルを示すカリフォルニア牛乳生産データ](./media/r-quickstart/unnamed-chunk-18.png)

傾向モデルは、このデータに非常によく適合しているようです。 さらに、モデル曲線のおかしな動きなど、過剰適合の形跡も見られません。

### <a name="seasonal-model"></a>季節モデル

傾向モデルの作成を続行し、季節的影響を含める必要があります。 線形モデルのダミー変数として 1 年の中の月を使用し、月ごとの影響を取得します。 因子変数をモデルに導入する際に、切片を計算してはなりません。 そうしないと、数式が互いに矛盾する条件を含んだ状態になり、R は必要な因子の 1 つを除去し、切片項を保持します。

満足のいく傾向モデルがあるため、`update()` 関数を使用して、新しい項を既存のモデルに追加できます。 update 式の -1 は、切片項を除去します。 しばらく、RStudio での処理が続きます。

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

この関数は、次の出力を生成します。

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.06879 -0.01693  0.00346  0.01543  0.08726
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0263 on 202 degrees of freedom
## Multiple R-squared:     1,    Adjusted R-squared:     1
## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16
```

モデルに切片項がなく、12 個の有意な月因子があることがわかります。 まさにこの結果が、希望していたものです。

別のカリフォルニア酪農生産データの時系列プロットを作成し、季節モデルがうまく機能することを確認します。 モデルとプロットを作成するために、Machine Learning スタジオの [Execute R Script][execute-r-script] に次のコードを追加しました。

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

このコードを Machine Learning スタジオで実行すると、こちらに示すプロットが生成されます。

![季節的影響を含むカリフォルニア牛乳生産のモデル](./media/r-quickstart/unnamed-chunk-20.png)

こちらの例のデータへの適合は、かなり有望なものです。 傾向と季節的影響 (月ごとの変化) の両方が、妥当と思われます。

このモデルに対するもう 1 つのチェックとして、残差を調べましょう。 次のコードは、2 つのモデルから予測値を計算し、季節モデルの残差を計算して、トレーニング データのこれらの残差をプロットします。

```r
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

残差のプロットをこちらに示します。

![トレーニング データの季節モデルの残差。](./media/r-quickstart/unnamed-chunk-21.png)

これらの残差は妥当と思われます。 このモデルであまり考慮していない 2008 ～ 2009 年の景気後退の影響を除き、特別な構造体はありません。

この例に示すプロットは、残差における時間依存のパターンの検出に役立ちます。 残差の計算とプロットで使用した明示的なアプローチでは、残差を時間順にプロットに配置します。 `milk.lm$residuals` をプロットした場合、プロットは時間順になりません。

また、 `plot.lm()` を使用して、一連の診断プロットを生成することもできます。

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

このコードは、次の例に示す一連の診断プロットを生成します。

![季節モデルの最初の診断プロット。](./media/r-quickstart/unnamed-chunk-221.png)

![季節モデルの 2 番目の診断プロット。](./media/r-quickstart/unnamed-chunk-222.png)

![季節モデルの 3 番目の診断プロット。](./media/r-quickstart/unnamed-chunk-223.png)

![季節モデルの 4 番目の診断プロット。](./media/r-quickstart/unnamed-chunk-224.png)

これらのプロットではいくつかの影響の大きな点が識別されましたが、あまり心配する必要はありません。 さらに、標準の Q-Q プロットから、残差が、線形モデルの重要な前提である正規分布に近いことがわかります。

### <a name="forecasting-and-model-evaluation"></a>予測とモデルの評価

この例を完了するために、もう 1 つだけやることがあります。 予測を計算し、実際のデータに対する誤差を測定する必要があります。 予測は、2013 年の 12 か月に対して行います。 この予測と、トレーニング データセットの一部ではない実際のデータとの誤差測定を計算できます。 さらに、トレーニング データの 18 年のパフォーマンスとテスト データの 12 か月のパフォーマンスとを比較することもできます。

時系列モデルのパフォーマンスを測定するために、さまざまな指標が使用されます。 この場合、二乗平均平方根 (RMS) 誤差を使用します。 次の関数は、2 つの時系列間の RMS 誤差を計算します。

```r
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

「値の変換」セクションで説明した `log.transform()` 関数と同様に、この関数には非常に多くのエラー チェック コードと例外復旧コードがあります。 採用されている原則は同じです。 作業は、 `tryCatch()`に含まれている 2 つの箇所で行います。 最初に、これまで値の対数を処理してきたため、時系列を累乗します。 次に、実際の RMS 誤差を計算します。

RMS 誤差を測定する関数を準備したので、ビルドを行い、RMS 誤差を含むデータフレームを出力しましょう。 傾向モデルのみの項、および季節因子を持つ完全なモデルの項を含めます。 次のコードは、これまで構築した 2 つの線形モデルを使用して、ジョブを実行します。

```r
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

このコードを実行すると、結果データセット出力ポートに、こちらに示す出力が生成されます。

![モデルの RMS 誤差の比較を示すスクリーンショット。](./media/r-quickstart/fig26.png)

これらの結果から、季節因子をモデルに追加すると、RMS 誤差が大幅に少なくなることがわかります。 それほど意外ではありませんが、トレーニング データの RMS 誤差は、予測よりもわずかに少なくなっています。

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>RStudio ドキュメント ガイド

RStudio はドキュメントが整っています。 ここでは、使用を開始するための RStudio ドキュメントの重要なセクションに対するリンクをいくつか示します。

* **プロジェクトの作成**:RStudio を使用して、R コードをプロジェクトに編成して管理できます。 詳細については、「[プロジェクトの使用](https://support.rstudio.com/hc/articles/200526207-Using-Projects)」をご覧ください。 これらの手順に従って、この記事の R コードの例に関するプロジェクトを作成します。
* **R コードの編集と実行**:RStudio は、R コードを編集し実行するための統合環境を提供します。 詳細については、「[コードの編集と実行](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code)」をご覧ください。
* **Debug**: RStudio には、強力なデバッグ機能が搭載されています。 デバッグ機能の詳細については、「[RStudio でのデバッグ](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio)」をご覧ください。 ブレークポイントのトラブルシューティング機能の詳細については、「[Breakpoint Troubleshooting (ブレークポイントのトラブルシューティング)](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)」を参照してください。

## <a name="further-reading"></a><a id="appendixb"></a>参考資料

この R プログラミングのチュートリアルでは、Machine Learning スタジオで R 言語を使用するために必要な基本事項について説明します。 R に慣れていない場合、CRAN に 2 つの入門書が用意されています。

* Emmanuel Paradis による『[R for Beginners](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf)』は入門書として適しています。
* 『[An Introduction to R](https://cran.r-project.org/doc/manuals/R-intro.html)』(W. Venables ほか) では、もう少し詳しく説明されています。

R の使用を開始するのに役立つ書籍は、多数存在します。

* Norman Matloff による『**The Art of R Programming: A Tour of Statistical Software Design**』は、R のプログラミングに関する優れた入門書です。
* Paul Teetor による『**R Cookbook**』は、R の使用に関する問題と解決のアプローチが記載されています。
* Robert Kabacoff による『**R in Action**』はもう 1 つの有用な入門書です。 手引書 [Quick R Web サイト](https://www.statmethods.net/)は有効なリソースです。
* Patrick Burns による『**R Inferno**』は、R でのプログラミングで発生する可能性のあるいくつかの厄介で困難なトピックに対処している、驚くほどユーモアにあふれた本です。この本は、「[The R Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/)」で無料で入手できます。
* Hadley Wickham による『**Advanced R**』では、R の高度なトピックが詳しく説明されています。この本のオンライン版は、「[Advanced R](http://adv-r.had.co.nz/)」で無料で入手できます。
* Paul Cowpertwait と Andrew Metcalfe による書籍『**Introductory Time Series with R**』は、時系列分析で R を使用する際の入門書です。 さらに多くの理論的テキストが、R の例を提供しています。

役に立つインターネット リソースをいくつか紹介します。

* 「[CRAN タスク ビュー:: 時系列分析](https://cran.r-project.org/web/views/TimeSeries.html)」には R 時系列分析パッケージのカタログがあります。 特定の時系列オブジェクト パッケージの情報については、そのパッケージのドキュメントをご覧ください。
* 「[R の概要](https://www.datacamp.com/courses/introduction-to-r)」は、DataCamp の無料の対話型コースです。ブラウザーでのビデオ レッスンとコーディング演習を通して R を学べます。 最新の R のテクニックとパッケージに関する対話型チュートリアルがあります。
* DataMentor が提供している「[Learn R Programming, The Definitive Guide](https://www.datamentor.io/r-programming/)」
* 「[R CODER](https://r-coder.com/)」には、詳細な R のチュートリアルと初心者向けの無料の R のコースがあります。
* クラークソン大学の Kelly Black による「[R チュートリアル](https://www.cyclismo.org/tutorial/R/)」は、手軽なチュートリアルです。
* 「[データ スキルを向上させる R 言語の主要なリソース](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html)」には、60 を超える R のリソースが掲載されています。

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
