---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93325269"
---
エントリ スクリプトは、デプロイされた Web サービスに送信されたデータを受け取り、それをモデルに渡します。 次に、モデルから返された応答を受け取り、それをクライアントに返します。 *このスクリプトはこのモデルに固有のものです*。 そのため、モデルが受け入れ、返すデータを認識する必要があります。

エントリ スクリプトでは、次の 2 つの処理を実行する必要があります。

1. モデルの読み込み (`init()` という関数を使用)
1. 入力データでのモデルの実行 (`run()` という関数を使用)

それでは、これらの手順を詳しく見ていきましょう。

### <a name="writing-init"></a>init() の記述 

#### <a name="loading-a-registered-model"></a>登録済みのモデルの読み込み

登録済みのモデルは、`AZUREML_MODEL_DIR` という環境変数が指すパスに格納されます。 正確なディレクトリ構造の詳細については、「[エントリ スクリプトでモデル ファイルを検索する](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)」をご覧ください。

#### <a name="loading-a-local-model"></a>ローカル モデルの読み込み

モデルを登録せず、モデルをソース ディレクトリの一部として受け渡すことを選択した場合は、スコアリング スクリプトからモデルへの相対パスを渡すことによって、ローカルで行うのと同様にそのモデルを読み取ることができます。 たとえば、ディレクトリが次のように構成されているとします。

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

次の Python コードを使用して、モデルを読み込むことができます。

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>run() の書き込み

モデルがスコアリング要求を受信するたびに `run()` が実行されます。要求の本文は次の構造を持つ JSON ドキュメントであることが想定されます。

```json
{
    "data": <model-specific-data-structure>
}

```

`run()` への入力は、"data" キーの後に何かが続く Python 文字列です。

次の例は、登録された scikit-learn モデルを読み込み、それを numpy データでスコア付けする方法を示しています。

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Automatic Swagger スキーマ生成とバイナリ (イメージ) データを含む、より高度な例については、「[高度なエントリ スクリプトの作成」の記事](../articles/machine-learning/how-to-deploy-advanced-entry-script.md)をご覧ください。