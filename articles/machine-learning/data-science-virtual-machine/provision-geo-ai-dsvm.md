---
title: クイック スタート:Geo Artificial Intelligence を設定する
titleSuffix: Azure Data Science Virtual Machine
description: Geo AI Data Science Virtual Machine を作成して構成する方法について説明します。 Geo AI Data Science Virtual Machine は、地理的なデータを使用して AI と ML のソリューションを作成するツールを提供します。
keywords: ディープ ラーニング、AI、データ サイエンス ツール、データ サイエンス仮想マシン、地理空間分析
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 03/05/2018
ms.openlocfilehash: 7afeec3f71cd1af30093801fedabf3f0357ae3d0
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208068"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>クイック スタート:Azure で Geo Artificial Intelligence Virtual Machine を設定する 

Geo AI Data Science Virtual Machine (Geo DSVM) は、広く使用されている [Azure Data Science Virtual Machine](https://aka.ms/dsvm) の拡張機能であり、AI と地理空間分析を組み合わせる目的で構成されています。 VM での地理空間分析には、[ArcGIS Pro](https://www.arcgis.com/features/index.html) が使用されます。 Data Science Virtual Machine (DSVM) では、機械学習モデルはもちろん、ディープラーニング モデルまで高速にトレーニングすることができます。 こうしたモデルの開発には、地理情報で強化されたデータが使用されます。 Geo-DSVM は、Windows 2016 DSVM でのみサポートされます。 

Geo-DSVM に付属する AI ツールの例を次に示します。

- 一般的なディープ ラーニング フレームワーク (Microsoft Cognitive Toolkit、TensorFlow、Keras、Caffe2、Chainer など) の GPU エディション
- 画像やテキスト データを取得し、前処理するためのツール
- 開発作業用のツール (Microsoft Machine Learning Server Developer Edition、Anaconda Python、Python および R 用の Jupyter Notebook、Python および R 用の IDE、SQL データベースなど)
- ESRI の ArcGIS Pro デスクトップ ソフトウェアと、AI アプリケーションの地理空間データを使用するための Python および R インターフェイス
 

## <a name="create-your-geo-ai-data-science-vm"></a>Geo AI データ サイエンス VM の作成

Geo AI Data Science VM のインスタンスを作成するには、次の手順を実行します。

1. [Azure portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows) に一覧表示されている仮想マシンに移動します。
1. 下部にある **[作成]** を選択してウィザードを起動します。

   ![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. ウィザードの 4 つのステップそれぞれについて必要事項を入力します。 この入力の詳細については、次のセクションを参照してください。

### <a name="wizard-details"></a>ウィザードの詳細 ###

**[基本]** :

- **[名前]** :作成するデータ サイエンス サーバーの名前です。
    
- **[ユーザー名]** : 管理者アカウントのサインイン ID です。
    
- **Password**:管理者アカウントのパスワードです。
    
- **サブスクリプション**:複数のサブスクリプションがある場合は、マシンが作成されて課金されるサブスクリプションを選択します。
    
- **[リソース グループ]** :新しいリソース グループを作成するか、サブスクリプション内にある**空**の既存の Azure リソース グループを使用することができます。
    
- **場所**: 最適なデータ センターを選択します。 通常は、データの大半が存在するデータ センターか、ネットワーク アクセスを最速にするために物理的に最も近いデータ センターになります。 GPU でディープ ラーニングを実行する場合は、Azure 内の場所のうち、NC シリーズ GPU VM インスタンスがある場所を選択する必要があります。 現時点で該当する場所は、**米国東部、米国中北部、米国中南部、米国西部 2、北ヨーロッパ、西ヨーロッパ**です。 最新の一覧については、[リージョン別の Azure 製品](https://azure.microsoft.com/regions/services/)に関するページを確認して、**コンピューティング**の **NC シリーズ**を探してください。 
    
    
**設定**:Geo DSVM 上の GPU でディープ ラーニングを実行する場合は、NC シリーズ GPU 仮想マシン サイズを選択します。 それ以外の場合は、CPU ベースのインスタンスを選択できます。 VM のストレージ アカウントを作成します。 
       
**概要**:入力したすべての情報が正しいことを確認します。
    
**[購入]** :プロビジョニング プロセスを開始するには、 **[購入]** をクリックします。 サービスの使用条件へのリンクが表示されます。 **[サイズ]** ステップで選択したサーバー サイズのコンピューティング料金を超える追加の課金が VM で発生することはありません。 
 
 >[!NOTE]
 > プロビジョニングには 20 分から 30 分程度かかります。 プロビジョニングの状態は、Azure ポータルに表示されます。

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Geo AI データ サイエンス仮想マシンにアクセスする方法

 VM が作成されたら、VM にインストールされて事前構成されたツールを使い始めることができます。 多くのツールには、スタート メニュー タイルとデスクトップ アイコンがあります。 **[基本]** セクションで構成した管理者アカウントの資格情報を使用し、リモート デスクトップで VM にアクセスできます。

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>VM にインストールされた ArcGIS Pro の使用

Geo-DSVM には ArcGIS Pro デスクトップがプレインストールされており、環境も、DSVM 内のすべてのツールを使用できるよう事前構成されています。 ArcGIS を起動すると、ArcGIS アカウントの資格情報が求められます。 既に ArcGIS アカウントがあり、ソフトウェアのライセンスを持っている場合は、既存の資格情報を使用できます。  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

それ以外の場合は、サインアップして新規の ArcGIS アカウントとライセンスを取得するか、[無料試用版](https://www.arcgis.com/features/free-trial.html)を取得できます。 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

ArcGIS の標準アカウントまたは無料試用版にサインアップしたら、[ArcGIS Pro のスタードガイド](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf)に記載されている手順に従って、ArcGIS Pro を自分のアカウント用に承認できます。

ArcGIS アカウントを使用して ArcGIS Pro デスクトップにサインインしたら、VM にインストールされた構成済みのデータ サイエンス ツールを、地理空間分析や機械学習のプロジェクトに使用できます。

## <a name="next-steps"></a>次の手順

次のリソースのガイダンスに従って、Geo AI Data Science VM の使用を開始しましょう。

* [Geo AI データ サイエンス VM の使用](use-geo-ai-dsvm.md)
