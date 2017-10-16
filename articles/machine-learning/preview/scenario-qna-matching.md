---
title: "Azure Machine Learning Workbench を使った Q & A 照合 | Microsoft Docs"
description: "効果の高いさまざまな機械学習手法を使って、自由形式の質問を既存の FAQ の質問と回答のペアと照合する方法を説明します。"
services: machine-learning
documentationcenter: 
author: mezmicrosoft
editor: mezmicrosoft
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: mez
ms.manager: tihazen
ms.openlocfilehash: 8edc21fb8f42ee5897c4e938045cc1f42aedb3ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench を使った Q & A 照合
自由形式の質問に答えることは難しく、該当領域の専門家 (SME) による対応が必要になることがよくあります。 多くの場合、企業は社内の SME の負荷を減らすために、ユーザーをサポートする手段としてよく寄せられる質問 (FAQ) のリストを作成しています。 この例では、効果の高いさまざまな機械学習手法を使って、自由形式の質問を既存の FAQ の質問と回答のペアと照合する方法を示します。 この例では Azure Machine Learning Workbench を使って、こうしたソリューションを構築するための簡単な開発プロセスを示します。 

## <a name="link-to-the-gallery-github-repository"></a>ギャラリーの GitHub リポジトリへのリンク
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>概要

この例では、ユーザーの質問を、よく寄せられる質問 (FAQ) で提供されている既存の質問と回答 (Q&A) のペアや、[Stack Overflow](https://stackoverflow.com/) などの Web サイト上にある Q&A のペアとマッピングする際の問題に対処します。 質問とそれに対応する回答を照合する方法は数多くあります。たとえば、最も類似した質問に対する回答を見つける方法です。 ですがこの例では、FAQ の個々の回答が、意味的に同等の複数の質問に対応するという前提のもと、自由形式の質問を既存の質問と照合します。

このソリューションを実行するための重要な手順は次のとおりです。

1. テキスト データをクリーンアップして処理する。
2. 情報量の多いフレーズ (個別に扱うよりも多くの情報を提供する、複数語のシーケンス) を学習する。
3. テキスト データから特徴を抽出する。
4. テキスト分類モデルをトレーニングしてモデルのパフォーマンスを評価する。


## <a name="prerequisites"></a>前提条件

この例を実行するための前提条件は次のとおりです。

1. [Azure アカウント](https://azure.microsoft.com/free/) (無料試用版もご利用いただけます)。
2. [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)。[クイックスタート インストール ガイド](./quickstart-installation.md)に従ってプログラムをインストールし、ワークスペースを作成します。
3. この例は、任意のコンピューティング コンテキストで実行できます。 ただし、少なくとも 16 GB のメモリと 5 GB のディスク領域を備えたマルチコア マシン上で実行することをお勧めします。

## <a name="create-a-new-workbench-project"></a>新しいワークベンチ プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。
1.  Azure Machine Learning Workbench を開きます
2.  **[プロジェクト]** ページで **+** 記号をクリックし、**[新しいプロジェクト]** を選択します
3.  **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します。
4.  **[プロジェクト テンプレートの検索]** ボックスに「Q & A Matching」と入力し、テンプレートを選択します。
5.  **[作成]**

## <a name="data-description"></a>データの説明

この例で使用するデータセットは、[archive.org](https://archive.org/details/stackexchange) に格納されている Stack Exchange Data Dump です。このデータは、[Stack Exchange ネットワーク](https://stackexchange.com/)にあるユーザー提供コンテンツをすべて匿名でダンプしたものです。 ネットワークの各サイトは、bzip2 圧縮を使用する 7-zip で圧縮した XML ファイルで構成された、個別のアーカイブとしてフォーマットされます。 各サイトのアーカイブには、Posts、Users、Votes、Comments、PostHistory、PostLinks が含まれます。 

### <a name="data-source"></a>データ ソース

この例では、Stack Overflow サイトの [Posts データ (10 GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) と [PostLinks データ (515 MB)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) を使用します。 完全なスキーマの情報については、[readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt) をお読みください。 

Posts データの `PostTypeId` フィールドは、投稿が `Question` と `Answer` のどちらであるかを示します。 PostLinks データの `PostLinkTypeId` フィールドは、2 つの投稿が関連しているか、または重複しているかを示します。 通常、質問の投稿にはタグが含まれています。タグは、質問を他の類似する質問や重複した質問に分類するキーワードです。 `javascript`、`java`、`c#`、`php` などのタグは頻出し、多数の質問の投稿を構成しています。 この例では、`javascript` タグを含む Q&A ペアのサブセットを抽出します。

また、1 つの質問の投稿が、複数の回答の投稿や重複した質問の投稿と関連している場合もあります。 この 2 つのデータセットから FAQ のリストを構築するには、データ収集の条件をいくつか考慮する必要があります。 SQL スクリプトを使って 3 つのコンパイルされたデータのセットを選択していますが、このスクリプトはこの例には含まれていません。 以下で、結果のデータについて説明します。

- `Original Questions (Q)`: 投稿された質問は、Stack Overflow サイトで質問および回答されている。
- `Duplications (D)`: 投稿された質問は、元の質問である別の既存の質問 (`PostLinkTypeId = 3`) と重複している。 元の質問への回答が重複した新しい質問の回答となるという点において、重複した質問は元の質問と意味的に同じであると見なされます。
- `Answers (A)`: 元の質問とその重複は、1 つ以上の回答と関連している可能性がある。 この例では、単に元の質問者によって承認された回答 (`AcceptedAnswerId` でフィルター) か、スコアが最も高い回答 (`Score` 順) のいずれかを選択します。 

次の図に示すように、この 3 つのデータセットの組み合わせによって Q&A のペアが作成されます。1 つの回答 (A) が、元の質問 (Q) と重複した複数の質問 (D) にマッピングされています。

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>データ構造

次の表に、3 つのデータセットのデータ スキーマとダウンロード リンクを示します。

| Dataset | フィールド | 型 | Description
| ----------|------------|------------|--------
| [questions](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | ID | String | 一意の質問 ID (主キー)
|  | AnswerId | String | 質問ごとの一意の回答 ID
|  | Text0 | String | 質問のタイトルと本文を含む未加工のテキスト データ
|  | CreationDate | Timestamp | 質問が投稿されたときのタイムスタンプ
| [dupes](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | ID | String | 一意の重複 ID (主キー)
|  | AnswerId | String | 重複する質問に関連付けられた回答 ID
|  | Text0 | String | 重複する質問のタイトルと本文を含む未加工のテキスト データ
|  | CreationDate | Timestamp | 重複する質問が回答されたときのタイムスタンプ
| [answers](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | ID | String | 一意の回答 ID (主キー)
|  | text0 | String | 回答の未加工のテキスト データ


## <a name="scenario-structure"></a>シナリオの構成

Q&A 照合の例では、3 種類のファイルを使用しています。 1 つ目は、ワークフロー全体の詳細な説明を示す一連の Jupyter Notebook です。 2 つ目は、フレーズ学習と特徴抽出に必要なカスタム Python モジュールを含む一連の Python ファイルです。 これらの Python モジュールは汎用的であるため、この例以外のユース ケースにも利用できます。 3 つ目は、Azure Machine Learning Workbench を使って、ハイパー パラメーターをチューニングしてモデルのパフォーマンスを追跡する一連の Python ファイルです。

この例のファイルは、次のように整理されます。

| ファイル名 | 型 | Description
| ----------|------------|--------
| `Image` | フォルダー | README ファイルの画像を保存するためのフォルダー
| `notebooks` | フォルダー | Jupyter Notebook のフォルダー
| `modules` | フォルダー | Python モジュールのフォルダー
| `scripts` | フォルダー | Python ファイルのフォルダー
| `notebooks/Part_1_Data_Preparation.ipynb` | Jupyter Notebook | サンプル データにアクセスし、テキストを前処理して、トレーニングとテスト データセットを準備する
| `notebooks/Part_2_Phrase_Learning.ipynb` | Jupyter Notebook | 情報量の多いフレーズを学習し、テキストを Bag-of-Words (BOW) 表記にトークン化する
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Jupyter Notebook | 特徴を抽出し、テキスト分類モデルをトレーニングして、モデルのパフォーマンスを評価する
| `modules/__init__.py` | Python ファイル | Python パッケージの init ファイル
| `modules/phrase_learning.py` | Python ファイル | 生データの変換と情報量の多いフレーズの学習に使用する Python モジュール
| `modules/feature_extractor.py` | Python ファイル | モデルのトレーニングのために特徴を抽出する Python モジュール
| `scripts/naive_bayes.py` | Python ファイル | Naive Bayes モデルのハイパー パラメーターをチューニングする Python
| `scripts/random_forest.py` | Python ファイル | Random Forest モデルのハイパー パラメーターをチューニングする Python
| `README.md` | マークダウン ファイル | README マークダウン ファイル

> [!NOTE]
> 一連のノートブックは Python 3.5 でビルドされています。
> 

### <a name="data-ingestion-and-transformation"></a>データの取り込みと変換

3 つのコンパイル済みデータセットは BLOB ストレージに格納され、`Part_1_Data_Preparation.ipynb` ノートブックに取り込まれます。  

テキスト分類モデルをトレーニングする前に、質問のテキストからコード スニペットが除外されるよう、クリーンアップと前処理が行われます。 情報量の多いマルチワード シーケンスの学習のために、教師なしのフレーズ学習がトレーニング資料を通じて適用されます。 テキスト分類モデルで使用されるダウンストリームの Bag-of-Words (BOW) 特徴抽出では、これらのフレーズは単一の複合語ユニットとして表されます。

テキストの前処理とフレーズ学習に関する詳しい説明については、それぞれ `Part_1_Data_Preparation.ipynb` と `Part_2_Phrase_Learning.ipynb` のノートブックを参照してください。

### <a name="modeling"></a>モデリング

この例は、テキスト分類モデルをトレーニングすることで、既存の Q&A ペアに対して新しい質問をスコア付けすることを目的として作られています。既存の各 Q&A ペアは一意のクラスであり、各 Q&A ペアの重複した質問のサブセットがトレーニング資料として使用されます。 この例では、元の質問と重複した質問の 75% がトレーニング用に保持され、重複した質問のうち最新の 25% が評価用のデータとして提供されます。

分類モデルは、アンサンブル法により 3 つの基本分類子 (**Naive Bayes**、**Support Vector Machine**、**Random Forest**) を集計します。 それぞれの基本分類子で、`AnswerId` はクラス ラベルとして使用され、BOW 表現は特徴として使用されます。

モデルのトレーニング プロセスは `Part_3_Model_Training_and_Evaluation.ipynb` に図示されています。

### <a name="evaluation"></a>評価

2 つの異なる評価メトリックを使ってパフォーマンスを評価します。 
1. `Average Rank (AR)`: (全 103 件の回答クラスから) 取得した Q&A ペアのリストで見つかった正しい回答の平均位置を示します。 
2. `Top 3 Percentage`: ランク順に返されたリストの上位 3 位以内から正しい回答を取得できたテスト質問の割合を示します。 

この評価のデモは `Part_3_Model_Training_and_Evaluation.ipynb` に示されています。


## <a name="conclusion"></a>まとめ

この例では、フレーズ学習やテキスト分類などのよく知られているテキスト分析手法を使って、堅牢なモデルを構築する方法を取り上げています。 また、対話型のソリューション開発やモデルのパフォーマンスの追跡に Azure Machine Learning Workbench がどのように役立つかについても紹介しています。 

この例の主要なポイントは次のとおりです。

- 質問と回答の照合の問題は、フレーズ学習とテキスト分類モデルで効果的に解決できます。
- Azure Machine Learning Workbench と Jupyter Notebook を使って、対話型のモデル開発を実演します。
- Azure Machine Learning Workbench は、比較のための評価メトリックのログを記録することで、実行履歴と学習したモデルを管理します。 こうした機能により、ハイパー パラメーターの迅速なチューニングが可能になり、最もパフォーマンスに優れたモデルを容易に特定することができます。


## <a name="references"></a>参照

Timothy J. Hazen, Fred Richardson, [_Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Spoken Language Technology Workshop (SLT), 2012 IEEE. IEEE, 2012.

Timothy J. Hazen, [_MCE Training Techniques for Topic Identification of Spoken Audio Documents_](http://ieeexplore.ieee.org/abstract/document/5742980/) in IEEE Transactions on Audio, Speech, and Language Processing, vol. 19, no. 8, pp. 2451-2460, Nov. 2011.
