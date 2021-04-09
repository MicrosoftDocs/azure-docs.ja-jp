---
title: 'ONNX: 高パフォーマンスなクロス プラットフォームの推論'
titleSuffix: Azure Machine Learning
description: Open Neural Network Exchange (ONNX) の使用が機械学習モデルの推論の最適化にどのように寄与するかについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: prasantp
author: prasanthpul
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: 5a1ed6c43e4d3a96e0e19d7be50e1e16bcab780e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98071240"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX と Azure Machine Learning:ML モデルの作成と能率化

[Open Neural Network Exchange](https://onnx.ai) (ONNX) の使用が機械学習モデルの推論の最適化にどのように寄与するかについて説明します。 推論、つまりモデル スコアリングとは、通常は運用環境のデータに基づいて、デプロイしたモデルを使用して予測を行うフェーズです。 

推論に使用される機械学習モデルを最適化するのは、ハードウェアの性能を最大限に活かせるようにモデルと推論 (つまりモデルのスコアリング) ライブラリをチューニングする必要があるため、簡単ではありません。 さまざまな種類のプラットフォーム (クラウドやエッジ、CPU や GPU など) で最適なパフォーマンスを引き出そうとすると、問題はきわめて難しくなります。性能と特性がそれぞれ異なるためです。 多種多様なフレームワークのモデルがあって、それらをさまざまなプラットフォームで実行する必要がある場合、複雑さが増します。 異なるフレームワークとハードウェアの組み合わせをすべて最適化するのは、時間がかかりすぎます。 候補となるフレームワークで一度だけトレーニングを行えば、クラウドやエッジのどこででも実行できるソリューションが求められます。 その切り札となるのが ONNX です。

ONNX は、機械学習モデルを表現するためのオープン スタンダードとして、Microsoft とパートナー コミュニティによって作成されました。 TensorFlow、PyTorch、SciKit-Learn、Keras、Chainer、MXNet、MATLAB、SparkML など、[さまざまなフレームワーク](https://onnx.ai/supported-tools)のモデルを標準の ONNX 形式にエクスポートまたは変換することができます。 ONNX 形式になったモデルは、さまざまなプラットフォームやデバイスで実行することができます。

[ONNX Runtime](https://onnxruntime.ai) は、ONNX モデルを運用環境にデプロイするためのハイパフォーマンスの推論エンジンです。 クラウドとエッジの両方に最適化され、Linux、Windows、Mac で動作します。 C++ で記述されていますが、さまざまな環境で使用するために、C、Python、C#、Java、Javascript (Node.js) API も用意されています。 ONNX ランタイムは、DNN と従来の ML モデルの両方をサポートし、NVidia GPU 上の TensorRT、Intel プロセッサ上の OpenVINO、Windows 上の DirectML などのさまざまなハードウェアのアクセラレータと統合されます。 ONNX Runtime を使用することにより、広範囲にわたる運用グレードの最適化、テスト、継続的改善の恩恵が得られます。

ONNX Runtime は、Bing、Office、Azure Cognitive Services といった高スケールの Microsoft サービスで使用されています。 パフォーマンス向上はさまざまな要素に左右されますが、これらの Microsoft サービスは、__CPU のパフォーマンスが平均 2 倍に向上__ しています。 ONNX ランタイムは、Azure Machine Learning サービスに加えて、Machine Learning のワークロードをサポートする次のような他の製品でも実行できます。
+ Windows: ランタイムは [Windows Machine Learning](/windows/ai/windows-ml/) の一部として Windows に組み込まれており、数億台のデバイスで実行されています。 
+ Azure SQL 製品ファミリ:[Azure SQL Edge](../azure-sql-edge/onnx-overview.md) および [Azure SQL Managed Instance](../azure-sql/managed-instance/machine-learning-services-overview.md) のデータに対して、ネイティブなスコアリングを実行します。
+ ML.NET:[ML.NET で ONNX モデルを実行します](/dotnet/machine-learning/tutorials/object-detection-onnx)。


[![トレーニング、コンバーター、デプロイを示す ONNX フロー図](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX モデルを取得する

ONNX モデルは、さまざまな方法で取得できます。
+ Azure Machine Learning (この記事の末尾にある例を参照してください) または[自動機械学習機能](concept-automated-ml.md#automl--onnx)で新しい ONNX モデルをトレーニングする
+ 別の形式で作成された既存のモデルを ONNX に変換する ([チュートリアル](https://github.com/onnx/tutorials)を参照) 
+ 事前トレーニング済みの ONNX モデルを [ONNX モデル ズー](https://github.com/onnx/models)から取得する
+ カスタマイズされた ONNX モデルを [Azure Custom Vision サービス](../cognitive-services/custom-vision-service/index.yml)から生成する 

画像の分類、オブジェクトの検出、テキスト処理など、多くのモデルは、ONNX モデルとして表現することができます。 モデルを正常に変換できない問題が発生した場合は、使用したコンバーターの GitHub で問題を報告してください。 問題が解決されるまでは、既存の形式のモデルを引き続きご使用ください。

## <a name="deploy-onnx-models-in-azure"></a>Azure 内に ONNX モデルをデプロイする

Azure Machine Learning を使用して、ONNX モデルのデプロイ、管理、および監視を実行できます。 標準的な[デプロイ ワークフロー](concept-model-management-and-deployment.md)と ONNX Runtime を使用して、クラウドでホストされる REST エンドポイントを作成できます。 この記事の最後にあるサンプル Jupyter Notebook を参照し、ご自身で試してみてください。 

### <a name="install-and-use-onnx-runtime-with-python"></a>Python の ONNX Runtime をインストールして使用する

ONNX Runtime 用 Python パッケージは、[PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime)、[GPU](https://pypi.org/project/onnxruntime-gpu)) から入手できます。 インストール前に、[システム要件](https://github.com/Microsoft/onnxruntime#system-requirements)をお読みください。 

 Python 用 ONNX Runtime をインストールするには、次のいずれかのコマンドを使用します。 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Python スクリプトで ONNX Runtime を呼び出すには、次を使用します:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

通常は、モデルに付属しているドキュメントに、モデルを使用するための入力と出力に関する情報が記載されています。 [Netron](https://github.com/lutzroeder/Netron) などの視覚化ツールを使用してモデルを表示することもできます。 ONNX Runtime では、モデルのメタデータ、入力、および出力のクエリを実行することもできます。    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

モデルを推論するには、`run` を使用し、返してほしい出力の一覧 (すべてが必要な場合は空のままにします) と入力値のマップを渡します。 結果として出力の一覧が返されます。  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

完全な Python API リファレンスについては、[ONNX Runtime リファレンス ドキュメント](https://aka.ms/onnxruntime-python)を参照してください。    

## <a name="examples"></a>例
ONNX モデルを作成してデプロイする Python ノートブックの例は、[how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) を参照してください。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

他の言語で使用するためのサンプルについては、[ONNX ランタイム の GitHub](https://github.com/microsoft/onnxruntime/tree/master/samples) を参照してください。

## <a name="more-info"></a>詳細情報

**ONNX** の詳細を確認するか、プロジェクトに協力します。
+ [ONNX プロジェクト Web サイト](https://onnx.ai)
+ [GitHub の ONNX コード](https://github.com/onnx/onnx)

**ONNX Runtime** の詳細を確認するか、プロジェクトに協力します。
+ [ONNX Runtime プロジェクトの Web サイト](https://onnxruntime.ai)
+ [ONNX Rumtime GitHub リポジトリ](https://github.com/Microsoft/onnxruntime)