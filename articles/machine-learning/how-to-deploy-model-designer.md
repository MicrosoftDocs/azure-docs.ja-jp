---
title: スタジオを使用して、デザイナーでトレーニングされたモデルをデプロイする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning スタジオを使用して、コードを 1 行も記述することなく、機械学習モデルをデプロイします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/29/2020
ms.topic: conceptual
ms.custom: how-to, deploy, studio, designer
ms.openlocfilehash: 35acfc51ae76fdacef11f03b1fbd91ad58650ae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97722625"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>スタジオを使用して、デザイナーでトレーニングされたモデルをデプロイする

この記事では、Azure Machine Learning スタジオのリアルタイム エンドポイントとしてデザイナー モデルをデプロイする方法について説明します。

登録後またはダウンロード後、他のモデルと同じように、デザイナーでトレーニングされたモデルを使用できます。 エクスポートされたモデルは、モノのインターネット (IoT) やローカルのデプロイなどのユース ケースでデプロイできます。

スタジオでのデプロイは、次の手順で構成されています。

1. トレーニングされたモデルを登録します。
1. モデルのエントリ スクリプトと Conda 依存関係ファイルをダウンロードします。
1. (省略可能) エントリ スクリプトを構成します。
1. コンピューティング先にモデルをデプロイします。

モデルの登録やファイルのダウンロードの手順を省略するために、デザイナーでモデルを直接デプロイすることもできます。 これは、迅速なデプロイに役立ちます。 詳細については、[デザイナーを使用してモデルをデプロイする](tutorial-designer-automobile-price-deploy.md)方法に関するページを参照してください。

デザイナーでトレーニングされたモデルは、SDK またはコマンド ライン インターフェイス (CLI) を使用してデプロイすることもできます。 詳細については、「[Azure Machine Learning を使用して既存のモデルをデプロイする](how-to-deploy-existing-model.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)

* 次のいずれかのモジュールを含む完成したトレーニング パイプライン:
    - [モデルのトレーニング モジュール](./algorithm-module-reference/train-model.md)
    - [異常検出モデルのトレーニング モジュール](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [クラスタリング モデルのトレーニング モジュール](./algorithm-module-reference/train-clustering-model.md)
    - [PyTorch モデルのトレーニング モジュール](./algorithm-module-reference/train-pytorch-model.md)
    - [SVD レコメンダーのトレーニング モジュール](./algorithm-module-reference/train-svd-recommender.md)
    - [Vowpal Wabbit モデルのトレーニング モジュール](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [ワイドかつディープなモデルのトレーニング モジュール](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>モデルを登録する

トレーニング パイプラインが完了したら、トレーニングされたモデルを Azure Machine Learning ワークスペースに登録して、他のプロジェクトのモデルにアクセスできるようにします。

1. [[モデルのトレーニング モジュール]](./algorithm-module-reference/train-model.md) を選択します。
1. 右側のウィンドウの **[出力 + ログ]** タブを選択します。
1. **[モデルの登録]** アイコンを選択します ![歯車アイコンのスクリーンショット](./media/how-to-deploy-model-designer/register-model-icon.png)。

    ![モデルのトレーニング モジュールの右側のウィンドウのスクリーンショット](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. モデルの名前を入力し、 **[保存]** を選択します。

モデルを登録した後は、スタジオの **[モデル]** アセット ページで確認できます。
    
![[モデル] アセット ページの登録済みモデルのスクリーンショット](./media/how-to-deploy-model-designer/models-asset-page.png)

## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>エントリ スクリプト ファイルと Conda 依存関係ファイルをダウンロードする

Azure Machine Learning スタジオにモデルをデプロイするには、次のファイルが必要です。

- **エントリ スクリプト ファイル** - トレーニングされたモデルを読み込み、要求から入力データを処理し、リアルタイムの推論を行い、結果を返します。 **モデルのトレーニング** モジュールが完了すると、デザイナーによって `score.py` エントリ スクリプト ファイルが自動的に生成されます。

- **Conda 依存関係ファイル** - Web サービスが依存する PIP および Conda パッケージを指定します。 **モデルのトレーニング** モジュールが完了すると、デザイナーによって `conda_env.yaml` ファイルが自動的に作成されます。

これら 2 つのファイルは、**モデルのトレーニング** モジュールの右側のウィンドウでダウンロードできます。

1. **Train Model** (モデルのトレーニング) モジュールを選択します。
1. **[出力 + ログ]** タブでフォルダー `trained_model_outputs` を選択します。
1. `conda_env.yaml` ファイルと `score.py` ファイルをダウンロードします。

    ![右側のウィンドウにあるデプロイするダウンロード ファイルのスクリーンショット](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

または、モデルを登録した後、 **[モデル]** アセット ページからファイルをダウンロードすることもできます。

1. **[モデル]** アセット ページに移動します。
1. デプロイするモデルを選択します。
1. **[成果物]** タブを選択します。
1. `trained_model_outputs` フォルダーを選択します
1. `conda_env.yaml` ファイルと `score.py` ファイルをダウンロードします。  

    ![モデルの詳細ページにあるデプロイするダウンロード ファイルのスクリーンショット](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> `score.py` ファイルには、**モデルのスコアリング** モジュールとほぼ同じ機能が用意されています。 ただし、[SVD レコメンダーのスコアリング](./algorithm-module-reference/score-svd-recommender.md)、[ワイドかつディープなレコメンダーのスコアリング](./algorithm-module-reference/score-wide-and-deep-recommender.md)、[Vowpal Wabbit モデルのスコアリング](./algorithm-module-reference/score-vowpal-wabbit-model.md)のような一部のモジュールには、さまざまなスコアリング モード パラメーターがあります。 これらのパラメーターは、エントリ スクリプトで変更することもできます。
>
>`score.py` ファイルでのパラメーターの設定の詳細については、[エントリ スクリプトの構成](#configure-the-entry-script)に関するセクションを参照してください。

## <a name="deploy-the-model"></a>モデルをデプロイする

必要なファイルをダウンロードしたら、モデルをデプロイする準備は完了です。

1. **[モデル]** アセット ページで、登録されているモデルを選択します。
1. **[デプロイ]** ボタンを選択します。
1. [構成] メニューで、次の情報を入力します。

    - エンドポイントの名前を入力します。
    - [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) または [Azure Container Instance](how-to-deploy-azure-container-instance.md) のどちらにモデルをデプロイするかを選択します。
    - **エントリ スクリプト ファイル** の `score.py` をアップロードします。
    - **Conda 依存関係ファイル** の `conda_env.yml` をアップロードします。 

    >[!TIP]
    > **[詳細]** 設定では、CPU/メモリ容量およびデプロイのためのその他のパラメーターを設定できます。 これらの設定は、大量のメモリ (約 4 GB) を消費する PyTorch モデルなどの特定のモデルにおいて重要です。

1. **[デプロイ]** を選択して、モデルをリアルタイム エンドポイントとしてデプロイします。

    ![[モデル] アセット ページのデプロイ モデルのスクリーンショット](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>リアルタイム エンドポイントを使用する

デプロイが成功した後は、 **[エンドポイント]** アセット ページでリアルタイム エンドポイントを見つけることができます。 そこには、クライアントがリアルタイム エンドポイントにリクエストを送信するために使用できる REST エンドポイントがあります。 

> [!NOTE]
> また、デザイナーによってテスト用のサンプル データ json ファイルも生成されます。`_samples.json` は、**trained_model_outputs** フォルダーからダウンロードできます。

リアルタイム エンドポイントを使用するには、次のコード サンプルを使用します。

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>コンピューター ビジョンに関連するリアルタイム エンドポイントを使用する

Web サービスの入力として受け入れられるのは文字列だけなので、コンピューター ビジョンに関連するリアルタイム エンドポイントを使用する場合は、イメージをバイトに変換する必要があります。 サンプル コードを次に示します:

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>エントリ スクリプトを構成する

[SVD レコメンダーのスコアリング](./algorithm-module-reference/score-svd-recommender.md)、[ワイドかつディープなレコメンダーのスコアリング](./algorithm-module-reference/score-wide-and-deep-recommender.md)、[Vowpal Wabbit モデルのスコアリング](./algorithm-module-reference/score-vowpal-wabbit-model.md)のようなデザイナーの一部のモジュールには、さまざまなスコアリング モード パラメーターがあります。 

このセクションでは、エントリ スクリプト ファイルでもこれらのパラメーターを更新する方法について説明します。

次の例では、トレーニングされた **ワイドかつディープなレコメンダー** モデルの既定の動作を更新します。 既定では、`score.py` ファイルは、ユーザーと項目間の評価を予測するように Web サービスに指示します。 

エントリ スクリプト ファイルを変更して項目の推奨を作成し、`recommender_prediction_kind` パラメーターを変更することによって推奨される項目を返すことができます。


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

**ワイドかつディープなレコメンダー** および **Vowpal Wabbit** モデルでは、次のメソッドを使用してスコアリング モード パラメーターを構成できます。

- パラメーター名は、[Vowpal Wabbit モデルのスコアリング](./algorithm-module-reference/score-vowpal-wabbit-model.md)と[ワイドかつディープなレコメンダーのスコアリング](./algorithm-module-reference/score-wide-and-deep-recommender.md)のパラメーター名の小文字とアンダースコアの組み合わせです。
- モードの型パラメーター値は、対応するオプション名の文字列です。 上記のコードの **レコメンダー予測の種類** を例にとると、値は `'Rating Prediction'` または `'Item Recommendation'` にすることができます。 他の値は許容されません。

**SVD レコメンダー** のトレーニングされたモデルでは、パラメーターの名前と値は明確ではありませんが、以下の表を参照してパラメータの設定方法を決定することができます。

| [SVD レコメンダーのスコアリング](./algorithm-module-reference/score-svd-recommender.md)のパラメーター名                           | エントリ スクリプト ファイル内のパラメーター名 |
| ------------------------------------------------------------ | --------------------------------------- |
| レコメンダーの予測の種類                                  | prediction_kind                         |
| 推奨項目の選択                                   | recommended_item_selection              |
| 1 人のユーザーの推奨プールの最小サイズ    | min_recommendation_pool_size            |
| ユーザーに推奨する項目の最大数               | max_recommended_item_count              |
| 項目の予測された評価をラベルと共に返すかどうか | return_ratings                          |

次のコードは、6 つのパラメータすべてを使用して、予測されたレーティングが添付された評価済みの項目を推奨する SVD レコメンダーのパラメータ設定方法を示しています。

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>次のステップ

* [デザイナーでのモデルのトレーニング](tutorial-designer-automobile-price-train-score.md)
* [Azure Machine Learning SDK を使用してモデルをデプロイする](how-to-deploy-and-where.md)
* [失敗したデプロイのトラブルシューティング](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service にデプロイする](how-to-deploy-azure-kubernetes-service.md)
* [Web サービスを使用するクライアント アプリケーションを作成する](how-to-consume-web-service.md)
* [Web サービスを更新する](how-to-deploy-update-web-service.md)
