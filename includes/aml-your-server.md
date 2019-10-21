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
ms.date: 09/26/2019
ms.openlocfilehash: 85d1c1cd294bfc02a2e0e327073bb6a80366548b
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841113"
---
1. [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) に関するページの手順を使用して、Azure Machine Learning SDK for Python をインストールします

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
