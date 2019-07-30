---
title: Azure HDInsight の Apache Spark 上で自動化された機械学習 (AutoML) を使用して Azure Machine Learning ワークロードを実行する
description: Azure HDInsight の Apache Spark 上で 自動化された機械学習 (AutoML) を使用して Azure Machine Learning ワークロードを実行する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: ff6a071a2d157bf79ab27fcbf4f9753fdbcac118
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68354866"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Azure HDInsight の Apache Spark 上で自動化された機械学習 (AutoML) を使用して Azure Machine Learning ワークロードを実行する

Azure Machine Learning では、機械学習モデルの構築、トレーニング、およびデプロイを簡略化して迅速化します。 Automated Machine Learning (AutoML) では、規定目標機能があるトレーニング データで開始し、アルゴリズムと機能選択の組み合わせを反復して、トレーニング スコアに基づきデータの最適なモデルを自動的に選択します。 HDInsight を使用することにより、お客様は多数のノードでクラスターをプロビジョニングできます。 HDInsight クラスターの Spark で実行されている AutoML では、ユーザーはこれらのノード間でコンピューティング能力を使用して、スケールアウト形式でトレーニング ジョブを実行したり、複数のトレーニング ジョブを並列で実行したりできます。 これによりユーザーは、他のビッグ データ ワークロードとコンピューティングを共有しながら AutoML 実験を実行できます。
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>HDInsight クラスターに Azure Machine Learning をインストールする

Automated Machine Learning の一般的なチュートリアルについては、「[チュートリアル: 自動化された機械学習を使用して回帰モデルを構築する](../../machine-learning/service/tutorial-auto-train-models.md)」を参照してください。
すべての新しい HDInsight Spark クラスターには、AzureML AutoML SDK がプレインストールされています。 AutoML は、HDInsight 上で、この[サンプル Jupyter ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi)を使用して開始することができます。 この Jupyter ノートブックでは、単純な分類問題に対して自動化された機械学習の分類器を使用する方法が示されます。

> [!Note]
> Azure Machine Learning パッケージは、Python3 conda 環境にインストールされます。 インストールされた Jupyter ノートブックは PySpark3 カーネルを使用して実行する必要があります。

Zeppelin ノートブックを使用して AutoML を使用することもできます。

> [!Note]
> Zeppelin には、PySpark3 が Python の適切なバージョンを選択しないという[既知の問題](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html)があります。 文書化された解決策を使用してください。

## <a name="authentication-for-workspace"></a>ワークスペースの認証

ワークスペースの作成と実験の送信を行うには、認証トークンが必要です。 このトークンは、[Azure AD アプリケーション](../../active-directory/develop/app-objects-and-service-principals.md)を使用して生成できます。 アカウントで多要素認証が有効になっていない場合は、必要な認証トークンを [Azure AD ユーザー](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python)を使用して生成することもできます。  

次のコード スニペットでは、**Azure AD アプリケーション**を使用して、認証トークンが作成されます。

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```
次のコード スニペットでは、**Azure AD ユーザー**を使用して、認証トークンが作成されます。

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>データセットの読み込み

Spark での自動化された機械学習では、データに対して遅延評価される不変の操作である**データフロー**が使用されます。  データフローでは、パブリックな読み取りアクセスを使用して BLOB から、または SAS トークンを使用して BLOB URL からデータセットを読み込むことができます。

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

ワンタイム登録を使用して、データ ストアをワークスペースに登録することもできます。

## <a name="experiment-submission"></a>実験の送信

[Automated Machine Learning 構成](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig)では、プロパティ `spark_context` は分散モードで実行するパッケージに対して設定する必要があります。 `concurrent_iterations` プロパティは並列で実行されるイテレーションの最大数であり、Spark アプリ用の Executor のコアよりも少ない数値を設定する必要があります。

## <a name="next-steps"></a>次の手順

* Automated Machine Learning を導入する理由の詳細については、「[Release models at pace using Microsoft’s automated machine learning! (Microsoft の 自動 Machine Learning を使用してモデルを効率的にリリースする)](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)」を参照してください。
* Azure ML Automated ML の機能の使用に関する詳細については、「[Azure Machine Learning service の新しい Automated Machine Learning 機能](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)」を参照してください。
* [Microsoft Research の AutoML プロジェクト](https://www.microsoft.com/research/project/automl/)
