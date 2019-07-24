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
ms.openlocfilehash: 8d8b314965253dc00b39d0b068b1d6fb3e4aa471
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395580"
---
1. [Azure Machine Learning service ワークスペースの作成](../articles/machine-learning/service/setup-create-workspace.md#portal)に関するページの手順に従って Miniconda 環境を作成し、ワークスペースを作成して、ワークスペース構成ファイル (**aml_config/config.json**) を作成します。

1. [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 次のいずれかの方法を使用して、ワークスペース構成ファイルを追加します。
    * 前提条件のクイック スタートを使用して作成した **aml_config/config.json** ファイルを複製されたディレクトリにコピーします。
    * [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 内のコードを使用して新しいワークスペースを作成します。
1. 複製したディレクトリから、Notebook サーバーを起動します。
    
    ```shell
    jupyter notebook
    ```