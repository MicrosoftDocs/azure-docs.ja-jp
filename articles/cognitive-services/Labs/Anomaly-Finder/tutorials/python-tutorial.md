---
title: 'チュートリアル: 異常検出、Python'
titlesuffix: Azure Cognitive Services
description: 異常検出 API を使用する Python ノートブックについて説明します。 API に元のデータ ポイントを送信し、予期される値と異常なポイントを取得します。
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 5a2fb54658599e0500944aaae9225f314277f9da
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539090"
---
# <a name="tutorial-anomaly-detection-with-python-application"></a>チュートリアル: Python アプリケーションによる異常検出

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

このチュートリアルでは、Python で異常検出 API を使用する方法と、広く使われているライブラリを使用して結果を視覚化する方法を紹介します。 このチュートリアルでは Jupyter を使用し、サブスクリプション キーを使って独自のデータを検証します。 対話型の Jupyter ノートブックを使い始める方法については、[Jupyter のドキュメント](https://jupyter.readthedocs.io/en/latest/index.html)を参照してください。 

## <a name="prerequisites"></a>前提条件

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>異常検出にサブスクライブしてサブスクリプション キーを取得する 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>サンプル コードをダウンロードする

1. [GitHub でチュートリアル ノートブック](https://github.com/MicrosoftAnomalyDetection/python-sample)に移動します。
2. 緑色のボタンをクリックし、チュートリアルを複製またはダウンロードします。 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Jupyter でチュートリアル ノートブックを開く

1. コマンド プロンプトを起動し、python-sample という名前のフォルダーに移動します。
2. コマンド プロンプトから jupyter notebook コマンドを実行し、Jupyter を起動します。
3. Jupyter のウィンドウで、<em>[Anomaly Detection API Example.ipynb]</em> をクリックしてチュートリアル ノートブックを開きます。   

## <a name="running-the-tutorial"></a>チュートリアルを実行する

このノートブックを使用するには、異常検出 API のサブスクリプション キーが必要です。 サインアップするには、[サブスクリプション] ページに移動します。 [サインイン] ページで、Microsoft アカウントを使用してサインインすると、サブスクライブしてキーを取得できます。 サインアップ プロセスを完了したら、ノートブックの変数のセクション (下の例を参照) にキーを貼り付けます。 プライマリ キーとセカンダリ キーのどちらでもかまいません。 キーは引用符で囲み、文字列になるようにしてください。

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API リファレンス](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
