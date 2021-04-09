---
title: リモートでのモデル デプロイのトラブルシューティング
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service と Azure Container Instances での一般的な Docker デプロイ エラーの回避、解決、またはトラブルシューティング方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: contperf-fy20q4, devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 8bec083e62bec6a0311487c1e64e780ad14f451b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518265"
---
# <a name="troubleshooting-remote-model-deployment"></a>リモートでのモデル デプロイのトラブルシューティング 

Azure Machine Learning を使用する Azure Container Instances (ACI) と Azure Kubernetes Service (AKS) にモデルをデプロイする際に発生する可能性がある一般的なエラーをトラブルシューティング、解決、または回避する方法について説明します。

> [!NOTE]
> Azure Kubernetes Service (AKS) にモデルをデプロイする場合は、そのクラスターで [Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) を有効にすることをお勧めします。 これは、全体的なクラスターの正常性とリソースの使用状況を把握するのに役立ちます。 また、次のリソースも役立つ場合があります。
>
> * [AKS クラスターに影響する Resource Health イベントを確認する](../aks/aks-resource-health.md)
> * [Azure Kubernetes Service 診断](../aks/concepts-diagnostics.md)
>
> 異常または過負荷のクラスターにモデルをデプロイしようとすると、問題が発生することが予想されます。 AKS クラスターでの問題のトラブルシューティングに関するヘルプが必要な場合は、AKS サポートにお問い合わせください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)。
* [Azure CLI](/cli/azure/install-azure-cli)。
* [Azure Machine Learning 用 CLI 拡張機能](reference-azure-machine-learning-cli.md)。

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>機械学習モデルの Docker デプロイの手順

Azure Machine Learning で非ローカル コンピューティングにモデルをデプロイすると、次のことが行われます。

1. InferenceConfig の Environments オブジェクトに指定した Dockerfile が、ソース ディレクトリの内容と共にクラウドに送信されます
1. 以前にビルドされたイメージがコンテナー レジストリで利用できない場合は、新しい Docker イメージがクラウド内でビルドされ、ワークスペースの既定のコンテナー レジストリに格納されます。
1. コンテナー レジストリの Docker イメージが、コンピューティング先にダウンロードされます。
1. ワークスペースの既定の BLOB ストアがコンピューティング先にマウントされ、登録済みのモデルにアクセスできるようになります
1. エントリ スクリプトの `init()` 関数を実行して、Web サーバーが初期化されます
1. デプロイされたモデルが要求を受け取ると、`run()` 関数によってその要求が処理されます

ローカル デプロイを使用する場合の主な違いは、コンテナー イメージがローカル コンピューターでビルドされることです。そのため、ローカル デプロイ用に Docker をインストールする必要があります。

こうした大まかな手順を理解することは、エラーが発生している場所を把握するのに役立ちます。

## <a name="get-deployment-logs"></a>デプロイ ログを取得する

エラーをデバッグする最初の手順は、デプロイ ログを取得することです。 最初に、[こちらの手順](how-to-deploy-and-where.md#connect-to-your-workspace)に従って、ワークスペースに接続します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

デプロイされた Web サービスからログを取得するには、以下を実行します。

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


`ws` と呼ばれる `azureml.core.Workspace` 型のオブジェクトがあると仮定した場合、以下を実行できます。

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>ローカル デバッグ

モデルを ACI または AKS にデプロイする際に問題が発生した場合は、ローカル Web サービスとしてデプロイしてください。 ローカル Web サービスを使用すると、問題のトラブルシューティングが簡単になります。 ローカルでのデプロイのトラブルシューティングについては、[ローカルでのトラブルシューティングに関する記事](./how-to-troubleshoot-deployment-local.md)を参照してください。

## <a name="container-cannot-be-scheduled"></a>コンテナーをスケジュールできない

Azure Kubernetes Service コンピューティング ターゲットにサービスをデプロイするときに、Azure Machine Learning では、要求された量のリソースを使用してサービスをスケジュールすることを試みます。 5 分後、適切な量のリソースがある利用可能なノードがクラスターにない場合、デプロイは失敗します。 エラー メッセージは `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` です。 このエラーに対処するには、ノードを追加するか、ノードの SKU を変更するか、またはサービスのリソース要件を変更します。 

通常、このエラー メッセージでは、追加する必要があるリソースが示されます。たとえば、"`0/3 nodes are available: 3 Insufficient nvidia.com/gpu`" というエラー メッセージが表示された場合、これは、サービスに GPU が必要であり、クラスターの 3 つのノードには利用可能な GPU がないことを意味します。 これに対処するには、ノードを追加するか (GPU SKU を使用している場合)、GPU 対応の SKU に切り替えるか (SKU が GPU 対応でない場合)、GPU を必要としないように環境を変更します。  

## <a name="service-launch-fails"></a>サービスを起動できない

イメージが正常にビルドされると、デプロイ構成を使用して、コンテナーの起動が試行されます。 コンテナーの起動プロセスの一部として、スコアリング スクリプトの `init()` 関数が呼び出されます。 `init()` 関数でキャッチされない例外がある場合、エラー メッセージに **CrashLoopBackOff** エラーが表示されることがあります。

[Docker ログの検査](how-to-troubleshoot-deployment-local.md#dockerlog)に関する記事の情報を使用してください。

## <a name="function-fails-get_model_path"></a>get_model_path() 関数が失敗する

多くの場合、スコアリング スクリプトの `init()` 関数では、コンテナー内のモデル ファイルまたはモデル ファイルのフォルダーを見つける目的で [Model.get_model_path()](/python/api/azureml-core/azureml.core.model.model#get-model-path-model-name--version-none---workspace-none-) 関数が呼び出されます。 モデル ファイルまたはフォルダーが見つからない場合、この関数は失敗します。 このエラーをデバッグする最も簡単な方法は、Container シェルで以下の Python コードを実行することです。

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

この例では、スコアリング スクリプトによってモデル ファイルまたはフォルダーの存在が予期されるコンテナー内のローカル パス (`/var/azureml-app` の相対パス) が出力されます。 その後、ファイルまたはフォルダーが予想される場所にあるかどうかを確認できます。

ログ レベルを DEBUG に設定すると、追加情報が記録される場合があり、エラーの特定に利用できる可能性があります。

## <a name="function-fails-runinput_data"></a>run(input_data) 関数が失敗する

サービスが正常にデプロイされたが、スコアリング エンドポイントにデータを投稿するとクラッシュする場合、代わりに詳細なエラー メッセージが返されるように `run(input_data)` 関数にエラーをキャッチするステートメントを追加できます。 次に例を示します。

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**注**:`run(input_data)` 呼び出しからエラー メッセージを返すことは、デバッグ目的のみで行ってください。 セキュリティ上の理由から、運用環境ではこの方法でエラー メッセージを返さないでください。

## <a name="http-status-code-502"></a>HTTP 状態コード 502

502 状態コードは、サービスが例外をスローしたか、score.py ファイルの `run()` メソッドでクラッシュしたことを示します。 この記事の情報を使用して、ファイルをデバッグします。

## <a name="http-status-code-503"></a>HTTP 状態コード 503

Azure Kubernetes Service のデプロイでは、自動スケールがサポートされているため、レプリカを加えて、追加の負荷に対応することができます。 自動スケールは、**段階的な** 負荷の変化に対処するように設計されています。 1 秒あたりに受信する要求の量が急増した場合、クライアントは HTTP 状態コード 503 を受信する可能性があります。 オートスケーラーは迅速に反応しますが、AKS で追加のコンテナーを作成するにはかなりの時間がかかります。

スケールアップ/スケールダウンの決定は、コンテナーの現在のレプリカの使用率に基づきます。 ビジー状態 (要求を処理中) のレプリカの数を現在のレプリカの総数で除算した数が、現在の使用率です。 この数が `autoscale_target_utilization` を超えると、さらにレプリカが作成されます。 これが下回ると、レプリカが減少します。 レプリカの追加は、集中的かつ迅速に決定されます (約 1 秒)。 レプリカの削除は慎重に決定されます (約 1 分)。 既定では、自動スケールの目標使用率は **70%** に設定されています。これは、1 秒あたりに受信する要求の量 (RPS) が **最大 30%** 増加した場合まで対処できることを意味します。

状態コード 503 を防ぐには、次の 2 つのことが役立ちます。

> [!TIP]
> これらの 2 つの方法は、個別に使用することも、組み合わせて使用することもできます。

* 自動スケールによって新しいレプリカが作成される使用率レベルを変更します。 使用率の目標を調整するには、`autoscale_target_utilization` をより小さい値に設定します。

    > [!IMPORTANT]
    > この変更によって、レプリカの作成時間は *短縮* されません。 その代わり、より低い使用率しきい値で作成されます。 サービスの使用率が 70% になるまで待機するのでなく値を 30% に変更すると、使用率が 30% になった段階でレプリカが作成されます。
    
    現在の最大数のレプリカが Web サービスによって既に使用されていて、状態コード 503 が引き続き発生する場合は、`autoscale_max_replicas` の値を大きくして、レプリカの最大個数を増やします。

* レプリカの最小個数を変更します。 レプリカの最小個数を増やすと、着信トラフィックの急増に対処するためのプールが大きくなります。

    レプリカの最小個数を増やすには、`autoscale_min_replicas` をより大きな値に設定します。 必要なレプリカ個数は次のコードを使用して計算できるので、値をご利用のプロジェクトに固有の値に置換します。

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

    > [!NOTE]
    > 受信する要求の量が、新しい最小レプリカ数で対処できるレベルを超えて急増した場合、再び 503 が発生する可能性があります。 たとえば、ご利用のサービスへのトラフィックが増えた場合、レプリカの最小個数を増やすことが必要な場合があります。

`autoscale_target_utilization`、`autoscale_max_replicas`、`autoscale_min_replicas` の設定方法の詳細については、[AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice) モジュール リファレンスを参照してください。

## <a name="http-status-code-504"></a>HTTP 状態コード 504

504 状態コードは、要求がタイムアウトしたことを示します。既定のタイムアウトは 1 分です。

タイムアウト値を増やすか、score.py を変更して不要な呼び出しを削除することで、サービスの高速化を試みることができます。 これらのアクションで問題が解決しない場合は、この記事の情報を使用して score.py ファイルをデバッグします。 コードが応答なし状態または無限ループになっている可能性があります。

## <a name="other-error-messages"></a>その他のエラー メッセージ

次のエラーに対して、これらのアクションを実行します。

|エラー  | 解決方法  |
|---------|---------|
|Web サービスのデプロイ時のイメージ構築エラー     |  イメージ構成用の pip の依存関係として "pynacl==1.2.1" を Conda ファイルに追加します。       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   デプロイで使用される VM の SKU を、メモリがより多い SKU に変更します。 |
|FPGA エラー     |  FPGA クォータを要求して承認されるまでは、FPGA にモデルをデプロイできません。 アクセスを要求するには、クォータ要求フォーム https://aka.ms/aml-real-time-ai に入力します。       |

## <a name="advanced-debugging"></a>高度なデバッグ

モデル デプロイに含まれる Python コードを対話的にデバッグする必要が生じることがあります。 たとえば、エントリ スクリプトが失敗し、追加のログ記録によっても理由を特定できない場合がこれにあたります。 Visual Studio Code と debugpy を使用すると、Docker コンテナー内で実行されているコードにアタッチできます。

詳細については、[VS Code での対話型デバッグのガイド](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments)を参照してください。

## <a name="model-deployment-user-forum"></a>[モデル デプロイ ユーザー フォーラム](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>次のステップ

デプロイの詳細については、以下を参照してください。

* [デプロイする方法とその場所](how-to-deploy-and-where.md)
* [チュートリアル:モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)
* [ローカルで実験を実行およびデバッグする方法](./how-to-debug-visual-studio-code.md)