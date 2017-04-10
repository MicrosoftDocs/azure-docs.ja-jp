---
title: "機械学習アルゴリズム チート シート | Microsoft Docs"
description: "印刷可能な機械学習アルゴリズム チート シートは、Azure Machine Learning Studio で予測モデルに適したアルゴリズムを選択するのに役立ちます。"
keywords: "アルゴリズム チート シート、チート シート、Machine Learning アルゴリズム"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e1dc31ec-1acb-463f-ba77-de565d4ddf4d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 2cf4f498a107b8c6085489723ae946b119bb6244
ms.lasthandoff: 03/17/2017


---
# <a name="machine-learning-algorithm-cheat-sheet-for-microsoft-azure-machine-learning-studio"></a>Microsoft Azure Machine Learning Studio の機械学習アルゴリズム チート シート
**Microsoft Azure Machine Learning チート シート** を使用すると、予測分析モデルに最適なアルゴリズムを選択できます。

[Azure Machine Learning Studio](https://studio.azureml.net/) には、***regression***、***classification***、***clustering***、***anomaly detection*** ファミリの多様なアルゴリズムのライブラリがあります。 各アルゴリズムは、異なる種類の機械学習の問題に対処するために設計されています。

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>ダウンロード: 機械学習アルゴリズム チート シート
**チート シートをダウンロードする: [Machine Learning アルゴリズム チート シート (11 x 17 インチ)](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![Machine Learning Algorithm Cheat Sheet : Machine Learning アルゴリズムの選択方法][cheat-sheet]

[cheat-sheet]: ./media/machine-learning-algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Machine Learning アルゴリズム チート シートをダウンロードし、タブロイド サイズで印刷すると、手元に保管しやすくなり、アルゴリズムを選択するときに役立ちます。

> [!NOTE]
> このチート シートを使用する詳細なガイドについては、「 [Microsoft Azure Machine Learning のアルゴリズムを選択する方法](machine-learning-algorithm-choice.md) 」をご覧ください。
> 
> 

## <a name="more-help-with-algorithms"></a>アルゴリズムに関するその他のヘルプ
* このチート シートを使用して適切なアルゴリズムを選ぶ方法や、各種機械学習アルゴリズムの詳細と使用方法については、「[Microsoft Azure Machine Learning のアルゴリズムの選択方法](machine-learning-algorithm-choice.md)」をご覧ください。
* アルゴリズムに関する説明と使用例が記載されたインフォグラフィックのダウンロードについては、「[インフォグラフィックのダウンロード: Machine Learning の基礎とアルゴリズムの使用例](machine-learning-basics-infographic-with-algorithm-examples.md)」をご覧ください。
* Machine Learning Studio で使用可能な機械学習アルゴリズムのカテゴリ別の一覧については、Machine Learning Studio アルゴリズムとモジュールのヘルプの[モデルの初期化][initialize-model]に関するページを参照してください。
* Machine Learning Studio で使用できるすべてのアルゴリズムとモジュールの一覧 (アルファベット順) については、Machine Learning Studio アルゴリズムとモジュールのヘルプの「[Machine Learning Studio モジュールの一覧 (アルファベット順)][a-z-list]」をご覧ください。
* Machine Learning Studio の機能の概要を示す図をダウンロードして印刷する場合は、「 [Azure Machine Learning Studio 機能の概要図](machine-learning-studio-overview-diagram.md)」ご覧ください。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="notes-and-terminology-definitions-for-the-machine-learning-algorithm-cheat-sheet"></a>機械学習アルゴリズム チート シートの注意事項と用語の定義

* このアルゴリズム チート シートに示した提案は経験則です。 変化する場合や著しく異なる場合があります。 これは、開始点を提案するものです。 データに使用した複数のアルゴリズム間で競合が発生しても心配しないでください。 それぞれのアルゴリズムの原則を理解し、データが生成されたシステムを理解することに代わるものはありません。

* すべての機械学習アルゴリズムには、独自のスタイルや "*帰納的バイアス*" があります。 特定の問題に対しては、複数のアルゴリズムが適切な場合や、1 つのアルゴリズムが他のアルゴリズムよりも適している場合があります。 しかし、事前にどれが最適かを知ることができるとは限りません。 このような場合は、複数のアルゴリズムがチート シートに一緒に記載されています。 1 つのアルゴリズムを試してみて、結果に満足できない場合は、他のアルゴリズムを試してみるのが適切な方策でしょう。 同一データに対して複数のアルゴリズムを試し、その結果を比較した、「[Cortana Intelligence Gallery (Cortana Intelligence ギャラリー)](http://gallery.cortanaintelligence.com/)」の「[Compare Multi-class Classifiers: Letter recognition (多クラス分類モデルの比較: 文字認識)](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92)」というサンプルを参照してください。

* 機械学習には、主に 3 つのカテゴリ (**教師あり学習**、**教師なし学習**、**強化学習**) があります。

  * **教師あり学習**では、各データ ポイントに、カテゴリや関心のある値がラベル付けまたは関連付けられています。  カテゴリのラベルには、たとえば '猫' または '犬' のいずれかの画像を割り当てています。  値のラベルの例は、中古車に関連付けられている販売価格です。 教師あり学習の目的は、このような多くのラベルの付いた例を学習し、将来のデータ ポイントを予測して、 たとえば、新しい写真の動物を正しく識別したり、他の中古車に正しい販売価格を割り当てることができるようになることです。 これは、人気のある便利な機械学習の種類です。 Azure Machine Learning のモジュールは、[K 平均法クラスタリング][k-means-clustering]を除いて、すべて教師あり学習アルゴリズムです。

  * **教師なし学習**では、データ ポイントにラベルが関連付けられていません。 代わりに、教師なし学習アルゴリズムの目的は、いくつかの方法でデータを整理したり、その構造を記述することです。 これは、K 平均法と同様にクラスターにグループ化したり、複雑なデータを簡単に表示できるようにさまざまな表示方法を検索することを意味します。

  * **強化学習**では、アルゴリズムが各データ ポイントへの応答を基にしてアクションを選択します。 これはロボット工学の一般的な手法です。ある時点での一連のセンサーの読み取りがデータ ポイントになり、アルゴリズムがロボットの次の動作を選択します。 モノのインターネット アプリケーションにも自然に適合します。 学習アルゴリズムでは、短時間で報酬信号を受信し、その決定がいかに優れていたかを示します。 アルゴリズムはこれに基に、最大の報酬を実現するために、戦略を変更します。 現時点で、Azure ML には強化学習アルゴリズム モジュールはありません。

* **ベイズ法**は、統計的に独立したデータ ポイントについて仮定を立てます。 これは、1 つのデータ ポイントでモデル化されていない変動性は、他と相関関係がないことを意味するため、予測できません。 たとえば、記録されているデータが、次の地下鉄が到着するまでの分単位の時間である場合は、1 日の間に行われた 2 つの測定値は統計的に独立しています。 ただし、1 分間に行われた 2 つの測定値は統計的に独立していません。一方の値は他の値の高い予測です。

* **ブースト デシジョン ツリー回帰**は、特徴間の特徴の重複や相互作用を活用します。 つまり、特定のデータ ポイントの 1 つの特徴の値は、ある程度別の値の予測であるということです。 たとえば、毎日の最高 / 最低気温データで、その日の最低気温がわかれば、最高気温を合理的に推測できます。 2 つの特徴に含まれる情報は、少し冗長です。

* データを 3 つ以上のカテゴリに分類するには、はじめから多クラス分類モデルを使用するか、2 クラス分類モデルを組み合わせて**アンサンブル**を作成します。 アンサンブルの手法では、クラスごとに 2 クラス分類モデルを用意し、各モデルがデータを 2 つのカテゴリ ("このクラス" と "これ以外のクラス") に分類します。 これらの分類モデルは、データ ポイントの適切な割り当てに投票します。 これは、[一対全多クラス][one-vs-all-multiclass]のベースになっている運用原理です。

* ロジスティック回帰やベイズ ポイント マシンなどのいくつかのメソッドでは、**線形クラス境界**、 つまり、クラス間の境界が直線に近い (または、より一般的には超平面である) ことを前提としています。 多くの場合、これは、分離を試みるまでわからないデータの特性ですが、通常は事前に視覚化することで学習できるものです。 クラス境界が非常に不規則に見える場合は、デシジョン ツリー、デシジョン ジャングル、サポート ベクター マシン、ニューラル ネットワークを使用します。

* ニューラル ネットワークは、各カテゴリに**ダミー変数**を作成して、カテゴリが適用される場合はこれを 1 に設定し、適用されない場合は 0 に設定することで、カテゴリ変数と一緒に使用できます。


<!-- This is how you can embed a link in an image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-docs-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/

