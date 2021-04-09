---
title: 信頼できる機械学習とは (プレビュー)
titleSuffix: Azure Machine Learning
description: 信頼できる機械学習とはどのようなものか、および Azure Machine Learning でそれを使用してモデルを理解し、データを保護し、モデルのライフサイクルを管理する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 02/25/2021
ms.custom: responsible-ml
ms.openlocfilehash: f849968c16d2c3d8940a71da0e0cda536882d9c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692064"
---
# <a name="what-is-responsible-machine-learning-preview"></a>信頼できる機械学習とは (プレビュー)

この記事では、信頼できる機械学習 (ML) とはどのようなものか、および Azure Machine Learning でそれを実践する方法について説明します。

## <a name="responsible-machine-learning-principles"></a>信頼できる機械学習の原則

AI システムの開発と使用を通して、信頼をその中心にする必要があります。 プラットフォーム、プロセス、モデルでの信頼。 Microsoft では、信頼できる機械学習には次のような価値と原則が含まれます。

- 機械学習モデルを理解する
  - モデルの動作を解釈して説明する
  - モデルの不公平性を評価して軽減する
- ユーザーとそのデータを保護する
  - 差分プライバシーによりデータの漏えいを防ぐ
  - 準同型暗号を使用して暗号化されたデータを操作する
- エンドツーエンドの機械学習プロセスを制御する
  - データシートで機械学習のライフサイクルを文書化する

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="信頼できる機械学習の柱 - 解釈可能性、差分プライバシー、準同型暗号、監査ログ - Azure Machine Learning":::

社会構造により多くの人工知能や自律システムが統合されるようになったら、これらのテクノロジの望ましくない結果を事前に予測し、軽減することが重要です。

## <a name="interpret-and-explain-model-behavior"></a>モデルの動作を解釈して説明する

説明するのが難しい (オペーク ボックス) システムは、システムで特定の決定が行われた理由を、システム開発者、規制者、ユーザー、ビジネス意思決定者などの利害関係者が理解するのが困難であるため、問題になる可能性があります。 説明のしやすさは AI システムによって差があり、場合によっては、正確性の高いシステムと説明しやすいシステムの間でトレードオフが発生します。

解釈可能な AI システムを構築するには、Microsoft によって構築されたオープンソース パッケージである [InterpretML](https://github.com/interpretml/interpret) を使用します。 InterpretML パッケージは、SHapley Additive exPlanations (SHAP)、Mimic Explainer、Permutation Feature Importance (PFI) など、さまざまな解釈可能性手法をサポートしています。  [InterpretML を Azure Machine Learning の内部で使用](how-to-machine-learning-interpretability.md)して、[自動化された機械学習モデル](how-to-machine-learning-interpretability-automl.md)など、[機械学習モデルを解釈して説明する](how-to-machine-learning-interpretability-aml.md)ことができます。

## <a name="mitigate-fairness-in-machine-learning-models"></a>機械学習モデルでの公平性を軽減する

社会の日々の意思決定への AI システムの関与がますます大きくなると、これらのシステムがすべてのユーザーに公正な結果を提供するよう適切に機能することが非常に重要です。

不公平な AI システムでは、次のような意図しない結果になる可能性があります。

- 個人から機会、リソース、情報を取り上げる。
- バイアスやステレオタイプを強める。

公平性の多くの側面は、メトリックでは把握または表現できません。 AI システムの設計と開発における公平性を向上させることができるツールとプラクティスがあります。

AI システムにおける不公平性を減らすための 2 つの重要なステップは、評価と軽減です。 AI システムの潜在的な不公平性を評価および軽減できるオープンソース パッケージである [FairLearn](https://github.com/fairlearn/fairlearn) をすることをお勧めします。 公平性と FairLearn パッケージの詳細については、[ML での公平性に関する記事](./concept-fairness-ml.md)を参照してください。

## <a name="prevent-data-exposure-with-differential-privacy"></a>差分プライバシーによりデータの漏えいを防ぐ

データを分析に使用する場合は、使用期間全体を通して、データのプライベート性と機密性が維持されることが重要です。 差分プライバシーとは、個人のデータを安全かつプライベートに保つうえで役立つ、一連のシステムとプラクティスのことです。

従来のシナリオでは、生データはファイルとデータベースに保存されます。 データを分析する場合、ユーザーは通常、生データを使用します。 しかし、この方法では個人のプライバシーを侵害する可能性があるため、問題があります。 差分プライバシーは、データに "ノイズ" やランダム性を追加し、ユーザーが個々のデータ ポイントを識別できないようにすることで、この問題に対処しようとするものです。

差分プライバシー システムを実装することは簡単ではありません。 [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core) は、グローバルな差分プライバシー システムを構築するためのさまざまなコンポーネントを含んだ、オープンソース プロジェクトです。 差分プライバシーと SmartNoise プロジェクトの詳細については、[差分プライバシーと SmartNoise を使用したデータ プライバシーの保持](./concept-differential-privacy.md)に関する記事を参照してください。

## <a name="work-on-encrypted-data-with-homomorphic-encryption"></a>準同型暗号を使用して暗号化されたデータを処理する

従来のクラウド ストレージおよび計算ソリューションでは、顧客データへのアクセスと計算にクラウドが必要とするのは、暗号化されていないアクセスです。 このようなアクセスでは、クラウド オペレーターにデータが公開されます。 データ プライバシーは、クラウドによって実装されるアクセス制御ポリシーに依存し、顧客によって信頼されます。

準同型暗号では、暗号化されたデータで計算を行う際に秘密 (復号化) キーを利用する必要がありません。 計算の結果は暗号化され、秘密キーの所有者だけに開示されます。 準同型暗号を使用すると、クラウド オペレーターは、格納および計算するデータに、暗号化されていないアクセスを使用することが一切できなくなります。 計算は、暗号化されたデータに対して直接実行されます。 データ プライバシーは最先端の暗号化に依存し、データ所有者はすべての情報リリースを制御します。 Microsoft での準同型暗号の詳細については、[Microsoft Research](https://www.microsoft.com/research/project/homomorphic-encryption/) に関するページを参照してください。

Azure Machine Learning で準同型暗号の使用を開始するには、[Microsoft SEAL](https://github.com/microsoft/SEAL) の [encrypted-inference](https://pypi.org/project/encrypted-inference/) Python バインドを使用します。 Microsoft SEAL は、オープンソースの準同型暗号化ライブラリであり、ここでは暗号化された整数または実数に対して加算と乗算を行うことができます。 Microsoft SEAL の詳細については、[Azure アーキテクチャ センター](/azure/architecture/solution-ideas/articles/homomorphic-encryption-seal)、または [Microsoft Research プロジェクトのページ](https://www.microsoft.com/research/project/microsoft-seal/)を参照してください。

[Azure Machine Learning で暗号化された推論の Web サービスをデプロイする方法](how-to-homomorphic-encryption-seal.md)については、次のサンプルを参照してください。

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>データシートで機械学習のライフサイクルを文書化する

機械学習プロセスで適切な情報を文書化することは、各段階で責任ある決定を行ううえで重要なことです。 データシートは、機械学習のライフサイクルの一部として使用および作成される機械学習資産を文書化する手段です。

モデルは、"オペーク ボックス" と見なされる傾向があり、多くの場合、それらに関する情報はほとんどありません。 機械学習システムはますます普及し、意思決定に使用されるようになるため、データシートを使用することは、より多くの信頼できる機械学習システムを開発するためのステップです。

データシートの一部としてドキュメント化するモデル情報には次のようなものがあります。

- 使用目的
- モデルのアーキテクチャ
- 使用されるトレーニング データ
- 使用される評価データ
- トレーニング モデルのパフォーマンス メトリック
- 公平性の情報。

Azure Machine Learning SDK を使用して[モデル用のデータシート](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb)を実装する方法についてのサンプルを参照してください。

## <a name="additional-resources"></a>その他のリソース

- 詳細については、[責任あるイノベーションのツールキット](/azure/architecture/guide/responsible-innovation/)を参照して、ベスト プラクティスを確認します。
- 詳細については、機械学習システムのドキュメントに関するガイドラインの [ABOUT ML](https://www.partnershiponai.org/about-ml/) のセットを参照してください。