---
title: コンテナーのインストールと実行
titleSuffix: Text Analytics -  Azure Cognitive Services
description: このチュートリアルでの Text Analytics のコンテナーのダウンロード、インストール、および実行方法。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: d6c0d04966d3a713493485d52ca4e81ba25ab743
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521484"
---
# <a name="install-and-run-text-analytics-containers"></a>Text Analytics コンテナーをインストールして実行する

Text Analytics コンテナーは、未加工のテキストに対して高度な自然言語処理を提供し、主要な機能として、感情分析、キー フレーズ抽出、言語検出の 3 つを備えています。 エンティティ リンク設定は現在コンテナーでサポートされていません。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

Text Analytics コンテナーのいずれかを実行するには、ホスト コンピューターおよびコンテナー環境が必要です。

## <a name="preparation"></a>準備

Text Analytics コンテナーを使用する前に、次の前提条件を満たす必要があります。

|必須|目的|
|--|--|
|Docker エンジン| [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> **Windows では**、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br>|
|Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。| 
|Text Analytics リソース |コンテナーを使用するためには、以下が必要です。<br><br>関連付けられている課金キーと課金エンドポイント URI を取得するための [_Text Analytics_](text-analytics-how-to-access-key.md) Azure リソース。 どちらの値も、Azure portal の [Text Analytics Overview]\(Text Analytics の概要\) ページと [キー] ページで使用でき、コンテナーを開始するために必要です。<br><br>**{BILLING_KEY}**: リソース キー<br><br>**{BILLING_ENDPOINT_URI}**: エンドポイントURI の例: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

### <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表に、各 Text Analytics コンテナーに割り当てる CPU コア (2.6 GHz (ギガヘルツ) 以上) とメモリ (GB 単位) の最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 | TPS<br>(最小、最大)|
|-----------|---------|-------------|--|
|キー フレーズ抽出 | 1 コア、2 GB メモリ | 1 コア、4 GB メモリ |15、30|
|言語検出 | 1 コア、2 GB メモリ | 1 コア、4 GB メモリ |15、30|
|感情分析 | 1 コア、2 GB メモリ | 1 コア、4 GB メモリ |15、30|

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。
* TPS - 1 秒あたりのトランザクション数

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

Text Analytics のコンテナー イメージは Microsoft コンテナー レジストリから入手できます。 

| コンテナー | リポジトリ |
|-----------|------------|
|キー フレーズ抽出 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|言語検出 | `mcr.microsoft.com/azure-cognitive-services/language` |
|感情分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して Microsoft Container Registry からコンテナー イメージをダウンロードします。

Text Analytics コンテナーで使用可能なタグの詳しい説明については、Docker Hub の次のコンテナーを参照してください。

* [キー フレーズ抽出](https://go.microsoft.com/fwlink/?linkid=2018757)
* [言語検出](https://go.microsoft.com/fwlink/?linkid=2018759)
* [感情分析](https://go.microsoft.com/fwlink/?linkid=2018654)

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して、コンテナー イメージをダウンロードします。


### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>キー フレーズ抽出コンテナー用の docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>言語検出コンテナー用の docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>感情コンテナー用の docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. 必要な課金設定を使用して[コンテナーを実行](#run-the-container-with-docker-run)します。 `docker run` コマンドの他の[例](../text-analytics-resource-container-config.md#example-docker-run-commands)もご覧いただけます。 
1. [コンテナーの予測エンドポイントに対するクエリを実行します](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

3 つのコンテナーのいずれかを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 このコマンドには、次のパラメーターが使用されます。

| プレースホルダー | 値 |
|-------------|-------|
|{BILLING_KEY} | このキーは、コンテナーを起動するために使用され、Azure portal の [Text Analytics Keys]\(Text Analytics キー\) ページ上で使用できます。  |
|{BILLING_ENDPOINT_URI} | 課金エンドポイント URI の値は、Azure portal の [Text Analytics Overview]\(Text Analytics の概要\) ページ上で使用できます。|

次の例の `docker run` コマンドでは、これらのパラメーターをお客様独自の値に置き換えてください。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージからキー フレーズ コンテナーを実行します
* 1 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。 

`docker run` コマンドの他の[例](../text-analytics-resource-container-config.md#example-docker-run-commands)もご覧いただけます。 

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。 

コンテナーの API のホストとしては `https://localhost:5000` を使用します。

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

## <a name="summary"></a>まとめ

この記事では、Text Analytics コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて説明しました。 要約すると:

* Text Analytics では、キー フレーズ抽出、言語検出、感情分析をカプセル化する、Docker 用の 3 つの Linux コンテナーが提供されます。
* コンテナー イメージは、Azure の Microsoft Container Registry (MCR) からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使用して、コンテナーのホスト URI を指定することによって、Text Analytics コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次の手順

* 構成設定について、[コンテナーの構成](../text-analytics-resource-container-config.md)を確認する
* [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md) を参照して、機能に関連する問題を解決する。

