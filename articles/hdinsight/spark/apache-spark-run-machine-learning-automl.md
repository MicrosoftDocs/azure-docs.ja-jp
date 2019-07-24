---
title: Azure HDInsight の Apache Spark 上で自動化された機械学習 (AutoML) を使用して Azure Machine Learning ワークロードを実行する
description: Azure HDInsight の Apache Spark 上で 自動化された機械学習 (AutoML) を使用して Azure Machine Learning ワークロードを実行する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 896cae9b7fc43765e340ba3b92351e04b5512efd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762553"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Azure HDInsight の Apache Spark 上で自動化された機械学習 (AutoML) を使用して Azure Machine Learning ワークロードを実行する

Azure Machine Learning は、データを活用した予測分析ソリューションの構築、テスト、およびデプロイをドラッグ アンド ドロップで行うことができる、コラボレーションに対応したツールです。 Azure Machine Learning でモデルを Web サービスとして公開して、カスタム アプリや BI ツール (Excel など) からそのモデルを簡単に利用することができます。 インテリジェントな自動化と最適化を使用する自動化された機械学習 (AutoML) は、高品質の機械学習モデルを作成するのに役立ちます。 特定の種類の問題に対して使用される適切なアルゴリズムとハイパー パラメーターは、AutoML が決定します。

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>HDInsight クラスターに Azure Machine Learning をインストールする

> [!Note]
> Azure ML ワークスペースは、現時点では、eastus、eastus2、および westcentralus リージョンで利用できます。 HDInsight クラスターも、これらのリージョンのいずれかの中に作成する必要があります。

Azure Machine Learning と自動化された機械学習の全般的なチュートリアルについては、「[チュートリアル:Azure Machine Learning Studio で初めてのデータ サイエンス実験を作成する](../../machine-learning/studio/create-experiment.md)」と「[チュートリアル:自動化された機械学習を使用して回帰モデルを構築する](../../machine-learning/service/tutorial-auto-train-models.md)」を参照してください。
AzureML を Azure HDInsight クラスターにインストールするには、HDInsight 3.6 Spark 2.3.0 クラスター (推奨) のヘッド ノードとワーカー ノードでスクリプト アクション ([install_aml](https://commonartifacts.blob.core.windows.net/automl/install_aml.sh)) を実行します。 このスクリプト アクションは、クラスター作成プロセスの一部として実行することも、Azure portal から既存のクラスターで実行することもできます。

スクリプト アクションの詳細については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](../hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。 このスクリプトでは、Azure Machine Learning パッケージと依存関係の他に、サンプル Jupyter ノートブックも (既定のストアのパス `HdiNotebooks/PySpark` に) ダウンロードされます。 この Jupyter ノートブックでは、単純な分類問題に対して自動化された機械学習の分類器を使用する方法が示されます。

> [!Note]
> Azure Machine Learning パッケージは、Python3 conda 環境にインストールされます。 インストールされた Jupyter ノートブックは PySpark3 カーネルを使用して実行する必要があります。

## <a name="authentication-for-workspace"></a>ワークスペースの認証

ワークスペースの作成と実験の送信を行うには、認証トークンが必要です。 このトークンは、[Azure AD アプリケーション](../../active-directory/develop/app-objects-and-service-principals.md)を使用して生成できます。 アカウントで多要素認証が有効になっていない場合は、必要な認証トークンを [Azure AD ユーザー](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python)を使用して生成することもできます。  

次のコード スニペットでは、**Azure AD アプリケーション**を使用して、認証トークンが作成されます。

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                username = '<Azure AD Application ID>',
                password = '<Azure AD Application Key>'
                )
```
次のコード スニペットでは、**Azure AD ユーザー**を使用して、認証トークンが作成されます。

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>データセットの読み込み

Spark での自動化された機械学習では、データに対して遅延評価される不変の操作である**データフロー**が使用されます。  データフローでは、パブリックな読み取りアクセスを使用して BLOB から、または SAS トークンを使用して BLOB URL からデータセットを読み込むことができます。

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

ワンタイム登録を使用して、データ ストアをワークスペースに登録することもできます。

## <a name="experiment-submission"></a>実験の送信

自動化された機械学習の構成では、`spark_context` プロパティを設定して、パッケージを分散モードで実行する必要があります。 `concurrent_iterations` プロパティは並列で実行されるイテレーションの最大数であり、Spark アプリ用の Executor のコアよりも少ない数値を設定する必要があります。

## <a name="next-steps"></a>次の手順

* 自動化された機械学習を導入する理由の詳細については、「[Release models at pace using Microsoft’s automated machine learning! (Microsoft の自動化された機械学習を使用してモデルを効率的にリリースする)](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)」を参照してください。
* [Microsoft Research の AutoML プロジェクト](https://www.microsoft.com/research/project/automl/)