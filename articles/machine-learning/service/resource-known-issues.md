---
title: 既知の問題とトラブルシューティング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning に関する既知の問題、回避策、トラブルシューティングの一覧を示します。
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 8fbb09ecf09008c25c84a11c7b43dfb26450e30a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338752"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Azure Machine Learning の既知の問題とトラブルシューティング

この記事は、Azure Machine Learning の使用時に発生したエラーや障害を見つけて修正するのに役立ちます。

## <a name="visual-interface-issues"></a>ビジュアル インターフェイスの問題

機械学習サービスのビジュアル インターフェースの問題。

### <a name="long-compute-preparation-time"></a>計算の準備時間が長い

新しい計算の作成や計算の終了に、数分またはそれ以上の時間がかかる場合があります。 現在、担当チームが最適化に取り組んでいます。


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>データセットのみを含む実験を実行できない 

データセットのみを含む実験を実行して、データセットを視覚化したい場合があります。 しかし、現在、データセットのみを含む実験は実行できません。 現在、この問題の解決に積極的に取り組んでいます。
 
修正が行われる前に、データセットを任意のデータ変換モジュール (データセット内の列の選択、メタデータの編集、データの分割など) に接続し、実験を実行できます。 その後、データセットを視覚化できます。 

次の図は、その方法を示しています: ![visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>SDK のインストールに関する問題

**エラー メッセージ:"Cannot uninstall 'PyYAML'" ('PyYAML' をアンインストールできません)**

Azure Machine Learning SDK for Python:PyYAML は distutils によってインストールされるプロジェクトです。 したがって、部分的なアンインストールが行われた場合、それに属しているファイルを正確に判別できません。 このエラーを無視して SDK のインストールを続行するには、次を使用します。

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**エラー メッセージ: `ERROR: No matching distribution found for azureml-dataprep-native`**

Anaconda の Python 3.7.4 ディストリビューションには、azureml-sdk のインストールが壊れるバグがあります。 この問題については、こちらの [GitHub の問題](https://github.com/ContinuumIO/anaconda-issues/issues/11195)で説明されています。これは、次のコマンドを使用して新しい Conda 環境を作成することによって回避できます。
```bash
conda create -n <env-name> python=3.7.3
```
これにより、Python 3.7.3 を使用する Conda 環境が作成され、この環境には 3.7.4 でのインストールの問題はありません。

## <a name="trouble-creating-azure-machine-learning-compute"></a>Azure Machine Learning コンピューティングの作成に関する問題

まれにですが、GA リリースの前に Azure portal から Azure Machine Learning ワークスペースを作成したユーザーが、そのワークスペースに Azure Machine Learning コンピューティングを作成できないことがあります。 サービスにサポート要求を送るか、ポータルまたは SDK を使って新しいワークスペースを作成してすぐにブロックを解除することができます。

## <a name="image-building-failure"></a>イメージ ビルド エラー

Web サービスのデプロイ時のイメージ ビルド エラー。 回避策は、イメージ構成のために pip の依存関係として "pynacl==1.2.1" を Conda ファイルに追加することです。

## <a name="deployment-failure"></a>デプロイの失敗

`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>` を監視する場合、デプロイで使用されている VM の SKU を、メモリがより多い SKU に変更してください。

## <a name="fpgas"></a>FPGA

FPGA クォータを要求して承認されるまでは、FPGA にモデルをデプロイできません。 アクセスを要求するには、クォータ要求フォーム https://aka.ms/aml-real-time-ai に入力します。

## <a name="automated-machine-learning"></a>自動化された機械学習

Tensor Flow の自動化された機械学習は現在、Tensor Flow バージョン 1.13 でサポートされていません。 このバージョンをインストールすると、パッケージの依存関係が動作を停止することになります。 Microsoft は、将来のリリースでこの問題を解決するよう取り組んでいます。 

### <a name="experiment-charts"></a>実験グラフ

自動化された ML の実験のイテレーションで示される二項分類グラフ (精度と再現率、ROC、ゲイン カーブなど) は、4/12 以降のユーザー インターフェイスでは正しくレンダリングされません。 グラフのプロットは現在、逆の結果を示しており、パフォーマンスが良いモデルほど低い結果で示されています。 解決策を調査中です。

## <a name="databricks"></a>Databricks

Databricks と Azure Machine Learning の問題。

### <a name="failure-when-installing-packages"></a>パッケージをインストールする際の失敗

追加のパッケージがインストールされていると、Azure Databricks で Azure Machine Learning SDK のインストールが失敗します。 `psutil` のようなパッケージでは、競合が発生することがあります。 インストール エラーを回避するには、ライブラリ バージョンを止めてパッケージをインストールします。 この問題は Databricks に関連したもので、Azure Machine Learning SDK には関連はありません。 他のライブラリでもこの問題が発生する場合があります。 例:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

別の方法として、Python ライブラリでインストールの問題が発生し続けている場合は、初期化スクリプトを使用することができます。 この方法は、公式にはサポートされていません。 詳細については、「[Cluster-scoped init scripts](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)」を参照してください。

### <a name="cancel-an-automated-machine-learning-run"></a>自動化された機械学習の実行をキャンセルする

自動化された機械学習機能を Azure Databricks で使用しているときに、実行をキャンセルして新しい実験の実行を開始するには、Azure Databricks クラスターを再起動してください。

### <a name="10-iterations-for-automated-machine-learning"></a>自動化された機械学習での 10 回を超える繰り返し

自動化された機械学習の設定で、繰り返し回数が 10 回を超えている場合は、実行を送信するときに `show_output` を `False` に設定します。

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Azure Machine Learning SDK/自動化された機械学習のウィジェット

Azure Machine Learning SDK ウィジェットは、Databricks ノートブックではサポートされていません。この理由は、ノートブックが HTML ウィジェットを解析できないからです。 Azure Databricks のノートブック セルで次の Python コードを使用することにより、portal でウィジェットを表示することができます。

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>インポート エラー:'pandas.core.indexes' という名前のモジュールはありません

自動化された機械学習を使用しているときにこのエラーが表示される場合は、以下を実行します。

1. 次のコマンドを実行して、Azure Databricks クラスターに 2 つのパッケージをインストールします。 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. クラスターをデタッチし、次にクラスターをノートブックに再アタッチします。 

これらの手順で問題が解決しない場合は、クラスターを再起動してみてください。

### <a name="failtosendfeather"></a>FailToSendFeather

Azure Databricks クラスター上のデータの読み取り時に `FailToSendFeather` エラーが表示された場合は、次の解決策を参照してください。

* `azureml-sdk[automl_databricks]` パッケージを最新バージョンにアップグレードします。
* `azure-dataprep` バージョン 1.1.8 以降を追加します。
* `pyarrow` バージョン 0.11 以降を追加します。

## <a name="azure-portal"></a>Azure ポータル

SDK またはポータルで共有リンクからワークスペースを直接表示した場合、拡張機能のサブスクリプション情報を含む通常の概要ページを表示できません。 また、別のワークスペースに切り替えることもできません。 別のワークスペースを表示する必要がある場合の回避策としては、[Azure portal](https://portal.azure.com) に直接移動し、ワークスペース名を検索してください。

## <a name="diagnostic-logs"></a>診断ログ

サポートを依頼するときに診断情報を提供できると、役に立つ場合があります。 いくつかのログを確認するには、[Azure portal](https://portal.azure.com) にアクセスし、自分のワークスペースに移動して、 **[ワークスペース]、[実験]、[実行]、[ログ]** の順に選択します。  この情報は、[ワークスペースのランディング ページ (プレビュー)](https://ml.azure.com) の **[実験]** セクションでも確認できます。

> [!NOTE]
> Azure Machine Learning では、AutoML やトレーニング ジョブを実行する Docker コンテナーなど、トレーニング中にさまざまなソースからの情報がログに記録すされます。 これらのログの多くについては、ドキュメントに記載されていません。 問題が発生し、Microsoft サポートに問い合わせた場合、サポートはトラブルシューティングの際にこれらのログを使用できる可能性があります。

## <a name="activity-logs"></a>アクティビティ ログ

Azure Machine Learning ワークスペース内の一部の操作では、情報は__アクティビティ ログ__に記録されません。 たとえば、トレーニングの開始やモデルの登録などです。

これらの操作の一部はワークスペースの __[アクティビティ]__ 領域に表示されますが、アクティビティを開始したユーザーは示されません。

## <a name="resource-quotas"></a>リソース クォータ

Azure Machine Learning の使用時に扱うことがあるリソース クォータの詳細は[こちら](how-to-manage-quotas.md)にあります。

## <a name="authentication-errors"></a>認証エラー

リモート ジョブからコンピューティング ターゲットで管理操作を実行すると、次のいずれかのエラーが発生します。

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

たとえば、リモート実行のために送信される ML パイプラインからコンピューティング ターゲットを作成またはアタッチしようとすると、エラーが発生します。

## <a name="overloaded-azurefile-storage"></a>オーバーロードされた AzureFile ストレージ

エラー `Unable to upload project files to working directory in AzureFile because the storage is overloaded` が発生する場合は、次の回避策を適用してください。

データ転送などの他のワークロードにファイル共有を使用している場合は、BLOB を使用して、ファイル共有を実行の送信のために自由に使用できるようにすることをお勧めします。 2 つの異なるワークスペース間でワークロードを分割することもできます。

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure Kubernetes Service の Web サービスのエラー 

Azure Kubernetes Service の多くの Web サービスのエラーは、`kubectl` を使用してクラスターに接続することでデバッグできます。 以下を実行して、Azure Kubernetes Service クラスターの `kubeconfig.json` を取得できます

```bash
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>AKS クラスター内の Azure Machine Learning コンポーネントの更新

Azure Kubernetes Service クラスターにインストールされている Azure Machine Learning コンポーネントの更新プログラムは、手動で適用する必要があります。 これらの更新プログラムを適用するには、Azure Machine Learning ワークスペースからクラスターをデタッチしてから、クラスターをワークスペースに再アタッチします。 クラスターで SSL が有効な場合は、クラスターを再接続するときに SSL 証明書と秘密キーを指定する必要があります。 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

SSL 証明書と秘密キーがなくなった場合、または Azure Machine Learning によって生成された証明書を使用している場合は、`kubectl` を使用してクラスターに接続し、シークレット `azuremlfessl` を取得することで、クラスターをデタッチする前にファイルを取得できます。

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes では、base-64 でエンコードされた形式でシークレットが格納されます。 シークレットの `cert.pem` コンポーネントと `key.pem` コンポーネントを `attach_config.enable_ssl` に提供する前に、base-64 でデ コードする必要があります。 

## <a name="recommendations-for-error-fix"></a>エラー修正に関する推奨事項
一般的な見地に基づいて、Azure ML においていくつかの一般的なエラーを修正するための Azure ML での推奨事項を示します。

### <a name="moduleerrors-no-module-named"></a>ModuleErrors (モジュール名が指定されていない)
Azure ML で実験を送信する際に ModuleErrors が発生した場合、トレーニング スクリプトではパッケージがインストールされていることを期待しているのに、それが追加されていないことを意味します。 パッケージ名を指定すると、Azure ML では、トレーニングに使用される環境にパッケージがインストールされます。 

[Estimator](https://docs.microsoft.com/en-us/azure/machine-learning/service/concept-azure-machine-learning-architecture#estimators) を使用して実験を送信する場合は、パッケージのインストール元に基づく Estimator 内の `pip_packages` または `conda_packages` パラメータ―を使って、パッケージ名を指定できます。 また、`conda_dependencies_file` を使用してすべての依存関係を含む yml ファイルを指定したり、`pip_requirements_file` パラメーターを使用して txt ファイル内のすべての pip 要件を一覧表示したりすることも可能です。

Azure ML では、Tensorflow、PyTorch、Chainer、および SKLearn に対応するフレームワーク固有の Estimator も提供されています。 これらの Estimator を使用すると、ユーザーに代わって、トレーニングに使用される環境にフレームワークの依存関係が確実にインストールされます。 前述のように、追加の依存関係を指定することもできます。 
 
 Azure ML によって保守される docker イメージとそのコンテンツは、[AzureML のコンテナー](https://github.com/Azure/AzureML-Containers)内で確認できます。
フレームワーク固有の依存関係は、それぞれのフレームワークのドキュメント ([Chainer](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks)、[PyTorch](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks)、[TensorFlow](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks)、[SKLearn](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)) に示されています。

>[注意!] 特定のパッケージが Azure ML によって保守されるイメージと環境に追加できるほど十分に一般的だと考えられる場合は、[AzureML のコンテナー](https://github.com/Azure/AzureML-Containers)に関するページで、GitHub の問題を作成してください。 
 
 ### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (名前が定義されていない)、AttributeError (オブジェクトに属性がない)
この例外は、トレーニング スクリプトに起因しているはずです。 Azure portal からログ ファイルを参照して、定義されていない特定の名前または属性エラーに関する詳細情報を取得できます。 SDK から、`run.get_details()` を使用して、エラー メッセージを確認できます。 これにより、実行に対して生成されたすべてのログ ファイルも一覧表示されます。 トレーニング スクリプトを確認して、再試行する前に必ずエラーを修正してください。 

### <a name="horovod-is-shutdown"></a>horovod がシャットダウンされる
ほとんどの場合、この例外は、プロセスの 1 つにおいて horovod のシャットダウンを引き起こした基になる例外が発生したことを意味します。 MPI ジョブの各ランクでは、Azure ML 内にある固有の専用ログ ファイルが取得されます。 これらのログは、`70_driver_logs` という名前です。 分散トレーニングの場合、ログを区別しやすいようにログ名の末尾に `_rank` が付与されます。 horovod シャットダウンの原因となった厳密なエラーを見つけるには、すべてのログ ファイルを確認して、driver_log ファイルの末尾にある `Traceback` を探します。 これらのファイルの 1 つから、基になる実際の例外がわかります。 
