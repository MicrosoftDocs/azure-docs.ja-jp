---
title: 'CSV への変換: モジュール リファレンス'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service の CSV への変換モジュールを使用して、R または Python スクリプトのモジュールでダウンロード、エクスポート、または共有できる CSV 形式にデータセットを変換する方法について学習します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 2b8f235014efe440e518f1f9c4d0d454f6918955
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128912"
---
# <a name="convert-to-csv-module"></a>CSV への変換モジュール

この記事では、Azure Machine Learning service に使用されるビジュアル インターフェイスのモジュール (プレビュー) について説明します。

このモジュールは、R または Python スクリプトのモジュールでダウンロード、エクスポート、または共有できる CSV 形式にデータセットを変換するために使用します。

### <a name="more-about-the-csv-format"></a>CSV 形式の詳細 

"comma-separated values (コンマ区切り値)" の略である CSV 形式は、多くの外部機械学習ツールで使用されるファイル形式です。 CSV は、R や Python などのオープンソース言語を使用するときの一般的な交換形式です。

作業の大部分を Azure Machine Learning で行う場合でも、データセットを CSV に変換して外部ツールで使用すると便利なときがあります。 例:

+ CSV ファイルをダウンロードして Excel で開いたり、リレーショナル データベースにインポートしたりします。  
+ CSV ファイルをクラウド ストレージに保存し、Power BI からそれに接続して視覚エフェクトを作成します。  
+ CSV 形式を使用して、R や Python で使用するためにデータを準備します。 モジュールの出力を右クリックするだけで、Python または Jupyter ノートブックからデータに直接アクセスするために必要なコードが生成されます。 

データセットを CSV に変換すると、ファイルは Azure ML ワークスペースに保存されます。 Azure ストレージ ユーティリティを使ってファイルを直接開いて使用することも、モジュールの出力を右クリックしてコンピューターに CSV ファイルをダウンロードするか、R または Python のコードで使用することもできます。  

## <a name="how-to-configure-convert-to-csv"></a>CSV への変換を構成する方法

1.  [CSV への変換](./convert-to-csv.md)モジュールを実験に追加します。 このモジュールは、インターフェイスの **[Data Format Conversions]\(データ形式の変換\)** グループにあります。 

2. データセットを出力する任意のモジュールに、それを接続します。   
  
3.  実験を実行します。

### <a name="results"></a>結果
  

[CSV への変換](./convert-to-csv.md)の出力をダブルクリックして、以下のオプションのいずれかを選択します。  

 + **[Result Dataset]\(結果データセット\) -> [ダウンロード]** : データの CSV 形式のコピーをすぐに開きます。それをローカル フォルダーに保存できます。 フォルダーを指定しないと、既定のファイル名が適用されて、CSV ファイルはローカル環境の**ダウンロード** ライブラリに保存されます。


 + **[Result Dataset]\(結果データセット\) -> [データセットとして保存]** : CSV ファイルを別のデータセットとして Azure ML ワークスペースに保存して戻します。

 + **[Generate Data Access Code]\(データ アクセス コードの生成\)** : Azure ML で、データにアクセスするための 2 セットのコード (Python を使用するものと、R を使用するもの) が生成されます。データにアクセスするには、コード スニペットをアプリケーションにコピーします。 ( *[Generate Data Access Code]\(データ アクセス コードの生成\) は近日中に提供されます。* )

## <a name="next-steps"></a>次の手順

Azure Machine Learning service で[使用できる一連のモジュール](module-reference.md)を参照してください。 