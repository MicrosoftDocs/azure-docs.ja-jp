---
title: 独自のノートブック サーバーでノートブックを実行するクイック スタート
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service の利用を開始します。 独自のローカル ノートブック サーバーを使用してワークスペースを試してみましょう。  ワークスペースは、機械学習モデルの実験、トレーニング、およびデプロイを行うために使用する、クラウドでの基礎ブロックとなります。
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: sdgilley
ms.author: sgilley
ms.date: 07/08/2019
ms.custom: seodec18
ms.openlocfilehash: 406797203a99df7e805e08ee7589148599eeffce
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670713"
---
# <a name="quickstart-use-your-own-notebook-server-to-get-started-with-azure-machine-learning"></a>クイック スタート:独自のノートブック サーバーを使用して Azure Machine Learning の利用を開始する

独自の Python 環境と Jupyter Notebook Server を使用して、Azure Machine Learning service の利用を開始します。  SDK のインストールなしのクイック スタートについては、次を参照してください:「[クイック スタート: クラウドベースのノートブック サーバーを使用して Azure Machine Learning の利用を開始する](quickstart-run-cloud-notebook.md)」を参照してください。

このクイック スタートでは、[Azure Machine Learning service ワークスペース](concept-azure-machine-learning-architecture.md)を使用して機械学習の実験を追跡する方法を示しています。 値のログをワークスペースに記録する Python コードを実行します。

このクイック スタートの動画バージョンをご覧ください。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2G9N6]

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning SDK がインストールされた Python 3.6 ノートブック サーバー
* Azure Machine Learning service ワークスペース
* ワークスペース構成ファイル ( **.azureml/config.json**)。

「[Create an Azure Machine Learning service workspace (Azure Machine Learning サービスのワークスペースを作成する)](setup-create-workspace.md#sdk)」の前提条件をすべて入手してください。



## <a name="use-the-workspace"></a>ワークスペースの使用

ワークスペース構成ファイルと同じディレクトリ ( **.azureml/config.json**) でノートブックを起動するか、スクリプトを作成します。

### <a name="attach-to-workspace"></a>ワークスペースにアタッチする

このコードでは、ワークスペースにアタッチする構成ファイルから情報を読み取ります。

```
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="log-values"></a>ログ値

SDK の基本的な API を使用した次のコードを実行して、実験の実行を追跡します。

1. ワークスペースで実験を作成します。
1. 1 つの値を実験にログ記録します。
1. 値の一覧を実験にログ記録します。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>ログに記録された結果の表示

実行が完了したら、Azure portal で実験の実行を表示できます。 前回の実行に関する結果に移動する URL を出力するには、次のコードを使用します。

```python
print(run.get_portal_url())
```

このコードは、ブラウザーの Azure portal でログに記録された値を表示するために使用できるリンクを返します。

![Azure portal 上のログに記録された値](./media/quickstart-run-local-notebook/logged-values.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

>[!IMPORTANT]
>作成したリソースは、Machine Learning に関連したその他のチュートリアルおよびハウツー記事の前提条件として使用できます。

この記事で作成したリソースを使用する予定がない場合は、料金の発生を避けるために削除してください。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>次の手順

この記事では、モデルを実験してデプロイするために必要なリソースを作成しました。 ノートブックでコードを実行し、クラウドのワークスペースでそのコードの実行履歴を確認しました。

> [!div class="nextstepaction"]
> [チュートリアル:画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)

[GitHub にあるより高度な例](https://aka.ms/aml-notebooks)を探索したり、[SDK ユーザー ガイド](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)を閲覧したりすることもできます。
