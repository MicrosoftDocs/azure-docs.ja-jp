---
title: Machine Learning Web サービス エンドポイントからのデータを監視および収集する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でデプロイされた Web サービスを Azure Application Insights を使用して監視します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ae66447e128b07ce942b8c2fcc66347a31cfe83f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848861"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>ML Web サービス エンドポイントからのデータを監視および収集する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、ログのクエリを実行し、Azure Application Insights を有効にすることで、Azure Kubernetes Service (AKS) または Azure Container Instances (ACI) で Web サービス エンドポイントにデプロイされたモデルのデータを収集および監視する方法について説明します。以下を使用します。 
* [Azure Machine Learning Python SDK](#python)
* [Azure Machine Learning Studio](#studio) (https://ml.azure.com )

エンドポイントの出力データと応答を収集するだけでなく、次の情報を監視できます。

* 要求率、応答時間、失敗率
* 依存率、応答時間、失敗率
* 例外

[Azure Application Insights の詳細を学習する](../azure-monitor/app/app-insights-overview.md)。 


## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を試してください

* Azure Machine Learning ワークスペース、スクリプトを保存するローカル ディレクトリ、および Azure Machine Learning SDK for Python のインストール。 これらの前提条件を満たす方法については、[開発環境を構成する方法](how-to-configure-environment.md)に関する記事を参照してください。

* Azure Kubernetes Service (AKS) または Azure コンテナー インスタンス (ACI) にデプロイするトレーニング済みの機械学習モデル。 ない場合は、[イメージ分類モデルのトレーニング](tutorial-train-models-with-aml.md)に関するチュートリアルを参照してください。

## <a name="query-logs-for-deployed-models"></a>デプロイ済みモデルのログのクエリを実行する

以前にデプロイした Web サービスからログを取得するには、サービスを読み込んで、`get_logs()` 関数を使用します。 ログには、デプロイ中に発生したエラーに関する詳細情報が含まれている場合があります。

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

## <a name="web-service-metadata-and-response-data"></a>Web サービスのメタデータと応答データ

> [!IMPORTANT]
> Azure Application Insights では、最大 64 KB のペイロードのみがログに記録されます。 この制限に達すると、メモリ不足などのエラーが発生したり、情報がログに記録されないことがあります。

Web サービスへの要求の情報を記録するには、`print` ステートメントを score.py ファイルに追加します。 各 `print` ステートメントによって、メッセージ `STDOUT` の Application Insights のトレース テーブルに 1 つのエントリが生成されます。 `print` ステートメントの内容は、`customDimensions` に格納されてから、トレース テーブルの `Contents` に格納されます。 JSON 文字列を出力すると、`Contents` のトレース出力に階層データ構造が生成されます。

Azure Application Insights に直接クエリを実行してこのデータにアクセスしたり、長期の保持やさらなる処理のためにストレージ アカウントに対する[連続エクスポート](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)を設定したりできます。 モデル データはその後、Azure Machine Learning で、ラベル付け、再トレーニング、説明、データ分析などの用途を設定するために使用できます。 


<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Python SDK を使用して構成する 

### <a name="update-a-deployed-service"></a>デプロイされたサービスを更新する

1. ワークスペースで、サービスを特定します。 `ws` の値はワークスペースの名前です。

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. サービスを更新し、Azure Application Insights を有効にします。

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>サービスのカスタム トレースをログに記録する

カスタム トレースをログに記録する場合は、[デプロイする方法と場所](how-to-deploy-and-where.md)のドキュメントにある AKS または ACI のための標準のデプロイ プロセスに従います。 次に、次の手順を使用します。

1. 推論中に Application Insights にデータを送信するには、print ステートメントを追加してスコアリング ファイルを更新します。 要求データや応答などのより複雑な情報をログに記録するには、JSON 構造体を使用してください。 次の例の score.py ファイルは、モデルが初期化された時刻、推論時の入力と出力、およびエラーが発生した時刻をログに記録します。

    > [!IMPORTANT]
    > Azure Application Insights では、最大 64 KB のペイロードのみがログに記録されます。 この制限に達すると、メモリ不足などのエラーが発生したり、情報がログに記録されないことがあります。 ログに記録するデータが 64 kb より大きい場合は、「[実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)」の情報を使用して、Blob ストレージに格納する必要があります。
    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. サービスの構成を更新します。
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. イメージをビルドし、[AKS または ACI](how-to-deploy-and-where.md) にデプロイします。

ログとデータ収集の詳細については、「[Azure Machine Learning でログ記録を有効にする](how-to-enable-logging.md)」および「[実稼働環境のモデルからデータを収集する](how-to-enable-data-collection.md)」を参照してください。

### <a name="disable-tracking-in-python"></a>Python で追跡を無効にする

Azure Application Insights を無効にするには、次のコードを使用します。

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Azure Machine Learning Studio を使用して構成する

次の手順でモデルをデプロイする準備ができたときに、Azure Machine Learning Studio から Azure Application Insights を有効にすることもできます。

1. ワークスペース (https://ml.azure.com/ ) にサインインします。
1. **[モデル]** に移動し、デプロイするモデルを選択します。
1. **[+ デプロイ]** を選択します。
1. **[モデルのデプロイ]** フフォームに入力します。
1. **[詳細設定]** メニューを展開します。

    ![デプロイ フォーム](./media/how-to-enable-app-insights/deploy-form.png)
1. **[Application Insights の診断とデータ収集を有効にする]** を選択します。

    ![App Insights を有効にする](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>メトリックとログを表示する

サービスのデータは、Azure Machine Learning と同じリソース グループ内の Azure Application Insights アカウントに保存されます。
表示するには:

1. [Studio](https://ml.azure.com/) の Azure Machine Learning ワークスペースに移動します。
1. **[エンドポイント]** を選択します。
1. デプロイ済みのサービスを選択します。
1. 下にスクロールして **[Application Insights URL]** を見つけ、リンクを選択します。

    [![Application Insights URL を見つける](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. Application Insights で、左側の一覧の **[概要]** タブまたは __[監視]__ セクションで、 __[ログ]__ を選択します。

    [![監視の [概要] タブ](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. score.py ファイルからログに記録された情報を表示するには、__traces__ テーブルを参照してください。 次のクエリは、__input__ 値が記録されたログを検索しています。

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![トレース データ](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Azure Application Insights の使用方法の詳細については、「[Application Insights とは何か?](../azure-monitor/app/app-insights-overview.md)」を参照してください。

## <a name="export-data-for-further-processing-and-longer-retention"></a>追加の処理と長期保持のためにデータをエクスポートする

>[!Important]
> Azure Application Insights では、BLOB ストレージへのエクスポートのみがサポートされています。 このエクスポート機能の追加の制限については「[Application Insights からのテレメトリのエクスポート](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration)」に示されています。

Azure Application Insights の[連続エクスポート](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)を使用して、サポートされているストレージ アカウントにメッセージを送信し、より長い保持を設定できます。 データは JSON 形式で格納され、簡単に解析してモデル データを抽出できます。 

Azure Data Factory、Azure ML パイプライン、またはその他のデータ処理ツールを使用して、必要に応じてデータを変換できます。 データを変換したら、データセットとして Azure Machine Learning ワークスペースに登録できます。 そのためには、[データセットを作成して登録する方法](how-to-create-register-datasets.md)に関するページをご覧ください。

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="連続エクスポート":::


## <a name="example-notebook"></a>ノートブックの例

[enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) ノートブックは、この記事の概念を示しています。 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次のステップ

* モデルを Web サービス エンドポイントにデプロイし、Azure Application Insights でデータの収集を利用してエンドポイントを監視できるようにするには、[Azure Kubernetes Service クラスターにモデルをデプロイする方法](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service)または[モデルを Azure Container Instances にデプロイする方法](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance)に関する記事をご覧ください。
* 「[MLOps: Azure Machine Learning でモデルを管理、デプロイ、および監視する](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment)」を参照して、運用環境のモデルから収集されたデータの利用の詳細について確認してください。 このようなデータは、機械学習プロセスを継続的に改善するのに役立ちます。
