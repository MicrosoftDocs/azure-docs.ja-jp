---
title: 'CSV への変換: モジュール リファレンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーの CSV への変換モジュールを使用して、後で再利用できる CSV ファイルにデータセットを変換する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: cc58689e30e9b03e490c0871f3decd50372371fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421925"
---
# <a name="convert-to-csv-module"></a>CSV への変換モジュール

この記事では Azure Machine Learning デザイナーのモジュールについて説明します。

このモジュールは、R または Python スクリプトのモジュールでダウンロード、エクスポート、または共有できる CSV 形式にデータセットを変換するために使用します。

### <a name="more-about-the-csv-format"></a>CSV 形式の詳細 

"comma-separated values (コンマ区切り値)" の略である CSV 形式は、多くの外部機械学習ツールで使用されるファイル形式です。 CSV は、R や Python などのオープンソース言語を使用するときの一般的な交換形式です。

作業の大部分を Azure Machine Learning で行う場合でも、データセットを CSV に変換して外部ツールで使用すると便利なときがあります。 次に例を示します。

+ CSV ファイルをダウンロードして Excel で開いたり、リレーショナル データベースにインポートしたりします。  
+ CSV ファイルをクラウド ストレージに保存し、Power BI からそれに接続して視覚エフェクトを作成します。  
+ CSV 形式を使用して、R や Python で使用するためにデータを準備します。 

データセットを CSV に変換すると、CSV は Azure ML ワークスペースに保存されます。 Azure ストレージ ユーティリティを使用して、ファイルを直接開いて使用することができます。 デザイナーで CSV にアクセスすることもできます。それには、 **[CSV に変換]** モジュールを選択し、右側のパネルの **[出力]** タブでヒストグラム アイコンを選択して出力を表示します。 CSV は、結果フォルダーからローカル ディレクトリにダウンロードできます。  

## <a name="how-to-configure-convert-to-csv"></a>CSV への変換を構成する方法


1.  [CSV に変換] モジュールをパイプラインに追加します。 このモジュールは、デザイナーの **[データ変換]** グループ内にあります。 

2. データセットを出力する任意のモジュールに、それを接続します。   
  
3.  パイプラインを送信します。

### <a name="results"></a>結果
  

**[CSV に変換]** の右側のパネルにある **[出力]** タブを選択し、 **[Port outputs]\(ポートの出力\)** の下にあるいずれかのアイコンを選択します。  

+ **データセットの登録**:このアイコンを選択して、CSV ファイルを別のデータセットとして Azure ML ワークスペースに保存して戻します。 データセットは、モジュール ツリーの **[My Datasets]\(マイ データセット\)** セクションにモジュールとして表示されます。

 + **View output (出力の表示)** :目のアイコンを選択し、指示に従って **Results_dataset** フォルダーを参照し、data.csv ファイルをダウンロードします。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 