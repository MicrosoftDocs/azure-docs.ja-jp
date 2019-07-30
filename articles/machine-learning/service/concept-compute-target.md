---
title: 'コンピューティング ターゲット: モデルをトレーニングしてデプロイする場所'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service を使用してモデルをトレーニングまたはデプロイする場所を定義します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: a7944b284a9c1c0424af54874554d05d49ad4b20
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806034"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>Azure Machine Learning service でのコンピューティング ターゲットとは 

**コンピューティング ターゲット**とは、トレーニング スクリプトを実行したり、サービスのデプロイをホストしたりする、指定されたコンピューティング リソース/環境のことです。 この場所は、ローカル コンピューターでも、クラウドベースのコンピューティング リソースでもかまいません。 コンピューティング ターゲットを使用すると、コードを変更しなくても、後でコンピューティング環境を容易に変更できます。  

一般的なモデル開発ライフサイクルでは、次のことを行う可能性があります。
1. 最初に、少量のデータを開発して実験します。 この段階では、コンピューティング ターゲットとしてローカル環境 (ローカル コンピューターまたはクラウド ベースの VM) をお勧めします。 
2. より大きなデータにスケールアップするか、またはこれらの[トレーニング コンピューティング ターゲット](#train)のいずれかを使用して分散トレーニングを実行します。  
3. モデルの準備ができたら、これらの[デプロイ コンピューティング ターゲット](#deploy)のいずれかを使用して、それを Web ホスティング環境または IoT デバイスにデプロイします。

コンピューティング ターゲットに使用するコンピューティング リソースは[ワークスペース](concept-workspace.md)にアタッチされています。 ローカル コンピューター以外のコンピューティング リソースは、ワークスペースのユーザーによって共有されます。

## <a name="train"></a> トレーニング コンピューティング ターゲット

Azure Machine Learning service では、異なるコンピューティング リソースに対してさまざまなサポートが提供されています。  また、独自のコンピューティング リソースを接続することもできますが、シナリオごとにサポートは異なることがあります。

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

[モデル トレーニングのためのコンピューティング ターゲットの設定と使用](how-to-set-up-training-targets.md)に関するページを参照してください。

## <a name="deploy"></a>デプロイ ターゲット

次のコンピューティング リソースを使用してモデル デプロイをホストできます。

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

[モデルをコンピューティング ターゲットにデプロイする場所と方法](how-to-deploy-and-where.md)に関するページを参照してください。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning コンピューティング (マネージド)

マネージド コンピューティング リソースは、Azure Machine Learning service によって作成および管理されます。 このコンピューティングは、機械学習ワークロード向けに最適化されています。 2019 年 5 月 30 日の時点では、Azure Machine Learning コンピューティングが唯一のマネージド コンピューティングとなります。 今後、他のマネージド コンピューティング リソースが追加される予定です。

Azure Machine Learning コンピューティングをトレーニングやバッチ推論 (プレビュー) に使用できます。  このコンピューティング リソースは、次のものを備えています。

* シングルノードまたはマルチノード クラスター
* 実行を送信するたびに自動スケーリング 
* 自動でのクラスター管理とジョブ スケジューリング 
* CPU と GPU の両方のリソースをサポートします

SDK または CLI を使用して、Azure portal で Azure Machine Learning コンピューティング インスタンスを作成できます。 作成されると、他の種類のコンピューティング ターゲットとは異なり、それが自動的にワークスペースの一部になります。

## <a name="unmanaged-compute"></a>アンマネージド コンピューティング

アンマネージド コンピューティング ターゲットは、Azure Machine Learning service によって管理され*ません*。 この種類のコンピューティング ターゲットは、Azure Machine Learning の外部で作成してからワークスペースに接続します。 アンマネージド コンピューティング リソースでは、機械学習ワークロードのパフォーマンスを維持するため、または向上させるために追加の手順が必要になる場合があります。

## <a name="next-steps"></a>次の手順

以下の項目について説明します。
* [モデルをトレーニングするためのコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md)
* [モデルをコンピューティング ターゲットにデプロイする](how-to-deploy-and-where.md)