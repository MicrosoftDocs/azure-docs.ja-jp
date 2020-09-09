---
title: 'Machine Learning Studio (classic): 例から実験を開始する - Azure'
description: Azure AI Gallery と Azure Machine Learning Studio (クラシック) を利用して、サンプルの機械学習実験を基に新しい実験を作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: sample
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/05/2018
ms.openlocfilehash: a715fdd50193a86abbb5baaa98599733f4a94b38
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685784"
---
# <a name="create-azure-machine-learning-studio-classic-experiments-from-working-examples-in-azure-ai-gallery"></a>Azure AI Gallery にある実用的なサンプルから Azure Machine Learning Studio (クラシック) の実験を作成する

**適用対象:** ![はい](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![いいえ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)




機械学習実験を一から作成する代わりに、[Azure AI ギャラリー](https://gallery.azure.ai/)にあるサンプル実験をベースとして使う方法について説明します。 サンプルを使って独自の機械学習ソリューションを構築することができます。

ギャラリーには、Microsoft Azure Machine Learning Studio (クラシック) チームによるサンプル実験だけでなく、Machine Learning コミュニティで共有されているサンプルもあります。 また、実験について質問したり、コメントを投稿したりすることができます。

ギャラリーの使用方法については、[初心者向けデータ サイエンス](data-science-for-beginners-the-5-questions-data-science-answers.md) シリーズの 3 分間のビデオ「[他のユーザーの成果物をコピーしてデータ サイエンスを実行する](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md)」をご覧ください。



## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Azure AI ギャラリーでコピーする実験を探す
どのような実験を利用できるかを確認するには、 [ギャラリー](https://gallery.azure.ai/) に移動して、ページの上部の **[Experiments (実験)]** をクリックします。

### <a name="find-the-newest-or-most-popular-experiments"></a>最も新しい実験または最も人気のある実験を探す
このページでは、**最近追加された**実験を確認できます。そのほか、**人気のある実験**や、**人気のある Microsoft の実験の最新**バージョンまで下へスクロールして確認することができます。

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>特定の要件を満たす実験を探す
すべての実験を参照するには、次の手順を実行します。

1. ページの上部にある **Browse all (すべて参照)** をクリックします。
2. 左側の **[Categories]\(カテゴリ\)** セクションの **[Refine by]\(絞り込み条件\)** で **[Experiment]\(実験\)** を選ぶと、ギャラリー内のすべての実験が表示されます。
3. 自分の要件を満たす実験は、いくつかの方法で探すことができます。
   * **左側のフィルターを選択する。** たとえば、PCA ベースの異常検出アルゴリズムを使用する実験を参照するには、 **[Categories]\(カテゴリ)** で **[Experiment]\(実験)** を選択します。 次に、 **[Algorithms Used]\(使用されたアルゴリズム\)** の **[Show all]\(すべて表示\)** をクリックし、ダイアログ ボックスで **[PCA-Based Anomaly Detection]\(PCA ベースの異常検出\)** を選択します。 スクロールしないと見えない場合があります。<br></br>
     ![フィルターの選択](./media/sample-experiments/choose-an-algorithm.png)
   * **検索ボックスを使う。** たとえば、Microsoft によって投稿された、2 クラス サポート ベクター マシン アルゴリズムを使用する数字認識に関連する実験を検索するには、検索ボックスで「digit recognition」と入力します。 次に、 **[Experiment (実験)]** 、 **[Microsoft content only (Microsoft のコンテンツのみ)]** 、 **[Two-Class Support Vector Machine (2 クラス サポート ベクター マシン)]** フィルターを選択します。<br></br>
     ![検索ボックスを使う](./media/sample-experiments/search-for-experiments.png)
4. 実験をクリックして詳細を表示します。
5. 実験を実行したり変更したりするには、実験のページで **[Open in Studio (Studio で開く)]** をクリックします。 <br></br>

    ![実験例](./media/sample-experiments/example-experiment.png)

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>サンプルをテンプレートとして使用して新しい実験を作成する
ギャラリーの例をテンプレートとして使用して、Machine Learning Studio (クラシック) で新しい実験を作成することもできます。

1. Microsoft アカウントの資格情報で [Studio](https://studio.azureml.net)にサインインし、 **[新規]** をクリックして実験を作成します。
2. サンプルのコンテンツを参照し、いずれかをクリックします。

サンプル実験をテンプレートとして使用して、Machine Learning Studio (クラシック) ワークスペースに新しい実験が作成されます。

## <a name="next-steps"></a>次のステップ
* [さまざまなソースからデータをインポートする](import-data.md)
* [Machine Learning Web サービスをデプロイする](deploy-a-machine-learning-web-service.md)
