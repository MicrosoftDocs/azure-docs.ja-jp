---
title: Geo 人工知能データ サイエンス仮想マシンの使用 - Azure | Microsoft Docs
description: Geo AI Data Science Virtual Machine を使用し、地理空間データに基づいてデータを分析し、モデルを構築する方法について説明します。
keywords: ディープ ラーニング、AI、データ サイエンス ツール、データ サイエンス仮想マシン、地理空間分析
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 6e6737e928ece820ea9119d8dfe1d7cf22477646
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880111"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Geo 人工知能データ サイエンス仮想マシンの使用

Geo AI データ サイエンス VM を使用すると、分析用のデータをフェッチしたり、データ ラングリングを実行したり、地理空間情報を使用する AI アプリケーション用のモデルを構築することができます。 Geo AI データ サイエンス VM をプロビジョニングし、ArcGIS アカウントを使用して ArcGIS Pro にサインインしたら、ArcGIS デスクトップと ArcGis オンライン を操作できるようになります。 また、ArcGIS には、Geo データ サイエンス VM で事前構成されている Python インターフェイスと R 言語ブリッジからもアクセスできます。 機械学習やディープ ラーニングのフレームワークや、データ サイエンス VM で使用できるその他のデータ サイエンス ソフトウェアと組み合わせることで、高度な AI アプリケーションを構築できます。  


## <a name="configuration-details"></a>構成の詳細

ArcGIS とのインターフェイスに使用される Python ライブラリ ([arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm)) は、データ サイエンス VM のグローバル ルート conda 環境 (```c:\anaconda``` にあります) にインストールされます。 

- コマンド プロンプトで Python を実行する場合は、```activate``` を実行して、conda ルートの Python 環境へとアクティブ化します。 
- IDE や Jupyter Notebook を使用している場合は、環境やカーネルを選択して、正しい conda 環境を使用していることを確認できます。 

ArcGIS への R ブリッジは、[arcgisbinding](https://github.com/R-ArcGIS/r-bridge) という名前の R ライブラリとして、メインの Microsoft R サーバー スタンドアロン インスタンス (```C:\Program Files\Microsoft\ML Server\R_SERVER``` にあります) にインストールされます。 Visual Studio、RStudio、および Jupyter は、この R 環境を使用するようあらかじめ事前構成されており、```arcgisbinding``` R ライブラリにアクセスできるようになっています。 


## <a name="geo-ai-data-science-vm-samples"></a>Geo AI データ サイエンス VM のサンプル

ベースのデータ サイエンス VM から提供される ML やディープ ラーニング フレームワーク ベースのサンプルに加え、Geo AI データ サイエンス VM では、一連の地理空間サンプルも提供されます。 これらのサンプルを使用すると、地理空間データと ArcGIS ソフトウェアを使用した AI アプリケーションの開発をスムーズに開始できます。 


1. [Python を使用した地理空間分析](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): ArcGIS への Python インターフェイス ([arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) ライブラリによって提供されます) を使用して地理空間データを操作する方法が、初歩的なサンプルによって示されています。 また、従来型の機械学習に地理空間データを組み合わせて、その結果を ArcGIS のマップ上で視覚化する方法も示されています。 

2. [R を使用した地理空間分析](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): ArcGIS への R インターフェイス ([arcgisbinding](https://github.com/R-ArcGIS/r-bridge) ライブラリによって提供されます) を使用して地理空間データを操作する方法が、初歩的なサンプルによって示されています。 

3. [ピクセル レベルの土地利用区分](https://github.com/Azure/pixel_level_land_classification): 航空写真の画像を入力として受け取り、土地被覆ラベルを返す、ディープ ニューラル ネットワーク モデルの作成方法を説明したチュートリアルです。 土地被覆ラベルの例としては、"forested" や "water" があります。 このモデルでは、画像内のすべてのピクセルに対してラベルが返されます。 モデルは Microsoft のオープン ソース [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) のディープ ラーニング フレームワークを使用して構築されます。 


## <a name="next-steps"></a>次の手順

データ サイエンス VM を使用したその他のサンプルは、次のリンクから参照できます。

* [サンプル](dsvm-samples-and-walkthroughs.md)

