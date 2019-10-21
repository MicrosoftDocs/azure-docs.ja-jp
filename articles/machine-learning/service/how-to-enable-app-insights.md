---
title: Azure Application Insights を設定して ML モデルを監視する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でデプロイされた Web サービスを Azure Application Insights を使用して監視します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 07/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 785507e9ae12d8da564a223c8cdf544a98b8de61
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002881"
---
# <a name="monitor-your-azure-machine-learning-models-with-application-insights"></a>Application Insights を使用して Azure Machine Learning のモデルを監視する

この記事では、Azure Machine Learning のために Azure Application Insights を設定する方法について説明します。 Application Insights を使用して、以下を監視できます。
* 要求率、応答時間、および失敗率。
* 依存率、応答時間、および失敗率。
* 例外。

[Application Insights](../../azure-monitor/app/app-insights-overview.md) の詳細 


## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* Azure Machine Learning ワークスペース、スクリプトを保存するローカル ディレクトリ、および Azure Machine Learning SDK for Python のインストール。 これらの前提条件を満たす方法については、[開発環境を構成する方法](how-to-configure-environment.md)に関する記事を参照してください。
* Azure Kubernetes Service (AKS) または Azure コンテナー インスタンス (ACI) にデプロイするトレーニング済みの機械学習モデル。 ない場合は、[イメージ分類モデルのトレーニング](tutorial-train-models-with-aml.md)に関するチュートリアルを参照してください。


## <a name="use-sdk-to-configure"></a>SDK を使って構成する 

### <a name="update-a-deployed-service"></a>デプロイされたサービスを更新する
1. ワークスペースで、サービスを特定します。 `ws` の値は、ワークスペースの名前です。

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. サービスを更新し、Application Insights を有効にします。 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>サービスのカスタム トレースをログに記録する
カスタム トレースをログに記録する場合は、[デプロイする方法と場所](how-to-deploy-and-where.md)のドキュメントにある AKS または ACI のための標準のデプロイ プロセスに従います。 次に、次の手順を使用します。

1. print ステートメントを追加してスコアリング ファイルを更新します。
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. サービスの構成を更新します。
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. イメージをビルドし、[AKS](how-to-deploy-to-aks.md) または [ACI](how-to-deploy-to-aci.md) にデプロイします。  

### <a name="disable-tracking-in-python"></a>Python で追跡を無効にする

Application Insights を無効にするには、次のコードを使用します。

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="use-portal-to-configure"></a>ポータルを使って構成する

Azure portal で Application Insights を有効または無効にすることができます。

1. [Azure portal](https://portal.azure.com) でワークスペースを開きます。

1. **[デプロイ]** タブに移動し、Application Insights を有効にするサービスを選択します。

   [![[デプロイ] タブ上のサービスの一覧](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. **[編集]** を選択します。

   [![[編集] ボタン](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. **[詳細設定]** で、 **[AppInsights 診断を有効にする]** チェック ボックスをオンにします。

   [![診断を有効にするために選択されたチェック ボックス](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. 画面下部の **[更新]** を選択して変更を適用します。 

### <a name="disable"></a>Disable
1. [Azure portal](https://portal.azure.com) でワークスペースを開きます。
1. **[デプロイ]** を選択し、サービスを選択し、 **[編集]** を選択します。

   [![[編集] ボタンを使用する](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. **[詳細設定]** で、 **[AppInsights 診断を有効にする]** チェック ボックスをオフにします。 

   [![診断を有効にするためのチェック ボックスをオフ](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. 画面下部の **[更新]** を選択して変更を適用します。 
 

## <a name="evaluate-data"></a>データを評価する
サービスのデータは、Azure Machine Learning と同じリソース グループ内の Application Insights アカウントに保存されます。
表示するには:
1. [Azure portal](https://portal.azure.com) で Machine Learning service ワークスペースに移動し、Application Insights のリンクをクリックします。

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. **[概要]** タブを選択すると、サービスの基本的なメトリック セットが表示されます。

   [![概要](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. カスタム トレースを確認するには、 **[分析]** を選択します。
4. [スキーマ] セクションで **[トレース]** を選択します。 次に、 **[実行]** を選択してクエリを実行します。 データは表形式で表示され、スコアリング ファイルのカスタムの呼び出しにマップされます。 

   [![カスタム トレース](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Application Insights の使用方法の詳細については、「[Application Insights とは何か?](../../azure-monitor/app/app-insights-overview.md)」を参照してください。


## <a name="example-notebook"></a>ノートブックの例

[enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) ノートブックは、この記事の概念を示しています。 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順
運用環境でモデルに関するデータを収集することもできます。 記事「[実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)」を参照してください。 

[コンテナーに対する Azure Monitor](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json) に関するページも参照してください。
