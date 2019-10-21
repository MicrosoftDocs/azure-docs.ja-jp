---
title: DSVM に含まれているツール
titleSuffix: Azure Data Science Virtual Machine tools
description: Windows および Ubuntu DSVM イメージに含まれるツールの一覧
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 09/27/2019
ms.openlocfilehash: 68c48bd4288b80d6cab515aa981d0f72461c0849
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035269"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine に含まれるツール

ここには、Data Science Virtual Machine に含まれるツールの最新の一覧と、各ツールの構成方法を理解するためのリンクが記載されています。


| **ツール**                                                           | **Windows DSVM** | **Linux DSVM** | **使用上の注意** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| 人気のパッケージがプレインストールされた [Microsoft R Open](https://mran.microsoft.com/open/)   |<span class='green-check'>&#9989;</span></br> (3.4.3)                    |<span class='green-check'>&#9989;</span></br> (3.4.3)  | [DSVM 上の R](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R、Python)](https://docs.microsoft.com/machine-learning-server/) Developer エディションには、次のものが含まれます。 <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR および revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) の並列および分散型の高機能フレームワーク (R および Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) - Microsoft の新しい最先端機械学習アルゴリズム <br />  &nbsp;&nbsp;&nbsp;&nbsp; [R および Python の運用化](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>  </br> (9.3.0)                 |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus (共有アクティベーション付き): Excel、Word、PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| 人気のパッケージがプレインストールされた [Anaconda Python](https://www.continuum.io/) 2.7 および 3.5    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span></br> (4.2)        | |
| Julia 言語用の人気のパッケージがプレインストールされた [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |<span class='green-check'>&#9989;</span> </br> (0.6.4)                   |<span class='green-check'>&#9989;</span></br> (0.6.2)              | [DSVM 上の Julia](./dsvm-languages.md#julia) |
| リレーショナル データベース                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer エディション| [PostgreSQL](https://www.postgresql.org/) (CentOS)、<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer エディション (Ubuntu) | [DSVM 上の SQL Server](./dsvm-data-platforms.md#sql-server-2017-developer-edition) |
| データベース ツール                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp、sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC および JDBC ドライバー|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (クエリ ツール)、 <br />  bcp、sqlcmd <br />  ODBC および JDBC ドライバー| |
| SQL Server 機械学習サービス (R、Python) によるスケーラブルなデータベース内分析 |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| 次のカーネルを備えた [Jupyter Notebook Server](https://jupyter.org/)                                  |<span class='green-check'>&#9989;</span></br> (5.5.0)   |<span class='green-check'>&#9989;</span> | [Jupyter Notebook のサンプル](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp;&nbsp;&nbsp;&nbsp; R |<span class='green-check'>&#9989;</span></br> (3.4.3) |<span class='green-check'>&#9989;</span></br> (3.4.3) | [R Jupyter のサンプル](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python |<span class='green-check'>&#9989;</span></br> (3) |<span class='green-check'>&#9989;</span> | [Python Jupyter のサンプル](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia |<span class='green-check'>&#9989;</span></br> (0.6.4) |<span class='green-check'>&#9989;</span></br> (0.6.2) | [Julia Jupyter のサンプル](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [pySpark Jupyter のサンプル](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (Ubuntu のみ) | |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (マルチユーザー Notebook サーバー)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (マルチユーザー Notebook サーバー) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> | |
| 開発ツール、IDE、コード エディター:| | |
| &nbsp;&nbsp;&nbsp;&nbsp;Git プラグイン、Azure HDInsight (Hadoop)、Azure Data Lake、SQL Server Data Tools、[Node.js](https://github.com/Microsoft/nodejstools)、[Python](https://aka.ms/ptvs)、および [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) を備えた [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上の Visual Studio](./dsvm-tools-development.md#visual-studio-2017) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span></br> (1.31.1) |<span class='green-check'>&#9989;</span></br> (1.31) | [DSVM 上の Visual Studio Code](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span></br> (1.1.463) |<span class='green-check'>&#9989;</span></br> (1.1.456) | [DSVM 上の RStudio Desktop](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [DSVM 上の RStudio Server](./dsvm-tools-development.md#rstudio--server)
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) |<span class='green-check'>&#9989;</span></br> (2018.1.4)  |<span class='green-check'>&#9989;</span></br> (2018.2.3) | [DSVM 上の PyCharm](./dsvm-tools-development.md#pycharm)
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.26.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span> | [DSVM 上の Juno](./dsvm-tools-development.md#juno)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vim](https://www.vim.org) |<span class='green-check'>&#9989;</span></br> (8.1.5) |<span class='green-check'>&#9989;</span></br> (7.4.1689) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (24.5.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Git](https://git-scm.com/) および Git Bash |<span class='green-check'>&#9989;</span></br> (2.20.1) |<span class='green-check'>&#9989;</span></br> (0.6.2) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [OpenJDK](https://openjdk.java.net) |<span class='green-check'>&#9989;</span></br> (1.8.0_201) |<span class='green-check'>&#9989;</span></br> (1.8.0_222) | |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework |<span class='green-check'>&#9989;</span></br> (4.7.2) |<span class='red-x'>&#10060;</span>   | |
| [Power BI Desktop](https://powerbi.microsoft.com/en-us/) |<span class='green-check'>&#9989;</span></br> (2.65.5313.1381) |<span class='red-x'>&#10060;</span>   |
| Azure および Cortana Intelligence Suite のサービスにアクセスするための SDK |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上の Power BI デスクトップ](./dsvm-tools-development.md#power-bi-desktop) |
| データ移動および管理ツール: | | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/) |<span class='green-check'>&#9989;</span></br> (1.6.2) |<span class='green-check'>&#9989;</span></br> (0.7.20160129.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure CLI](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span></br> (2.0.56) |<span class='green-check'>&#9989;</span></br> (2.0.58) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span></br> (8.1.0) |<span class='red-x'>&#10060;</span>   | [DSVM 上の Azcopy](./dsvm-tools-ingestion.md#azcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [BLOB FUSE ドライバー](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.0.2) | [DSVM 上の blobfuse](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span></br> (1.0.13) |<span class='red-x'>&#10060;</span>   | [DSVM 上の Adlcopy](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Cosmos DB データ移行ツール](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上の Cosmos DB](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp;&nbsp;&nbsp;&nbsp; Unix および Linux コマンドライン ツール |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| データ探索用 [Apache Drill](https://drill.apache.org) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>(1.14.0) | [DSVM 上の Apache Drill](./dsvm-tools-explore-and-visualize.md#apache-drill)
| 機械学習ツール: ||||
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) との統合 (R、Python) |<span class='green-check'>&#9989;</span></br> (0.2.7) |<span class='green-check'>&#9989;</span></br> (1.0.45) | [Azure ML SDK](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span></br> (0.81) |<span class='green-check'>&#9989;</span></br> (0.80) | [DSVM 上の XGBoost](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (8.1) | [DSVM 上の Vowpal Wabbit](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span></br> (3.8) |<span class='green-check'>&#9989;</span></br> (3.8.0) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span></br> (5.1.0) |<span class='green-check'>&#9989;</span></br> (5.2.2) | [DSVM 上の Weka](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (2.1.2) | [DSVM 上の LightGBM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.10.3) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/)、[Sparkling Water](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (3.20.0.7) | [DSVM 上の H20](./dsvm-ml-data-science-tools.md#h2o) |
| GPU または CPU 上で動作するディープ ラーニング ツール: |  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe](https://github.com/BVLC/caffe) および [Caffe2](https://github.com/facebookarchive/caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | DSVM 上の [Caffe](./dsvm-deep-learning-ai-frameworks.md#caffe) および [Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](https://chainer.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (5.2.0) | [DSVM 上の Chainer](./dsvm-deep-learning-ai-frameworks.md#chainer) |
| &nbsp;&nbsp;&nbsp;&nbsp; [CUDA、cuDNN、NVIDIA ドライバー](https://developer.nvidia.com/cuda-toolkit) |<span class='green-check'>&#9989;</span></br> (10.0.130) |<span class='green-check'>&#9989;</span></br> (10.0.130) | [DSVM 上の CUDA、cuDNN、NVIDIA ドライバー](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/horovod/horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.16.1) | [DSVM 上の Horovod](./dsvm-deep-learning-ai-frameworks.md#horovod) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](https://keras.io) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (2.2.4) | [DSVM 上の Keras](./dsvm-deep-learning-ai-frameworks.md#keras) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/en-us/cognitive-toolkit/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (2.5.1) | [DSVM 上の CNTK](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet](https://mxnet.apache.org/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (1.3.0) | [DSVM 上の MXNet](./dsvm-deep-learning-ai-frameworks.md#mxnet) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet モデル サーバー](https://github.com/awslabs/mxnet-model-server#quick-start) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (1.0.1) | [DSVM 上の MXNet モデル サーバー](./dsvm-deep-learning-ai-frameworks.md#mxnet-model-server) |
| &nbsp;&nbsp;&nbsp;&nbsp; [NVidia システム管理インターフェイス (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>| [DSVM 上の nvidia-smi](./dsvm-deep-learning-ai-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](https://pytorch.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.2.0) | [DSVM 上の PyTorch](./dsvm-deep-learning-ai-frameworks.md#pytorch) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org) |<span class='green-check'>&#9989;</span></br> (1.13) |<span class='green-check'>&#9989;</span></br> (1.13) | [DSVM 上の TensorFlow](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](https://www.tensorflow.org/tfx/guide/serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.12.0) | [DSVM 上の TensorFlow Serving](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Theano](https://github.com/Theano/Theano) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (1.0.3) | [DSVM 上の Theano](./dsvm-deep-learning-ai-frameworks.md#theano)

