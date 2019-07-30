---
title: 実稼働環境のモデルでデータを収集する
titleSuffix: Azure Machine Learning service
description: Azure Blob ストレージで Azure Machine Learning の入力モデル データを収集する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 07/15/2019
ms.custom: seodec18
ms.openlocfilehash: 81ffe8618e07f2e49e4439ea57e254e5d37974f9
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227946"
---
# <a name="collect-data-for-models-in-production"></a>実稼働環境でモデルのデータを収集する

この記事では、Azure Kubernetes Cluster (AKS) にデプロイした Azure Machine Learning サービスの入力モデル データを収集し、Azure Blob ストレージに格納する方法について説明します。 

この方法を習得すると、収集したデータで次のことができるようになります。
* 実稼働データがモデルに追加されたときの[データの変動を監視する](how-to-monitor-data-drift.md)

* モデルの再トレーニングや最適化の時期をより適切に判断する

* 収集したデータを使用してモデルを再トレーニングする

## <a name="what-is-collected-and-where-does-it-go"></a>収集される情報とその行き先

次のデータを収集できます。
* Azure Kubernetes Cluster (AKS) にデプロイされた Web サービスのモデル**入力**データ (音声、画像、およびビデオは**収集されません**) 
  
* 実稼働環境入力データを使用したモデル予測

> [!Note]
> このデータの事前集計または事前計算は、現時点ではサービスに含まれていません。   

出力は Azure Blob に保存されます。 データは Azure Blob に追加されるので、分析の実行には任意のツールを選択できます。 

出力データは、BLOB 内で次のような構文のパスに保存されます。

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

- Azure Machine Learning service ワークスペース、スクリプトを保存するローカル ディレクトリ、Azure Machine Learning SDK for Python のインストール。 これらの前提条件を満たす方法については、[開発環境を構成する方法](how-to-configure-environment.md)に関するドキュメントを参照してください。

- Azure Kubernetes Service (AKS) にデプロイするトレーニング済みの機械学習モデル。 ない場合は、[イメージ分類モデルのトレーニング](tutorial-train-models-with-aml.md)に関するチュートリアルを参照してください。

- Azure Kubernetes Service クラスター。 作成してデプロイする方法については、[デプロイする方法とその場所](how-to-deploy-and-where.md)に関するドキュメントを参照してください。

- [環境を設定](how-to-configure-environment.md)し、[Monitoring SDK](https://aka.ms/aml-monitoring-sdk) をインストールします。

## <a name="enable-data-collection"></a>データ収集を有効にする
モデルのデプロイに使用されているツールが Azure Machine Learning Service かどうかにかかわらず、データ収集を有効にすることができます。 

有効にするには、次の手順を実行します。

1. スコア付けファイルを開きます。 

1. ファイルの先頭に[次のコード](https://aka.ms/aml-monitoring-sdk)を追加します。

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. `init()` 関数でデータ収集変数を宣言します。

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* は省略可能なパラメーターです。モデルに不要な場合、設定する必要はありません。 correlationId を指定すると、他のデータとのマッピングが簡単になります (例:LoanNumber、CustomerId など)
    
    *Identifier* は BLOB のフォルダー構造を構築するために後で使用します。"生" データと "処理済み" データの区別に使用できます。

3.  `run(input_df)` 関数に次のコード行を追加します。

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. AKS にサービスをデプロイしてもデータ収集は自動的に **true** に**設定されない**ので、次のように構成ファイルを更新する必要があります。 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    また、この構成を変更して、サービス監視の AppInsights を有効にすることもできます。
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. 新しいイメージを作成してサービスをデプロイするには、[デプロイする方法とその場所](how-to-deploy-and-where.md)に関するドキュメントを参照してください。


依存関係がインストールされたサービスが**環境ファイル**と**スコア付けファイル**に既に存在する場合は、次の手順でデータ収集を有効にします。

1. [Azure portal](https://portal.azure.com) に移動します。

1. ワークスペースを開きます。

1. **[デプロイ]**  ->  **[サービスの選択]**  ->  **[編集]** の順に選択します。

   ![サービスの編集](media/how-to-enable-data-collection/EditService.PNG)

1. **[詳細設定]** で、 **[モデルのデータ コレクションを有効にする]** をオフにします。 

    [![データ収集をオンにする](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   このウィンドウでは、[AppInsights 診断を有効にする] をオンにしてサービスの正常性を追跡することもできます。  

1. **[更新]** をクリックして変更を適用します。


## <a name="disable-data-collection"></a>データ収集を無効にする
データ収集はいつでも停止できます。 データ収集を無効にするには、Python コードまたは Azure portal を使用します。

+ オプション 1 - Azure portal で無効にする: 
  1. [Azure ポータル](https://portal.azure.com)にサインインします。

  1. ワークスペースを開きます。

  1. **[デプロイ]**  ->  **[サービスの選択]**  ->  **[編集]** の順に選択します。

     [![編集オプション](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. **[詳細設定]** で、 **[モデルのデータ コレクションを有効にする]** をオフにします。 

     [![データ コレクションをオフにする](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. **[更新]** をクリックして変更を適用します。

+ オプション 2 - Python を使用してデータ収集を無効にする:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>データを検証して分析する
好みの任意のツールを選択して、Azure Blob に収集されたデータを分析できます。 

BLOB のデータにすばやくアクセスするには:
1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. ワークスペースを開きます。
1. **[ストレージ]** をクリックします。

    [![ストレージ](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. BLOB 内での出力データへのパスは次のような構文です。

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Power BI でのモデル データの分析

1. [Power BI Desktop](https://www.powerbi.com) をダウンロードして開きます

1. **[データを取得]** を選択し、[**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources) をクリックします。

    [![PBI の BLOB のセットアップ](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. ストレージ アカウント名を追加し、ストレージ キーを入力します。 この情報は、BLOB の **[設定]** > [アクセス キー] で確認できます。 

1. コンテナー **modeldata** を選択し、 **[編集]** をクリックします。 

    [![PBI ナビゲーター](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. クエリ エディターで、[名前] 列の下をクリックし、ストレージ アカウント 1. モデル パスをフィルターに追加します。 注: 特定の年または月のファイルだけを検索する場合は、そのフィルター パスだけを展開します。 たとえば、3 月のデータだけを検索する場合は、/modeldata/subscriptionid>/resourcegroupname>/workspacename>/webservicename>/modelname>/modelversion>/identifier>/year>/3 とします

1. **名前**に基づいて関連するデータをフィルター処理します。 **予測**と**入力**を保存した場合、それごとにクエリを作成する必要があります。

1. **[コンテンツ]** 列の二重矢印をクリックして、ファイルを結合します。 

    [![PBI コンテンツ](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. [OK] をクリックして、データの事前読み込みを行います。

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. **[閉じて適用する]** をクリックしてかまいません。

1.  入力と予測を追加した場合、テーブルは **RequestId** によって自動的に関連付けられます。

1. モデル データでのカスタム レポートの作成を開始します。


### <a name="analyzing-model-data-using-databricks"></a>Databricks を使用したモデル データの分析

1. [Databricks ワークスペース](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)を作成します。 

1. Databricks ワークスペースに移動します。 

1. Databricks ワークスペースで、 **[Upload Data]\(データのアップロード\)** を選択します。

    [![DB のアップロード](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. 新しいテーブルを作成し、 **[他のデータ ソース]** > [Azure Blob Storage] > [Create Table in Notebook]\(ノートブックにテーブルを作成\) を選択します。

    [![DB テーブル](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. データの場所を更新します。 たとえば次のようになります。

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. データを表示および分析するには、テンプレートの手順に従います。 

## <a name="example-notebook"></a>ノートブックの例

[how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) ノートブックでは、この記事の概念を示しています。  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
