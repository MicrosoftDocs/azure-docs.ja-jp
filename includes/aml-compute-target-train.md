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
ms.date: 08/19/2020
ms.openlocfilehash: 54328c7ae4fe2112a718bd82b9f7330c9e95ec69
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146737"
---
**コンピューティング ターゲットは、各トレーニング ジョブで順に再利用できます**。 たとえば、リモート VM をワークスペースにアタッチした後、複数のジョブでそれを再利用できます。  機械学習パイプラインの場合は、各コンピューティング ターゲットに対して、適切な[パイプライン ステップ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)を使用します。

|トレーニング &nbsp;ターゲット|[自動化された ML](../articles/machine-learning/concept-automated-ml.md) | [ML パイプライン](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning デザイナー](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[ローカル コンピューター](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| はい | &nbsp; | &nbsp; |
|[Azure Machine Learning コンピューティング クラスター](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| はい、 <br/>ハイパーパラメーター&nbsp;調整 | はい | はい |
|[Azure Machine Learning コンピューティング インスタンス](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | はい、 <br/>ハイパーパラメーター調整 | はい |  |
|[リモート VM](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | はい、 <br/>ハイパーパラメーター調整 | はい | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| はい (SDK ローカル モードのみ) | はい | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | はい | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | はい | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | はい | &nbsp; |
