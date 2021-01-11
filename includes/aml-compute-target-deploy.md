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
ms.date: 06/25/2020
ms.openlocfilehash: bd3ac8d512c1b9d151c0dc549ffeee6a05c7f94b
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542797"
---
モデルをホストするために使用するコンピューティング先は、デプロイされているエンドポイントのコストと可用性に影響を及ぼします。 次の表を使用して、適切なコンピューティング先を選択します。

| コンピューティング ターゲット | 使用目的 | GPU のサポート | FPGA のサポート | 説明 |
| ----- | ----- | ----- | ----- | ----- |
| [ローカル &nbsp;Web&nbsp; サービス](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | テスト/デバッグ | &nbsp; | &nbsp; | 制限付きのテストとトラブルシューティングに使用。 ハードウェア アクセラレーションは、ローカル システムでのライブラリの使用に依存します。
| [Azure Machine Learning コンピューティング インスタンス&nbsp;Web&nbsp;サービス](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | テスト/デバッグ | &nbsp; | &nbsp; | 制限付きのテストとトラブルシューティングに使用。
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | リアルタイムの推論 |  [はい](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (Web サービスのデプロイ) | [はい](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |高スケールの運用デプロイに使用。 高速な応答時間と、デプロイされたサービスの自動スケールを提供します。 Azure Machine Learning SDK では、クラスターの自動スケールはサポートされていません。 AKS クラスター内のノードを変更するには、Azure portal でお使いの AKS クラスター用の UI を使用します。 AKS は、デザイナーで使用できる唯一のオプションです。 |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | テストまたは開発 | &nbsp;  | &nbsp; | 必要な RAM が 48 GB より少ない低スケール CPU ベース ワークロードに使用。 |
| [Azure Machine Learning コンピューティング クラスター](../articles/machine-learning/how-to-use-parallel-run-step.md) | バッチ推論&nbsp; | [はい](../articles/machine-learning/how-to-use-parallel-run-step.md) (機械学習パイプライン) | &nbsp;  | サーバーレス コンピューティングでバッチ スコアリングを実行します。 優先順位が中程度または低い VM をサポートします。 |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | (プレビュー) リアルタイムの推論 | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (プレビュー) IoT &nbsp;モジュール |  &nbsp; | &nbsp; | IoT デバイスに ML モデルをデプロイし、サービスを提供します。 |
| [Azure Data Box Edge](../articles/databox-online/azure-stack-edge-overview.md)   | IoT Edge を使用 |  &nbsp; | はい | IoT デバイスに ML モデルをデプロイし、サービスを提供します。 |

> [!NOTE]
> ローカル、Azure Machine Learning コンピューティング インスタンス、Azure Machine Learning コンピューティング クラスターなどのコンピューティング先では、トレーニングと実験のために GPU をサポートしていますが、__Web サービスとしてデプロイされる場合__ に、推論に GPU を使用することは、Azure Kubernetes Service でのみサポートされています。
>
> __機械学習パイプラインでスコアリングするとき__、推論に GPU を使用することは、Azure Machine Learning コンピューティングでのみサポートされます。

> [!NOTE]
> * Azure Container Instances (ACI) が適しているのは、サイズが 1 GB 未満の小さいモデルのみです。 
> * より大きいモデルの開発テストには、単一ノードの Azure Kubernetes Service (AKS) クラスターを使用することをお勧めします。