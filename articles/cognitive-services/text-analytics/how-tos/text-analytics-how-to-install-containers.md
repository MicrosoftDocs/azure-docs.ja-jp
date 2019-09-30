---
title: コンテナーをインストールして実行する - Text Analytics
titleSuffix: Azure Cognitive Services
description: このチュートリアルでの Text Analytics のコンテナーのダウンロード、インストール、および実行方法。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 893beb479ee42f5de39d6e2e52251ab4113e3e43
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105068"
---
# <a name="install-and-run-text-analytics-containers"></a>Text Analytics コンテナーをインストールして実行する

コンテナーを使用すると、独自の環境で Text Analytic API を実行でき、セキュリティとデータ ガバナンスの固有の要件に対応できます。 Text Analytics コンテナーは、未加工のテキストに対して高度な自然言語処理を提供し、主要な機能として、感情分析、キー フレーズ抽出、言語検出の 3 つを備えています。 エンティティ リンク設定は現在コンテナーでサポートされていません。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

Text Analytics コンテナーのいずれかを実行するには、ホスト コンピューターおよびコンテナー環境が必要です。

## <a name="preparation"></a>準備

Text Analytics コンテナーを使用する前に、次の前提条件を満たす必要があります。

|必須|目的|
|--|--|
|Docker エンジン| [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> **Windows では**、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br>|
|Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。| 
|Text Analytics リソース |コンテナーを使用するためには、以下が必要です。<br><br>関連付けられている API キーとエンドポイント URI を取得するための Azure [Text Analytics](../../cognitive-services-apis-create-account.md) リソース。 どちらの値も、Azure portal の [Text Analytics Overview]\(Text Analytics の概要\) ページと [キー] ページで使用でき、コンテナーを開始するために必要です。<br><br>**{API_KEY}** : **[キー]** ページにある 2 つの利用可能なリソース キーのどちらか<br><br>**{ENDPOINT_URI}** : **[概要]** ページで提供されるエンドポイント|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表に、各 Text Analytics コンテナーに割り当てる CPU コア (2.6 GHz (ギガヘルツ) 以上) とメモリ (GB 単位) の最小値と推奨値を示します。

# <a name="key-phrase-extractiontabkeyphrase"></a>[キー フレーズ抽出](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detectiontablanguage"></a>[言語検出](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysistabsentiment"></a>[感情分析](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。
* TPS - 1 秒あたりのトランザクション数

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

Text Analytics のコンテナー イメージは Microsoft コンテナー レジストリで入手できます。

# <a name="key-phrase-extractiontabkeyphrase"></a>[キー フレーズ抽出](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detectiontablanguage"></a>[言語検出](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysistabsentiment"></a>[感情分析](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Text Analytics コンテナーの Docker プル

# <a name="key-phrase-extractiontabkeyphrase"></a>[キー フレーズ抽出](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detectiontablanguage"></a>[言語検出](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysistabsentiment"></a>[感情分析](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. 必要な課金設定を使用して[コンテナーを実行](#run-the-container-with-docker-run)します。 `docker run` コマンドの他の[例](../text-analytics-resource-container-config.md#example-docker-run-commands)もご覧いただけます。
1. [コンテナーの予測エンドポイントに対するクエリを実行します](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

3 つのコンテナーのいずれかを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 `{ENDPOINT_URI}` と `{API_KEY}` の値を取得する方法について詳しくは、[必要なパラメーターの収集](#gathering-required-parameters)に関するページを参照してください。

`docker run` コマンドの[例](../text-analytics-resource-container-config.md#example-docker-run-commands)を利用できます。

# <a name="key-phrase-extractiontabkeyphrase"></a>[キー フレーズ抽出](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detectiontablanguage"></a>[言語検出](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysistabsentiment"></a>[感情分析](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。

コンテナーの API のホストとしては `http://localhost:5000` を使用します。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

出力[マウント](../text-analytics-resource-container-config.md#mount-settings)とログを有効にした状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。

## <a name="billing"></a>課金

Text Analytics コンテナーは、Azure アカウントの _Text Analytics_ リソースを使用して、Azure に課金情報を送信します。 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](../text-analytics-resource-container-config.md)」を参照してください。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>まとめ

この記事では、Text Analytics コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて説明しました。 要約すると:

* Text Analytics は、Docker 用に 3 つの Linux コンテナーを提供し、さまざまな機能をカプセル化します。
   * *キー フレーズ抽出*
   * *言語検出*
   * *感情分析*
* コンテナー イメージは、Azure の Microsoft Container Registry (MCR) からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使用して、コンテナーのホスト URI を指定することによって、Text Analytics コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次の手順

* 構成設定について、[コンテナーの構成](../text-analytics-resource-container-config.md)を確認する
* [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md) を参照して、機能に関連する問題を解決する。
