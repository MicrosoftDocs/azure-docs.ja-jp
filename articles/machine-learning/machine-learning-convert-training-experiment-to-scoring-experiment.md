---
title: "Azure Machine Learning の予測実験に変換する | Microsoft Docs"
description: "予測分析モデルのトレーニングに使用されるMachine Learning のトレーニング実験を、Web サービスとしてデプロイできる予測実験に変換する方法。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: eb943c45-541a-401d-844a-c3337de82da6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: db91a464843a7c2dc5460f12f7f306972d3a7da8
ms.lasthandoff: 03/22/2017


---
# <a name="convert-a-machine-learning-training-experiment-to-a-predictive-experiment"></a>Machine Learning のトレーニング実験から予測実験に変換する
Azure Machine Learning では、予測分析ソリューションをビルド、テスト、およびデプロイできます。

予測分析モデルをトレーニングするために、 *トレーニング実験* で作成および反復処理を実行し、新しいデータのスコア付けに使用する準備ができたら、スコア付け実験を準備して効率化する必要があります。 その後、この "*予測実験*" を Azure Web サービスとして運用可能な状態にし、ユーザーがデータをモデルに送信して、モデルの予測を受信できるようになります。

予測実験に変換することで、トレーニングされたモデルを Web サービスとしてデプロイできるようになります。 Web サービスのユーザーがモデルに入力データを送信し、モデルは予測結果を返します。 したがって、予測実験を変換した場合は、モデルが他のユーザーに使用されることを想定しておいてください。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

トレーニング実験を予測実験に変換するプロセスには、次の 3 つの手順が含まれます。

1. トレーニング済みの機械学習モデルを保存し、機械学習のアルゴリズムと[モデルのトレーニング][train-model] モジュールを、保存したトレーニング済みのモデルに置き換えます。
2. スコア付けに必要なモジュールのみに実験を絞り込みます。 トレーニング実験には、トレーニングに必要な多数のモジュールが含まれますが、モデルがトレーニングされてスコア付けに使用できるようになると、それらのモジュールは必要なくなります。
3. 実験の中で Web サービスのユーザーからのデータをどこで受け入れ、どのデータを返すか定義します。

## <a name="set-up-web-service-button"></a>[Web サービスの設定] ボタン
実験後 (実験キャンバスの一番下にある **[実行]** ボタン)、**[Web サービスの設定]** ボタン (**[予測 Web サービス]** オプションの選択) により、トレーニング実験を予測実験に変換するための 3 つの手順が実行されます。

1. トレーニング済みのモデルを、(実験キャンバスの左側にある) モジュール パレットの **[Trained Models (トレーニング済みモデル)]** セクションのモジュールとして保存し、機械学習アルゴリズムと[モデルのトレーニング][train-model] モジュールを保存されたトレーニング済みのモデルで置き換えます。
2. これは明らかに不要なモジュールを削除します。 この例では、[データの分割][split]<sup></sup>、2 番目の[モデルのスコア付け][score-model]、および [モデルの評価][evaluate-model]モジュールなどが該当します。
3. Web サービスの入力モジュールと出力モジュールを作成し、実験の既定の場所に追加します。

たとえば、次の実験では、サンプルの国勢調査のデータを使用した 2 クラスのブースト デシジョン ツリー モデルをトレーニングします。

![トレーニング実験][figure1]

この実験のモジュールは、基本的に 4 つの異なる機能を実行します。

![モジュール関数][figure2]

このトレーニング実験を予測実験に変換するときにこれらのモジュールの一部が不要になったり、別の目的を持つようになったりします。

* **データ**- このサンプル データセット内のデータは、スコア付け中には使用されません。Web サービスのユーザーは、スコア付けするデータを指定します。 ただし、データ型など、このデータセットからのメタデータは、トレーニング済みのモデルで使用されます。 このため、このメタデータを提供できるように、予測実験のデータセットを保持する必要があります。
* **準備** -スコア付けのために送信されるデータによっては、これらのモジュールが受信データの処理に必要な場合と不必要な場合があります。
  
    たとえば、この例では、サンプルのデータセットの値が欠落しており、モデルのトレーニングに必要ない列が含まれています。 このため、[見つからないデータのクリーンアップ][clean-missing-data] モジュールが見つからない値の処理のために含まれ、[データセット内の列の選択][select-columns]モジュールがこれらの余分な列をデータ フローから除外するために含まれます。 Web サービスを通じてスコア付けのために送信されるデータに不足している値がないことがわかっている場合は、[見つからないデータのクリーンアップ][clean-missing-data] モジュールを削除できます。 ただし、[データセット内の列の選択][select-columns]モジュールはスコア付けされる機能セットの定義に役立つため、このモジュールは残す必要があります。
* **トレーニング** - これらのモジュールは、モデルのトレーニングに使用します。 **[Set Up Web Service (Web サービスの設定)]** をクリックすると、これらのモジュールは単一のトレーニング済みのモデルに置き換えられます。 この新しいモジュールは、モジュール パレットの **[Trained Models (トレーニング済みのモデル)]** セクションに保存されます。
* **スコア** - この例では、データ ストリームをテスト データとトレーニング データのセットに分割するために、[分割][split]モジュールが使用されます。 予測実験では、これは必要ないため削除できます。 同様<sup></sup>に、2 番目の[モデルのスコア付け][score-model]モジュールと[モデルの評価][evaluate-model]モジュールは、テスト データの結果を比較するために使用されるため、これらのモジュールも予測実験では必要ありません。 ただし、残りの[モデルのスコア付け][score-model]モジュールは、Web サービスを通じてスコア結果を返すために必要です。

**[Web サービスの設定]**をクリックした後で、この例がどのようになるのかを次に示します。

![変換された予測実験][figure3]

これは、実験を Web サービスとしてデプロイするための準備として十分な場合があります。 ただし、実験に合わせて追加の作業を実行する可能性があります。

### <a name="adjust-input-and-output-modules"></a>入力と出力のモジュールを調整する
トレーニング実験では、トレーニング データのセットを使用して、機械学習アルゴリズムが必要とする形式でデータを取得するいくつかの処理を実行しました。 Web サービスを通じて受信することを想定しているデータで、この処理が不要な場合は、**Web サービスの入力モジュール**を実験の別のノードに移動できます (**Web サービスの入力モジュール**の出力をクリックし、適切なモジュールの入力ポートにドラッグします)。

たとえば、既定では、上の図で示すように、**Web サービスの設定**が **Web サービスの入力**モジュールをデータ フローの上部に置きます。 ただし、入力データがこの処理を必要としない場合、 **Web サービスの入力** を、データ処理モジュールの先に手動で配置できます。

![Web サービス入力の移動][figure4]

Web サービスを通じて提供される入力データが、これで処理なしでモデルのスコア付けモジュールに直接渡されるようになりました。

同様に、既定では **Web サービスの設定** によって、Web サービスの出力モジュールがデータ フローの下部に置かれます。 この例では、Web サービスはユーザーに完全な入力データのベクトル、およびスコア付けの結果を含む[モデルのスコア付け][score-model]モジュールの出力を返します。

ただし、別のものを返したい場合は、**Web サービスの出力**モジュールの前に別のモジュールを追加することができます。 たとえば、入力データのベクトル全体ではなく、スコア付けの結果のみを返すようにする場合は、[データセット内の列の選択][select-columns]モジュールを挿入して、スコア付けの結果以外のすべての列を除外することができます。 次に、**Web サービスの出力**モジュールを[データセット内の列の選択][select-columns]モジュールの出力に移動します。 実験は次のようになります。

![Web サービス出力の移動][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>データ処理モジュールを追加または削除する
スコア付け中に必要ないことがわかっているモジュールが、他にも実験に存在する場合は、それらを削除できます。 たとえば、**Web サービスの入力**モジュールをデータ処理モジュールの後の地点に移動したので、[見つからないデータのクリーンアップ][clean-missing-data] モジュールを予測実験から削除できます。

これで、予測実験が次のようになります。

![その他のモジュールの取り外し][figure6]

> [!TIP]
> データセットまたは[データセット内の列の選択][select-columns]モジュールを削除していないことに注意してください。 Web サービスのモデルは元のデータセットのデータを使用しませんが、データセットに定義された各列のデータ型などのメタデータは使用します。 同様に、[データセット内の列の選択][select-columns]モジュールは、どのデータ列がモデルに使用されるのかを特定します。 したがって、予測実験ではこれらモジュールの両方を残す必要があります。

### <a name="add-optional-web-service-parameters"></a>オプションの Web サービス パラメーターを追加する
場合によっては、Web サービスのユーザーがサービスにアクセスしたときに、モジュールの動作を変更できるようにすることもあります。 *Web サービスのパラメーター* を使えば変更できます。

一般的な例として、デプロイされた Web サービスのユーザーが Web サービスにアクセスしたときに別のデータ ソースを指定できるように、[データのインポート][import-data] モジュールを設定する場合があります。 または、別の宛先を指定できるように[データのエクスポート][export-data] モジュールを構成します。

Web サービスのパラメーターを定義して、1 つまたは複数のモジュール パラメーターに関連付け、必須かオプションかを指定することができます。 Web サービスのユーザーは、サービスにアクセスするときにこれらのパラメーターの値を指定することができ、モジュールの動作はそれに応じて変更されます。

Web サービスのパラメーターの詳細については、「[Azure Machine Learning Web サービスのパラメーターの使用][webserviceparameters]」を参照してください。

[webserviceparameters]: machine-learning-web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Web サービスとして予測実験をデプロイする
予測実験の準備を十分に実行したので、Azure Web サービスとしてデプロイできます。 Web サービスを使用してユーザーはデータをモデルに送信でき、モデルは予測を返します。

完全なデプロイ プロセスの詳細については、「[Azure Machine Learning Web サービスをデプロイする][deploy]」を参照してください。

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

