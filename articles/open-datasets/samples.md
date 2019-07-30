---
title: NOAA オープン データセットを使用するサンプル Jupyter Notebooks
titleSuffix: Azure Open Datasets
description: Azure オープン データセット用のサンプル Jupyter Notebooks を使用して、オープン データセットを読み込んでデモ データのエンリッチメントのために使用する方法を学習します。 データを処理するために手法として Spark と Pandas の使用が含まれます。
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: 6991b641bc85a8944bfd12a43f9b049e44ea88d8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598963"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>サンプル Jupyter Notebooks は、オープン データセットを使用してデータをエンリッチする方法を示します。 
Azure オープン データセット用のサンプル Jupyter Notebooks は、オープン データセットを読み込んでデモ データのエンリッチメントのために使用する方法を示します。 データを処理するために手法として Apache Spark と Pandas の使用が含まれます。

>[!IMPORTANT]
>Spark 以外の環境で作業している場合、オープン データセットでは、大きなデータセットによる MemoryError を回避するために、特定のクラスの 1 か月分のデータしか一度にダウンロードできません。

## <a name="load-noaa-integrated-surface-database-isd-data"></a>NOAA Integrated Surface Database (ISD) データを読み込む 
|ノートブック        | 説明                                    |
|----------------|------------------------------------------------|
|[直近 1 か月の気象データを Pandas データフレームに読み込む](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | 過去の気象データをお気に入りの Pandas データフレームに読み込む方法について説明します。 |
|[直近 1 か月の気象データを Spark データフレームに読み込む](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | 過去の気象データをお気に入りの Spark データフレームに読み込む方法について説明します。  |

## <a name="join-demo-data-with-noaa-isd-data"></a>デモ データを NOAA ISD データと結合する 
|ノートブック        | 説明                                    |
|----------------|------------------------------------------------|
|[デモ データと気象データを結合する - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | センサー地点の 1 か月のデモ データセットを Pandas データフレームの気象測定値と結合します。  |
|[デモ データを気象データと結合する - Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | センサー地点のデモ データセットを Spark データフレームの気象測定値と結合します。 |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>ニューヨーク市のタクシー データを NOAA ISD データと結合する 
|ノートブック        | 説明                                    |
|----------------|------------------------------------------------|
|[気象データによってエンリッチされたタクシー乗車データ - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | ニューヨーク市のグリーン タクシー データ (1 か月分) を読み込み、気象データによって Pandas データフレームでエンリッチします。 この例は、メソッド `get_pandas_limit` をオーバーライドして、データ読み込みパフォーマンスとデータ容量のバランスを調整します。|
|[気象データによってエンリッチされたタクシー乗車データ - Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | ニューヨーク市のグリーン タクシー データを読み込み、気象データによって Spark データフレームでエンリッチします。  |

## <a name="next-steps"></a>次の手順

* [チュートリアル:自動機械学習とオープン データセットを使う回帰モデル](tutorial-opendatasets-automl.md)
* [オープン データセット用 Python SDK](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Azure オープン データセット カタログ](https://azure.microsoft.com/services/open-datasets/catalog/)
