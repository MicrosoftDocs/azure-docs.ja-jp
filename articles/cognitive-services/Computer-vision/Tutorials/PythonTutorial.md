---
title: チュートリアル:イメージの操作を実行する - Python
titlesuffix: Azure Cognitive Services
description: Jupyter Notebook を使用することで、Computer Vision API を Python で使用する方法について説明します。 人気のあるライブラリを使用して結果を視覚化します。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: b55b1fdf3b93d3865bdb115e43ff8583af61b45d
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326427"
---
# <a name="tutorial-computer-vision-api-python"></a>チュートリアル:Computer Vision API Python

このチュートリアルでは、Python で Computer Vision API を使用する方法と、広く使われているライブラリを使用して結果を視覚化する方法を示します。 チュートリアルの実行には、Jupyter を使用します。 対話型の Jupyter Notebook を使い始める方法については、[Jupyter のドキュメント](https://jupyter.readthedocs.io/en/latest/index.html)を参照してください。

## <a name="prerequisites"></a>前提条件

- [Python 2.7 以降または 3.5 以降](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) ツール
- [Jupyter Notebook](https://jupyter.org/install) がインストールされていること

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Jupyter でチュートリアル ノートブックを開く 

1. [Cognitive Vision Python](https://github.com/Microsoft/Cognitive-Vision-Python) GitHub リポジトリにアクセスします。 
2. 緑色のボタンをクリックして、リポジトリを複製またはダウンロードします。 
3. コマンド プロンプトを開き、**Cognitive-Vision-Python\Jupyter Notebook** フォルダーに移動します。
1. コマンド プロンプトから `pip install requests opencv-python numpy matplotlib` コマンドを実行して、必要なライブラリがすべてあることを確認します。
1. コマンド プロンプトから `jupyter notebook` コマンドを実行して Jupyter を起動します。
1. Jupyter のウィンドウで、_[Computer Vision API Example.ipynb]_ をクリックしてチュートリアル ノートブックを開きます。

## <a name="run-the-tutorial"></a>チュートリアルを実行する

このノートブック使用するには、Computer Vision API のサブスクリプション キーが必要です。 サインアップするため、[サブスクリプション ページ](https://azure.microsoft.com/try/cognitive-services/)に移動します。 **[サインイン]** ページで、Microsoft アカウントを使用してサインインすると、サブスクライブして無料のキーを取得できます。 サインアップ プロセスを完了したら、ノートブックの `Variables` セクション (下の例を参照) にキーを貼り付けます。 プライマリ キーとセカンダリ キーのどちらでもかまいません。 キーは引用符で囲み、文字列になるようにしてください。

また、`_region` フィールドは、ご利用のサブスクリプションに該当するリージョンと一致させる必要があります。

```python
# Variables
_region = 'westcentralus' #Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(_region)
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```

このチュートリアルを実行すると、URL とローカル記憶域のどちらからでも、分析する画像を追加できるようになります。 画像と分析情報がノートブックに表示されます。
