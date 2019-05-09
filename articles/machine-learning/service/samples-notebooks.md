---
title: Jupyter ノートブックの例
titleSuffix: Azure Machine Learning service
description: Python 内で Azure Machine Learning service を探索するための Jupyter Notebook の例を検索し、使用します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b597d6efa87aa2811ce42f3315698acfa17426b2
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548590"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Jupyter Notebook を使用して Azure Machine Learning service を探索する

作業を容易にするために、Azure Machine Learning service の探索に使用できる一連の Jupyter Python Notebook を開発しました。 

このサイトのドキュメントでサービスの使用方法を確認し、これらのノートブックを使用して、それらを自分の状況に合わせてカスタマイズします。 

以下のいずれかのパスを使用して、これらのサンプル ノートブックと共にノートブック サーバーを稼働します。  サーバーを稼働したら、**tutorials** フォルダーでチュートリアル ノートブックを見つけます。または、**how-to-use-azureml** フォルダーでさまざまな機能を確認します。


## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Azure Notebooks を試す:クラウド上の無料の Jupyter Notebook

Azure Notebooks の利用を開始するのは簡単です。 [Azure Notebooks](https://notebooks.azure.com/) には [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) が既にインストールされて構成されています。 インストールと今後の更新プログラムは、Azure サービスを介して自動的に管理されます。
  
[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM) を使用する

DSVM には [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) とノートブック サーバーが既にインストールされて構成されています。 

[DSVM を作成](how-to-configure-environment.md#dsvm)した後、DSVM でこれらの手順を使用してノートブックを実行します。

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]


## <a name="use-your-own-jupyter-notebook-server"></a>自分の Jupyter Notebook サーバーを使用する

次の手順を使用して、コンピューターにローカルの Jupyter Notebook サーバーを作成します。

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

このクイック スタートの各手順では、クイック スタートとチュートリアルのノートブックを実行するために必要なパッケージをインストールします。  他のサンプル ノートブックでは、さらに別のコンポーネントのインストールが必要になることがあります。  これらのコンポーネントの詳細については、「[Install the Azure Machine Learning SDK for Python (Azure Machine Learning SDK for Python をインストールする)](https://docs.microsoft.com/python/api/overview/azure/ml/install)」を参照してください。

## <a name="next-steps"></a>次の手順

[Azure Machine Learning service 用の GitHub ノートブック リポジトリ](https://aka.ms/aml-notebooks)

以下のチュートリアルを試します:
+ [MNIST を使用した画像分類モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)

+ [NYC タクシー データ セットを使用して回帰モデルをトレーニングするためのデータの準備と自動化された機械学習](tutorial-data-prep.md)
