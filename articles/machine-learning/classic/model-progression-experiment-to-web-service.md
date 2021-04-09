---
title: 'ML Studio (classic): モデルが Web サービスになるまでの過程 - Azure'
description: Azure Machine Learning Studio (クラシック) モデルが開発中の実験から Web サービスになるまでにたどる過程の概要。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: 4e0f5786047977a319825aae9f3c7b89c0aa118b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518625"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Machine Learning Studio (クラシック) モデルが実験から Web サービスになるまでの過程

**適用対象:** ![これはチェック マークです。つまり、この記事は Machine Learning Studio (クラシック) を対象としています。](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (クラシック)   ![これは X 印です。つまり、この記事は Azure Machine Learning を対象としています。](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Azure Machine Learning Studio (クラシック) によって提供される対話形式のキャンバスで、予測分析モデルを表す "***実験***" を開発、実行、テスト、反復処理できます。 以下に対応する、多様なモジュールが用意されています。

* 実験にデータを入力
* データを操作します。
* 機械学習アルゴリズムを使用してモデルをトレーニング
* モデルにスコアを付ける
* 結果を評価
* 最終的な値を出力

実験で問題がなければ、ユーザーが新しいデータを送信して折り返し結果を受信できるように、"***従来の Azure Machine Learning Web サービス** _" または "_ *_新しい Azure Machine Learning Web サービス_**" としてデプロイできます。

この記事では、Machine Learning モデルが開発中の実験から運用可能な Web サービスになるまでにたどる過程について、概要を説明します。

> [!NOTE]
> 機械学習モデルを開発し、デプロイする方法はほかにもありますが、この記事では Machine Learning Studio (クラシック) を使用する方法に焦点を当てています。 たとえば、R を使用して従来の予測 Web サービスを作成する方法については、[RStudio と Azure Machine Learning Studio を使用した予測 Web Apps の開発とデプロイ](/archive/blogs/machinelearning/build-deploy-predictive-web-apps-using-rstudio-and-azure-ml)に関するブログ記事を参照してください。
>
>

Azure Machine Learning Studio (クラシック) は "*予測分析モデル*" の開発とデプロイを目的に設計されていますが、予測分析モデルを含まない実験の開発に Studio (クラシック) を使用することもできます。 たとえば、データを入力して操作し、結果を出力するだけの実験などです。 この非予測実験は、予測分析実験と同様に、Web サービスとしてデプロイすることができますが、この実験では機械学習モデルのトレーニングやスコア付けが実施されないため、より簡単なプロセスになります。 これは Studio (クラシック) の一般的な使用方法ではありません。一般的な使用方法についてこれから説明し、Studio (クラシック) の機能を網羅することにします。

## <a name="developing-and-deploying-a-predictive-web-service"></a>予測 Web サービスの開発とデプロイ
Machine Learning Studio (クラシック) を使用して予測 Web サービスを開発し、デプロイする場合、一般的なソリューションがたどる段階は次のようになります。

![デプロイ フロー](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*図 1 - 一般的な予測分析モデルの段階*

### <a name="the-training-experiment"></a>トレーニング実験
"***トレーニング実験***" は、Machine Learning Studio (クラシック) で Web サービスを開発する最初のフェーズです。 トレーニング実験の目的は、機械学習モデルの開発、テスト、反復処理、そしてトレーニングの場所を提供することです。 最適なソリューションを見つけるために複数のモデルを同時にトレーニングすることもできますが、実験が終わったら、トレーニング済みのモデルを 1 つ選択し、残りのモデルを実験から除外することになります。 予測分析実験の開発の例については、[Azure Machine Learning Studio (クラシック) での信用リスク評価のための予測分析ソリューション開発](tutorial-part1-credit-risk.md)に関する記事を参照してください。

### <a name="the-predictive-experiment"></a>予測実験
トレーニング実験でモデルをトレーニングしたら、Machine Learning Studio (クラシック) で **[Web サービスの設定]** をクリックし、 **[Predictive Web Service]\(予測 Web サービス\)** を選択して、トレーニング実験を "**_予測実験_**" に変換する処理を開始します。 予測実験の目的は、トレーニング済みのモデルを使用して新しいデータにスコアを付け、最終的に Azure Web サービスとして運用できる状態にすることです。

この変換は、次の手順で行われます。

* トレーニングに使用したモジュールのセットを単一のモジュールに変換し、トレーニング済みのモデルとして保存する
* スコア付けに関係しない余分なモジュールをすべて除外する
* 最終的な Web サービスで使用する入力ポートと出力ポートを追加する

予測実験を Web サービスとしてデプロイできる状態にするには、さらに変更が必要になることがあります。 たとえば、Web サービスから結果のサブセットのみを出力するには、出力ポートの前にフィルター処理モジュールを追加します。

この変換プロセスでは、トレーニング実験は破棄されません。 このプロセスが完了すると、2 つのタブが Studio (クラシック) に表示されます。1 つがトレーニング実験用で、もう 1 つが予測実験用です。 このように、トレーニング実験に変更を加えてから、Web サービスをデプロイして、予測実験を再構築できます。 また、トレーニング実験のコピーを保存しておいて、新たに別の実験を始めることもできます。

> [!NOTE]
> **[Predictive Web Service (予測 Web サービス)]** をクリックすると、トレーニング実験を予測実験に変換する自動処理が開始されます。ほとんどの場合、この方法を利用できます。 トレーニング実験が複雑な場合 (結合するトレーニングのパスが複数ある場合など) は、この変換を手動で実行した方が良いこともあります。 詳細については、[Azure Machine Learning Studio (クラシック) でのデプロイのためにモデルを準備する方法](deploy-a-machine-learning-web-service.md)に関するページを参照してください。
>
>

### <a name="the-web-service"></a>Web サービス
予測実験の準備が問題なくできたら、Azure Resource Manager に基づいて、従来の Web サービスか新しい Web サービスのどちらかとしてサービスをデプロイできます。 "*従来の Machine Learning Web サービス*" としてデプロイすることでモデルを運用可能にするには、 **[Web サービスのデプロイ]** をクリックして、 **[Deploy Web Service [Classic]\(Web サービスのデプロイ [従来])]** を選択します。 "*新しい Machine Learning Web サービス*" としてデプロイするには、 **[Web サービスのデプロイ]** をクリックして、 **[Deploy Web Service [New]\(Web サービスのデプロイ [新規])]** を選択します。 ユーザーは、Web サービス REST API を使用してモデルにデータを送信し、折り返し結果を受信できるようになりました。 詳しくは、「[Azure Machine Learning Web サービスを使用する方法](consume-web-services.md)」をご覧ください。

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>一般的でないケース: 非予測 Web サービスの作成
実験で予測分析モデルをトレーニングしない場合、トレーニング実験とスコア付け実験の両方を作成する必要はありません。実験は 1 つのみになり、この実験を Web サービスとしてデプロイすることができます。 Machine Learning Studio (クラシック) は、使用したモジュールを分析することで、実験に予測モデルが含まれるかどうかを検出します。

実験を反復処理して問題がなければ、次の手順に従います。

1. **[Web サービスの設定]** をクリックし、 **[Retraining Web Service (Web サービスの再トレーニング)]** を選択します。入力ノードと出力ノードが自動的に追加されます。
2. **[実行]**
3. **[Web サービスのデプロイ]** をクリックし、デプロイ先の環境に応じて **[Deploy Web Service [Classic]\(Web サービスのデプロイ [従来])]** または **[Deploy Web Service [New]\(Web サービスのデプロイ [新規])]** を選択します。

これで Web サービスがデプロイされます。予測 Web サービスと同様に、アクセスして管理することができます。

## <a name="updating-your-web-service"></a>Web サービスの更新
これで、実験が Web サービスとしてデプロイされました。更新するにはどうすればよいでしょうか。

その方法は更新する対象によって異なります。

**入力または出力を変更する場合や、Web サービスでデータを操作する方法を変更する場合**

モデルは変更せず、Web サービスでのデータの処理方法だけを変更する場合は、予測実験を編集して、もう一度 **[Web サービスのデプロイ]** をクリックし、 **[Deploy Web Service [Classic]\(Web サービスのデプロイ [従来])]** または **[Deploy Web Service [New]\(Web サービスのデプロイ [新規])]** をクリックします。 Web サービスが停止し、更新済みの予測実験がデプロイされ、Web サービスが再び開始されます。

次に例を示します。入力データ行全体と予測の結果を返す予測実験があるとします。 Web サービスから結果のみを返すことに決まりました。 結果以外の列を除外するには、予測実験の出力ポートの直前に **プロジェクト列** モジュールを追加します。 もう一度 **[Web サービスのデプロイ]** をクリックし、 **[Deploy Web Service [Classic]\(Web サービスのデプロイ [従来])]** または **[Deploy Web Service [New]\(Web サービスのデプロイ [新規])]** を選択したときに、Web サービスが更新されます。

**新しいデータでモデルを再トレーニングする必要がある場合**

機械学習モデルを維持したまま新しいデータで再トレーニングする必要がある場合、選択肢は 2 つあります。

1. **Web サービスの実行中にモデルを再トレーニングする**: 予測 Web サービスの実行中にモデルを再トレーニングするには、トレーニング実験にいくつか変更を加えて "**_再トレーニング実験_ *_" にすることで実行できます。さらにそれを "_* _再トレーニング Web_ サービス**" としてデプロイできます。 この方法の手順については、「[プログラムによる Machine Learning のモデルの再トレーニング](./retrain-machine-learning-model.md)」を参照してください。
2. **元のトレーニング実験に戻り、さまざまなトレーニング データを使用してモデルを開発する**: 予測実験は Web サービスにリンクされますが、この方法では、トレーニング実験は直接リンクされません。 元のトレーニング実験を変更し、 **[Web サービスの設定]** をクリックすると、"*新しい*" 予測実験が作成されます。これをデプロイすると、"*新しい*" Web サービスが作成されます。 単に元の Web サービスを更新するのではありません。

   トレーニング実験を変更する必要がある場合は、トレーニング実験を開き、 **[名前を付けて保存]** をクリックしてコピーを作成します。 これにより、元のトレーニング実験、予測実験、Web サービスはそのままの状態で維持されます。 そのうえで、変更を加えることにより、新しい Web サービスを作成できます。 新しい Web サービスをデプロイした後で、前の Web サービスを停止するか、新しい Web サービスと共に実行し続けるかを決定できます。

**別のモデルをトレーニングする必要がある場合**

別の機械学習アルゴリズムを選択する、別のトレーニング方法を試行するなど、元の予測実験を変更するには、前に説明したモデルの再トレーニングの 2 つ目の手順を実行する必要があります。トレーニング実験を開き、 **[名前を付けて保存]** をクリックしてコピーを作成したうえで、新たにモデルを開発し、予測実験を作成して Web サービスをデプロイします。 これにより、元の予測実験と関係のない新しい Web サービスが作成されます。どちらか一方を実行するか、両方を実行し続けるかを決定できます。

## <a name="next-steps"></a>次のステップ
開発および実験のプロセスの詳細については、次の記事をご覧ください。

* 実験の変換 - [Azure Machine Learning Studio (クラシック) でのデプロイのためにモデルを準備する方法](deploy-a-machine-learning-web-service.md)
* Web サービスのデプロイ: [Azure Machine Learning Web サービスをデプロイする](deploy-a-machine-learning-web-service.md)
* モデルの再トレーニング: [プログラムによる Machine Learning のモデルの再トレーニング](./retrain-machine-learning-model.md)

プロセス全体の例については、以下を参照してください。

* [Machine Learning のチュートリアル: Azure Machine Learning Studio (クラシック) で初めての実験を作成する](create-experiment.md)
* [チュートリアル:信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する](tutorial-part1-credit-risk.md)