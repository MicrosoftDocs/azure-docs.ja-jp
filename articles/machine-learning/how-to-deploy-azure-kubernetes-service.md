---
title: ML モデルを Kubernetes Service にデプロイする
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service を使用して Web サービスとして Azure Machine Learning のモデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: ef9c03b687bbc9b8fe736c872bbde14b8daba899
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519386"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service クラスターにモデルをデプロイする

Azure Machine Learning を使って Azure Kubernetes Service (AKS) 上の Web サービスとしてモデルをデプロイする方法について説明します。 Azure Kubernetes Service は高スケールの運用デプロイに適してします。 次のいずれかの機能が必要な場合は、Azure Kubernetes Service を使用します。

- __高速の応答時間__
- デプロイされたサービスの __自動スケール__
- __Logging__
- __モデル データ収集__
- __認証__
- __TLS 終了__
- GPU や Field Programmable Gate Array (FPGA) などの __ハードウェア アクセラレータ__ オプション

Azure Kubernetes Service にデプロイするときは、__ご利用のワークスペースに接続されている__ AKS クラスターにデプロイします。 AKS クラスターのワークスペースへの接続に関する詳細については、「[Azure Kubernetes Service クラスターを作成してアタッチする](how-to-create-attach-kubernetes.md)」を参照してください。

> [!IMPORTANT]
> Web サービスにデプロイする前にローカルでデバッグすることをお勧めします。 詳細については、「[ローカル デバッグ](./how-to-troubleshoot-deployment-local.md)」を参照してください。
>
> Azure Machine Learning の[ローカルの Notebook へのデプロイ](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)に関する記事を参照することもできます

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

- ワークスペースに登録されている機械学習モデル。 モデルが登録されていない場合は、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

- [Machine Learning サービス向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)、または [Azure Machine Learning Visual Studio Code 拡張機能](tutorial-setup-vscode-extension.md)。

- この記事の __Python__ コード スニペットは、次の変数が設定されていることを前提としています。

    * `ws` - 使用しているワークスペースに設定されている。
    * `model` - 登録済みのモデルに設定されている。
    * `inference_config` - モデルの推論構成に設定されている。

    これらの変数の設定について詳しくは、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

- この記事の __CLI__ スニペットは、`inferenceconfig.json` ドキュメントを作成済みであることを前提としています。 このドキュメントの作成の詳細については、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

- ワークスペースに接続された Azure Kubernetes Service クラスターです。 詳細については、「[Azure Kubernetes Service クラスターを作成してアタッチする](how-to-create-attach-kubernetes.md)」を参照してください。

    - GPU ノードまたは FPGA ノード (または特定の SKU) にモデルをデプロイする場合は、特定の SKU でクラスターを作成する必要があります。 既存のクラスターにセカンダリ ノード プールを作成し、そのセカンダリ ノード プールにモデルをデプロイすることはサポートされていません。

## <a name="understand-the-deployment-processes"></a>デプロイ プロセスについて

"デプロイ" という用語は、Kubernetes と Azure Machine Learning の両方で使用されます。 これらの 2 つのコンテキストでは、"デプロイ"の意味が異なります。 Kubernetes では、`Deployment` は具体的なエンティティで、宣言型の YAML ファイルで指定されます。 Kubernetes の `Deployment` には、定義されたライフサイクルが定義されており、`Pods` や `ReplicaSets` などの他の Kubernetes エンティティと具体的な関係があります。 Kubernetes については、「[What is Kubernetes?](https://aka.ms/k8slearning)」 (Kubernetes とは) にあるドキュメントとビデオを参照してください。

Azure Machine Learning では、"デプロイ"は、「プロジェクト リソースを使用可能にする、およびクリーンアップする」というより一般的な意味で使用されます。 Azure Machine Learning でデプロイ部分が考慮される手順は、次のとおりです。

1. プロジェクト フォルダー内のファイルの解凍。.amlignore または .gitignore で指定されたファイルは無視されます
1. コンピューティング クラスターのスケール アップ (Kubernetes 関連)
1. Dockerfile のビルドまたは計算ノードへのダウンロード (Kubernetes 関連)
    1. システムにより、次のハッシュが計算されます。 
        - 基本イメージ 
        - カスタム Docker の手順 (「[カスタム Docker ベース イメージを使用してモデルをデプロイする](./how-to-deploy-custom-docker-image.md)」を参照してください)
        - Conda 定義 YAML ([Azure Machine Learning でのソフトウェア環境の作成と使用](./how-to-use-environments.md)に関するページを参照してください)
    1. システムでは、ワークスペース Azure Container Registry (ACR) の検索で、このハッシュがキーとして使用されます
    1. 見つからない場合、グローバル ACR で一致するものが検索されます
    1. 見つからない場合、システムでは新しいイメージがビルドされます (これは、キャッシュされ、ワークスペース ACR にプッシュされます)
1. 計算ノード上の一時記憶域への圧縮されたプロジェクト ファイルのダウンロード
1. プロジェクト ファイルの解凍
1. `python <entry script> <arguments>` を実行する計算ノード
1. ログ、モデル ファイル、`./outputs` に書き込まれたその他のファイルの、ワークスペースに関連付けられているストレージ アカウントへの保存
1. 一時記憶域の削除など、コンピューティングのスケールダウン (Kubernetes 関連)

### <a name="azure-ml-router"></a>Azure ML ルーター

デプロイされたサービスへ受信推論要求をルーティングするフロントエンド コンポーネント (azureml-fe) は、必要に応じて自動的にスケーリングされます。 azureml-fe のスケーリングは、AKS クラスターの目的とサイズ (ノードの数) に基づいています。 クラスターの目的とノードは、[AKS クラスターを作成またはアタッチする](how-to-create-attach-kubernetes.md)ときに構成されます。 クラスターごとに 1 つの azureml-fe サービスがあり、複数のポッドで実行されている可能性があります。

> [!IMPORTANT]
> __dev-test__ として構成されているクラスターを使用する場合、セルフスケーラーは **無効** になります。

azureml-fe は、より多くのコアを使用するためのスケールアップ (垂直方向)、およびより多くのポッドを使用するためのスケールアウト (水平方向) の両方を行います。 スケールアップするかどうかを決定するときは、受信推論要求のルーティングにかかる時間が使用されます。 この時間がしきい値を超えると、スケールアップが発生します。 受信要求をルーティングする時間がしきい値を超え続けると、スケールアウトが発生します。

スケールダウンとスケールインを行うときは、CPU 使用率が使用されます。 CPU 使用率のしきい値に達した場合、フロントエンドでは、まずスケールダウンが行われます。 CPU 使用率がスケールインのしきい値にまで低下した場合は、スケールイン操作が行われます。 スケールアップとスケールアウトは、使用可能なクラスター リソースが十分にある場合にのみ発生します。

## <a name="understand-connectivity-requirements-for-aks-inferencing-cluster"></a>AKS 推論クラスターの接続要件を理解する

Azure Machine Learning で AKS クラスターを作成またはアタッチすると、次の 2 つのネットワーク モデルのいずれかで AKS クラスターがデプロイされます。
* Kubenet ネットワーク - AKS クラスターのデプロイ時に、通常はネットワーク リソースが作成され、構成されます。
* Azure Container Networking Interface (CNI) ネットワーク - AKS クラスターは、既存の仮想ネットワーク リソースと構成に接続されます。

最初のネットワーク モードでは、ネットワークが作成され、Azure Machine Learning service 用に適切に構成されます。 2 番目のネットワーク モードでは、クラスターが既存の仮想ネットワークに接続されるため、特に既存の仮想ネットワークでカスタム DNS を使用している場合は、お客様は AKS 推論クラスターの接続要件に注意し、AKS 推論の DNS 解決と送信接続を確認する必要があります。

次の図は、AKS 推論のすべての接続要件をキャプチャします。 黒い矢印は実際の通信を表し、青い矢印はドメイン名を表します。このドメイン名は、お客様が管理する DNS で解決されます。

 ![AKS 推論の接続要件](./media/how-to-deploy-aks/aks-network.png)

### <a name="overall-dns-resolution-requirements"></a>全体的な DNS 解決の要件
既存の VNET 内の DNS 解決は、お客様の管理下にあります。 次の DNS エントリが解決可能である必要があります。
* \<cluster\>.hcp.\<region\>.azmk8s.io の形式の AKS API サーバー
* Microsoft Container Registry (MCR): mcr.microsoft.com
* \<ACR name\>.azurecr.io の形式のお客様の Azure Container Registry (ARC)
* \<account\>.table.core.windows.net および \<account\>.blob.core.windows.net の形式の Azure Storage アカウント
* (省略可能) AAD 認証の場合: api.azureml.ms
* スコアリング エンドポイントのドメイン名。 Azure ML またはカスタム ドメイン名で自動生成されます。 自動生成されたドメイン名は、\<leaf-domain-label \+ auto-generated suffix\>.\<region\>.cloudapp.azure.com のようになります

### <a name="connectivity-requirements-in-chronological-order-from-cluster-creation-to-model-deployment"></a>クラスターの作成からモデル デプロイまで、時系列順の接続要件

AKS の作成またはアタッチのプロセスでは、Azure ML ルーター (azureml-fe) が AKS クラスターにデプロイされます。 Azure ML ルーターをデプロイするために、AKS ノードで次の処理が可能である必要があります。
* AKS API サーバーの DNS を解決する
* Azure ML ルーターの docker イメージをダウンロードするために、MCR の DNS を解決する
* 送信接続が必要な MCR からイメージをダウンロードする

azureml-fe はデプロイされるとすぐに開始しようとしますが、これには次の処理を行う必要があります。
* AKS API サーバーの DNS を解決する
* AKS API サーバーにそれ自体の他のインスタンスを検出するためにクエリを実行する (マルチポッド サービス)
* それ自体の他のインスタンスに接続する

azureml-fe が開始されると、正常に機能するために追加の接続が必要になります。
* Azure Storage に接続して動的構成をダウンロードする
* デプロイされたサービスが AAD 認証を使用している場合は、AAD 認証サーバー api.azureml.ms の DNS を解決して、そのサーバーと通信します。
* デプロイされたモデルを検出するために AKS API サーバーにクエリを実行する
* デプロイされたモデル POD と通信する

モデルのデプロイ時に、モデル デプロイが成功すると、AKS ノードでは次の操作が可能になります。 
* お客様の ACR の DNS を解決する
* お客様の ACR からイメージをダウンロードする
* モデルが格納されている Azure BLOB の DNS を解決する
* Azure BLOB からモデルをダウンロードする

モデルがデプロイされ、サービスが開始されると、azureml-fe は AKS API を使用してモデルを自動的に検出して、要求をそこにルーティングできるようになります。 モデル POD と通信できる必要があります。
>[!Note]
>デプロイされたモデルに接続が必要な場合 (外部データベースやその他の REST サービスへのクエリの実行やブログのダウンロードなど)、これらのサービスの DNS 解決と送信通信の両方を有効にする必要があります。

## <a name="deploy-to-aks"></a>AKS にデプロイする

Azure Kubernetes Service にモデルをデプロイするには、必要なコンピューティング リソースを記述した __デプロイ構成__ を作成します。 たとえば、コアの数やメモリなどです。 また、モデルと Web サービスのホストに必要な環境を記述した __推論構成__ も必要です。 推論構成の作成の詳細については、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

> [!NOTE]
> デプロイされるモデルの数は、デプロイごとに 1,000 モデル (コンテナーごと) に制限されます。

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

この例で使われているクラス、メソッド、パラメーターの詳細については、次のリファレンス ドキュメントをご覧ください。

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI を使用してデプロイするには、次のコマンドを使用します。 AKS コンピューティング先の名前に `myaks` を置き換えます。 登録されているモデルの名前とバージョンに `mymodel:1` を置き換えます。 このサービスに付ける名前に `myservice` を置き換えます。

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

詳細については、[az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) のリファレンスを参照してください。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

VS Code の使用については、「[モデルを展開して管理する](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)」を参照してください。

> [!IMPORTANT]
> VS Code を使ってデプロイするには、事前にワークスペースに AKS クラスターを作成するかアタッチしておく必要があります。

---

### <a name="autoscaling"></a>自動スケール

Azure ML モデル デプロイの自動スケールを処理するコンポーネントは、azureml-fe というスマート要求ルーターです。 すべての推論要求が通過するため、ここには、デプロイされたモデルを自動的にスケーリングするために必要なデータが保持されます。

> [!IMPORTANT]
> * **モデル デプロイでは、Kubernetes のポッドの水平オートスケーラー (HPA) を有効にしないでください**。 有効にすると、2 つの自動スケール コンポーネントが互いに競合します。 azureml-fe は、Azure ML によってデプロイされたモデルを自動スケールするように設計されています。この設計では、HPA が CPU 使用率やカスタム メトリック構成などの汎用メトリックから、モデルの使用率を推測または概算する必要があります。
> 
> * **azureml-fe によって、AKS クラスターのノードの数はスケーリングされません**。これは、予期しないコストの増加につながる可能性があるからです。 その代わりに、物理クラスターの境界内で **モデルのレプリカの数をスケーリング** します。 クラスター内のノードの数をスケーリングする必要がある場合は、手動でクラスターをスケーリングするか、[AKS クラスター オートスケーラーを構成する](../aks/cluster-autoscaler.md)ことができます。

自動スケールは、AKS Web サービスに `autoscale_target_utilization`、`autoscale_min_replicas`、および `autoscale_max_replicas` を設定することで制御できます。 自動スケールを有効にする方法を次の例に示します。

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

スケールアップ/スケールダウンの決定は、コンテナーの現在のレプリカの使用率に基づきます。 ビジー状態 (要求を処理中) のレプリカの数を現在のレプリカの総数で除算した数が、現在の使用率です。 この数が `autoscale_target_utilization` を超えると、さらにレプリカが作成されます。 これが下回ると、レプリカが減少します。 既定では、ターゲット使用率は 70% です。

レプリカの追加は、集中的かつ迅速に決定されます (約 1 秒)。 レプリカの削除は慎重に決定されます (約 1 分)。

必要なレプリカの数は、次のコードを使用して計算できます。

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

`autoscale_target_utilization`、`autoscale_max_replicas`、`autoscale_min_replicas` の設定に関する詳細については、[AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice) モジュール リファレンスを参照してください。

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>制御されたロールアウト (プレビュー) を使用して AKS にモデルをデプロイする

エンドポイントを使用して、制御された方法でモデル バージョンの分析とレベル上げを行います。 1 つのエンドポイントの背後に最大 6 つのバージョンをデプロイできます。 エンドポイントには次の機能があります。

* __各エンドポイントに送信されるスコアリング トラフィックの割合__ を構成します。 たとえば、トラフィックの 20% をエンドポイント 'test' にルーティングし、80% を 'production' にルーティングします。

    > [!NOTE]
    > トラフィックの 100% を指定しない場合、残りの割合は __既定の__ エンドポイント バージョンにルーティングされます。 たとえば、エンドポイント バージョン 'test' がトラフィックの 10% を取得し、"prod" が 30% を取得するよう構成した場合、残りの 60% は既定のエンドポイント バージョンに送信されます。
    >
    > 作成された最初のエンドポイント バージョンが、既定値として自動的に構成されます。 これは、エンドポイント バージョンを作成または更新するときに `is_default=True` を設定することによって変更できます。
     
* エンドポイント バージョンに、__コントロール__ または __処理__ のいずれかのタグを付けます。 たとえば、現在の運用エンドポイントのバージョンがコントロールであっても、潜在的な新しいモデルは処理バージョンとしてデプロイされます。 処理バージョンのパフォーマンスを評価した後、現在のコントロールのパフォーマンスを上回る場合は、新しい運用/コントロールに昇格される可能性があります。

    > [!NOTE]
    > コントロールは __1 つ__ しか持つことができません。 処理は複数持つことができます。

App Insights を有効にすると、エンドポイントおよびデプロイされたバージョンの運用メトリックを表示できます。

### <a name="create-an-endpoint"></a>エンドポイントの作成
モデルをデプロイする準備ができたら、スコアリング エンドポイントを作成し、最初のバージョンをデプロイします。 次の例は、SDK を使用してエンドポイントをデプロイおよび作成する方法を示しています。 最初のデプロイは既定のバージョンとして定義されます。これは、すべてのバージョンの未指定のトラフィック パーセンタイルが既定のバージョンに設定されることを意味します。  

> [!TIP]
> 次の例では、構成によって最初のエンドポイント バージョンがトラフィックの 20% を処理するように設定されます。 これは最初のエンドポイントであるため、既定のバージョンでもあります。 また、他の 80% のトラフィックについては他のバージョンがないため、同様に既定にルーティングされます。 トラフィックの一定の割合を受け取る他のバージョンがデプロイされるまでは、これが実質的にトラフィックの 100% を受け取ります。

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')

# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>バージョンの更新とエンドポイントへの追加

エンドポイントに別のバージョンを追加し、スコアリング トラフィック パーセンタイルをバージョンに合わせて構成します。 バージョンには、コントロールと処理バージョンの 2 種類があります。 1 つのコントロール バージョンと比較するために、複数の処理バージョンを使用できます。

> [!TIP]
> 次のコード スニペットで作成される 2 番目のバージョンは、トラフィックの 10% を受け入れます。 最初のバージョンは 20% に構成されているため、トラフィックの 30% だけが特定のバージョンに対して構成されます。 残りの 70% は最初のエンドポイント バージョンに送信されます。これは既定のバージョンでもあるためです。

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

既存のバージョンを更新するか、エンドポイント内で削除します。 バージョンの既定の種類、コントロールの種類、およびトラフィック パーセンタイルを変更できます。 次の例では、2 番目のバージョンがトラフィックを 40% に増やし、これが既定になります。

> [!TIP]
> 次のコード スニペットの後は、2 番目のバージョンが既定になります。 現在は 40% に構成されていますが、元のバージョンは引き続き 20% に構成されています。 これは、トラフィックの 40% がバージョン構成によって考慮されないことを意味します。 残りのトラフィックは 2 番目のバージョンにルーティングされます。これが既定になったためです。 実質的にトラフィックの 80% を受け取ります。

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```

## <a name="web-service-authentication"></a>Web サービス認証

Azure Kubernetes Service にデプロイする場合、__キーベース__ の認証は既定で有効になります。 __トークン ベース__ の認証を有効にすることもできます。 トークン ベースの認証では、クライアントが Azure Active Directory アカウントを使用して認証トークンを要求する必要があります。これは、展開されたサービスへの要求を行うために使用されます。

認証を __無効__ にするには、デプロイ構成の作成時に `auth_enabled=False` パラメーターを設定します。 次の例では、SDK を使用して認証を無効にします。

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

クライアント アプリケーションからの認証の詳細については、「[Web サービスとしてデプロイされた Azure Machine Learning モデルを使用する](how-to-consume-web-service.md)」を参照してください。

### <a name="authentication-with-keys"></a>キーによる認証

キー認証が有効になっている場合は、`get_keys` メソッドを使用して、プライマリおよびセカンダリ認証キーを取得できます。

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> キーを再生成する必要がある場合は、[`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29) を使用します

### <a name="authentication-with-tokens"></a>トークンによる認証

トークン認証を有効にするには、デプロイの作成時や更新時に `token_auth_enabled=True` パラメーターを設定します。 次の例では、SDK を使用してトークン認証を有効にします。

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

トークン認証が有効になっている場合は、`get_token` メソッドを使用して、JWT トークンとそのトークンの有効期限を取得できます。

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> トークンの `refresh_by` 時刻の後に新しいトークンを要求する必要があります。
>
> Azure Machine Learning ワークスペースは、ご利用の Azure Kubernetes Service クラスターと同じリージョンに作成することを強くお勧めします。 トークンを使用して認証するために、Web サービスは、Azure Machine Learning ワークスペースの作成先のリージョンに対して呼び出しを行います。 ワークスペースのリージョンが利用不可になった場合、ワークスペースとは異なるリージョンにクラスターがあったとしても、Web サービスのトークンがフェッチできなくなります。 その場合、ワークスペースのリージョンが利用可能な状態に戻るまで、事実上、トークン ベースの認証が利用できない状態となります。 また、クラスターのリージョンとワークスペースのリージョンとの間の距離が長くなるほど、トークンのフェッチにかかる時間も長くなります。
>
> トークンを取得するには、Azure Machine Learning SDK または [az ml service get-access-token](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-get-access-token) コマンドを使用する必要があります。


### <a name="vulnerability-scanning"></a>脆弱性のスキャン

Azure Security Center は、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と高度な脅威保護を実現します。 Azure Security Center に対して、リソースのスキャンと推奨事項の順守を許可する必要があります。 詳細については、[Azure Kubernetes Services と Security Center の統合](../security-center/defender-for-kubernetes-introduction.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [Kubernetes 認可に Azure RBAC を使用する](../aks/manage-azure-rbac.md)
* [Azure Virtual Network で推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [デプロイ トラブルシューティング](how-to-troubleshoot-deployment.md)
* [Web サービスを更新する](how-to-deploy-update-web-service.md)
* [TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
