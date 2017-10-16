--- 
title: "予測メンテナンスの実際のシナリオ | Microsoft Docs"
description: "PySpark を使用した予測メンテナンスの実際のシナリオ"
services: machine-learning
author: jaymathe
ms.author: jaymathe
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7bb5672a2b9de0a62af8b6155bfdead6a0105eb5
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

--- 

# <a name="predictive-maintenance-real-world-scenario"></a>予測メンテナンスの実際のシナリオ


予期しない設備のダウンタイムは、どのような企業にとっても損害になる可能性があります。 そのため、使用率やパフォーマンスを最大化するには、またコストのかかる予定外のダウンタイムを最小化するには、現場の設備を稼働し続けることが必須です。 問題を早期に特定することで、コスト効率の高い方法で限られた保守リソースをデプロイし、品質管理やサプライ チェーンのプロセスを向上させることができます。 

このシナリオでは、比較的[大規模なデータ](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)を使用して、データの取り込み、特徴エンジニアリング、モデルの構築の手順を案内し、最後にモデルの運用化とデプロイについて説明します。 プロセス全体のコードは、PySpark で記述され、Azure ML Workbench で Jupyter ノートブックを使用して実装されています。 最終的に最適なモデルは Azure Machine Learning モデル管理を使用して運用化され、運用環境でリアルタイムの障害予測を行うために使用されます。   

## <a name="link-to-the-gallery-github-repository"></a>ギャラリーの GitHub リポジトリへのリンク

パブリック GitHub リポジトリのリンクは [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance) です


## <a name="use-case-overview"></a>ユース ケースの概要

資産の量が多い業種の企業が直面する主な問題は、機械的な問題による遅延に関係する重大なコストです。 ほとんどの企業は、このような問題が生じる前に事前に防ぐために、問題が発生するタイミングの予測に関心があります。 予測によって、ダウンタイムを減らすことでコストを削減し、場合によっては安全性を向上することができます。 予測メンテナンスの一般的なユース ケースとモデリング アプローチの詳細な説明については、[予測メンテナンスのプレイブック](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)に関するページを参照してください。

このシナリオでは、複数の実際の問題の複合に基づくシナリオに予測モデルを実装する手順を提供する目的で、プレイブックのアイデアを利用します。 次の例は、多くの予測メンテナンスのユース ケースで見られる一般的なデータ要素をまとめたものです。

このシミュレートされたデータのビジネスの問題は、コンポーネント エラーで発生する問題を予測することです。 そのため、ビジネスの質問は、"*コンポーネント エラーが原因でマシンが停止する可能性はどのくらいか*" です。 この問題は、多クラス分類 (1 つのマシンにつき複数のコンポーネント) の問題として構成され、予測モデルを作成するために機械学習アルゴリズムが使用されます。 モデルは、マシンから収集された履歴データに対してトレーニングされます。 このシナリオでは、Azure Machine Learning Workbench 環境内のこのようなモデルを実装する多様な手順を実行します。

## <a name="prerequisites"></a>前提条件

* [Azure アカウント](https://azure.microsoft.com/en-us/free/) (無料試用版もご利用いただけます)。
* [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) のインストール済みコピー。[クイックスタート インストール ガイド](./quickstart-installation.md)に従ってプログラムをインストールし、ワークスペースを作成します。
* このシナリオで Jupyter ノートブック全体で使用する結果は、Azure Blob Storage コンテナーにそのまま格納されます。 Azure Storage アカウントを設定する手順は、[こちらのリンク](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-python-how-to-use-blob-storage)を参照してください。 
* モデルの[運用化](https://github.com/Azure/Machine-Learning-Operationalization)の場合、[Docker エンジン](https://www.docker.com/)をローカルにインストールして実行することをお勧めします。 インストールせずにクラスター オプションを使用することもできますが、多くの場合、[Azure Container Service (ACS)](https://azure.microsoft.com/en-us/services/container-service/) の実行コストは高くなります。
* このシナリオでは、Docker エンジンをローカルにインストールした Windows 10 マシンで Azure ML Workbench を実行していることを前提とします。 
* このシナリオは、Windows 10 マシンで構築およびテストされています。このマシンの仕様は、Docker バージョン 17.06.0-ce-win19 (12801) がインストールされている Intel Core i7-4600U CPU @ 2.10 GHz、8-GB RAM、64 ビット OS、x64 ベースのプロセッサです。 
* モデルの運用化は、azure-cli-ml==0.1.0a22 バージョンの Azure ML CLI を使用して実行されました。

## <a name="create-a-new-workbench-project"></a>新しいワークベンチ プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。
1.  Azure Machine Learning Workbench を開きます
2.  **[プロジェクト]** ページで **+** 記号をクリックし、**[新しいプロジェクト]** を選択します
3.  **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します
4.  **[プロジェクト テンプレートの検索]** 検索ボックスに「Predictive Maintenance」と入力し、テンプレートを選択します
5.  **[作成]** をクリックします

## <a name="data-description"></a>データの説明

[シミュレートされたデータ](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)は、5 個のコンマ区切り値 (.csv) ファイルで構成されます。 

* [マシン](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): 各マシンを区別する特徴。 たとえば、製造年、モデルなどです。
* [エラー](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): エラー ログには、マシンが動作したままの状態でスローされる重要ではないエラーが含まれています。 これらのエラーは障害として見なされませんが、将来的な障害イベントの予測になる可能性があります。 テレメトリ データは、1 時間間隔で収集されるため、エラー日時は最も近い時刻に丸められます。
* [メンテナンス](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): メンテナンス ログには、スケジュールされたメンテナンス レコードとスケジュールされていないメンテナンス レコードの両方が含まれています。 スケジュールされたメンテナンスは、コンポーネントの通常の検査に対応し、スケジュールされていないメンテナンスは、機械的な障害または他のパフォーマンス低下から生じる可能性があります。 テレメトリ データは、1 時間間隔で収集されるため、メンテナンス日時は最も近い時刻に丸められます。
* [テレメトリ](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): テレメトリの時系列データは、リアルタイムで各マシンから収集された電圧、回転、負荷、および振動センサーの測定で構成されます。 データは 1 時間の平均が計算され、テレメトリ ログに保存されます。
* [障害](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): 障害は、メンテナンス ログ内のコンポーネントの置き換えに対応します。 各レコードには、メンテナンス ID、コンポーネントの種類、置き換えの日時が含まれています。 これらのレコードは、モデルが予測しようとする機械学習ラベルを作成するために使用されます。

Jupyter ノートブック シナリオの[データの取り込み](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb)に関するページのコード セクションを参照して、GitHub リポジトリの生データ セットをダウンロードし、この分析のために PySpark データセットを作成します。

## <a name="scenario-structure"></a>シナリオの構造
シナリオの内容については、[GitHub リポジトリ](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)を参照してください。 

リポジトリには、データの準備から、いくつかのモデルを構築し、最終的に最適なモデルの 1 つを運用化するまでのプロセスの概要について説明した [Readme](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) ファイルがあります。 4 つの Jupyter ノートブックをリポジトリ内の [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) フォルダーで使用できます。   

次に、手順のシナリオ ワークフローについて説明します。 エンド ツー エンドのシナリオは PySpark で記述され、以下のように 4 つのノートブックに分割されています。

* [データの取り込み](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): このノートブックは、5 つの入力 .csv ファイルのデータの取り込みを処理し、前段階のクリーンアップをいくつか実行し、いくつかの概略図を作成してデータのダウンロードを検証し、最後に結果のデータセットを Azure BLOB コンテナーに格納して、次のノートブックで使用できるようにします。

* [特徴エンジニアリング](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): 前の手順でクリーニングされたデータセットを使用し、テレメトリ センサーのラグ特徴が作成されます。また、最後の置換後の日数などの変数を作成する特徴エンジニアリングが追加されます。 最後に、障害には関連するレコードのタグが付けられ、最終的なデータセットが作成されます。このデータセットは、次の手順のために Azure BLOB に保存されます。 

* [モデルの構築](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): 最終的な特徴エンジニアリングされたデータセットは、日時スタンプに基づくトレーニングとテスト データセットという 2 つに分割されます。 この 2 つのモデル、言い換えるとランダム フォレスト分類器とデシジョン ツリー分類器は、トレーニング データセットに対して構築され、テスト データセットでスコア付けされます。 

* [モデルの運用化とデプロイ](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): 前の手順で構築された最適なモデルは、デプロイに関連する .json スキーム ファイルと共に、.model ファイルとして保存されます。 init() 関数と run() 関数は、まずローカルでテストされてから、Azure Machine Learning モデル管理環境を使用してモデルが運用化され、運用環境でリアルタイムの障害予測に使用されます。  

## <a name="conclusion"></a>まとめ

このシナリオを読むと、Azure ML Workbench の Jupyter ノートブック環境内で PySpark を使用して、エンド ツー エンドの予測メンテナンス ソリューションを構築する方法の概要を理解できます。 また、運用環境でリアルタイムの障害予測に使用するために、Azure Machine Learning モデル管理環境を使用して最適なモデルを簡単に運用化してデプロイする方法についても案内しています。 また、ビジネスのニーズに合わせてシナリオの関連する部分を編集することができるようになります。  

## <a name="references"></a>参照

このユース ケースは、以下のように、以前は複数のプラットフォームで開発されていました。

* [予測メンテナンス ソリューション テンプレート](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [予測メンテナンスのモデリング ガイド](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [SQL R サービスを使用した予測メンテナンス モデリング ガイド](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [予測メンテナンス モデリング ガイド Python ノートブック](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [PySpark を使用する予測メンテナンス](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)



