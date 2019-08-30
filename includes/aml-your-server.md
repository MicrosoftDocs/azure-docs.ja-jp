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
ms.date: 01/25/2019
ms.openlocfilehash: 6c93d1243db1b3c4277a54cf71e10f6bbc648d26
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846022"
---
- Azure Machine Learning SDK for Python がインストールされている場合。 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) の手順を使用して、次のことを実行します。


1. [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) の手順を使用して、次のことを実行します。
    * Miniconda 環境を作成する[Azure Machine Learning service ワークスペースを作成し、管理する]
    * Azure Machine Learning SDK for Python をインストールする

1. [Azure Machine Learning service ワークスペース](../articles/machine-learning/service/how-to-manage-workspace.md)を作成します。

1. [構成ファイル](../articles/machine-learning/service/how-to-configure-environment.md#workspace)  (**aml_config/config.json**) を記述します。

1. [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 複製したディレクトリから、Notebook サーバーを起動します。

    ```shell
    jupyter notebook
    ```