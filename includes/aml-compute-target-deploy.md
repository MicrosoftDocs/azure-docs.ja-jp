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
ms.date: 08/23/2019
ms.openlocfilehash: 29da4ab949cf38419bc7a4d0a3b5da6669441887
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70847707"
---
| コンピューティング ターゲット | 使用対象 | GPU のサポート | FPGA のサポート | 説明 |
| ----- | ----- | ----- | ----- | ----- |
| [ローカル &nbsp;Web&nbsp; サービス](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | テスト/デバッグ | &nbsp; | &nbsp; | 制限付きのテストとトラブルシューティングに使用。 ハードウェア アクセラレーションは、ローカル システムでのライブラリの使用に依存します。
| [Notebook VM&nbsp;Web&nbsp;サービス](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | テスト/デバッグ | &nbsp; | &nbsp; | 制限付きのテストとトラブルシューティングに使用。
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | リアルタイムの推論 |  [はい](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md) (Web サービスのデプロイ) | [はい](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |高スケールの運用デプロイに使用。 高速な応答時間と、デプロイされたサービスの自動スケールを提供します。 Azure Machine Learning SDK では、クラスターの自動スケールはサポートされていません。 AKS クラスター内のノードを変更するには、Azure portal でお使いの AKS クラスター用の UI を使用します。 AKS は、ビジュアル インターフェイスに使用できる唯一のオプションです。 |
| [Azure Container Instances](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | テストまたは開発 | &nbsp;  | &nbsp; | 必要な RAM が 48 GB より少ない低スケール CPU ベース ワークロードに使用。 |
| [Azure Machine Learning コンピューティング](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (プレビュー) バッチ &nbsp;推論 | [はい](../articles/machine-learning/service/how-to-run-batch-predictions.md) (機械学習パイプライン) | &nbsp;  | サーバーレス コンピューティングでバッチ スコアリングを実行します。 優先順位が中程度または低い VM をサポートします。 |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (プレビュー) IoT &nbsp;モジュール |  &nbsp; | &nbsp; | IoT デバイスに ML モデルをデプロイし、サービスを提供します。 |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | IoT Edge を使用 |  &nbsp; | はい | IoT デバイスに ML モデルをデプロイし、サービスを提供します。 |

> [!NOTE]
> ローカル、Notebook VM、Azure Machine Learning コンピューティングなどのコンピューティング先はトレーニングと実験のために GPU をサポートしていますが、__Web サービスとしてデプロイされるとき__、推論に GPU を使用することは、Azure Kubernetes Service でのみサポートされています。
>
> __機械学習パイプラインでスコアリングするとき__、推論に GPU を使用することは、Azure Machine Learning コンピューティングでのみサポートされます。