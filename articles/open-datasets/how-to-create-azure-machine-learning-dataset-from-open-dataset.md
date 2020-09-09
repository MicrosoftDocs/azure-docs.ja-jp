---
title: Azure Open Datasets を使用してデータセットを作成する
titleSuffix: Azure Open Datasets
description: Azure Open Datasets から Azure Machine Learning データセットを作成する方法について説明します。
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 00718e15b069e9be25abdaf0e4460eae20c6afd0
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998076"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Azure Open Datasets から Azure Machine Learning データセットを作成する
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、[Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) データセットと [Azure Open Datasets](https://docs.microsoft.com/azure/open-datasets/) を使用して、ローカルまたはリモートの機械学習の実験にキュレーションされたエンリッチメント データを取り込む方法について説明します。 

[Azure Machine Learning データセット](../machine-learning/how-to-create-register-datasets.md)を作成すると、データ ソースの場所への参照とそのメタデータのコピーを作成できます。 データセットは遅延評価され、データは既存の場所に残るため:
* 追加のストレージ コストは発生しません。
* 意図せずに元のデータ ソースを変更するリスクはありません。 
* ML ワークフローのパフォーマンスが向上します。

Azure Machine Learning のデータ アクセス ワークフロー全体におけるデータ セットの位置付けの詳細については、[データへの安全なアクセス](../machine-learning/concept-data.md#data-workflow)に関するページを参照してください。

Azure Open Datasets は、シナリオ固有の機能を追加して予測ソリューションを強化し、精度を向上させるために使用できる、キュレーションされたパブリック データセットです。 機械学習モデルのトレーニングに役立つパブリック ドメイン データについては、[Open Datasets カタログ](https://azure.microsoft.com/en-in/services/open-datasets/catalog/)を参照してください。次に例を示します。

* [気象](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [国勢調査](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [祝日](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [公安](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* location

Open Datasets はクラウドにある Microsoft Azure 上にあり、[Azure Machine Learning Python SDK](#create-datasets-with-the-sdk) および [Azure Machine Learning Studio](#create-datasets-with-the-studio) の両方に含まれています。


## <a name="prerequisites"></a>前提条件

この記事では、以下が必要です。

* Azure サブスクリプション。 まだ持っていない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

* [Azure Machine Learning ワークスペース](../machine-learning/how-to-manage-workspace.md)。

* [Azure Machine Learning SDK for Python がインストール済み](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) (これには `azureml-datasets` パッケージが含まれています)。

    * [Azure Machine Learning コンピューティング インスタンス](../machine-learning/concept-compute-instance.md#managing-a-compute-instance)を作成します (これは、統合ノートブックと SDK が既にインストールされている、完全に構成および管理された開発環境です)。

    **OR**

    * ご自分の Python 環境で作業し、[こちらの手順](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)に従って SDK をインストールします。

> [!NOTE]
> 一部の Dataset クラスは、[azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) パッケージに依存しています。これは、64 ビットの Python とのみ互換性があります。 Linux ユーザーの場合、これらのクラスは次のディストリビューションでのみサポートされています。Red Hat Enterprise Linux (7、8)、Ubuntu (14.04、16.04、18.04)、Fedora (27、28)、Debian (8、9)、および CentOS (7)

## <a name="create-datasets-with-the-sdk"></a>SDK を使用してデータセットを作成する

Python SDK の Azure Open Datasets クラスを使用して Azure Machine Learning データセットを作成するには、`pip install azureml-opendatasets` でパッケージがインストールされていることを確認します。 個々のデータセットは、SDK で独自のクラスによって表され、特定のクラスは Azure Machine Learning [`TabularDataset`、`FileDataset`](../machine-learning/how-to-create-register-datasets.md#dataset-types)、またはその両方として使用できます。 `opendatasets` クラスの完全な一覧については、[リファレンス ドキュメント](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)を参照してください。

特定の `opendatasets` クラスは、ファイルを直接操作したり、ダウンロードしたりできる、`TabularDataset` または `FileDataset` として取得できます。 他のクラスは、Python SDK の `Dataset` クラスの `get_tabular_dataset()` または `get_file_dataset()` 関数を使用して**のみ**、データセットを取得できます。

次のコードは、MNIST `opendatasets` クラスで `TabularDataset` または `FileDataset` のいずれかを返すことができることを示しています。 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

この例では、Diabetes `opendatasets` クラスは `TabularDataset` としてのみ使用できるため、`get_tabular_dataset()` が使用されます。

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>データセットを登録する

Azure Machine Learning データセットをワークスペースに登録すると、他のユーザーと共有したり、ワークスペース内の実験間で再利用したりすることができます。 Open Datasets から作成された Azure Machine Learning データセットを登録すると、データはすぐにはダウンロードされませんが、中央の保存場所から要求したとき (たとえばトレーニング中に) にデータにアクセスできます。

データセットをワークスペースに登録するには、[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) メソッドを使用します。 
```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>Studio を使用してデータセットを作成する

[Azure Machine Learning Studio](https://ml.azure.com) を使用して、Azure Open Datasets から Azure Machine Learning データセットを作成することもできます。これは、あらゆるスキル レベルのデータ サイエンス実務者がデータ サイエンス シナリオを実行するための機械学習ツールを含む統合 Web インターフェイスです。

> [!Note]
> Azure Machine Learning Studio を介して作成されたデータセットは、自動的にワークスペースに登録されます。

1. ワークスペースで、 **[資産]** の下にある **[データセット]** タブを選択します。 **[データセットの作成]** ドロップダウンをクリックし、 **[From Open Datasets]\(Open Datasets から\)** を選択します。

    ![UI と Open Dataset](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. データセットを、そのタイルを選択して選択します (検索バーを使用してフィルター処理をするオプションがあります)。 **[次へ]** を選択します。

    ![データセットを選択する](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. データセットの登録先の名前を選択し、必要に応じて、使用可能なフィルターを使用してデータをフィルター処理します。 この例では、**Public Holidays** データセットについて、期間を 1 年に、国番号を米国のみにしてフィルター処理します。 フィールドの説明や日付範囲などのデータの詳細については、[Azure Open Datasets カタログ](https://azure.microsoft.com/services/op[en-datasets/catalog/)を参照してください。 **［作成］** を選択します

    ![データセット パラメーターを設定してデータセットを作成する](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    この時点で、データセットはワークスペース内の **[データセット]** の下で利用できます。 これを、自分で作成した他のデータセットと同じ方法で使用できます。


## <a name="access-datasets-for-your-experiments"></a>実験用のデータセットにアクセスする

ML モデルのトレーニングのために、機械学習の実験でデータセットを使用します。 [データセットを使ってトレーニングする方法の詳細をご覧ください](../machine-learning/how-to-train-with-datasets.md)。

## <a name="example-notebooks"></a>サンプルの Notebook

Open Datasets 機能の例とデモについては、これらの[サンプル ノートブック](samples.md)を参照してください。

## <a name="next-steps"></a>次のステップ

* [最初の ML モデルをトレーニングする](../machine-learning/tutorial-1st-experiment-sdk-train.md)。

* [データセットを使ってトレーニングする](../machine-learning/how-to-train-with-datasets.md)。

* [Azure Machine Learning データセットを作成する](../machine-learning/how-to-create-register-datasets.md)。



