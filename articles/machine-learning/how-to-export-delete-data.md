---
title: ワークスペース データのエクスポートまたは削除
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Studio、CLI、SDK、および認証済み REST API を使用してワークスペースをエクスポートまたは削除する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 030e09f0bb1514e8d3828da52604d751a2a8a11a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520084"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Machine Learning service のワークスペース データをエクスポートまたは削除する

Azure Machine Learning では、ポータルのグラフィカル インターフェイスまたは Python SDK を使用して、ワークスペース データをエクスポートまたは削除できます。 この記事では、両方のオプションについて説明します。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>ワークスペース データを制御する

Azure Machine Learning によって保存された製品内のデータは、エクスポートおよび削除することができます。 エクスポートと削除は、Azure Machine Learning Studio、CLI、および SDK を使用して行うことができます。 テレメトリ データには、Azure Privacy Portal を介してアクセスできます。 

Azure Machine Learning では、個人データは実行履歴ドキュメント内のユーザー情報で構成されます。 

## <a name="delete-high-level-resources-using-the-portal"></a>ポータルを使用して高レベル リソースを削除する

ワークスペースを作成すると、Azure によってリソース グループ内に次の多数のリソースが作成されます。

- ワークスペース自体
- ストレージ アカウント
- コンテナー レジストリ
- Application Insights インスタンス
- キー コンテナー

これらのリソースを削除するには、一覧から選択し、 **[削除]** を選択します 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="削除アイコンが強調表示されているポータルのスクリーンショット":::

個人のユーザー情報が含まれている可能性がある実行履歴ドキュメントは、ストレージ アカウントの BLOB ストレージ (`/azureml` のサブフォルダー) に格納されます。 データは、ポータルからダウンロードおよび削除することができます。

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="ポータル内のストレージ アカウントの中にある azureml ディレクトリのスクリーンショット":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>Azure Machine Learning Studio を使用して機械学習リソースをエクスポートおよび削除する

Azure Machine Learning Studio は、ノートブック、データセット、モデル、実験などの機械学習リソースの統合ビューを提供します。 Azure Machine Learning Studio では、データと実験の記録を保持することに注力しています。 パイプラインやコンピューティング リソースなどの計算リソースは、ブラウザーを使用して削除できます。 これらのリソースについては、問題のリソースに移動し、 **[削除]** を選択します。 

データセットの登録は解除でき、実験はアーカイブできますが、これらの操作によってデータが削除されることはありません。 データを完全に削除するには、データセットと実行データをストレージ レベルで削除する必要があります。 ストレージ レベルでの削除は、前に説明したように、ポータルを使用して行います。

Studio を使用して、実験の実行からトレーニング アーティファクトをダウンロードできます。 目的の **[実験]** と **[実行]** を選択します。 **[Output + logs]\(出力とログ\)** を選択し、ダウンロードする特定のアーティファクトに移動します。 **...** 、 **[ダウンロード]** の順に選択します。

登録済みのモデルをダウンロードするには、目的の **[モデル]** に移動し、 **[ダウンロード]** を選択します。 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="[ダウンロード] オプションが強調表示されている Studio モデル ページのスクリーンショット":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>Python SDK を使用してリソースをエクスポートおよび削除する

以下を使用して、特定の実行の出力をダウンロードできます。 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

次の機械学習リソースは、Python SDK を使用して削除できます。 

| Type | 関数呼び出し | Notes | 
| --- | --- | --- |
| `Workspace` | [`delete`](/python/api/azureml-core/azureml.core.workspace.workspace#delete-delete-dependent-resources-false--no-wait-false-) | `delete-dependent-resources` を使用して削除を連鎖させる |
| `Model` | [`delete`](/python/api/azureml-core/azureml.core.model%28class%29#delete--) | | 
| `ComputeTarget` | [`delete`](/python/api/azureml-core/azureml.core.computetarget#delete--) | |
| `WebService` | [`delete`](/python/api/azureml-core/azureml.core.webservice%28class%29) | |