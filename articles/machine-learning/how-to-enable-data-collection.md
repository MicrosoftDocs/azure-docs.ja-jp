---
title: 実稼働環境のモデルでデータを収集する
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service (AKS) クラスターにデプロイされた Azure Machine Learning モデルからデータを収集する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 07/14/2020
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.openlocfilehash: f42a2f9e606eaa0475f2e35c479e99545ecf8193
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521902"
---
# <a name="collect-data-from-models-in-production"></a>実稼働環境のモデルからデータを収集する

この記事では、Azure Kubernetes Service (AKS) クラスターにデプロイされた Azure Machine Learning モデルからデータを収集する方法について説明します。 収集したデータは、Azure Blob ストレージに格納されます。

収集を有効にすると、収集したデータで次のことができるようになります。

* 収集した実稼働データについて[データの誤差を監視する](how-to-monitor-datasets.md)。

* [Power BI](#powerbi) または [Azure Databricks](#databricks)を使用して、収集したデータを分析する。

* モデルの再トレーニングや最適化の時期をより適切に判断する。

* 収集したデータを使用してモデルを再トレーニングする。

## <a name="what-is-collected-and-where-it-goes"></a>収集されるデータとその行き先

次のデータを収集できます。

* AKS クラスターにデプロイされた Web サービスのモデル入力データ。 音声オーディオ、画像、および動画は、収集 "*されません*"。
  
* 実稼働環境入力データを使用したモデル予測

>[!NOTE]
> このデータの事前集計および事前計算は、現時点では収集サービスの一部ではありません。

出力は BLOB ストレージに保存されます。 データは BLOB ストレージに追加されるため、分析の実行には任意のツールを選択できます。

出力データは、BLOB 内で次のような構文のパスに保存されます。

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> バージョン 0.1.0a16 より前の Azure Machine Learning SDK for Python のバージョンでは、`designation` 引数が `identifier` という名前になっています。 以前のバージョンで開発したコードは、これに応じて更新する必要があります。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://aka.ms/AMLFree) を作成してください。

- Azure Machine Learning ワークスペース、スクリプトを保存するローカル ディレクトリ、および Azure Machine Learning SDK for Python をインストールする必要があります。 これらのインストール方法については、[開発環境を構成する方法](how-to-configure-environment.md)に関するページを参照してください。

- AKS にデプロイするトレーニング済みの機械学習モデルが必要です。 モデルがない場合は、[画像分類モデルのトレーニング](tutorial-train-models-with-aml.md)に関するチュートリアルを参照してください。

- AKS クラスターが必要です。 作成とデプロイの方法については、[デプロイする方法とその場所](how-to-deploy-and-where.md)に関するページを参照してください。

- [環境を設定](how-to-configure-environment.md)し、[Azure Machine Learning Monitoring SDK](/python/api/overview/azure/ml/install) をインストールします。

## <a name="enable-data-collection"></a>データ収集を有効にする

[データ収集](/python/api/azureml-monitoring/azureml.monitoring.modeldatacollector.modeldatacollector)は、Azure Machine Learning または他のツールを使用してデプロイするモデルに関係なく、有効にすることができます。

データ コレクションを有効にするには、次の操作を行う必要があります。

1. スコア付けファイルを開きます。

1. ファイルの先頭に次のコードを追加します。

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. `init` 関数でデータ収集変数を宣言します。

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3", "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* は省略可能なパラメーターです。 モデルで必要ない場合は、使用する必要はありません。 *CorrelationId* を使用すると、*LoanNumber*、*CustomerId* などの他のデータとより簡単にマッピングできます。
    
    *Identifier* パラメーターは、BLOB でフォルダー構造を構築するために後で使用します。 生データと処理済みデータの区別に使用できます。

1. `run(input_df)` 関数に次のコード行を追加します。

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. AKS にサービスをデプロイしても、データ コレクションは自動的には **true** に設定 "*されません*"。 次の例のように、構成ファイルを更新してください。

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    また、この構成を変更して、サービス監視のために Application Insights を有効にすることもできます。

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. 新しいイメージを作成して機械学習モデルをデプロイするには、[デプロイする方法とその場所](how-to-deploy-and-where.md)に関するページを参照してください。

1. Web サービス環境の conda 依存関係に "Azure-Monitoring" pip パッケージを追加します。
  ```Python
    env = Environment('webserviceenv')
    env.python.conda_dependencies = CondaDependencies.create(conda_packages=['numpy'],pip_packages=['azureml-defaults','azureml-monitoring','inference-schema[numpy-support]'])
  ```


## <a name="disable-data-collection"></a>データ収集を無効にする

データ コレクションはいつでも停止できます。 Python コードを使用してデータ コレクションを無効にします。

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>データを検証して分析する

任意のツールを選択して、BLOB ストレージに収集されたデータを分析できます。

### <a name="quickly-access-your-blob-data"></a>BLOB データにすばやくアクセスする

1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. ワークスペースを開きます。

1. **[ストレージ]** を選択します。

    [![[ストレージ] オプションを選択する](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. 次のような構文の、BLOB の出力データへのパスをたどります。

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a><a id="powerbi"></a>Power BI を使用してモデル データを分析する

1. [Power BI Desktop](https://www.powerbi.com) をダウンロードして開きます。

1. **[データを取得]** を選択し、[**Azure Blob Storage**](/power-bi/desktop-data-sources) を選択します。

    [![Power BI BLOB の設定](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. ストレージ アカウント名を追加し、ストレージ キーを入力します。 この情報は、BLOB で **[設定]**  >  **[アクセス キー]** の順に選択して確認できます。

1. **[modeldata]** コンテナーを選択し、 **[編集]** を選択します。

    [![Power BI ナビゲーター](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. クエリ エディターで、 **[名前]** 列の下をクリックし、ストレージ アカウントを追加します。

1. モデルのパスをフィルターに入力します。 特定の年または月のファイルだけを検索する場合は、そのフィルター パスだけを展開します。 たとえば、3 月のデータのみを検索するには、次のフィルター パスを使用します。

   /modeldata/\<subscriptionid>/\<resourcegroupname>/\<workspacename>/\<webservicename>/\<modelname>/\<modelversion>/\<designation>/\<year>/3

1. **[名前]** の値に基づいて、関連するデータをフィルター処理します。 予測と入力を格納した場合は、それぞれに対するクエリを作成する必要があります。

1. ファイルを結合するには、 **[コンテンツ]** の列見出しの横にある下向きの二重矢印を選択します。

    [![Power BI コンテンツ](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. **[OK]** を選択します。 データがプリロードされます。

    [![Power BI の [ファイルの結合]](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. **[Close and Apply]\(閉じて適用する\)** を選択します。

1. 入力と予測を追加した場合、テーブルは **RequestId** の値によって自動的に並べ替えられます。

1. モデル データでのカスタム レポートの作成を開始します。

### <a name="analyze-model-data-using-azure-databricks"></a><a id="databricks"></a> Azure Databricks を使用してモデル データを分析する

1. [Azure Databricks ワークスペース](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)を作成します。

1. Databricks ワークスペースに移動します。

1. Databricks ワークスペースで、 **[データのアップロード]** を選択します。

    [![Databricks の [データのアップロード] オプションを選択したところ](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. **[新しいテーブルの作成]** を選択し、 **[他のデータ ソース]**  >  **[Azure Blob Storage]**  >  **[Create Table in Notebook]\(ノートブックにテーブルを作成\)** の順に選択します。

    [![Databricks のテーブルの作成](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. データの場所を更新します。 たとえば次のようになります。

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks の設定](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. データを表示および分析するには、テンプレートの手順に従います。

## <a name="next-steps"></a>次のステップ

収集したデータについて[データの誤差を検出します](how-to-monitor-datasets.md)。