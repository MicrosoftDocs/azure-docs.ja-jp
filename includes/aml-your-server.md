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
ms.date: 03/05/2020
ms.openlocfilehash: 0d7622217d192a100fd809c32c9e85970cf61fd7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511076"
---
1. [Azure Machine Learning SDK](/python/api/overview/azure/ml/install) に関するページの手順を使用して、Azure Machine Learning SDK for Python をインストールします

1. [Azure Machine Learning ワークスペース](../articles/machine-learning/how-to-manage-workspace.md)を作成します。

1. [構成ファイル](../articles/machine-learning/how-to-configure-environment.md#workspace)  (**aml_config/config.json**) を記述します。

1. [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 複製したディレクトリから、Notebook サーバーを起動します。

    ```bash
    jupyter notebook
    ```