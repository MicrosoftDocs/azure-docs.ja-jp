---
title: コンテナー サポート
titleSuffix: Azure Cognitive Services
description: Docker コンテナーを使用して Cognitive Services をデータに近い場所に配置する方法について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 172774c90633c96c3a8e2c128df050fedeb8b52b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005963"
---
# <a name="container-support-in-azure-cognitive-services"></a>Azure Cognitive Services でのコンテナーのサポート

Azure Cognitive Services でのコンテナーのサポートを使用すると、開発者は Azure で利用できるものと同じリッチな API を使用できます。また、[Docker コンテナー](https://www.docker.com/what-container)に付随するサービスをデプロイおよびホストできる柔軟性があります。 現在、コンテナー サポートは、[Computer Vision](Computer-vision/Home.md)、[Face](Face/Overview.md)、[Text Analytics](text-analytics/overview.md)、[Language Understanding](LUIS/luis-container-howto.md) (LUIS) など、Azure Cognitive Services のサブセットに対するプレビューで使用できます。

コンテナー化とは、アプリケーションまたはサービスとその依存関係や構成をコンテナー イメージとしてパッケージ化するソフトウェア配布のアプローチです。 コンテナー イメージは、ほとんどまたはまったく変更せずに、コンテナー ホストにデプロイできます。 コンテナーは、相互および基になるオペレーティング システムから分離され、仮想マシンよりもフット プリントが小さくなります。 短期間のタスクに対してコンテナーをコンテナー イメージからインスタンス化し、不要になったら削除できます。

次のビデオでは、Cognitive Services コンテナーを使用するデモンストレーションを行っています。

[![Cognitive Services のコンテナーのデモンストレーション](./media/index/containers-video-image.png)](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services)

[Computer Vision](Computer-vision/Home.md)、[Face](Face/Overview.md)、[Text Analytics](text-analytics/overview.md)、および [Language Understanding (LUIS) ](LUIS/what-is-luis.md) サービスは、[Microsoft Azure](https://azure.microsoft.com) で使用できます。 これらのサービス用の Azure リソースを作成して調べるには、[Azure portal](https://portal.azure.com/) にサインインしてください。

## <a name="features-and-benefits"></a>機能とメリット

- **データの制御**:これらの Cognitive Services がお客様のデータをどこで処理するかをお客様が選択できるようにします。 これは、クラウドにデータを送信することはできないが、Cognitive Services テクノロジにアクセスする必要があるお客様にとって重要です。 ハイブリッド環境でデータ、管理、ID、セキュリティの整合性をサポートします。
- **モデルの更新の制御**:お客様は、ソリューションにデプロイされているモデルのバージョン管理と更新を柔軟に行うことができます。
- **移植可能なアーキテクチャ**:Azure、オンプレミス、エッジにデプロイできる移植可能なアプリケーション アーキテクチャを作成できます。 コンテナーは、[Azure Kubernetes Service](../aks/index.yml)、[Azure Container Instances](../container-instances/index.yml)、または [Azure Stack](/azure-stack/operator) にデプロイされた [Kubernetes](https://kubernetes.io/) クラスターに直接デプロイできます。 詳しくは、「[Kubernetes を Azure Stack にデプロイする](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)」をご覧ください。
- **高スループット/低待ち時間**:お客様は、アプリケーションのロジックとデータに物理的に近い場所で Cognitive Services を実行できるようにすることで、高いスループットと低待ち時間の要件に合わせてスケーリングできます。 コンテナーでは、1 秒あたりのトランザクション数 (TPS) は制限されません。必要なハードウェア リソースを提供した場合は、コンテナーのスケールアップとスケールアウトの両方を行って需要を処理することができます。 


## <a name="containers-in-azure-cognitive-services"></a>Azure Cognitive Services でのコンテナー

Azure Cognitive Services のコンテナーでは次の Docker コンテナー セットが提供され、各コンテナーには Azure Cognitive Services のサービスの機能のサブセットが含まれます。

| Service | サポートされている価格レベル | コンテナー | 説明 |
|---------|----------|----------|-------------|
|[Computer Vision](Computer-vision/computer-vision-how-to-install-containers.md) |F0、S1|**Recognize Text** |レシート、ポスター、名刺など、さまざまな表面や背景を持ついろいろなオブジェクトのイメージから、印刷されたテキストを抽出します。<br/><br/>**重要:** テキスト認識コンテナーは現在のところ、英語でのみ機能します。<br>[アクセスの要求](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Face](Face/face-how-to-install-containers.md) |F0、S0|**Face** |Face には、画像中の人の顔を検出し、顔のパーツ (鼻や目など)、性別、年齢のほか、マシンが予測するその他の顔の特徴などの属性を識別します。 検出に加えて、Face では、同じ画像または異なる画像中の 2 つの顏が同じかどうかを信頼スコアを使って確認したり、データベースと顏を比較して、似ている顏や同一の顔が既に存在するかどうかを調べたりできます。 また、同じ視覚的特徴を使用して、似た顔をグループに分けて整理することもできます。<br>[アクセスの要求](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[LUIS](LUIS/luis-container-howto.md) |F0、S0|**LUIS** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2043204))|トレーニング済みまたは発行済みの Language Understanding モデル ("LUIS アプリ" と呼ばれます) を Docker コンテナーに読み込みます。ユーザーは、そのコンテナーの API エンドポイントからクエリ予測を利用することができます。 コンテナーからクエリのログを収集し、それらを [LUIS ポータル](https://www.luis.ai)に再度アップロードすることで、アプリの予測精度を高めることができます。|
|[Text Analytics](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |F0、S|**キー フレーズ抽出** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2018757)) |主なポイントを識別するキー フレーズを抽出します。 たとえば、「食べ物はおいしくて、すばらしいスタッフがいた」というテキストを入力すると、この API は話題の中心として "食べ物" と "すばらしいスタッフ" を返します。 |
|[Text Analytics](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0、S|**言語検出** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2018759)) |最大 120 の言語に対して、入力テキストが書かれている言語を検出し、要求で送信されたドキュメントごとに 1 つの言語コードを報告します。 言語コードは、評価値の強度を示すスコアと組みになります。 |
|[Text Analytics](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0、S|**感情分析** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2018654)) |肯定的または否定的な感情の手がかりを探して未加工のテキストを分析します。 この API はドキュメントごとに 0 から 1 までの感情スコアを返します。1 が最も肯定的となります。 分析モデルは、広範囲にわたるテキスト本文と Microsoft の自然言語技術を利用して事前トレーニングされています。 [一部の言語](./text-analytics/language-support.md)については、この API はユーザーが指定したあらゆる未加工テキストを分析し、評価し、呼び出し元のアプリケーションに結果を直接返すことができます。 |

また、コンテナーは Cognitive Services [オールインワン オファリング](https://azure.microsoft.com/pricing/details/cognitive-services/)でサポートされています。 単一の Cognitive Services オールインワン リソースを 1 つ作成し、上記のすべてのコンテナーの種類に同じ課金キーを使用できます。

## <a name="container-availability-in-azure-cognitive-services"></a>Azure Cognitive Services でのコンテナーの使用可能性

Azure Cognitive Services コンテナーはお使いの Azure サブスクリプションを通じてパブリックに使用でき、Docker コンテナー イメージは Microsoft Container Registry または Docker Hub からプルできます。 適切なレジストリからコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用できます。

> [!IMPORTANT]
> 現在、[Face](Face/face-how-to-install-containers.md) と [Recognize Text](Computer-vision/computer-vision-how-to-install-containers.md) にアクセスするにはサインアップ プロセスを完了する必要があります。サインアップでは、お客様自身、お客様の会社、コンテナーを実装したいユース ケースに関するアンケートに答えて送信します。 アクセスを許可されて資格情報を提供されると、Azure Container Registry によってホストされているプライベート コンテナー レジストリから、Face コンテナーと Recognize Text コンテナーのコンテナー イメージをプルできます。

## <a name="prerequisites"></a>前提条件

Azure Cognitive Services コンテナーを使用するには、次の前提条件を満たしている必要があります。

**Docker エンジン**: Docker エンジンをローカルにインストールしている必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms)、[Windows](https://docs.docker.com/docker-for-windows/) 上で Docker 環境を構成するパッケージが用意されています。 Windows では、Linux コンテナーをサポートするように Docker を構成する必要があります。 Docker コンテナーは、[Azure Kubernetes Service](../aks/index.yml) または [Azure Container Instances](../container-instances/index.yml) に直接デプロイできます。

コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。

**Microsoft Container Registry と Docker に関する知識**: レジストリ、リポジトリ、コンテナー、コンテナー イメージなどの Microsoft Container Registry と Docker の両方の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。

Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。

個々のコンテナーには、サーバーやメモリ割り当ての要件など、独自の要件もある場合があります。

## <a name="developer-samples"></a>開発者向けサンプル

開発者向けサンプルは、[GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-containers-samples)から入手できます。

## <a name="next-steps"></a>次の手順

Azure Cognitive Services のコンテナーによって提供される機能をインストールして試してください。

* [Computer Vision コンテナーをインストールして使用する](Computer-vision/computer-vision-how-to-install-containers.md)
* [Face コンテナーをインストールして使用する](Face/face-how-to-install-containers.md)
* [Text Analytics コンテナーをインストールして使用する](text-analytics/how-tos/text-analytics-how-to-install-containers.md)
* [Language Understanding (LUIS) コンテナーをインストールして使用する](LUIS/luis-container-howto.md)
