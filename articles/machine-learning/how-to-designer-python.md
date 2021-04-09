---
title: デザイナーで Python スクリプトを実行する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーの Execute Python Script モデルを使用して、Python で記述されたカスタム操作を実行する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, designer, devx-track-python
ms.openlocfilehash: dcc28d98efbc82079586de8cfbecd35effc93d6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94556235"
---
# <a name="run-python-code-in-azure-machine-learning-designer"></a>Azure Machine Learning デザイナーでの Python コードの実行

この記事では、[Python スクリプトの実行](algorithm-module-reference/execute-python-script.md)モジュールを使用して、Azure Machine Learning デザイナーにカスタム ロジックを追加する方法について説明します。 次の攻略ガイドでは、Pandas ライブラリを使用して簡単な特徴エンジニアリングを行います。

組み込みのコード エディターを使用して、簡単な Python ロジックをすばやく追加できます。 より複雑なコードを追加したり、追加の Python ライブラリをアップロードしたりする場合は、ZIP ファイル方式を使用する必要があります。

既定の実行環境では、Python の Anaconda ディストリビューションが使用されます。 プレインストールされているパッケージの完全な一覧については、[「Python スクリプトの実行モジュール」のリファレンス](algorithm-module-reference/execute-python-script.md) ページを参照してください。

![Python 実行入力マップ](media/how-to-designer-python/execute-python-map.png)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="execute-python-written-in-the-designer"></a>デザイナーで記述された Python を実行する

### <a name="add-the-execute-python-script-module"></a>Python スクリプトの実行モジュールの追加

1. デザイナー パレットで **[Execute Python Script]\(Python スクリプトの実行\)** モジュールを見つけます。 これは、 **[Python Language]\(Python 言語\)** セクションにあります。

1. このモジュールをパイプライン キャンバスにドラッグ アンド ドロップします。

### <a name="connect-input-datasets"></a>入力データセットの接続

この記事では、**Automobile price data (Raw)** というサンプル データセットを使用します。 

1. データセットをパイプライン キャンバスにドラッグ アンド ドロップします。

1. データセットの出力ポートを **[Execute Python Script]\(Python スクリプトの実行\)** モジュールの左上の入力ポートに接続します。 デザイナーは、入力をパラメーターとしてエントリ ポイント スクリプトに公開します。
    
    右側の入力ポートは、zip 圧縮された Python ライブラリ用に予約されています。

    ![データセットの接続](media/how-to-designer-python/connect-dataset.png)
        

1. 使用する入力ポートをメモしておきます。 デザイナーによって、左側の入力ポートが変数 `dataset1` に、中央の入力ポートが `dataset2` に割り当てられます。 

**[Execute Python Script]\(Python スクリプトの実行\)** モジュールで直接データの生成やインポートを行えるため、入力モジュールは省略可能です。

### <a name="write-your-python-code"></a>Python コードの記述

デザイナーには、独自の Python コードを編集および入力するための初期エントリ ポイント スクリプトが用意されています。 

この例では、Pandas を使用して、automobile データセット内の 2 つの列、**Price** と **Horsepower** を結合し、**Dollars/Horsepower** (ドル/馬力) の新しい列を作成します。 この列は、馬力ごとの支払い額を表します。これは、車がその金額に対してお得であるかどうかを判断するのに便利な機能です。 

1. **[Execute Python Script]\(Python スクリプトの実行\)** モジュールを選択します。

1. キャンバスの右側に表示されるペインで、 **[Python スクリプト]** テキスト ボックスを選択します。

1. 以下のコードをコピーして、テキスト ボックスに貼り付けます。

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    パイプラインは次の画像のようになります。
    
    ![Python パイプラインの実行](media/how-to-designer-python/execute-python-pipeline.png)

    エントリ ポイント スクリプトには、関数 `azureml_main` が含まれている必要があります。 2 つの関数パラメーターがあり、これらは **[Execute Python Script]\(Python スクリプトの実行\)** モジュールの 2 つの入力ポートにマップされます。

    戻り値は、Pandas データフレームである必要があります。 最大 2 つのデータフレームをモジュールの出力として返すことができます。
    
1. パイプラインを送信します。

これで、新しい特徴 **Dollars/HP** を含むデータセットができました。これは、車のレコメンダーのトレーニングに役立ちます。 これは、特徴抽出と次元削減の一例です。 

## <a name="next-steps"></a>次のステップ

Azure Machine Learning デザイナーに[独自のデータ をインポートする](how-to-designer-import-data.md)方法を確認します。
