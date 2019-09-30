---
title: Geo AI を使用する
titleSuffix: Azure Data Science Virtual Machine
description: Geo AI Data Science Virtual Machine を使用し、地理空間データに基づいてデータを分析し、モデルを構築する方法について説明します。
keywords: ディープ ラーニング、AI、データ サイエンス ツール、データ サイエンス仮想マシン、地理空間分析
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278415"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Geo 人工知能データ サイエンス仮想マシンの使用

Geo AI データ サイエンス VM を使用すると、分析用のデータをフェッチしたり、データ ラングリングを実行したり、地理空間情報を使用する AI アプリケーション用のモデルを構築することができます。 Geo AI Data Science VM をプロビジョニングし、ArcGIS アカウントを使用して ArcGIS Pro にサインインすると、ArcGIS デスクトップと ArcGIS オンラインを操作できるようになります。 また、ArcGIS には、Geo Data Science VM で事前構成されている Python インターフェイスと R 言語ブリッジからもアクセスできます。 高度な AI アプリケーションを構築するには、機械学習やディープ ラーニングのフレームワークやそこで使用できるその他のデータ サイエンス ソフトウェアと Geo Data Science VM を組み合わせます。  


## <a name="configuration-details"></a>構成の詳細

ArcGIS とのインターフェイスに使用される Python ライブラリ ([arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm)) は、Data Science VM のグローバル ルート conda 環境 (```c:\anaconda``` にあります) にインストールされます。

- コマンド プロンプトで Python を実行する場合は、```activate``` を実行して、conda ルートの Python 環境へとアクティブ化します。
- IDE や Jupyter Notebook を使用している場合は、環境やカーネルを選択して、正しい conda 環境を使用していることを確認できます。

ArcGIS への R ブリッジは、[arcgisbinding](https://github.com/R-ArcGIS/r-bridge) という名前の R ライブラリとして、```C:\Program Files\Microsoft\ML Server\R_SERVER``` にあるメイン Microsoft Machine Learning Server スタンドアロン インスタンスにインストールされます。 Visual Studio、RStudio、および Jupyter は、この R 環境を使用するようあらかじめ事前構成されており、```arcgisbinding``` R ライブラリにアクセスできるようになっています。 


## <a name="geo-ai-data-science-vm-samples"></a>Geo AI データ サイエンス VM のサンプル

ベースの Data Science VM から提供される機械学習やディープ ラーニング フレームワーク ベースのサンプルに加え、Geo AI Data Science VM では、一連の地理空間サンプルも提供されます。 これらのサンプルを使用すると、地理空間データと ArcGIS ソフトウェアを使用した AI アプリケーションの開発をスムーズに開始できます。


1. [Python を使用した地理空間分析の概要](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): ArcGIS への Python インターフェイスを介して地理空間データを操作する方法を示す初歩的なサンプルが、[arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) ライブラリで提供されています。 また、従来型の機械学習に地理空間データを組み合わせて、その結果を ArcGIS のマップ上で視覚化する方法も示されています。

2. [R を使用した地理空間分析の概要](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): ArcGIS への R インターフェイスを使用して地理空間データを操作する方法を示す初歩的なサンプル ([arcgisbinding](https://github.com/R-ArcGIS/r-bridge) ライブラリで提供されています)。 

3. [ピクセル レベルの土地利用区分](https://github.com/Azure/pixel_level_land_classification): 航空写真の画像を入力として受け取り、土地被覆ラベルを返す、ディープ ニューラル ネットワーク モデルの作成方法を説明したチュートリアルです。 土地被覆ラベルの例としては、"*forested*" や "*water*" があります。 このモデルでは、画像内のすべてのピクセルに対してラベルが返されます。 


## <a name="next-steps"></a>次の手順

データ サイエンス VM を使用したその他のサンプルは、次のリンクから参照できます。

* [サンプル](dsvm-samples-and-walkthroughs.md)