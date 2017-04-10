---
title: "機械学習のサンプル実験のコピー | Microsoft Docs"
description: "Cortana Intelligence ギャラリーと Microsoft Azure Machine Learning を利用して、サンプルの機械学習実験を基に新しい実験を作成する方法について説明します。"
services: machine-learning
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: 81e6c1d8-682c-4db3-bfd5-d7bfb1150ff3
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/13/2017
ms.author: cgronlun;olgali
translationtype: Human Translation
ms.sourcegitcommit: 25e1def880dbb8431e1a0cae9d8b84c6f5906315
ms.openlocfilehash: 7b2bca108770a79a693a408a628ccc6370c36b7c
ms.lasthandoff: 02/13/2017


---
# <a name="copy-sample-experiments-to-create-new-machine-learning-experiments"></a>サンプル実験をコピーして新しい機械学習実験を作成する
機械学習実験を一から作成する代わりに、 [Cortana Intelligence ギャラリー](https://gallery.cortanaintelligence.com/) にあるサンプル実験をベースとして使う方法について説明します。 サンプルを使って独自の機械学習ソリューションを構築することができます。

ギャラリーには、Microsoft Azure Machine Learning チームによるサンプル実験だけでなく、Machine Learning コミュニティで共有されているサンプルもあります。 また、実験について質問したり、コメントを投稿したりすることができます。

ギャラリーの使用方法については、[初心者向けデータ サイエンス](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) シリーズの 3 分間のビデオ「[他のユーザーの成果物をコピーしてデータ サイエンスを実行する](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md)」をご覧ください。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="find-an-experiment-to-copy-in-cortana-intelligence-gallery"></a>Cortana Intelligence ギャラリーでコピーする実験を探す
どのような実験を利用できるかを確認するには、 [ギャラリー](https://gallery.cortanaintelligence.com/) に移動して、ページの上部の **[Experiments (実験)]** をクリックします。

### <a name="find-the-newest-or-most-popular-experiments"></a>最も新しい実験または最も人気のある実験を探す
このページでは、**最近追加された**実験を確認できます。そのほか、**人気のある実験**や、**人気のある Microsoft の実験の最新**バージョンまで下へスクロールして確認することができます。

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>特定の要件を満たす実験を探す
すべての実験を参照するには、次の手順を実行します。

1. ページの上部にある **[Browse all (すべて参照)]** をクリックします。
2. **[Categories (カテゴリ)]** セクションの **[Refine by (絞り込み条件)]** で **[Experiment (実験)]** を選ぶと、ギャラリー内のすべての実験が表示されます。
3. 自分の要件を満たす実験は、いくつかの方法で探すことができます。
   * **左側のフィルターを選択する。** たとえば、PCA ベースの異常検出アルゴリズムを使用する実験を参照するには、**[Categories (カテゴリ)]** で **[Experiment (実験)]** を選択し、**[Algorithms Used (使用されたアルゴリズム)]** で **[PCA-Based Anomaly Detection (PCA ベースの異常検出)]** を選択します  (このアルゴリズムが表示されない場合は、一覧の末尾にある **[Show all (すべて表示)]** をクリックしてください)。<br></br>
     ![Select filters](./media/machine-learning-sample-experiments/refine-the-view.png)
   * **検索ボックスを使う。** たとえば、Microsoft によって投稿された、2 クラス サポート ベクター マシン アルゴリズムを使用する数字認識に関連する実験を検索するには、検索ボックスで「digit recognition」と入力します。 次に、**[Experiment (実験)]**、**[Microsoft content only (Microsoft のコンテンツのみ)]**、**[Two-Class Support Vector Machine (2 クラス サポート ベクター マシン)]** フィルターを選択します。<br></br>
     ![検索ボックスを使う](./media/machine-learning-sample-experiments/search-for-experiments.png)
4. 実験をクリックして詳細を表示します。
5. 実験を実行したり変更したりするには、実験のページで **[Open in Studio (Studio で開く)]** をクリックします。 <br></br>

    ![実験例](./media/machine-learning-sample-experiments/example-experiment.png)

    > [!NOTE]
    > Machine Learning Studio で初めて実験を開くときは、Azure サブスクリプションを無料で試すか購入できます。 [Machine Learning Studio の無料試用版と有料サービスの詳細](https://azure.microsoft.com/pricing/details/machine-learning/)
    >
    >

## <a name="use-a-template-in-machine-learning-studio"></a>Machine Learning Studio でのテンプレートの使用
ギャラリーのサンプルをテンプレートとして、Machine Learning Studio で新しい実験を作成することもできます。

1. Microsoft アカウントの資格情報で [Studio](https://studio.azureml.net)にサインインし、**[新規]** をクリックして実験を作成します。
2. サンプルのコンテンツを参照し、いずれかをクリックします。

サンプル実験をテンプレートとして、ワークスペースに新しい実験が作成されます。

## <a name="next-steps"></a>次のステップ
* [さまざまなソースからデータをインポートする](machine-learning-data-science-import-data.md)
* [Machine Learning 向け R 言語のクイックスタート チュートリアル](machine-learning-r-quickstart.md)
* [Machine Learning Web サービスをデプロイする](machine-learning-publish-a-machine-learning-web-service.md)

