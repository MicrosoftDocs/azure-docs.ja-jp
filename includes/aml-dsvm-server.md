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
ms.openlocfilehash: 12d1576c3bfbf96c0445fcd2a6f0bc37d6a68f11
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302207"
---
1. [Azure Machine Learning Python のクイック スタート](../articles/machine-learning/service/quickstart-create-workspace-with-python.md)を完了して、ワークスペースを作成します。  「**ノートブックを使用する**」セクションは、スキップしてかまいません。
1. [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
1. 次のいずれかの方法を使用して、ワークスペース構成ファイルを追加します。
    * 前提条件のクイック スタートを使用して作成した **aml_config\config.json** ファイルを複製されたディレクトリにコピーします。
    * 複製したディレクトリの [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) ノートブック内のコードを使用して新しいワークスペースを作成します。
1. 複製したディレクトリから、Notebook サーバーを起動します。
    
    ```shell
    jupyter notebook
    ```