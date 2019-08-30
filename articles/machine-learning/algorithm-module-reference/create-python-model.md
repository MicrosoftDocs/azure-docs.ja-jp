---
title: 'Python モデルの作成: モジュール リファレンス'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service で Python モデルの作成モジュールを使用して、カスタム モデルまたはデータ処理モジュールを作成する方法について学習します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: c6d7aabd41e9d0e872926adbbcb2d18332cb7d5e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128915"
---
# <a name="create-python-model"></a>Python モデルの作成

この記事では、**Python モデルの作成**モジュールを使用して Python スクリプトから未トレーニング モデルを作成する方法をについて説明します。 

Azure Machine Learning 環境の Python パッケージに含まれる任意の学習器を基にしたモデルを作成できます。 

モデルを作成した後は、Azure Machine Learning の他の学習器と同じように、[モデルのトレーニング](train-model.md)を使用してデータセットでモデルをトレーニングできます。 トレーニング済みのモデルを[モデルのスコア付け](score-model.md)に渡し、予測を行うために使用できます。 その後、トレーニング済みモデルは保存でき、スコアリング ワークフローは Web サービスとして発行できます。

> [!WARNING]
> 現在、Python モデルのスコア付けの結果を[モデルの評価](evaluate-model.md)に渡すことはできません。 モデルを評価する必要がある場合は、カスタム Python スクリプトを作成し、[Python スクリプトの実行](execute-python-script.md)モジュールを使用してそれを実行することができます。  


## <a name="how-to-configure-create-python-model"></a>Python モデルの作成を構成する方法

このモジュールを使用するには、Python の中級またはエキスパートの知識が必要です。 モジュールでは、Azure Machine Learning に既にインストールされている Python パッケージに含まれる任意の学習器の使用がサポートされています。 「[Execute Python Script (Python スクリプトの実行)](execute-python-script.md)」で事前にインストールされている Python パッケージの一覧をご覧ください。
  

この記事では、簡単な実験で **Python モデルの作成**を使用する方法を示します。 実験のグラフを次に示します。

![create-python-model](./media/module/aml-create-python-model.png)

1.  **[Create Python Model]\(Python モデルの作成\)** をクリックし、モデリングまたはデータ管理プロセスを実装するようにスクリプトを編集します。 Azure Machine Learning 環境の Python パッケージに含まれる任意の学習器を基にしたモデルを作成できます。


    広く普及している *sklearn* パッケージを使用する 2 クラス Naive Bayes 分類器のサンプル コードを次に示します。

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. 作成した **Python モデルの作成**モジュールを、**モデルのトレーニング**および**モデルのスコア付け**に接続します

3. モデルを評価する必要がある場合は、[Python スクリプトの実行](execute-python-script.md)を追加し、Python スクリプトを編集して評価を実装します。

評価のサンプル コードを次に示します。

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
