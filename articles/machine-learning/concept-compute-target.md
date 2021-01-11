---
title: コンピューティング ターゲットとは
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用してモデルをトレーニングまたはデプロイする場所を定義します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2020
ms.openlocfilehash: 27c129af9fbf3e76c6c57fbf084596876b51955b
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141927"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure Machine Learning でのコンピューティング ターゲットとは 

**コンピューティング ターゲット**とは、トレーニング スクリプトを実行したり、サービスのデプロイをホストしたりする、指定されたコンピューティング リソース/環境のことです。 この場所は、ローカル コンピューターでも、クラウドベースのコンピューティング リソースでもかまいません。 コンピューティング ターゲットを使用すると、コードを変更しなくても、後でコンピューティング環境を容易に変更できます。  

一般的なモデル開発ライフサイクルでは、次のことを行う可能性があります。
1. 最初に、少量のデータを開発して実験します。 この段階では、コンピューティング ターゲットとしてローカル環境 (ローカル コンピューターまたはクラウド ベースの VM) をお勧めします。 
2. より大きなデータにスケールアップするか、またはこれらの[トレーニング コンピューティング ターゲット](#train)のいずれかを使用して分散トレーニングを実行します。  
3. モデルの準備ができたら、これらの[デプロイ コンピューティング ターゲット](#deploy)のいずれかを使用して、それを Web ホスティング環境または IoT デバイスにデプロイします。

コンピューティング ターゲットに使用するコンピューティング リソースは[ワークスペース](concept-workspace.md)にアタッチされています。 ローカル コンピューター以外のコンピューティング リソースは、ワークスペースのユーザーによって共有されます。

## <a name="training-compute-targets"></a><a name="train"></a> トレーニング コンピューティング ターゲット

Azure Machine Learning では、異なるコンピューティング リソースに対してさまざまなサポートが提供されています。  また、独自のコンピューティング リソースを接続することもできますが、シナリオごとにサポートは異なることがあります。

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

[モデル トレーニングのためのコンピューティング ターゲットの設定と使用](how-to-set-up-training-targets.md)に関するページを参照してください。

## <a name="deployment-targets"></a><a name="deploy"></a>デプロイ ターゲット

次のコンピューティング リソースを使用してモデル デプロイをホストできます。

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

[モデルをコンピューティング ターゲットにデプロイする場所と方法](how-to-deploy-and-where.md)に関するページを参照してください。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning コンピューティング (マネージド)

マネージド コンピューティング リソースは、Azure Machine Learning によって作成され、管理されます。 このコンピューティングは、機械学習ワークロード向けに最適化されています。 Azure Machine Learning コンピューティング クラスターと[コンピューティング インスタンス](concept-compute-instance.md)だけがマネージド コンピューティングです。 今後、他のマネージド コンピューティング リソースが追加される予定です。

Azure Machine Learning コンピューティング インスタンスまたはコンピューティング クラスターは次の場所から作成できます。
* Azure Machine Learning Studio
* Azure portal
* Python SDK [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) と [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py) クラス
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (プレビュー)
* Resource Manager テンプレート。 テンプレートの例については、[Azure Machine Learning コンピューティング テンプレートの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute)に関する記事を参照してください。
* 機械学習の [Azure CLI 用拡張機能](reference-azure-machine-learning-cli.md#resource-management)。  

作成されると、これらのコンピューティング リソースは、他の種類のコンピューティング ターゲットとは異なり、自動的にワークスペースの一部になります。


|機能  |コンピューティング クラスター  |コンピューティング インスタンス  |
|---------|---------|---------|
|シングルノードまたはマルチノード クラスター     |    **&check;**       |         |
|実行を送信するたびに自動スケーリング     |     **&check;**      |         |
|自動でのクラスター管理とジョブ スケジューリング     |   **&check;**        |     **&check;**      |
|CPU と GPU の両方のリソースをサポートします     |  **&check;**         |    **&check;**       |


> [!NOTE]
> コンピューティング クラスターがアイドル状態の場合、ノードは 0 に自動スケーリングされるため、使用されていないときは料金がかかりません。  ただし、コンピューティング *インスタンス*は常にオンであり、自動スケーリングされません。  追加コストを回避するには、使用していないときに[コンピューティング インスタンスを停止](tutorial-1st-experiment-sdk-train.md#stop-the-compute-instance)します。

### <a name="supported-vm-series-and-sizes"></a>サポートされている VM シリーズおよびサイズ

Azure Machine Learning でマネージド コンピューティング リソースのノード サイズを選択すると、Azure で使用可能な VM サイズの中から選択できます。 Azure では、さまざまなワークロードに対応する Linux および Windows の幅広いサイズが提供されています。 さまざまな [VM の種類とサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)の詳細については、こちらを参照してください。

VM サイズを選択する際には次のような例外と制限事項があります。
* 一部の VM シリーズは、Azure Machine Learning でサポートされていません。
* 一部の VM シリーズは制限されています。 制限付きシリーズを使用するには、サポートに連絡し、そのシリーズのクォータの引き上げを依頼してください。 サポートへのお問い合わせについては、[Azure のサポート オプション](https://azure.microsoft.com/support/options/)に関するページを参照してください。

サポートされているシリーズと制限の詳細については、次の表を参照してください。 

| **サポートされている VM シリーズ**  | **制限事項** |
|------------|------------|
| D | なし |
| Dv2 | なし |  
| DSv2 | なし |  
| FSv2 | なし | 
| HBv2 | 承認が必要 |  
| HCS | 承認が必要 |  
| M | 承認が必要 |
| NC | なし |    
| NCsv2 | 承認が必要 |
| NCsv3 | 承認が必要 |  
| NDs | 承認が必要 |
| NDv2 | 承認が必要 |
| NV | なし |
| NVv3 | 承認が必要 | 


これらの VM シリーズは Azure Machine Learning でサポートされていますが、すべての Azure リージョンで使用できるとは限りません。 VM シリーズが使用可能かどうかを確認するには、次のページを参照してください。「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)」。

## <a name="unmanaged-compute"></a>アンマネージド コンピューティング

アンマネージド コンピューティング先は、Azure Machine Learning によって管理*されません*。 この種類のコンピューティング ターゲットは、Azure Machine Learning の外部で作成してからワークスペースに接続します。 アンマネージド コンピューティング リソースでは、機械学習ワークロードのパフォーマンスを維持するため、または向上させるために追加の手順が必要になる場合があります。

## <a name="next-steps"></a>次のステップ

具体的には、次の方法を学習します。
* [モデルをトレーニングするためのコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md)
* [モデルをコンピューティング ターゲットにデプロイする](how-to-deploy-and-where.md)
