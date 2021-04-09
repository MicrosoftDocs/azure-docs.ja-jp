---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/11/2020
ms.openlocfilehash: 09dd6e9a9d69797c2c33270d1620e861a052efe2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97505122"
---
モデルをホストするために使用するコンピューティング先は、デプロイされているエンドポイントのコストと可用性に影響を及ぼします。 次のテーブルを使用して、適切なコンピューティング先を選択します。

| コンピューティング ターゲット | 使用目的 | GPU のサポート | FPGA のサポート | 説明 |
| ----- | ----- | ----- | ----- | ----- |
| [ローカル &nbsp;Web&nbsp; サービス](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | テスト/デバッグ | &nbsp; | &nbsp; | 制限付きのテストとトラブルシューティングに使用。 ハードウェア アクセラレーションは、ローカル システムでのライブラリの使用に依存します。
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | リアルタイムの推論 |  [はい](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (Web サービスのデプロイ) | [はい](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |高スケールの運用デプロイに使用。 高速な応答時間と、デプロイされたサービスの自動スケールを提供します。 Azure Machine Learning SDK では、クラスターの自動スケールはサポートされていません。 AKS クラスター内のノードを変更するには、Azure portal でお使いの AKS クラスター用の UI を使用します。 <br/><br/> デザイナーでサポートされています。 |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | テストまたは開発 | &nbsp;  | &nbsp; | 必要な RAM が 48 GB より少ない低スケール CPU ベース ワークロードに使用。 <br/><br/> デザイナーでサポートされています。 |
| [Azure Machine Learning コンピューティング クラスター](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | バッチ推論&nbsp; | [はい](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) (機械学習パイプライン) | &nbsp;  | サーバーレス コンピューティングでバッチ スコアリングを実行します。 優先順位が中程度または低い VM をサポートします。 リアルタイムの推論はサポートされていません。|

> [!NOTE]
> ローカル、Azure Machine Learning コンピューティング、Azure Machine Learning コンピューティング クラスターなどのコンピューティング先は、トレーニングと実験のために GPU をサポートしていますが、"_Web サービスとしてデプロイされる場合_" に GPU を推論に使用することは、AKS でのみサポートされています。
>
> "_機械学習パイプラインでスコアリングする場合_" に GPU を推論に使用することは、Azure Machine Learning コンピューティングでのみサポートされています。
> 
> クラスター SKU を選択する場合は、まずスケールアップしてからスケールアウトします。モデルで必要とされる RAM の 150% が搭載されたマシンから始め、結果をプロファイルして、必要なパフォーマンスを備えたマシンを見つけます。 これについて学習した後は、同時推定のニーズに合うようにマシンの数を増やします。

> [!NOTE]
> * コンテナー インスタンスが適しているのは、サイズが 1 GB 未満の小さいモデルのみです。
> * より大きいモデルの開発およびテストには、単一ノードの AKS クラスターを使用します。