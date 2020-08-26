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
ms.openlocfilehash: e6f3a541f1e9dbca2c9949fb0c5cde28cd43e8e5
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88183032"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Azure Open Datasets から Azure Machine Learning データセットを作成する
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) から Azure Machine Learning データセットを作成して、ローカルまたはリモートの実験用のデータにアクセスする方法について説明します。 

[Azure Machine Learning データセット](../machine-learning/how-to-create-register-datasets.md)を作成すると、データ ソースの場所への参照とそのメタデータのコピーを作成できます。 データは既存の場所に残るため、追加のストレージ コストは発生せず、誤って元のデータ ソースが変更されるリスクもありません。 また、データセットは遅延評価されるので、ワークフローのパフォーマンス向上に役立ちます。
 
Azure Machine Learning のデータ アクセス ワークフロー全体におけるデータ セットの位置付けの詳細については、[データへの安全なアクセス](../machine-learning/concept-data.md#data-workflow)に関するページを参照してください。


Open Datasets は Microsoft Azure 上のクラウド内にあり、[Azure Machine Learning Python SDK](#create-datasets-with-the-sdk) および [Azure Machine Learning Studio](#create-datasets-with-the-studio) の両方に含まれています。

## <a name="why-use-azure-open-datasets"></a>Azure Open Datasets を使用する理由 

Azure オープン データセットは選別されたパブリック データセットであり、機械学習ソリューションにシナリオ固有の機能を追加してモデルの精度を上げるために使用できます。 データセットには、機械学習モデルのトレーニングと予測ソリューションのエンリッチメントに役立つ天気、国勢調査、祝日、公共の安全、場所に関するパブリック ドメイン データが含まれます。 

詳細については、[Open Datasets](overview-what-are-open-datasets.md) に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

データセットを作成して操作するには、以下が必要です。

* Azure サブスクリプション。 まだ持っていない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

* [Azure Machine Learning ワークスペース](../machine-learning/how-to-manage-workspace.md)。

* [Azure Machine Learning SDK for Python がインストール済み](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) (これには azureml-datasets パッケージが含まれています)。

    * [Azure Machine Learning コンピューティング インスタンス](../machine-learning/concept-compute-instance.md#managing-a-compute-instance)を作成します (これは、統合ノートブックと SDK が既にインストールされている、完全に構成および管理された開発環境です)。

    **OR**

    * ご自分の Jupyter Notebook で作業し、 [こちらの手順](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)に従って SDK をご自身でインストールします。

> [!NOTE]
> 一部の Dataset クラスは、[azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) パッケージに依存しています。これは、64 ビットの Python とのみ互換性があります。 Linux ユーザーの場合、これらのクラスは次のディストリビューションでのみサポートされています。Red Hat Enterprise Linux (7、8)、Ubuntu (14.04、16.04、18.04)、Fedora (27、28)、Debian (8、9)、および CentOS (7)

## <a name="create-datasets-with-the-sdk"></a>SDK を使用してデータセットを作成する

Azure Machine Learning Python SDK の Open Datasets クラスを使用してデータセットを作成するには、`pip install azureml-opendatasets` でパッケージがインストールされていることを確認します。 個々のデータセットは、SDK で独自のクラスによって表され、特定のクラスは `TabularDataset`、`FileDataset`、またはその両方として使用できます。 すべてのクラスの一覧については、[リファレンス ドキュメント](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)を参照してください。

特定のクラスは、`TabularDataset` または `FileDataset` として取得できます。こうすることで、ファイルを直接操作したり、ダウンロードしたりできます。 他のクラスは、`get_tabular_dataset()` 関数または`get_file_dataset()` 関数を使用する場合に**のみ**、データセットを取得できます。 次のコード サンプルは、これらの種類のクラスのいくつかの例を示しています。

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Open Datasets から作成されたデータセットを登録すると、データはすぐにはダウンロードされませんが、中央の保存場所から要求したとき (たとえばトレーニング中に) にデータにアクセスできます。

## <a name="create-datasets-with-the-studio"></a>Studio を使用してデータセットを作成する

[Azure Machine Learning Studio](https://ml.azure.com) を使用して Open Datasets からデータセットを作成することもできます。

1. ワークスペースで、 **[資産]** の下にある **[データセット]** タブを選択します。 **[データセットの作成]** ドロップダウンをクリックし、 **[From Open Datasets]\(Open Datasets から\)** を選択します。

    ![UI と Open Dataset](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. データセットを、そのタイルを選択して選択します (検索バーを使用してフィルター処理をするオプションがあります)。 **[次へ]** を選択します。

    ![データセットを選択する](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. データセットの登録先の名前を選択し、必要に応じて、使用可能なフィルターを使用してデータをフィルター処理します。 この例では、**public holidays** データセットについて、期間を 1 年に、国番号を米国のみにしてフィルター処理します。 **［作成］** を選択します

    ![データセット パラメーターを設定してデータセットを作成する](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    この時点で、データセットはワークスペース内の **[データセット]** の下で利用できます。 これを、自分で作成した他のデータセットと同じ方法で使用できます。


## <a name="access-datasets-for-your-experiments"></a>実験用のデータセットにアクセスする

ML モデルのトレーニングのために、機械学習の実験でデータセットを使用します。 [データセットを使ってトレーニングする方法の詳細をご覧ください](../machine-learning/how-to-train-with-datasets.md)。

## <a name="example-notebooks"></a>サンプルの Notebook

Open Datasets 機能の例とデモについては、これらの[ノートブック](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials)をご覧ください。

## <a name="next-steps"></a>次のステップ

* [データセットを使用してモデルをトレーニングします](../machine-learning/how-to-train-with-datasets.md)。

* [Azure Machine Learning データセットを作成します](../machine-learning/how-to-create-register-datasets.md)。



