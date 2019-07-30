---
title: コンテナーをインストールして実行する方法 - Computer Vision
titlesuffix: Azure Cognitive Services
description: このチュートリアルでの Computer Vision のコンテナーのダウンロード、インストール、および実行方法。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: d72b47d375b8e50cde43e263261551d3010ba013
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704715"
---
# <a name="install-and-run-recognize-text-containers"></a>テキスト認識コンテナーをインストールして実行する

Computer Vision のテキスト認識部分も Docker コンテナーとして利用できます。 レシート、ポスター、名刺など、さまざまな表面や背景を持ついろいろなオブジェクトのイメージから、印刷されたテキストを検出して、抽出できます。  
> [!IMPORTANT]
> テキスト認識コンテナーは現在のところ、英語でのみ機能します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

テキスト認識コンテナーを使用する前に、次の前提条件を満たす必要があります。

|必須|目的|
|--|--|
|Docker エンジン| [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> **Windows では**、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br>|
|Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。| 
|Azure `Cognitive Services` リソース |コンテナーを使用するためには、以下が必要です。<br><br>_Cognitive Services_ Azure リソースおよび関連する課金キー (課金エンドポイント URI)。 どちらの値も、対象リソースの概要ページとキー ページで使用でき、コンテナーを開始するために必要です。 `vision/v2.0` ルーティングをエンドポイント URI に追加する必要があります。次の BILLING_ENDPOINT_URI の例を参照してください。 <br><br>**{BILLING_KEY}** : リソース キー<br><br>**{BILLING_ENDPOINT_URI}** : エンドポイントURI の例: `https://westus.api.cognitive.microsoft.com/vision/v2.0`|

## <a name="request-access-to-the-private-container-registry"></a>プライベート コンテナー レジストリへのアクセスの要求

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表に、各テキスト認識コンテナーに割り当てる CPU コアとメモリの最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 |TPS<br>(最小、最大)|
|-----------|---------|-------------|--|
|テキスト認識|1 コア、8 GB メモリ、0.5 TPS|2 コア、8 GB メモリ、1 TPS|0.5、1|

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。
* TPS - 1 秒あたりのトランザクション数

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

テキスト認識のコンテナー イメージを利用できます。 

| コンテナー | リポジトリ |
|-----------|------------|
|テキスト認識 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して、コンテナー イメージをダウンロードします。


### <a name="docker-pull-for-the-recognize-text-container"></a>テキスト認識コンテナー用の Docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. 必要な課金設定を使用して[コンテナーを実行](#run-the-container-with-docker-run)します。 `docker run` コマンドの他の[例](computer-vision-resource-container-config.md)もご覧いただけます。 
1. [コンテナーの予測エンドポイントに対するクエリを実行します](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

コンテナーを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 このコマンドには、次のパラメーターが使用されます。

| プレースホルダー | 値 |
|-------------|-------|
|{BILLING_KEY} | このキーは、コンテナーを起動するために使用され、Azure `Cognitive Services` のキー ページ上で使用できます。  |
|{BILLING_ENDPOINT_URI} | 課金エンドポイント URI の値。 例: `https://westus.api.cognitive.microsoft.com/vision/v2.0`|

`vision/v2.0` ルーティングをエンドポイント URI に追加する必要があります。次の BILLING_ENDPOINT_URI の例を参照してください。

次の例の `docker run` コマンドでは、これらのパラメーターをお客様独自の値に置き換えてください。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから認識コンテナーを実行します。
* 1 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。 

`docker run` コマンドの他の[例](./computer-vision-resource-container-config.md#example-docker-run-commands)もご覧いただけます。 

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。 

コンテナーの API のホストとしては `http://localhost:5000` を使用します。

### <a name="asynchronous-text-recognition"></a>非同期のテキスト認識

Computer Vision サービスで該当する REST 操作を使用する方法と同じように、`POST /vision/v2.0/recognizeText` 操作と `GET /vision/v2.0/textOperations/*{id}*` 操作を同時に使用し、イメージ内の印刷テキストを非同期認識できます。 テキスト認識コンテナーでは現在のところ、印刷されたテキストのみ認識され、手書きのテキストは認識されません。そのため、Computer Vision サービス操作に通常指定される `mode` パラメーターはテキスト認識コンテナーで無視されます。

### <a name="synchronous-text-recognition"></a>同期のテキスト認識

`POST /vision/v2.0/recognizeTextDirect` 操作を使用し、イメージ内の印刷されたテキストが同期認識されます。 この操作は同期のため、この操作の要求本文は `POST /vision/v2.0/recognizeText` 操作のそれと同じになりますが、この操作の応答本文は `GET /vision/v2.0/textOperations/*{id}*` 操作によって返されるそれと同じになります。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

出力[マウント](./computer-vision-resource-container-config.md#mount-settings)とログを有効にした状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。 


## <a name="billing"></a>課金

テキスト認識コンテナーは、Azure アカウントの _テキスト認識_ リソースを使用して、Azure に課金情報を送信します。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](./computer-vision-resource-container-config.md)」を参照してください。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>まとめ

この記事では、テキスト認識コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて説明しました。 要約すると:

* テキスト認識は、テキスト認識をカプセル化する、Docker 用の Linux コンテナーを提供します。
* コンテナー イメージは、Azure の Microsoft Container Registry (MCR) からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使用して、コンテナーのホスト URI を指定することによって、テキスト認識コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次の手順

* 構成設定について、[コンテナーの構成](computer-vision-resource-container-config.md)を確認する
* [Computer Vision の概要](Home.md)ページを読み、印刷されたテキストと手書きのテキストの認識の詳細について確認する  
* コンテナーによりサポートされるメソッドの詳細を [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) ページで確認する。
* [よく寄せられる質問 (FAQ)](FAQ.md) を参照して、Computer Vision 機能に関連する問題を解決する。
* さらに [Cognitive Services コンテナー](../cognitive-services-container-support.md)を使用する
