---
title: コンテナー サポート
titleSuffix: Azure Cognitive Services
description: Docker コンテナーを使用して Cognitive Services をデータに近い場所に配置する方法について説明します。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: d9f226213215f66b53eb1ef248fd47f7b6dfee5a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705373"
---
# <a name="container-support-in-azure-cognitive-services"></a>Azure Cognitive Services でのコンテナーのサポート

Azure Cognitive Services でのコンテナーのサポートを使用すると、開発者は Azure で利用できるものと同じリッチな API を使用できます。また、[Docker コンテナー](https://www.docker.com/what-container)に付随するサービスをデプロイおよびホストできる柔軟性があります。 コンテナー サポートは現在、以下を含む Azure Cognitive Services のサブセットにおいて、プレビューで使用できます。

* [Anomaly Detector][ad-containers]
* [Computer Vision][cv-containers]
* [Face][fa-containers]
* [Form Recognizer][fr-containers]
* [Language Understanding (LUIS)][lu-containers]
* [Speech Service API][sp-containers]
* [Text Analytics][ta-containers]

<!--
* [Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->

コンテナー化とは、アプリケーションまたはサービスとその依存関係や構成をコンテナー イメージとしてパッケージ化するソフトウェア配布のアプローチです。 コンテナー イメージは、ほとんどまたはまったく変更せずに、コンテナー ホストにデプロイできます。 コンテナーは、相互および基になるオペレーティング システムから分離され、仮想マシンよりもフット プリントが小さくなります。 短期間のタスクに対してコンテナーをコンテナー イメージからインスタンス化し、不要になったら削除できます。

次のビデオでは、Cognitive Services コンテナーを使用するデモンストレーションを行っています。

[![Cognitive Services のコンテナーのデモ](./media/index/containers-video-image.png)](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services)

Cognitive Services リソースは [Microsoft Azure](https://azure.microsoft.com) 上で使用できます。 これらのサービス用の Azure リソースを作成して調べるには、[Azure portal](https://portal.azure.com/) にサインインしてください。

## <a name="features-and-benefits"></a>機能とメリット

- **データの制御**:これらの Cognitive Services がお客様のデータをどこで処理するかをお客様が選択できるようにします。 これは、クラウドにデータを送信することはできないが、Cognitive Services テクノロジにアクセスする必要があるお客様にとって重要です。 ハイブリッド環境でデータ、管理、ID、セキュリティの整合性をサポートします。
- **モデルの更新の制御**:お客様は、ソリューションにデプロイされているモデルのバージョン管理と更新を柔軟に行うことができます。
- **移植可能なアーキテクチャ**:Azure、オンプレミス、エッジにデプロイできる移植可能なアプリケーション アーキテクチャを作成できます。 コンテナーは、[Azure Kubernetes Service](../aks/index.yml)、[Azure Container Instances](../container-instances/index.yml)、または [Azure Stack](/azure-stack/operator) にデプロイされた [Kubernetes](https://kubernetes.io/) クラスターに直接デプロイできます。 詳しくは、「[Kubernetes を Azure Stack にデプロイする](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)」をご覧ください。
- **高スループット/低待ち時間**:お客様は、アプリケーションのロジックとデータに物理的に近い場所で Cognitive Services を実行できるようにすることで、高いスループットと低待ち時間の要件に合わせてスケーリングできます。 コンテナーでは、1 秒あたりのトランザクション数 (TPS) は制限されません。必要なハードウェア リソースを提供した場合は、コンテナーのスケールアップとスケールアウトの両方を行って需要を処理することができます。 

## <a name="containers-in-azure-cognitive-services"></a>Azure Cognitive Services でのコンテナー

Azure Cognitive Services のコンテナーでは次の Docker コンテナー セットが提供され、各コンテナーには Azure Cognitive Services のサービスの機能のサブセットが含まれます。

| Service | サポートされている価格レベル | コンテナー | 説明 |
|---------|----------|----------|-------------|
|[Anomaly Detector][ad-containers] |F0、S0|**Anomaly-Detector** |Anomaly Detector API では、機械学習を利用することで、時系列データを監視し、その中の異常を検出できます。<br>[アクセスの要求](https://aka.ms/adcontainer)|
|[Computer Vision][cv-containers] |F0、S1|**Recognize Text** |レシート、ポスター、名刺など、さまざまな表面や背景を持ついろいろなオブジェクトのイメージから、印刷されたテキストを抽出します。<br/><br/>**重要:** テキスト認識コンテナーは現在のところ、英語でのみ機能します。<br>[アクセスの要求](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Face][fa-containers] |F0、S0|**Face** |Face には、画像中の人の顔を検出し、顔のパーツ (鼻や目など)、性別、年齢のほか、マシンが予測するその他の顔の特徴などの属性を識別します。 検出に加えて、Face では、同じ画像または異なる画像中の 2 つの顏が同じかどうかを信頼スコアを使って確認したり、データベースと顏を比較して、似ている顏や同一の顔が既に存在するかどうかを調べたりできます。 また、同じ視覚的特徴を使用して、似た顔をグループに分けて整理することもできます。<br>[アクセスの要求](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Form recognizer][fr-containers] |F0、S0|**Form Recognizer** |Form Understanding では、機械学習の技術を適用して、フォームからキーと値のペアおよびテーブルを識別して抽出します。<br>[アクセスの要求](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0、S0|**LUIS** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|トレーニング済みまたは発行済みの Language Understanding モデル ("LUIS アプリ" と呼ばれます) を Docker コンテナーに読み込みます。ユーザーは、そのコンテナーの API エンドポイントからクエリ予測を利用することができます。 コンテナーからクエリのログを収集し、それらを [LUIS ポータル](https://www.luis.ai)に再度アップロードすることで、アプリの予測精度を高めることができます。|
|[Speech Service API][sp-containers] |F0、S0|**音声テキスト変換** |連続するリアルタイムの音声をテキストに書き起こします。<br>[アクセスの要求](https://aka.ms/speechcontainerspreview/)|
|[Speech Service API][sp-containers] |F0、S0|**テキスト読み上げ** |テキストを自然な音声に変換します。<br>[アクセスの要求](https://aka.ms/speechcontainerspreview/)|
|[Text Analytics][ta-containers] |F0、S|**キー フレーズ抽出** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |主なポイントを識別するキー フレーズを抽出します。 たとえば、「食べ物はおいしくて、すばらしいスタッフがいた」というテキストを入力すると、この API は話題の中心として "食べ物" と "すばらしいスタッフ" を返します。 |
|[Text Analytics][ta-containers]|F0、S|**言語検出** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |最大 120 の言語に対して、入力テキストが書かれている言語を検出し、要求で送信されたドキュメントごとに 1 つの言語コードを報告します。 言語コードは、評価値の強度を示すスコアと組みになります。 |
|[Text Analytics][ta-containers]|F0、S|**感情分析** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |肯定的または否定的な感情の手がかりを探して未加工のテキストを分析します。 この API はドキュメントごとに 0 から 1 までの感情スコアを返します。1 が最も肯定的となります。 分析モデルは、広範囲にわたるテキスト本文と Microsoft の自然言語技術を利用して事前トレーニングされています。 [一部の言語](./text-analytics/language-support.md)については、この API はユーザーが指定したあらゆる未加工テキストを分析し、評価し、呼び出し元のアプリケーションに結果を直接返すことができます。 |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

また、一部のコンテナーは Cognitive Services [**オールインワン オファリング**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)のリソース キー内でサポートされています。 単一の Cognitive Services オールインワン リソースを作成し、次のサービスに対してサポートされているサービス全体で同じ課金キーを使用できます。

* Computer Vision
* Face
* LUIS
* Text Analytics

## <a name="container-availability-in-azure-cognitive-services"></a>Azure Cognitive Services でのコンテナーの使用可能性

Azure Cognitive Services コンテナーはお使いの Azure サブスクリプションを通じてパブリックに使用でき、Docker コンテナー イメージは Microsoft Container Registry または Docker Hub からプルできます。 適切なレジストリからコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用できます。

> [!IMPORTANT]
> 現在、以下のコンテナーにアクセスするにはサインアップ プロセスを完了する必要があります。サインアップでは、お客様自身、お客様の会社、コンテナーを実装したいユース ケースに関するアンケートに答えて送信します。 アクセスが許可され、資格情報が提供されたら、Azure Container Registry によってホストされているプライベート コンテナー レジストリからコンテナー イメージをプルできます。
> * [Anomaly Detector](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Face](Face/face-how-to-install-containers.md)
> * [Form Recognizer](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Recognize Text](Computer-vision/computer-vision-how-to-install-containers.md)
> * [Speech-to-text と Text-to-speech](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>前提条件

Azure Cognitive Services コンテナーを使用するには、次の前提条件を満たしている必要があります。

**Docker エンジン**: Docker エンジンをローカルにインストールしている必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms)、[Windows](https://docs.docker.com/docker-for-windows/) 上で Docker 環境を構成するパッケージが用意されています。 Windows では、Linux コンテナーをサポートするように Docker を構成する必要があります。 Docker コンテナーは、[Azure Kubernetes Service](../aks/index.yml) または [Azure Container Instances](../container-instances/index.yml) に直接デプロイできます。

コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。

**Microsoft Container Registry と Docker に関する知識**: レジストリ、リポジトリ、コンテナー、コンテナー イメージなどの Microsoft Container Registry と Docker の両方の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。

Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。

個々のコンテナーには、サーバーやメモリ割り当ての要件など、独自の要件もある場合があります。

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>次の手順

Cognitive Services で使用できる[コンテナー レシピ](containers/container-reuse-recipe.md)について学習します。

Azure Cognitive Services のコンテナーによって提供される機能をインストールして試してください。

* [Anomaly Detector コンテナー][ad-containers]
* [Computer Vision コンテナー][cv-containers]
* [Face コンテナー][fa-containers]
* [Form Recognizer コンテナー][fr-containers]
* [Language Understanding (LUIS) コンテナー][lu-containers]
* [Speech Service API コンテナー][sp-containers]
* [Text Analytics コンテナー][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->


[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md