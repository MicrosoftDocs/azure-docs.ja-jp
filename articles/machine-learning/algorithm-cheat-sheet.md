---
title: 機械学習アルゴリズム チート シート - デザイナー
titleSuffix: Azure Machine Learning
description: 印刷可能な機械学習アルゴリズム チート シートは、Azure Machine Learning デザイナーで予測モデルに適したアルゴリズムを選択するのに役立ちます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
adobe-target: true
ms.openlocfilehash: 730fcf3fb552b92a02b255ad654f64d5ff7a92fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100367058"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Azure Machine Learning デザイナーの機械学習アルゴリズム チート シート

**Azure Machine Learning アルゴリズム チート シート** を使用すると、デザイナーから予測分析モデルに最適なアルゴリズムを選択できます。

Azure Machine Learning には、***分類** _、_*_レコメンダー システム_*_、_*_クラスタリング_*_、_*_異常検出_*_、_*_回帰_*_ 、_ *_テキスト分析_* * の各ファミリのアルゴリズムの大きなライブラリが用意されています。 各アルゴリズムは、異なる種類の機械学習の問題に対処するために設計されています。

その他のガイダンスについては、[アルゴリズムの選択方法](how-to-select-algorithms.md)に関する記事を参照してください。

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>ダウンロード:機械学習アルゴリズム チート シート

**チート シートをダウンロードする: [機械学習アルゴリズム チート シート (11 x 17 in.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![機械学習アルゴリズム チート シート: 機械学習アルゴリズムの選択方法について説明します。](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Machine Learning アルゴリズム チート シートをダウンロードし、タブロイド サイズで印刷すると、手元に保管しやすくなり、アルゴリズムを選択するときに役立ちます。

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>機械学習アルゴリズム チート シートの使用方法

このアルゴリズム チート シートに示した提案は経験則です。 変化する場合や著しく異なる場合があります。 このチート シートは、出発点を提案することを目的としています。 データに使用した複数のアルゴリズム間で競合が発生しても心配しないでください。 それぞれのアルゴリズムの原則と、データが生成されたシステムを理解することに代わるものはありません。

すべての機械学習アルゴリズムには、独自のスタイルや帰納的バイアスがあります。 特定の問題に対しては、複数のアルゴリズムが適切な場合や、1 つのアルゴリズムが他のアルゴリズムよりも適している場合があります。 しかし、事前にどれが最適かを知ることができるとは限りません。 このような場合は、複数のアルゴリズムがチート シートに一緒に記載されています。 1 つのアルゴリズムを試してみて、結果に満足できない場合は、他のアルゴリズムを試してみるのが適切な方策でしょう。 

Azure Machine Learning デザイナーのアルゴリズムの詳細については、[アルゴリズムとモジュールのリファレンス](algorithm-module-reference/module-reference.md)に関する記事を参照してください。

## <a name="kinds-of-machine-learning"></a>機械学習の種類

機械学習には、主に 3 つのカテゴリ (*教師あり学習*、*教師なし学習*、*強化学習*) があります。

### <a name="supervised-learning"></a>教師あり学習

教師あり学習では、各データ ポイントに、カテゴリや関心のある値がラベル付けまたは関連付けられています。 カテゴリのラベルには、たとえば '猫' または '犬' のいずれかの画像を割り当てています。 値のラベルの例は、中古車に関連付けられている販売価格です。 教師あり学習の目的は、このような多くのラベルの付いた例を学習し、将来のデータ ポイントを予測して、 たとえば、新しい写真の動物を正しく識別したり、他の中古車に正しい販売価格を割り当てることができるようになることです。 これは、人気のある便利な機械学習の種類です。

### <a name="unsupervised-learning"></a>教師なし学習

教師なし学習では、データ ポイントにラベルが関連付けられていません。 代わりに、教師なし学習アルゴリズムの目的は、いくつかの方法でデータを整理したり、その構造を記述することです。 教師なし学習を使うと、K-means と同様にデータをクラスターにグループ化することができます。また、複雑なデータをより単純に見えるようにするさまざまな方法が見つかります。

### <a name="reinforcement-learning"></a>強化学習

強化学習では、アルゴリズムが各データ ポイントに応答してアクションを選択します。 これはロボット工学の一般的な手法です。ある時点での一連のセンサーの読み取りがデータ ポイントになり、アルゴリズムがロボットの次の動作を選択します。 モノのインターネット アプリケーションにも自然に適合します。 また、学習アルゴリズムはその決定がどの程度優れていたかを示す報酬信号をその後短時間で受信します。 このアルゴリズムでは、この信号を基に戦略を変更し、最大の報酬を実現しようとします。 

## <a name="next-steps"></a>次のステップ

* [アルゴリズムの選択方法](how-to-select-algorithms.md)に関する追加のガイダンスを参照してください。

* [Azure Machine Learning と Azure portal での Studio について確認する](overview-what-is-azure-ml.md)。

* [チュートリアル:Azure Machine Learning デザイナーで予測モデルを構築する](tutorial-designer-automobile-price-train-score.md)。

* [ディープ ラーニングと機械学習の比較について確認する](concept-deep-learning-vs-machine-learning.md)。
