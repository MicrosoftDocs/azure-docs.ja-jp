---
title: Linux Data Science Virtual Machine の使用方法について説明します。
titleSuffix: Azure
description: Linux データ サイエンス VM を使用して、いくつかの一般的なデータ サイエンス タスクを実行する方法。
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
editor: cgronlun
ms.custom: seodec18
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: vijetaj
ms.openlocfilehash: 95d128a7af60a1504d05fb5316969fccc49c397f
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574967"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Azure での Linux データ サイエンス仮想マシンを使用したデータ サイエンス
このチュートリアルでは、Linux データ サイエンス VM を使用して、いくつかの一般的なデータ サイエンス タスクを実行する方法を示します。 Linux データ サイエンス仮想マシン (DSVM) は Azure で使用できる仮想マシン イメージであり、データ分析と機械学習で一般的に使用されているいくつかのツールがプレインストールされています。 主なソフトウェア コンポーネントは、トピック「 [Linux データ サイエンス仮想マシンのプロビジョニング](linux-dsvm-intro.md) 」にまとめられています。 この VM イメージを使うと、各ツールを個別にインストールして構成する必要がないため、データ サイエンスを数分で簡単に開始できます。 VM は、必要に応じて簡単にスケールアップし、使用しないときには停止できます。 したがって、このリソースは弾力性があるうえに、コスト効率が優れています。

このチュートリアルで説明するデータ サイエンス タスクは、「 [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)」で説明されている手順に従います。 このプロセスは、データ サイエンティストのチームがインテリジェント アプリケーションの構築ライフサイクルにわたって効果的に共同作業できるようにする体系的なアプローチをデータ サイエンスにもたらします。 また、個人が従うことができるデータ サイエンスの反復的なフレームワークも提供します。

このチュートリアルでは、[spambase](https://archive.ics.uci.edu/ml/datasets/spambase) データセットを分析します。 このデータセットは、スパムまたはハム (スパムではないメール) としてマークされた一連のメールです。メールの内容に関する統計情報も含まれています。 含まれている統計情報については、次の次のセクションで説明します。

## <a name="prerequisites"></a>前提条件
Linux データ サイエンス仮想マシンを使用する前に、次を用意する必要があります。

* **Azure サブスクリプション**。 Azure サブスクリプションがない場合は、「 [無料の Azure アカウントを今すぐ作成しましょう](https://azure.microsoft.com/free/)」をご覧ください。
* [**Linux データ サイエンス VM**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm)。 この VM のプロビジョニング方法については、「 [Linux データ サイエンス仮想マシンのプロビジョニング](linux-dsvm-intro.md)」をご覧ください。
* [X2Go](https://wiki.x2go.org/doku.php) がコンピューターにインストールされており、XFCE セッションが開かれている。 **X2Go クライアント**のインストールと構成については、「[X2Go クライアントのインストールと構成](linux-dsvm-intro.md#installing-and-configuring-x2go-client)」をご覧ください。
* スクロールをスムーズにするために、VM の FireFox ブラウザーで about:config の gfx.xrender.enabled フラグを切り替えます。 詳細については、[こちら](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/)を参照してください。 *mousewheel.enable_pixel_scrolling* を False に切り替えることも検討してください。 手順については、[こちら](https://support.mozilla.org/questions/981140)を参照してください。
* **AzureML アカウント**。 AzureML アカウントがない場合は、 [AzureML ホームページ](https://studio.azureml.net/)で新しいアカウントにサインアップしてください。 開始する際に役立つ Free レベルがあります。

## <a name="download-the-spambase-dataset"></a>spambase データセットをダウンロードする
[spambase](https://archive.ics.uci.edu/ml/datasets/spambase) データセットは、比較的小さく、4601 例しか含まれていません。 このサイズでは、リソース要件が中程度に抑えられるため、データ サイエンス VM の主要な機能をいくつか示す際に便利です。

> [!NOTE]
> このチュートリアルは、D2 v2 サイズの Linux Data Science Virtual Machine (CentOS Edition) で作成されました。 このサイズの DSVM でこのチュートリアルの手順を処理できます。
>
>

ストレージ領域がもっと必要な場合は、追加のディスクを作成し、VM に接続できます。 これらのディスクでは永続的な Azure Storage を使用します。そのため、サーバーがサイズ変更のために再プロビジョニングされた場合や、シャットダウンされた場合でも、データが保持されます。 ディスクを追加し、VM に接続するには、「[Linux VM へのディスクの追加](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」の手順に従ってください。 次の手順では、Azure コマンド ライン インターフェイス (Azure CLI) を使用します。Azure CLI は、DSVM に既にインストールされています。 そのため、これらの手順をすべて VM 自体から実行できます。 ストレージを増やす方法として、[Azure Files](../../storage/files/storage-how-to-use-files-linux.md) を使用することもできます。

データをダウンロードするには、ターミナル ウィンドウを開き、次のコマンドを実行します。

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

ダウンロードされたファイルにはヘッダー行がありません。そのため、ヘッダーのある別のファイルを作成します。 次のコマンドを実行して、適切なヘッダーを含むファイルを作成します。

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

次のコマンドで、2 つのファイルを連結します。

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

このデータセットには、各メールに関する何種類かの統計情報が含まれています。

* ***word\_freq\_WORD*** という列は、メール内の全単語のうち *WORD* と一致する単語の割合を示しています。 たとえば、*word\_freq\_make* が 1 の場合、メール内の全単語の 1% が *make* です。
* ***char\_freq\_CHAR*** という列は、メール内の全文字のうち *CHAR* と一致する文字の割合を示しています。
* ***capital\_run\_length\_longest*** は、連続する大文字の最大の長さを示しています。
* ***capital\_run\_length\_average*** は、連続する大文字の平均の長さを示しています。
* ***capital\_run\_length\_total*** は、連続する大文字すべての合計の長さを示しています。
* ***spam*** は、メールがスパムと判断されたかどうかを示しています (1 = スパム、0 = スパムではない)。

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Microsoft R Open でデータセットを探索する
R を使って、データを確認し、基本的な機械学習を実行してみましょう。データ サイエンス VM には、[Microsoft R Open](https://mran.revolutionanalytics.com/open/) がプレインストールされています。 このバージョンの R のマルチスレッドの数式ライブラリは、さまざまなシングルスレッド バージョンよりもパフォーマンスが優れています。 また、Microsoft R Open は、CRAN パッケージ リポジトリのスナップショットを使用することで、再現性を保証します。

このチュートリアルで使用するコード サンプルのコピーを入手するには、VM にプレインストールされている git を使用して、 **Azure-Machine-Learning-Data-Science** リポジトリを複製します。 git コマンド ラインから次を実行します。

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

ターミナル ウィンドウを開き、R の対話型コンソールを使用して、新しい R セッションを開始するか、コンピューターにプレインストールされている RStudio を使用します。


データをインポートし、環境を設定するには、次を実行します。

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

各列に関する概要統計情報を表示するには、次を実行します。

    summary(data)

データの別のビューを表示するには、次を実行します。

    str(data)

実行すると、各変数の型とデータセット内の最初のいくつかの値が表示されます。

*spam* 列は整数として読み取られますが、実際にはカテゴリ変数 (factor) です。 型を設定するには、次を実行します。

    data$spam <- as.factor(data$spam)

探索式の分析を行うには、 [ggplot2](https://ggplot2.tidyverse.org/) パッケージ (VM に既にインストールされている R の一般的なグラフ作成ライブラリ) を使用します。 先ほど表示した概要データには、感嘆符文字の出現頻度に関する概要統計情報があります。 次のコマンドを使って、それらの頻度をプロットしてみましょう。

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

ゼロの棒があると、プロットにゆがみが生じるため、取り除きます。

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

興味深い、1 を超える非自明な密度があります。 そのデータだけを見てみましょう。

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

次に、スパムとハムに分けます。

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

これらの例を基に、他の列を同じようにプロットして、そこに含まれているデータを探索できます。

## <a name="train-and-test-an-ml-model"></a>ML モデルをトレーニングおよびテストする
次は、データセット内のメールをスパムとハムに分類するように、いくつかの機械学習モデルをトレーニングしましょう。 このセクションでは、デシジョン ツリー モデルとランダム フォレスト モデルをトレーニングし、その後、それぞれの予測の精度をテストします。

> [!NOTE]
> 次のコードで使用する rpart (Recursive Partitioning and Regression Trees) パッケージは、データ サイエンス VM に既にインストールされています。
>
>

まず、データセットをトレーニング セットとテスト セットに分割します。

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

次に、メールを分類するデシジョン ツリーを作成します。

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

次に結果を示します。

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

トレーニング セットに対するパフォーマンスを確認するには、次のコードを使用します。

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

テスト セットに対するパフォーマンスを確認するには、次のコードを使用します。

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

ランダム フォレスト モデルも試してみましょう。 ランダム フォレストは、さまざまなデシジョン ツリーをトレーニングし、個々のデシジョン ツリーすべてからの分類のモードであるクラスを出力します。 これは、より強力な機械学習アプローチです。トレーニング データセットが過剰適合されるデシジョン ツリー モデルの傾向が修正されます。

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio"></a>Azure Machine Learning Studio にモデルをデプロイする
[Azure Machine Learning Studio](https://studio.azureml.net/) は、予測分析モデルを簡単に構築してデプロイできるクラウド サービスです。 Azure Machine Learning Studio の便利な機能の 1 つに、任意の R 関数を Web サービスとして発行する機能があります。 Azure Machine Learning Studio R パッケージを使うと、DSVM 上の R セッションから直接簡単にデプロイできます。

前のセクションのデシジョン ツリー コードをデプロイするには、Azure Machine Learning Studio にサインインする必要があります。 サインインするには、ワークスペース ID と認証トークンが必要です。 これらの値を見つけ、これらの値で Azure Machine Learning の変数を初期化するには、次を実行します。

左側のメニューにある **[設定]** を選択します。 **[ワークスペース ID]** の値をメモします。 ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

上部にあるメニューの **[Authorization Tokens (認証トークン)]** を選択し、 **[Primary Authorization Token (プライマリ認証トークン)]** の値をメモします。![3](./media/linux-dsvm-walkthrough/workspace-token.png)

DSVM の R セッションで、 **AzureML** パッケージを読み込み、変数の値にメモしたトークンとワークスペース ID を設定します。

    if(!require("AzureML")) install.packages("AzureML")
    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


このデモを実装しやすくするためにモデルを簡略化します。 ルートに最も近い 3 つの変数をデシジョン ツリーから選択し、これら 3 つの変数だけを使用して新しいツリーを構築します。

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

特徴を入力として受け取り、予測値を返す予測関数が必要です。

    predictSpam <- function(newdata) {
      predictDF <- predict(model.rpart, newdata = newdata)
      return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }


**publishWebService** 関数を使用して、predictSpam 関数を AzureML に発行します。

    spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)


この関数は、**predictSpam** 関数を受け取り、入力と出力が定義された **spamWebService** という名前の Web サービスを作成し、新しいエンドポイントに関する情報を返します。

次のコマンドを使用して、API エンドポイントやキー アクセスなど、公開されている最新の Web サービスの詳細を確認します。

    s<-tail(services(ws, name = "spamWebService"), 1)
    ep <- endpoints(ws,s)
    ep

テスト セットの最初の 10 行に対して試すには、次を実行します。

    consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>ディープ ラーニングのチュートリアル

フレームワーク ベースのサンプルに加えて、DLVM で検証済みの包括的なチュートリアルのセットも提供しています。 これらのチュートリアルは、イメージやテキスト/言語の理解などの分野でディープ ラーニング アプリケーションの開発をすぐに開始するのに役立ちます。 さまざまな分野とテクノロジのエンド ツー エンドのチュートリアルが引き続き追加される予定です。   


- [さまざまなフレームワークでのニューラル ネットワークの実行](https://github.com/ilkarman/DeepLearningFrameworks): あるフレームワークから別のフレームワークにコードを移行する方法を示す包括的なチュートリアルです。 フレームワーク間でモデルと実行時のパフォーマンスを比較する方法も示します。 

- [イメージ内で製品を検出するエンド ツー エンド ソリューションを構築するための攻略ガイド](https://github.com/Azure/cortana-intelligence-product-detection-from-images): イメージ検出は、イメージ内のオブジェクトを特定して分類する手法です。 このテクノロジには、多くの実際のビジネス ドメインに大きなメリットをもたらす可能性があります。 たとえば、小売り業者は、この手法を使用して、顧客が棚から選ぶ製品を判断できます。 この情報は、店舗が製品在庫を管理するのに役立ちます。 

- [音声の深層学習](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) このチュートリアルでは、[都市音声データセット](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)で発生する音声イベントを検出するための深層学習モデルの教育方法と、音声データの操作方法の概要について説明します。

- [テキスト ドキュメントの分類](https://github.com/anargyri/lstm_han): このチュートリアルでは、2 つの異なるニューラル ネットワーク アーキテクチャを構築してトレーニングする方法を示します。Hierarchical Attention Network と Long Short Term Memory (LSTM) ネットワークです。 これらのニューラル ネットワークでは、ディープ ラーニング用の Keras API を使用して、テキスト ドキュメントを分類します。 Keras は、最も普及している 3 つのディープ ラーニング フレームワーク (Microsoft Cognitive Toolkit、TensorFlow、および Theano) のフロントエンドです。

## <a name="use-other-tools-available"></a>利用できるその他のツールを使用する
残りのセクションでは、Linux データ サイエンス VM にインストールされているツールの一部について、その使用方法を示します。次に説明するツールの一覧を示します。

* XGBoost
* Python
* Jupyterhub
* Rattle
* PostgreSQL と Squirrel SQL
* SQL Server Data Warehouse

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) は、迅速かつ正確なブースト ツリー実装を提供するツールです。

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

python またはコマンド ラインから呼び出すこともできます。

## <a name="python"></a>Python
Python を使用して開発するために、DSVM には Anaconda Python ディストリビューション 2.7 および 3.5 がインストールされています。

> [!NOTE]
> Anaconda ディストリビューションには、[Conda](https://conda.pydata.org/docs/index.html) が含まれています。これを使用して、さまざまなバージョンやパッケージがインストールされている、Python 用のカスタム環境を作成できます。
>
>

scikit-learn のサポート ベクター マシンを使用して、spambase データセットの一部を読み取り、メールを分類してみましょう。

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

予測を行うには、次を実行します。

    clf.predict(X.ix[0:20, :])

AzureML エンドポイントを発行する方法を示すために、前に R モデルを発行したときのように、3 つの変数の簡略化されたモデルを作成します。

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

このモデルを AzureML に発行するには、次を実行します。

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

> [!NOTE]
> このコードは python 2.7 でのみ使用できます。3.5 ではまだサポートされていません。 **/anaconda/bin/python2.7** で実行します。
>
>

## <a name="jupyterhub"></a>Jupyterhub
DSVM の Anaconda ディストリビューションには、Jupyter Notebook (Python、R、または Julia のコードと分析を共有するためのクロスプラットフォーム環境) が付属しています。 Jupyter Notebook には JupyterHub からアクセスします。 ローカルの Linux ユーザー名とパスワードを使用して、***https://\<VM の DNS 名または IP アドレス\>:8000/*** にサインインします。 JupyterHub のすべての構成ファイルは、 **/etc/jupyterhub**ディレクトリにあります。

> [!NOTE]
> 現在のカーネルの Jupyter Notebook から (`pip` コマンドを通して) Python Package Manager を使用するには、コード セルで、次のコマンドを次の例のように使用できます。
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> 
> 
> [!NOTE]
> 現在のカーネルの Jupyter Notebook から (`conda` コマンドを通して) Conda インストーラーを使用するには、コード セルで、次のコマンドを次の例のように使用できます。
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

いくつかのサンプル Notebook は、VM に既にインストールされています。

* サンプル Python Notebook については、 [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) をご覧ください。
* サンプル [R](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) Notebook については、 **IntroTutorialinR** をご覧ください。
* 別のサンプル [Python](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) Notebook については、 **IrisClassifierPyMLWebService** をご覧ください。

> [!NOTE]
> Linux データ サイエンス VM のコマンド ラインから Julia 言語を使用することもできます。
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (R Analytical Tool To Learn Easily) は、データ マイニング用のグラフィカル R ツールです。 直感的なインターフェイスにより、データの読み込み、探索、変換のほか、モデルの構築と評価を簡単に行うことができます。  記事「[Rattle: A Data Mining GUI for R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf)」 (Rattle: R 向けのデータ マイニング GUI) で、その機能を示すチュートリアルが提供されています。

次のコマンドで Rattle をインストールして起動します。

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> DSVM には、インストールする必要がありません。 ただし、Rattle の読み込み時に、追加のパッケージをインストールするよう求められる場合があります。
>
>

Rattle では、タブベースのインターフェイスを使用します。 タブのほとんどは、「 [データ サイエンス プロセス](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)」の手順に対応しています (データの読み込みや探索など)。 データ サイエンス プロセスは、タブの左から右へと進んで行きます。 ただし、最後のタブには、Rattle で実行された R コマンドのログが含まれます。

データセットを読み込んで構成するには:

* ファイルを読み込み、 **[Data (データ)]** タブをクリックします。
* **[Filename (ファイル名)]** の横にあるセレクターを選択し、**spambaseHeaders.data** を選択します。
* ファイルを読み込むには、 上部の一連のボタンから **[Execute (実行)]** を選択します。 識別されたデータ型 (入力、ターゲット、またはその他の型の変数)、一意の値の数など、各列の概要が表示されます。
* Rattle では、 **spam** 列がターゲットとして正しく識別されます。 spam 列を選択し、 **[Target Data Type (ターゲットのデータ型)]** を **[Categoric (分類)]** に設定します。

データを探索するには:

* **[Explore (探索)]** タブをクリックします。
* **[Summary (概要)]** 、 **[Execute (実行)]** の順にクリックし、変数の型に関する情報と概要統計情報を表示します。
* 各変数に関する他の種類の統計情報を表示するには、 **[Describe (説明)]** や **[Basics (基本)]** などの他のオプションを選択します。

**[Explore (探索)]** タブでは、洞察力に富んださまざまなプロットを生成することもできます。 データのヒストグラムをプロットするには:

* **[Distributions (ディストリビューション)]** を選択します。
* **word_freq_remove** と **word_freq_you** の **[Histogram (ヒストグラム)]** チェック ボックスをオンにします。
* **[Execute (実行)]** を選択します。 1 つのグラフ ウィンドウに両方の密度プロットが表示され、メールには "you" という単語が "remove" よりも頻繁に出現することが明確に示されます。

相関関係プロットも興味深いものです。 相関関係プロットを作成するには:

* **[Type (種類)]** として **[Correlation (相関関係)]** を選択します。
* **[Execute (実行)]** を選択します。
* 推奨される変数の最大数が 40 である旨の警告が表示されます。 **[Yes (はい)]** を選択して、プロットを表示します。

興味深い相関関係が表示されます。たとえば、"technology" が "HP" および "labs" と強力に相関しています。 また、"650" とも強力に相関しています。これは、データセットの提供者の市外局番が 650 であるためです。

単語間の相関関係の数値は、[Explore (探索)] ウィンドウで確認できます。 興味深いことに、たとえば、"technology" は "your" および "money" と否定的に相関しています。

Rattle では、データセットを変換して、一般的な問題の一部を処理することができます。 たとえば、特徴のスケール変更、欠落値の補完、外れ値の処理、およびデータが欠落している変数または観測値の削除を行うことができます。 また、観測値や変数の間の相関ルールを特定することもできます。 これらのタブについては、この入門編のチュートリアルでは詳しく説明しません。

Rattle では、クラスター分析を実行することもできます。 出力を読みやすくするために、いくつかの特徴を除外してみましょう。 **[Data (データ)]** タブで、次の 10 個の項目以外の各変数の横にある **[Ignore (無視)]** を選択します。

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

次に、 **[Cluster (クラスター)]** タブに移動し、 **[KMeans]** を選択して、 *[Number of clusters (クラスター数)]* を 4 に設定します。 **[Execute (実行)]** を選択します。 結果が出力ウィンドウに表示されます。 1 つのクラスターには高い頻度で "george" と "hp" が含まれているため、おそらく本物の仕事のメールでしょう。

単純なデシジョン ツリーの機械学習モデルを作成するには:

* **[Model (モデル)]** タブをクリックします。
* **[Type (種類)]** として **[Tree (ツリー)]** を選択します。
* **[Execute (実行)]** を選択して、出力ウィンドウにテキスト形式でツリーを表示します。
* **[Draw (描画)]** をクリックして、グラフィカル バージョンを表示します。 これは、前に *rpart*を使用して作成したツリーにかなりよく似ています。

Rattle の便利な機能の 1 つに、複数の機械学習メソッドを実行し、それらをすばやく評価する機能があります。 手順は次のとおりです。

* **[Type (種類)]** として **[All (すべて)]** を選択します。
* **[Execute (実行)]** を選択します。
* その後で、 **[Type (種類)]** で **[SVM]** などのいずれか 1 つをクリックして、結果を表示できます。
* また、 **[Evaluate (評価)]** タブを使用して、検証セットに対するモデルのパフォーマンスを比較することもできます。たとえば、 **[Error Matrix (誤差マトリックス)]** を選択すると、検証セットに対する各モデルの混同行列、全体の誤差、および平均クラス誤差が表示されます。
* また、ROC 曲線のプロット、感度解析の実行、および他の種類のモデル評価を行うこともできます。

モデルの構築が完了したら、 **[Log (ログ)]** タブをクリックして、セッション中に Rattle によって実行された R コードを表示します。 **[Export (エクスポート)]** をクリックすると、保存することができます。

> [!NOTE]
> 現在のリリースの Rattle にはバグがあります。 スクリプトを変更したり、スクリプトを使って後で手順を繰り返したりするには、ログのテキストの「*Export this log ...* 」の前に # 文字を挿入する必要があります。
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL と Squirrel SQL
DSVM には、PostgreSQL がインストールされています。 PostgreSQL は、高度なオープン ソース リレーショナル データベースです。 このセクションでは、PostgreSQL にスパム データセットを読み込んでクエリを実行する方法を示します。

データを読み込む前に、ローカル ホストからのパスワード認証を許可する必要があります。 コマンド プロンプトで、次を実行します。

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

構成ファイルの下部付近に、許可される接続の詳細を記述している複数の行があります。

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

「IPv4 local connections」という行を、ident の代わりに md5 を使用するように変更して、ユーザー名とパスワードでログインできるようにします。

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

postgres サービスを再起動します。

    sudo systemctl restart postgresql

psql (PostgreSQL の対話型ターミナル) を組み込みの postgres ユーザーとして起動するには、プロンプトから次のコマンドを実行します。

    sudo -u postgres psql

現在ログインしている Linux アカウントと同じユーザー名を使用して新しいユーザー アカウントを作成し、パスワードを指定します。

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

次に、そのユーザーとして psql にログインします。

    psql

データを新しいデータベースにインポートします。

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

次は、 **Squirrel SQL**(JDBC ドライバー経由でデータベースを操作できるグラフィカル ツール) を使用して、データを探索し、いくつかのクエリを実行してみましょう。

開始するには、[Applications (アプリケーション)] メニューから Squirrel SQL を起動します。 ドライバーを設定するには:

* **[Windows]** 、 **[View Drivers (ドライバーの表示)]** の順に選択します。
* **[PostgreSQL]** を右クリックし、 **[Modify Driver (ドライバーの変更)]** を選択します。
* **[Extra Class Path (その他のクラス パス)]** 、 **[Add (追加)]** の順に選択します。
* **[File Name (ファイル名)]** に「***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar***」と入力します。
* **[Open (開く)]** を選択します。
* [List Drivers (ドライバーの一覧)] を選択し、 **[Class Name (クラス名)]** で **[org.postgresql.Driver]** を選択して、 **[OK]** を選択します。

ローカル サーバーへの接続を設定するには:

* **[Windows]** 、 **[View Aliases (エイリアスの表示)]** の順に選択します。
* **+** をクリックして、新しいエイリアスを作成します。
* 「*Spam database*」という名前を付け、 **[Driver (ドライバー)]** ボックスの一覧の **[PostgreSQL]** を選択します。
* URL を「*jdbc:postgresql://localhost/spam*」に設定します。
* "*ユーザー名*" と "*パスワード*" を入力します。
* Click **OK**.
* **[Connection (接続)]** ウィンドウを開くには、***Spam database*** エイリアスをダブルクリックします。
* **[接続]** を選択します。

クエリを実行するには:

* **[SQL]** タブをクリックします。
* [SQL] タブの上部にあるクエリ ボックスに `SELECT * from data;` などの単純なクエリを入力します。
* **Ctrl + Enter** キーを押して、クエリを実行します。 既定では、Squirrel SQL はクエリから先頭の 100 行を返します。

このデータを探索するために実行できるクエリは他にもたくさんあります。 たとえば、 *make* という単語の出現頻度がスパムとハムでどのように異なるかを調べるには、次のクエリを実行します。

    SELECT avg(word_freq_make), spam from data group by spam;

*3d*が頻繁に含まれるメールの特徴を調べるには、次のクエリを実行します。

    SELECT * from data order by word_freq_3d desc;

*3d* が高い頻度で含まれているメールのほとんどは実際にはスパムです。そのため、これは、メールを分類する予測モデルを構築する際に便利な特徴となります。

PostgreSQL データベースに格納されたデータを使用して機械学習を実行する場合は、 [MADlib](https://madlib.incubator.apache.org/)の使用を検討してください。

## <a name="sql-server-data-warehouse"></a>SQL Server Data Warehouse
Azure SQL Data Warehouse は、クラウドベースのスケールアウト データベースであり、リレーショナルか非リレーショナルかを問わず、大規模なデータを処理できます。 詳しくは、「 [Azure SQL Data Warehouse の概要](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

データ ウェアハウスに接続し、テーブルを作成するには、コマンド プロンプトから次のコマンドを実行します。

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

次に、sqlcmd プロンプトで次を実行します。

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

bcp を使用してデータをコピーします。

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> ダウンロードしたファイルの行の終わりは Windows スタイルですが、bcp は UNIX スタイルを想定しています。そのため、-r フラグを使用して、Windows スタイルであることを bcp に伝える必要があります。
>
>

sqlcmd を使用してクエリを実行します。

    select top 10 spam, char_freq_dollar from spam;
    GO

Squirrel SQL を使用してクエリを実行することもできます。 Microsoft MSSQL Server JDBC Driver (***/usr/share/java/jdbcdrivers/sqljdbc42.jar*** で見つけることができます) を使用して、PostgreSQL の同様の手順に従います。

## <a name="next-steps"></a>次の手順
Azure でのデータ サイエンス プロセスを構成するタスクについて説明したトピックの概要については、 [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)に関するページをご覧ください。

シナリオごとの Team Data Science Process の手順を示したエンド ツー エンドのチュートリアルの詳細については、「 [Team Data Science Process のチュートリアル](../team-data-science-process/walkthroughs.md)」をご覧ください。 これらのチュートリアルでは、クラウドとオンプレミスのツールおよびサービスをワークフローまたはパイプラインに組み込んで、インテリジェントなアプリケーションを作成する方法についても説明します。
