---
title: Jupyter ノートブックの例
titleSuffix: Azure Machine Learning service
description: サンプルの Jupyter Notebook を検索して使用し、Azure Machine Learning service Python SDK を探索します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: 2ea124ecf6786142ac1703dab1e267b225751079
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035936"
---
# <a name="explore-azure-machine-learning-service-with-jupyter-notebooks"></a>Jupyter ノートブックを使用して Azure Machine Learning service を探索する

[サンプルの Azure Machine Learning Notebooks のリポジトリ](https://github.com/azure/machinelearningnotebooks)には、最新の Azure Machine Learning Python SDK のサンプルが含まれています。 これらの Juypter Notebook は、SDK の探索を支援し、独自の機械学習プロジェクトのモデルとして機能するように設計されています。

この記事では、次の環境からこのリポジトリにアクセスする方法を示します。

- [Azure Machine Learning Notebook VM](#notebookvm)
- [独自の Notebook サーバーを使用する](#byo)
- [データ サイエンス仮想マシン](#dsvm)
- [Azure Notebooks](#aznb)

> [!NOTE]
> リポジトリを複製すると、**tutorials** フォルダー内にチュートリアル ノートブック、**how-to-use-azureml** フォルダー内に機能固有のノートブックがあることがわかります。

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-notebook-vm"></a>Azure Machine Learning Notebook VM 上でサンプルを入手する

サンプルを開始する最も簡単な方法は、「[チュートリアル:環境とワークスペースを設定する](tutorial-1st-experiment-sdk-setup.md)」を完了することです。 完了すると、SDK とサンプル リポジトリが事前に読み込まれた専用の Notebook サーバーが用意されます。 ダウンロードやインストールは必要ありません。

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>ノートブック サーバー上でサンプルを入手する

ローカル開発に独自の Notebook サーバーを使用するには、次の手順に従ってください。

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

これらの手順では、クイック スタートとチュートリアルのノートブックに必要な基本 SDK パッケージがインストールされます。 その他のサンプル ノートブックでは、追加のコンポーネントのインストールが必要になる場合があります。 詳細については、「[Install the Azure Machine Learning SDK for Python (Azure Machine Learning SDK for Python をインストールする)](https://docs.microsoft.com/python/api/overview/azure/ml/install)」を参照してください。

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>DSVM 上でサンプルを入手する

データ サイエンス仮想マシン (DSVM) とは、データ サイエンスの実行専用に構築されたカスタマイズ済み VM イメージです。 [DSVM を作成する](how-to-configure-environment.md#dsvm)と、SDK と Notebook サーバーがユーザーのためにインストールされて構成されます。 ただし、その場合も、ワークスペースの作成とサンプル リポジトリの複製は必要になります。

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

<a name="aznb"></a>
## <a name="get-samples-on-azure-notebooks"></a>Azure Notebooks 上でサンプルを入手する

[Azure Notebooks](https://notebooks.azure.com/) で、SDK と Notebook サーバーがユーザーのためにインストールされて構成されます。 Azure Notebooks は、ユーザーが探索できる、フル マネージド型で軽量の Notebook 環境を提供します。

Azure Notebooks のサンプル リポジトリにアクセスするには、[Azure portal](https://portal.azure.com) から Azure Machine Learning ワークスペースに移動します。 **[概要]** セクションから、 **[Azure Notebooks で開始する]** を選択します。

## <a name="next-steps"></a>次の手順

[サンプル ノートブック](https://aka.ms/aml-notebooks)を探索して、Azure Machine Learning service で実行できることを把握します。または、次のチュートリアルをお試しください。

- [MNIST を使用した画像分類モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)

- [NYC タクシー データ セットを使用して回帰モデルをトレーニングするためのデータの準備と自動化された機械学習](tutorial-auto-train-models.md)
