---
title: Azure での Geo 人工知能仮想マシンのプロビジョニング - Azure | Microsoft Docs
description: Geo AI Data Science Virtual Machine を作成して構成する方法について説明します。 Geo AI Data Science Virtual Machine は、地理的なデータを使用して AI と ML のソリューションを作成するツールを提供します。
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
ms.openlocfilehash: dde9b71c3615a592f8c08e040c5e9ba7bc756bd6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59008840"
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Azure での Geo 人工知能仮想マシンのプロビジョニング 

Geo AI データ サイエンス仮想マシン (Geo DSVM) は、広く使用されている [Azure データ サイエンス仮想マシン](https://aka.ms/dsvm)の拡張機能であり、AI と地理空間分析を組み合わせる目的で構成されています。 VM での地理空間分析には、[ArcGIS Pro](https://www.arcgis.com/features/index.html) が使用されます。 データ サイエンス VM では、地理情報で強化されたデータを対象にして、機械学習モデルやディープ ラーニング モデルを高速にトレーニングすることができます。 この VM は、Windows 2016 DSVM でのみサポートされます。 

Geo-DSVM には、AI 用のツールが複数含まれています。これには次のものが含まれます。

- 一般的なディープ ラーニング フレームワーク (Microsoft Cognitive Toolkit、TensorFlow、Keras、Caffe2、Chainer など) の GPU エディション 
- 画像やテキスト データを取得し、前処理するためのツール 
- 開発作業用のツール (Microsoft R Server Developer Edition、Anaconda Python、Python および R 用の Jupyter Notebook、Python および R 用の IDE、SQL データベースなど)
- ESRI の ArcGIS Pro デスクトップ ソフトウェアと、AI アプリケーションの地理空間データを使用するための Python および R インターフェイス。 
 

## <a name="create-your-geo-ai-data-science-vm"></a>Geo AI データ サイエンス VM の作成

次に示すのは、Geo AI データ サイエンス仮想マシンのインスタンスを作成するための手順です。 


1. [Azure ポータル](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows)に一覧表示されている仮想マシンに移動します。
2. 下部にある **[作成]** ボタンを選択してウィザードを起動します。
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. Geo-DSVM を作成するために使用されるウィザードでは、この図の右側に列挙されている **4 つのステップ**それぞれで**入力**が必要です。 以下は、これらの各ステップを構成するために必要な入力項目です。



   - **基本**

      1. **名前**: 作成するデータ サイエンス サーバーの名前です。

      2. **ユーザー名**: 管理者アカウントのログイン ID です。

      3. **パスワード**: 管理者アカウントのパスワードです。

      4. **サブスクリプション**:複数のサブスクリプションがある場合は、マシンが作成されて課金されるサブスクリプションを選択します。

      5. **リソース グループ**: 新しいリソース グループを作成するか、サブスクリプション内にある**空**の既存の Azure リソース グループを使用することができます。

      6. **場所**: 最適なデータ センターを選択します。 通常は、ネットワーク アクセスを最速にするために、データの大部分があるか、物理的に最も近いデータ センターを選びます。 GPU でディープ ラーニングを実行する必要がある場合は、Azure 内の場所のうち、NC シリーズ GPU VM インスタンスがある場所を選択する必要があります。 現在 GPU VM が展開されている地域は、**米国東部、米国中北部、米国中南部、米国西部 2、北ヨーロッパ、西ヨーロッパ**です。 最新の一覧については、[リージョン別の Azure 製品](https://azure.microsoft.com/regions/services/)に関するページを確認して、**コンピューティング**の **NC シリーズ**を探してください。 


   - **設定**:Geo DSVM 上の GPU でディープ ラーニングを実行する場合は、NC シリーズ GPU 仮想マシン サイズを選択します。 それ以外の場合は、CPU ベースのインスタンスを選択できます。  VM のストレージ アカウントを作成します。 
   
   - **概要**:入力したすべての情報が正しいことを確認します。

   - **[購入]**:プロビジョニングを開始するには、**[購入]** をクリックします。 サービスの使用条件へのリンクが表示されます。 **[サイズ]** ステップで選択したサーバー サイズのコンピューティングを超える追加の課金が VM によって発生することはありません。 
 
>[!NOTE]
> プロビジョニングには、20 ～ 30 分くらいかかります。 プロビジョニングの状態は、Azure ポータルに表示されます。

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Geo AI データ サイエンス仮想マシンにアクセスする方法

 VM が作成されたら、VM にインストールされて事前構成されたツールを使い始めることができます。 多くのツールには、スタート メニュー タイルとデスクトップ アイコンがあります。 前述の **[基本]** セクションで作成した管理者アカウントの資格情報を使って、リモート デスクトップを使用できます。 

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>VM にインストールされた ArcGIS Pro の使用

Geo-DSVM には ArcGIS Pro デスクトップがプレインストールされており、環境も、DSVM 内のすべてのツールを使用できるよう事前構成されています。 ArcGIS を起動すると、ArcGIS アカウントへのログインを求めるプロンプトが表示されます。 既に ArcGIS アカウントがあり、ソフトウェアのライセンスを持っている場合は、既存の資格情報を使用できます。  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

それ以外の場合は、サインアップして新規の ArcGIS アカウントとライセンスを取得するか、[無料試用版](https://www.arcgis.com/features/free-trial.html)を取得できます。 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

有料または無料試用版の ArcGIS アカウントにサインアップしたら、[ArcGIS Pro のスタードガイド](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf)に記載されている手順に従って、ArcGIS Pro を自分のアカウント用に承認できます。 

ArcGIS アカウントを使用して ArcGIS Pro デスクトップにサインインしたら、VM にインストールされた構成済みのデータ サイエンス ツールを、地理空間分析や機械学習のプロジェクトに使用できます。

## <a name="next-steps"></a>次の手順

次のトピックのガイダンスに従って、Geo AI データ サイエンス VM の使用を開始しましょう。

* [Geo AI データ サイエンス VM の使用](use-geo-ai-dsvm.md)
