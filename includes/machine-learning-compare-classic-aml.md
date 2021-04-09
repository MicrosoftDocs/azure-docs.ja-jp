---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: ff64a0948402ff152e45bd4702d986f51b9547aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563193"
---
次の表は、ML Studio (classic) と Azure Machine Learning との主な違いをまとめたものです。

| 特徴量 | ML Studio (クラシック) | Azure Machine Learning |
|---| --- | --- |
| ドラッグ アンド ドロップ インターフェイス | クラシック エクスペリエンス | 更新されたエクスペリエンス - [Azure Machine Learning デザイナー](../articles/machine-learning/concept-designer.md)| 
| コード SDK | サポートされていない | [Azure Machine Learning Python](/python/api/overview/azure/ml/) および [R](https://github.com/Azure/azureml-sdk-for-r) SDK との完全な統合 |
| 実験 | スケーラブル (10 GB トレーニング データの上限) | コンピューティング先に合わせてスケーリング |
| コンピューティング ターゲットのトレーニング | 独自のコンピューティング ターゲット、CPU のサポートのみ | カスタマイズできる広範な[トレーニング コンピューティング先](../articles/machine-learning/concept-compute-target.md#train)。 GPU と CPU のサポートが含まれます | 
| デプロイのコンピューティング ターゲット | 独自の Web サービス形式 (カスタマイズ不可) | カスタマイズできる広範な[デプロイ コンピューティング先](../articles/machine-learning/concept-compute-target.md#deploy)。 GPU と CPU のサポートが含まれます |
| ML パイプライン | サポートされていません | ワークフローを自動化する柔軟性の高いモジュール式の[パイプライン](../articles/machine-learning/concept-ml-pipelines.md)を作成します |
| MLOps | 基本的なモデル管理とデプロイ。CPU のみのデプロイ | エンティティのバージョン管理 (モデル、データ、ワークフロー)、ワークフロー オートメーション、CICD ツールとの統合、CPU および GPU のデプロイ、[その他](../articles/machine-learning/concept-model-management-and-deployment.md) |
| モデル形式 | 専用の形式、Studio (クラシック) のみ | トレーニング ジョブの種類に応じて複数の形式がサポートされます |
| 自動化されたモデル トレーニングとハイパーパラメーター調整 |  サポートされていません | [サポートされています](../articles/machine-learning/concept-automated-ml.md)。 コード ファースト オプションとコードなしオプション。 | 
| データ ドリフト検出 | サポートされていません | [サポートされています](../articles/machine-learning/how-to-monitor-datasets.md) |
| プロジェクトのラベル付けデータ | サポートされていません | [サポートされています](../articles/machine-learning/how-to-create-labeling-projects.md) |
| ロール ベースのアクセス制御 (RBAC) | 共同作成者と所有者ロールのみ | [柔軟なロール定義と RBAC 制御](../articles/machine-learning/how-to-assign-roles.md) |
| AI Gallery | サポート対象 ([https://gallery.azure.ai/](https://gallery.azure.ai/)) | サポートされていない <br><br> [Python SDK ノートブックのサンプル](https://github.com/Azure/MachineLearningNotebooks)を参照してください。 |