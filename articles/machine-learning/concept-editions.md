---
title: Azure Machine Learning Enterprise および Basic エディション
description: Azure Machine Learning の各エディションの違いについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: j-martens
ms.author: jmartens
ms.date: 06/11/2020
ms.openlocfilehash: fbe3f0109ca3af2cf9ff44061c7882e6bd72c8a6
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508604"
---
# <a name="enterprise-preview-and-basic-editions-of-azure-machine-learning"></a>Azure Machine Learning の Enterprise (プレビュー) および Basic エディション 

Azure Machine Learning には、機械学習のニーズに合わせて調整された 2 つのエディションがあります。 これらのエディションによって、開発者やデータ科学者が自分のワークスペースから利用可能な機械学習ツールはどれかが決定します。

## <a name="choose-an-edition"></a>エディションの選択

ワークスペースを作成するときは必ずエディションを割り当てます。 この期間中、コンピューティングやその他の Azure リソースで発生するコストは、お客様の負担になります。 [Azure Machine Learning のコストを管理する](concept-plan-manage-cost.md)方法を説明します。

Basic のワークスペースを Enterprise (プレビュー) エディションにアップグレードする方法については、[こちら](how-to-manage-workspace.md#upgrade)を参照してください。 

## <a name="whats-in-each-edition"></a>各エディションの内容

### <a name="data-for-machine-learning-capabilities"></a>機械学習機能のデータ  

| 機能                     | Edition                 |
|------------------------------------------------------------------------------------|:-----------:|
| ラベル付け:スタジオ (Web) での[ラベル付けプロジェクトの作成および管理](tutorial-labeling.md)                                                | All                     |
| ラベル付け:スタジオ (Web) でのラベラー                                    | All                     |
| ラベル付け:プライベートの人員の使用                               | All                     |
| ラベル付け:[ML によるイメージ分類とオブジェクトの検出](how-to-label-images.md)                  | Enterprise エディションのみ |
| データセット + データストア: Python での作成と管理                       | All                     |
| データセット + データストア: スタジオ (Web) での作成と管理                         | All                     |
| ドリフト: Python でのデータセット モニターの表示と管理                           | All                     |
| ドリフト: スタジオ (Web) でのデータセット モニターの表示と管理                            | Enterprise エディションのみ |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>自動トレーニング機能 (AutoML)

| 機能    | Edition                 |
|------------------------------------------------------------------------------------|:-----------:|
| [ノートブックでの AutoML 実験](how-to-configure-auto-train.md)の作成と実行               | All                     |
| [Studio (Web) での AutoML 実験](how-to-use-automated-ml-for-ml-models.md)の作成と実行   | Enterprise エディションのみ |
| 業界トップ レベルの自動 Ml 予測機能             | Enterprise エディションのみ |
| ディープ ラーニングとその他の高度な学習機能のサポート | Enterprise エディションのみ |
| 大規模なデータ サポートの分類と回帰のタスク (最大 100 GB)                     | Enterprise エディションのみ |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>信頼できる機械学習

| 機能    | Edition                 |
|------------------------------------------------------------------------------------|:-----------:|
| [モデル説明](how-to-machine-learning-interpretability-automl.md)                                              | All                     |
| [差分プライバシー](how-to-differential-privacy.md)                          | All                     |
| データシートを実装するカスタム タグ    | All                     |
| 公平性 AzureML 統合                                      | All                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>ビルドとトレーニング機能

| 機能    | Edition                 |
|------------------------------------------------------------------------------------|:-----------:|
| Visual Studio Code の統合                                                     | All                     |
| 強化学習                                                             | All                     |
| 実験 UI                                                                 | All                     |
| Jupyter、JupyterLab の統合                                                    | All                     |
| Python SDK サポート                                                                 | All                     |
| R SDK のサポート                                                                      | All                     |
| ML パイプライン: Python での作成、実行、および発行                           | All                     |
| ML パイプライン: Python でのパイプラインのスケジュールされた実行の作成、編集、削除| All                     |
| ML パイプライン: Python SDK でパイプライン エンドポイントの作成                                   | All                     |
| ML パイプライン: Studio (Web) での実行の詳細の表示                                              | All                     |
| ML パイプライン: デザイナーでの作成、実行、ビジュアル化、および発行                  | Enterprise エディションのみ |
| ML パイプライン: デザイナーでのパイプラインエンド ポイントの作成 | Enterprise エディションのみ |
| 統合ノートブックのマネージド コンピューティング インスタンス                                 | All                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>デプロイ機能とモデル管理機能

| 機能                            | Edition                 |
|------------------------------------------------------------------------------------|:-----------:|
| Machine Learning と Azure ML CLI 用の Azure DevOps 拡張機能                 | All                     |
| [イベント グリッドの統合](how-to-use-event-grid.md)                                                             | All                     |
| Azure Stream Analytics と Azure Machine Learning の統合                       | All                     |
| SDK で ML パイプラインを作成する                                                         | All                     |
| バッチ推論                                                                  | All                     |
| FPGA ベースの Hardware Accelerated Models                                             | All                     |
| データ プロファイル                                                                    | All                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>セキュリティ、ガバナンス、制御機能

| 機能     | Edition                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Azure ロールベースのアクセス制御 (Azure RBAC)](how-to-assign-roles.md) のサポート                                           | All                     |
| コンピューティングの [Virtual Network (VNet)](how-to-enable-virtual-network.md) のサポート                                         | All                     |
| エンドポイント認証のスコア付け                                                    | All                     |
| [ワークスペースでの Private Link](how-to-configure-private-link.md)                                                            | All                     |
| ワークスペース間の[クォータ管理](how-to-manage-quotas.md)                                                 | Enterprise エディションのみ |

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で何が利用可能かについては、[エディションの概要と価格のページ](https://azure.microsoft.com/pricing/details/machine-learning/)を参照してください。 

Basic のワークスペースを Enterprise Edition にアップグレードする方法については、[こちら](how-to-manage-workspace.md#upgrade)を参照してください。 
