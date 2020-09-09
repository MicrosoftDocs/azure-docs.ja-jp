---
title: ラベル付きデータセットを作成して探索する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のラベル付けプロジェクトからデータ ラベルをエクスポートして、機械学習タスクに使用する方法について説明します。
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 05ecce2d6ef0f8a3f241570ba9364c5e38682f3e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319441"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>ラベル付き Azure Machine Learning データセットを作成して探索する

この記事では、Azure Machine Learning データのラベル付けプロジェクトからデータ ラベルをエクスポートし、データ探索用の Pandas データフレーム、または画像変換用の Torchvision データセットなどの一般的な形式に読み込む方法について説明します。 

## <a name="what-are-datasets-with-labels"></a>ラベル付きデータセットとは 

ラベル付き Azure Machine Learning データセットとは、ラベル プロパティを持つ [TabularDataset](how-to-create-register-datasets.md#dataset-types) のことで、ラベル付きデータセットと呼ばれています。 これらの特定の種類の TabularDataset は、Azure Machine Learning データ ラベル付けプロジェクトの出力としてのみ作成されます。 データ ラベル付けプロジェクトを作成するには、[こちらの手順](how-to-create-labeling-projects.md)を使用します。 Machine Learning では、画像の分類 (複数ラベルまたは多クラス) のほか、境界ボックスと組み合わせたオブジェクトの識別でデータ ラベル付けプロジェクトをサポートしています。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://aka.ms/AMLFree)を作成してください。
* [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)、または [Azure Machine Learning Studio](https://ml.azure.com/) へのアクセス。
    * [azure-contrib-dataset](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py) パッケージをインストールする
* Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成する](how-to-manage-workspace.md)方法に関するページを参照してください。
* Azure Machine Learning データのラベル付けプロジェクトへのアクセス。 ラベル付けプロジェクトがない場合は、[こちらの手順](how-to-create-labeling-projects.md)を使用して作成します。

## <a name="export-data-labels"></a>データ ラベルをエクスポートする 

データのラベル付けプロジェクトを完了したら、ラベル付けプロジェクトからラベル データをエクスポートできます。 そうすることで、データとそのラベルの両方への参照をキャプチャし、[COCO 形式](http://cocodataset.org/#format-data)で、または Azure Machine Learning データセットとしてエクスポートすることができます。 ラベル付けプロジェクトの **[プロジェクトの詳細]** ページにある **[エクスポート]** ボタンを使用します。

### <a name="coco"></a>COCO 

 COCO ファイルは、Azure Machine Learning ワークスペースの既定の BLOB ストアにある *export/coco* 内のフォルダーに作成されます。 

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning のデータセット

エクスポートした Azure Machine Learning データセットには、Azure Machine Learning Studio の **[データセット]** セクションでアクセスできます。 また、データセットの **[詳細]** ページには、Python からラベルにアクセスするためのサンプル コードも用意されています。

![エクスポートされたデータセット](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>ラベル付きデータセットを探索する

ラベル付きデータセットを Pandas データフレームまたは Torchvision データセットに読み込んで、データ探索用の人気のオープンソース ライブラリのほか、画像変換とトレーニング用に PyTorch で提供されているライブラリを活用できます。

### <a name="pandas-dataframe"></a>Pandas データフレーム

ラベル付きデータセットは、`azureml-contrib-dataset` クラスの [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) メソッドを使用して Pandas データフレーム に読み込むことができます。 次のシェル コマンドを使用して、このクラスをインストールします。 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>azureml.contrib 名前空間は、サービスの改善に伴って頻繁に変更されます。 そのため、この名前空間内のものはすべて Microsoft によって完全にサポートされているものではなく、プレビューとして見なされる必要があります。

Pandas データフレームに変換する場合、ファイル ストリームに対して次のファイル処理オプションが用意されています。
* ダウンロード:データ ファイルをローカル パスにダウンロードします。
* マウント: データ ファイルをマウント ポイントにマウントします。 マウントは、Azure Machine Learning ノートブック VM や Azure Machine Learning コンピューティングなど、Linux ベースのコンピューティングでのみ機能します。

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision データセット

`azureml-contrib-dataset` クラスの [to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) メソッドを使用して、ラベル付きデータセットを Torchvision データセットに読み込むことができます。 このメソッドを使用するには、[PyTorch](https://pytorch.org/) がインストールされている必要があります。 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>次のステップ

* 完全なトレーニング サンプルについては、[ラベル付きデータセットのノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)を参照してください。
